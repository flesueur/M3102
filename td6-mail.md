TD6 Mail (3 heures)
====================

_Compte-rendu à préparer et déposer en binôme_

> Si vous n'aviez pas validé le TP précédent (DNS) et avez besoin de repartir d'une base DNS fonctionnelle, vous trouverez les fichiers et explications nécessaires sur Moodle.

Ce TD couvre la configuration et l'utilisation du mail, côté client et serveur. Les interactions sont complexes, l'objectif n'est pas d'avoir absolument un système mail entièrement fonctionnel, mais plutôt de bien appréhender la construction générale de ce type de système. Soyez méthodiques dans votre travail car vous allez manipuler de nombreuses perspectives différentes !

Ce TD sera réalisé dans la VM MI-LXC disponible [ici](https://filesender.renater.fr/?s=download&token=2f121a18-f94d-45d1-a079-f68229ebdfa9). Avant de lancer la VM, il peut être nécessaire de diminuer la RAM allouée. Par défaut, la VM a 3GO : si vous avez 4GO sur votre machine physique, il vaut mieux diminuer à 2GO, voire 1.5GO pour la VM (la VM devrait fonctionner de manière correcte toujours).

> Si vous êtes sous Windows et que la VM ne fonctionne pas avec VirtualBox, vous pouvez utiliser à la place VMWare Player. Dans ce cas, il faudra cliquer sur "Retry" lors de l'import puis installer le paquet open-vm-tools-desktop dans la VM pour profiter du redimensionnement automatique du bureau (`apt install open-vm-tools-desktop` dans un shell).


Cheat sheet
===========

Voici un petit résumé des commandes dont vous aurez besoin :

| Commande | Description | Utilisation |
| -------- | ----------- | ----------- |
| print    | Génère la cartographie du réseau | ./mi-lxc.py print |
| attach   | Permet d'avoir un shell sur une machine | ./mi-lxc.py attach iutva-infra |
| display  | Lance un affichage sur la machine cible | ./mi-lxc.py display isp-a-home |
| start    | Démarre la plateforme pédagogique    | ./mi-lxc.py start |
| stop     | Éteint la plateforme pédagogique        | ./mi-lxc.py stop |

Rappel: Vous devez être dans le répertoire `/root/mi-lxc/` pour exécuter ces commandes.


Observation d'un mail (45 minutes)
=====================

En dehors de MI-LXC, ouvrez un mail quelconque sur une de vos boîtes mails personnelles. Affichez le code source de ce mail (souvent accessible par clic droit puis "Montrer l'original" (zimbra) ou "Afficher le source", ou Ctrl+u) et analysez le résultat. Vous y trouverez une partie d'en-têtes (au format `Mot-clé: Valeur`, en début de source) puis une partie contenu.

> Question 1 : Sur ce mail :
>
> 1. Dissociez la partie en-têtes de la partie contenu
> 2. Décrivez les en-têtes qui vous paraissent clairs et transparents.
> 3. Les en-têtes `Received:`, ajoutés par chaque serveur intermédiaire, permettent de retracer le chemin du mail. Dans quel sens ce chemin est-il décrit ?
> 4. Décrivez le chemin de votre mail en analysant les adresses IP : où a lieu chaque étape ? Vous pouvez utiliser `dig -x 1.2.3.4` pour obtenir le DNS inversé de l'IP 1.2.3.4, ou des sites comme [https://ipinfo.io/](https://ipinfo.io/), [https://www.onyphe.io/](https://www.onyphe.io/) ou [https://www.ipinfodb.com/](https://www.ipinfodb.com/) pour obtenir des informations supplémentaires sur les adresses IP. Lorsque les adresses IP sont de type local, locales à quelle organisation ?


Installation et configuration d'un système de mail (1h45)
============================================================

Architecture (20 minutes)
------------

Dans votre organisation "iutva", vous allez ajouter 2 machines supplémentaires :

* un serveur mail, qui hébergera les serveurs SMTP et IMAP pour votre domaine, et qui peut prendre l'IP 100.90.0.3
* un poste de travail, sur lequel nous installerons un logiciel graphique pour écrire et lire des mails avec l'adresse `debian@iutva.milxc`, et qui peut prendre l'IP 100.90.0.4

Pour cela, le contenu de votre fichier `groups/iutva/local.json` peut devenir :
```json
{
  "comment": "IUTVA AS",
  "containers": {
    "infra": {
      "container": "infra",
      "interfaces": [
        {"bridge": "lan", "ipv4": "100.90.0.2/16", "ipv6": "2001:db8:90::2/48"}
      ],
      "gatewayv4": "100.90.0.1",
      "gatewayv6": "2001:db8:90::1",
      "templates": [
        {"template": "nodhcp", "domain": "iutva.milxc", "ns": "100.100.100.100"}
      ]
    },
    "mail": {
      "container": "mail",
      "interfaces": [
        {"bridge": "lan", "ipv4": "100.90.0.3/16", "ipv6": "2001:db8:90::3/48"}
      ],
      "gatewayv4": "100.90.0.1",
      "gatewayv6": "2001:db8:90::1",
      "templates": [
        {"template": "nodhcp", "domain": "iutva.milxc", "ns": "100.100.100.100"}
      ]
    },
    "poste": {
      "container": "poste",
      "interfaces": [
        {"bridge": "lan", "ipv4": "100.90.0.4/16", "ipv6": "2001:db8:90::4/48"}
      ],
      "gatewayv4": "100.90.0.1",
      "gatewayv6": "2001:db8:90::1",
      "templates": [
        {"template": "nodhcp", "domain": "iutva.milxc", "ns": "100.100.100.100"}
      ]
    }
  }
}
```

Une fois ce fichier local.json adapté, exécutez :

* `./mi-lxc.py print` pour visualiser le résultat
* `./mi-lxc.py create` pour créer ces deux nouveaux hôtes sur lesquels nous allons travailler dans la suite du TD
* `./mi-lxc.py start` pour démarrer le tout

Afin d'échanger des mails, vous aurez besoin d'un interlocuteur. Vous pourrez pour cela utiliser la machine target-commercial qui a un compte mail "commercial@target.milxc" configuré pour l'utilisateur local commercial. L'infrastructure DNS et mail de "target.milxc" est opérée sur la machine target-dmz (pour info, mais il est probable que vous n'ayez pas besoin de vous y connecter).

Petit résumé :

| Machine | Description | Connexion |
| ------- | ----------- | --------- |
| iutva-mail | Serveur SMTP/IMAP pour les adresses "@iutva.milxc" | `./mi-lxc.py attach iutva-mail` |
| iutva-poste | Bureau graphique pour écrire/lire des mails avec l'adresse "debian@iutva.milxc" | `./mi-lxc.py display iutva-poste` |
| iutva-infra | Serveur DNS pour le domaine "iutva.milxc" | `./mi-lxc.py attach iutva-infra` |
| target-commercial | Bureau graphique pour écrire/lire des mails avec l'adresse "commercial@target.milxc" | `./mi-lxc.py display commercial@target-commercial` |

**Attention ! La connexion à target-commercial se fait avec commercial@target-commercial (et non target-commercial tout court) afin de se connecter avec l'utilisateur local commercial, qui est celui pour lequel le mail est pré-configuré.**

> Question 2 : Faîtes un schéma global (capture de `./mi-lxc.py print` ou juste les machines impliquées) inventoriant les 5 machines iutva-poste, iutva-mail, iutva-infra, target-dmz, target-commercial, leurs adresses IPv4, leurs noms DNS (déjà existants ou à configurer par la suite) et leur rôle dans le système de mail. Pour la partie Target, vous pouvez vous aider de l'extrait de la zone DNS de Target dans le [cours DNS](https://git.kaz.bzh/francois.lesueur/M3102/src/branch/master/cm3-dns.md#exemple-de-zone-dns).

SMTP (50 minutes)
----

### Installation

Nous allons installer un serveur SMTP (Postfix) sur la machine iutva-mail. Il aura pour rôle d'envoyer les mails issus de l'organisation iutva et de recevoir les mails à destination de "@iutva.milxc".

Il faut d'abord l'installer avec `apt install postfix`. Lors de la configuration du paquet, choisissez la configuration "Internet Site" (vous allez recevoir et envoyer des mails) et spécifiez "iutva.milxc" comme "System mail name".

### Envoi d'un mail avec netcat

Depuis un shell sur la machine iutva-mail, nous allons envoyer un mail à l'adresse "commercial@target.milxc". SMTP est un protocole en mode texte, nous allons utiliser netcat. Pour vous connecter au serveur SMTP avec netcat : `netcat localhost 25`

Ensuite, vous aurez à taper les textes suivants (sans les guillemets, entrée après chaque commande) :

* "HELO" : message de présentation, par exemple "HELO iutva.milxc"
* "MAIL FROM: " : spécifie l'adresse d'expédition, par exemple "MAIL FROM: debian@iutva.milxc"
* "RCPT TO: " : spécifie l'adresse du destinataire, par exemple "RCPT TO: commercial@target.milxc"
* "DATA" : annonce le contenu du message ensuite
* Après avoir entré la commande DATA (+ entrée), tapez votre texte. C'est au début de ce texte qu'iraient les champs (non strictement nécessaires ici) From, To, Subject tels que vus dans les en-têtes à la question 1, puis le contenu du mail
* En fin de mail, un "." seul sur une ligne, puis encore entrée pour valider la fin du message
* "QUIT" pour se déconnecter ensuite

> Question 3 : Faîtes une copie de votre session netcat.

Sur le claws-mail de `./mi-lxc.py display commercial@target-commercial`, validez que vous avez bien reçu ce mail. Essayez en changeant l'adresse d'expéditeur, ou en écrivant à une adresse réelle hors de MI-LXC.

**Attention ! Ne prenez pas une identité réelle comme expéditeur. Il s'agirait d'une usurpation d'identité qui est une infraction pénale.**

Sur la machine iutva-mail, affichez les logs de Postfix qui sont dans le fichier "/var/log/mail.log", retrouvez la trace du mail que vous venez d'émettre.

> Question 4 : Faîtes une copie de cette partie de log.

### Réception d'un mail par iutva-mail

Depuis le display de target-commercial, envoyez un mail à "debian@iutva.milxc". Vous constaterez a priori une erreur (un mail d'erreur qui arrive dans la boîte de réception de target-commercial); si vous ne recevez pas d'erreur explicite (dépendant d'éléments de configuration de votre côté), le mail est en fait toujours en tampon sur le serveur SMTP du côté de l'organisation Target, et donc non livré. En effet, le serveur SMTP côté Target ne sait pas trouver le serveur SMTP pour @iutva.milxc.

Ajoutez maintenant le champ MX adapté dans la zone DNS de iutva. Ce champ MX doit être situé avant les champs CNAME/A déjà existants dans la zone et doit mentionner un nom (pas une IP) qui sera défini par un champ A ou CNAME ensuite. Reprenez l'exemple du cours DNS [ici](https://git.kaz.bzh/francois.lesueur/M3102/src/branch/master/cm3-dns.md#exemple-de-zone-dns), mettez à jour votre zone sur iutva-infra puis redémarrez le serveur NSD.

> Question 5 : Faîtes une copie de votre zone DNS.

> Si le champ MX n'est pas placé au début de la zone, avant les enregistrements de type CNAME/A, il faut commencer sa ligne en ajoutant un @ pour "retourner en haut de zone".

Renvoyez un mail depuis target-commercial et vérifiez qu'il n'y a plus d'erreur retournée. S'il y a une erreur, depuis cette même machine, vérifiez que vous avez bien enregistré le champ MX avec `dig iutva.milxc MX`

Enfin, retrouvez la trace de ce mail dans les logs sur iutva-mail. Une fois arrivé, vous pouvez voir ce mail sur iutva-mail dans le fichier `/var/spool/mail/debian`. Il est reçu, stocké et prêt à être récupéré par le destinataire !

> Question 6 : Faîtes une copie de cette partie de log.

> Une documentation assez complète pour un déploiement complet et sain est proposée sur le [Wiki Debian](https://wiki.debian.org/Postfix).

IMAP (15 minutes)
----

Toujours sur iutva-mail, installez dovecot-imapd : `apt install dovecot-imapd`

IMAP est aussi un protocole en mode texte : `netcat localhost 143`. Ensuite, vous aurez à taper :

* a LOGIN debian debian
* a SELECT INBOX
* a FETCH 1 full
* a LOGOUT

Vous verrez normalement ainsi le contenu du mail précédemment envoyé.

> Question 7 : Faîtes une copie de votre session netcat.


Client (20 minutes)
------

Nous allons maintenant installer et configurer un client mail sur la machine iutva-poste. En graphique : `./mi-lxc.py display iutva-poste`

Installons claws-mail : `apt install claws-mail`. Lors du premier lancement, vous aurez à configurer le compte : le compte est debian, mot de passe debian, sur le serveur mail précédemment entré dans le DNS. Vous aurez à accepter des certificats auto-signés (c'est mal, cf cours crypto et rappels ensuite, on le fait ici car nous n'avons pas déployé la CA dans MI-LXC mais c'est évidemment à proscrire dans un vrai déploiement).

> La configuration des clients mail peut être plus ou moins automatisée. Par exemple, Thunderbird essaie automatiquement les serveurs dont l'entrée DNS vaut "mail.<le domaine de l'adresse>", "smtp.<le domaine de l'adresse>", etc. Des champs DNS de type SRV permettent également d'annoncer les serveurs :
>
> * _imap._tcp   			SRV   0 1 143 mail
> * _submission._tcp  SRV 	0 1 587 mail

La consultation des mails doit fonctionner. Lors de l'envoi d'un mail vers commercial@target.milxc, votre serveur SMTP va par contre refuser votre demande : dans sa configuration par défaut, il n'accepte pas de relayer les mails qui ne sont pas pour son domaine (éviter d'être un relais ouvert). Il faudrait configurer une authentification sur le serveur SMTP (afin qu'il puisse authentifier ses utilisateurs légitimes à expédier du courrier par son biais), comme décrit par exemple [ici](https://doc.dovecot.org/configuration_manual/howto/postfix_and_dovecot_sasl/). Dans ce TD, on se contentera d'accepter le relai pour le réseau local : sur iutva-mail, éditez le fichier `/etc/postfix/main.cf` et modifiez le paramètre `mynetworks` pour lui faire accepter "100.90.0.0/16", ie, votre réseau interne, puis redémarrez Postfix.

> Question 8 : Faîtes une capture d'écran de la boîte de réception de claws-mail.

Un peu de Wireshark (30 minutes)
===================

Certaines communications passent en clair ici. Pour un vrai déploiement, il faudrait évidemment chiffrer toutes les communications avec des certificats valides, mais nous pouvons du coup observer le protocole.

> Question 9 : Proposez où lancer des wireshark pour observer, lors d'un mail envoyé depuis target-commercial vers l'adresse debian@iutva.milxc qui sera relevée sur iutva-poste : l'envoi depuis claws-mail, l'échange entre les SMTP, la relève par claws-mail. Décrivez les points de capture, intégrez des screenshots de vos captures, précisez les communications claires ou chiffrées.

> Question 10 : Avez-vous selon vous un "bon" certificat sur le SMTP de iutva-mail ? Qu'en déduire sur la sécurité de l'échange entre target-dmz et iutva-mail ? En notant que ceci est bien le comportement par défaut lors de l'installation.


SpamAssassin (Bonus)
============

SpamAssassin est un filtre antispam à intégrer sur les mails reçus. Pour l'installer sur iutva-mail : `apt install spamassassin`. Vous utliserez ensuite la partie "Setup Postfix with SA 3.4.2 (Buster) as a Content Filter", sous-partie "Postfix" du guide du [Wiki Debian](https://wiki.debian.org/DebianSpamAssassin)

Envoyez un mail depuis target-commercial à l'adresse debian@iutva.milxc et vérifiez, dans les logs de postfix (sur iutva-mail) et dans le code source du mail reçu, le bon passage par SpamAssassin.

> Question 11 : Intégrez la partie de log postfix et la partie d'en-têtes du mail reçu qui montre le bon passage par SpamAssassin.


SPF/DKIM/DMARC (Bonus)
==============

Vous trouverez un bon guide pour la configuration SPF/DKIM/DMARC pour Postfix sous Debian [ici](https://www.malekal.com/installer-configurer-postfix-spf-dkim-dmarc/)

> Question 12 : Décrivez les modifications de fichiers de configuration.

Un petit webmail ? (Bonus)
==========================

Roundcube est un webmail simple disponible dans les paquets Debian.

> Question 13 : Décrivez l'installation et la configuration.



**Votre compte-rendu doit être déposé sur Moodle en fin de journée au format PDF uniquement, un dépôt par binôme.**
