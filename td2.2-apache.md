TD2.2 Apache/CMS
================

Ce TD couvre la configuration et l'utilisation d'un serveur HTTP Apache ainsi qu'un CMS Wordpress.

Ce TD sera réalisé dans la VM MI-LXC disponible [ici](https://filesender.renater.fr/?s=download&token=2ca6036b-49b8-4b4c-93bb-95c5de051400). Avant de lancer la VM, il peut être nécessaire de diminuer la RAM allouée. Par défaut, la VM a 3GO : si vous avez 4GO sur votre machine physique, il vaut mieux diminuer à 2GO, voire 1.5GO pour la VM (la VM devrait fonctionner de manière correcte toujours).

> Si vous êtes sous Windows et que la VM ne fonctionne pas avec VirtualBox, vous pouvez utiliser à la place VMWare Player. Dans ce cas, il faudra cliquer sur "Retry" lors de l'import puis installer le paquet open-vm-tools-desktop dans la VM pour profiter du redimensionnement automatique du bureau (`apt install open-vm-tools-desktop` dans un shell).


Cheat sheet
===========

Voici un petit résumé des commandes dont vous aurez besoin :

| Commande | Description | Utilisation |
| -------- | ----------- | ----------- |
| print    | Génère la cartographie du réseau | ./mi-lxc.py print |
| attach   | Permet d'avoir un shell sur une machine | ./mi-lxc.py attach root@target-commercial |
| display  | Lance un affichage sur la machine cible | ./mi-lxc.py display target-commercial |
| start    | Lance la construction du bac à sable    | ./mi-lxc.py start |
| stop     | Éteint la plateforme pédagogique        | ./mi-lxc.py stop |

Rappel: Vous devez être dans le répertoire `/root/mi-lxc/` pour exécuter ces commandes.


Conception
==========

Vous allez étudier une pile serveur LAMP :

* Linux
* Apache
* MariaDB (MySQL)
* PHP

> Question 1 : Sur quel serveur allez-vous installer/configurer cette pile LAMP ? Depuis quelle machine et comment allez-vous tester l'utilisation en tant que client ? Quelles sont les adresses IP de ces deux machines ?


Apache - Serveur HTTP
=====================

Un serveur Apache est déjà installé par défaut. Validez que la connexion à l'Apache du serveur retenu dans la question 1 fonctionne bien avec Firefox depuis le poste client choisi.

Wireshark
---------

Étudiez la connexion avec Wireshark depuis le poste client (en session graphique "display"). Il faut être root pour lancer wireshark (donc "su" dans le terminal graphique sur le bureau du client avant de lancer wireshark. Attention à faire juste "su" et non "su -" !). Dans wireshark, lancez la capture sur eth0, puis vous pouvez utiliser le filtre "http" pour n'afficher que ce qui a trait au HTTP.

> Question 2 : Décrivez en quelques lignes la connexion HTTP entre le client et le serveur (les quelques échanges ou une capture d'écran de la zone wireshark montrant cet échange)

Connectez-vous ensuite avec deux logiciels clients en ligne de commande (cherchez sur internet comment faire) :

* curl ou wget d'une part
* netcat (binaire "nc", à lancer avec l'option -C) d'autre part

> Question 3 : Quelles commandes ont permis d'obtenir l'index ? À quoi sert l'option -C de netcat ?


Ajout de pages HTML
-----------------------

La racine du serveur est dans le dossier `/var/www/html`, c'est-à-dire que le contenu de ce dossier est celui servi par le serveur Apache. Retrouvez le fichier d'index que vous aviez précédemment reçu côté client.

Créez un sous-dossier dans `/var/www/html` et ajoutez-y un second fichier html. Récupérez ce nouveau fichier depuis un client.


Contrôle d'accès
----------------

Nous allons maintenant contrôler l'accès au sous-dossier créé (par login/mot de passe).

Vous devez tout d'abord créer un fichier `.htpasswd` qui va contenir les couples login/mot de passe autorisés. Vous pouvez le placer dans le sous-dossier à protéger ou ailleurs, par exemple dans un dossier (à créer) `/etc/apache2/htpasswd/`. Vous utiliserez pour cela la commande `htpasswd` en faisant attention à utiliser la fonction de hachage bcrypt (une option à htpasswd) au lieu du MD5 utilisé par défaut (Explication [ici](https://nakedsecurity.sophos.com/2013/11/20/serious-security-how-to-store-your-users-passwords-safely/), pas à lire en TD mais pour les curieux).

> Question 4 : Quelles commandes avez-vous tapées ? Quel est votre fichier .htpasswd résultat ?

Vous devez ensuite spécifier quel dossier doit être protégé. Vous trouverez le nécessaire dans la [documentation officielle](https://httpd.apache.org/docs/2.4/fr/howto/auth.html), à parcourir jusqu'à la section "Autorisation d'accès à plusieurs personnes" (incluse). Lisez attentivement la partie sur les prérequis, votre fichier de configuration apache2 est `/etc/apache2/apache2.conf` (et non `httpd.conf` comme mentionné à certains endroits de cette documentation). Validez enfin le fonctionnement en vérifiant que l'authentification est bien demandée au client lors d'une connexion graphique avec Firefox.

> Question 5 : Quelles modifications/ajouts avez-vous fait ?

> Question 6 : Retrouvez dans Wireshark la phase d'authentification.



PHP
===

Ajoutez un fichier PHP (un simple hello world) dans le dossier `/var/www/html` puis récupérez-le depuis un client : que constatez-vous ?

Le paquet PHP pour Apache est libapache2-mod-php. Installez-le et validez l'interprétation de votre fichier PHP.

> Question 7 : Faîtes une capture d'écran de votre code source PHP et du résultat dans un navigateur.


MariaDB
=======

MariaDB est un fork de MySQL que nous allons utiliser ici, avec PHPMyAdmin pour aider l'administration (création de bases, d'utilisateurs, etc.).

Installez les paquets mariadb-server et phpmyadmin. Lors de l'installation de phpmyadmin, vous aurez à répondre à quelques questions : utilisez la fonctionnalité dbconfig, notez le mot de passe que vous choisirez pour l'utilisateur phpmyadmin et activez la configuration pour apache2.

Pour finaliser l'installation, exécutez `mysql_secure_installation`, qui permettra de mieux configurer MariaDB et de spécifier un mot de passe root (par défaut vide et non autorisé).

> Question 8 : Faîtes une capture d'écran de PHPMyAdmin connecté en tant que root


WordPress
=========

Enfin, nous allons installer le CMS WordPress. Un CMS (Content Management System) permet de gérer du contenu. WordPress est un projet libre qui va s'exécuter sur notre pile LAMP.

Vous pouvez procéder de deux façons différentes :

* Installer le paquet debian "wordpress", présent dans les dépôts, puis suivre la documentation associée sur le [wiki Debian](https://wiki.debian.org/WordPress) (configuration apache, création de la base de données, configuration wordpress);
* Ou suivre la documentation du site [WordPress](https://fr.wordpress.org/txt-install/) (téléchargement et extraction d'un tar.gz, configuration apache, création de la base de données, configuration wordpress).

Enfin, installez un thème, un plugin et écrivez un premier billet

> Question 9 : Faîtes une capture d'écran de votre WordPress fonctionnel

> Question 10 : Comme tout logiciel (que ce soit un binaire compilé depuis un code source C comme le serveur HTTPD Apache ou un code source PHP interprété à l'exécution), WordPress doit être tenu à jour afin d'installer les correctifs de sécurité. Pour chacune des méthodes d'installation, comment se passera ce mécanisme de mise à jour ? Pouvez-vous trouver des avantages et inconvénients à ces méthodes ?

**Votre compte-rendu doit être déposé sur Moodle en fin de journée au format PDF uniquement**
