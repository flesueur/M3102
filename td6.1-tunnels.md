TD6.1 Tunnels et bonus (3 heures)
====================

_Compte-rendu à préparer et déposer en binôme_

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


Tunnels
=======

* Dans le TP Firewall, vous avez protégé des _ports_ pour prévenir certains usages
* En utilisant des tunnels, vous allez voir comment cacher une connexion (par exemple HTTP) dans une autre connexion (par exemple SSH)

SSH
---

L'outil ssh permet de réaliser des tunnels avec ses options -L (Local) et -R (Remote). Deux exemples :
* `ssh -L 8080:192.168.1.2:80 192.168.2.4`:
  * La machine locale ouvre une connexion SSH vers la machine 192.168.2.4
  * La machine locale ouvre le port 8080 en écoute
  * Tout ce qui entre localement sur ce port 8080 emprunte le tunnel SSH jusqu'à 192.168.2.4 puis la machine 192.168.2.4 route ces paquets vers 192.168.1.2 sur le port 80
* `ssh -R 8080:192.168.1.2:80 192.168.2.4` est symétrique :
  * La machine locale ouvre une connexion SSH vers la machine 192.168.2.4
  * La machine 192.168.2.4 ouvre le port 8080 en écoute
  * Tout ce qui entre sur 192.168.2.4 sur ce port 8080 emprunte le tunnel SSH jusqu'au client SSH puis ce client SSH route ces paquets vers 192.168.1.2 sur le port 80

Vous allez mettre en place deux tunnels SSH, chacun depuis target-dev vers isp-a-home :
* Dans le premier, vous utiliserez -L pour qu'un `curl localhost:8080` exécuté sur target-dev récupère la page sur le serveur web (port 80) de 100.81.0.2 (un site externe dont on aurait souhaité interdire l'accès depuis target)
* Dans le second, vous utiliserez -R pour qu'un `curl localhost:8080` exécuté sur isp-a-home récupère la page sur le serveur web (port 80) de 100.80.0.5 (l'intranet de target)

> Question 1 : Recopiez les commandes ssh exécutées.

> Question 2 : Utilisez Wireshark (avec le filtre ssh ou http) pour afficher les paquets SSH entre target-dev et isp-a-home et les paquets HTTP vers 100.81.0.2 et 100.80.0.5.

Netcat
------


Imaginez que vous êtes le développeur et que vous souhaitez fournir un accès au serveur web interne de prototypage "target-intranet" à un client externe, alors que celui-ci n'est normalement pas accessible de l'externe ! Vous allez créer un tunnel pour contourner la politique de sécurité. Vous disposez pour cela des machines "target-dev" (votre poste de travail interne) et "isp-a-home" (une machine extérieure, à votre domicile).

Nous allons utiliser l'outil `netcat` pour établir un tunnel très simple.

Connectez-vous sur la machine "isp-a-home". Nous allons commencer par éteindre le service _Apache_ en écoute pour libérer le port 80 qui nous sera utile puis nous allons écouter les connexions sur le port HTTP (TCP/80).
```bash
service apache2 stop
while true; do nc -v -l -p 80 -c "nc -l -p 8080"; done
```

Enfin, côté "target-dev", nous mettons en place la connexion sortante vers la machine distante:
```bash
while true; do nc -v 100.120.0.3 80 -c "nc 100.80.0.5 80"; sleep 2; done
```

>Pour rappel :
>* 100.120.0.3 = isp-a-home
>* 100.80.0.5 = target-intranet

Testez avec la machine "isp-a-hacker" que vous pouvez bien accéder au serveur intranet depuis l'externe sans aucun contrôle via l'URL `http://100.120.0.3:8080`

> Question 3 : À l'aide d'un schéma, expliquez ce tunnel.

> Question 4 : Retrouvez-le dans Wireshark

Il est très difficile de bloquer ou même détecter les tunnels (tunnel chiffré par SSH, ou qui mime une apparence de HTTP, etc.)

> Pour la suite, vous pouvez aborder la section de votre choix entre HTTP, Mail et interactions. Expliquez le déroulé de vos actions dans votre compte-rendu.

HTTP
====

* Maintenant que l'on a un DNS, configurez des virtualhosts : [doc apache, partie "Fonctionnement de plusieurs serveurs virtuels par nom sur une seule adresse IP."](http://httpd.apache.org/docs/2.4/fr/vhosts/examples.html), [doc adaptée debian](https://linuxize.com/post/how-to-set-up-apache-virtual-hosts-on-debian-10/). Prenez le temps de comprendre le concept : héberger plusieurs sites sur le même serveur, avec des CNAME (au niveau DNS) distincts pointant au final sur la même IP
* Reprendre le TD2.2 à partir de "PHP"

Le mail
=======

* Les bonus du TD 4.1
* Installez un webmail

Les interactions
================
Faîtes du Wireshark à plusieurs endroits, reprenez en main l'infra générale et expliquez les différents fonctionnements observés :
* Par exemple, montrez un enchaînement DNS-SMTP (recherche du MX distant, puis envoi SMTP)
* Proposez d'autres séquences de protocoles liées à une même action


**Votre compte-rendu doit être déposé sur Moodle en fin de journée au format PDF uniquement, un dépôt par binôme.**
