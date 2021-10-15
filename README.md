# M3102 - DUT2 Info - Services réseaux

Cette page recense les séances du module M3102 "Services réseaux". L'objectif est d'expliquer le fonctionnement d'Internet en tant que réseau de réseaux, Internet est donc notamment l'union des systèmes d'information de tous les acteurs. Séance après séance, vous allez créer un SI minimal (DNS, HTTP, Mail, etc.) interconnectable avec le reste du monde et ainsi constater qu'Internet est le résultat de chaque acteur/AS qui offre et consomme des services.

Ce module se situe entre M2102 (Couches OSI, de la modulation à IP, avec un peu de routage/adressage IP) et M4101C. Il dure 6 semaines, avec chaque semaine 1 séance de cours (1h30) et 2 séances de TD (2*1h30). Le contenu sera détaillé au fur et à mesure de la période.

Une large part des séances pratiques sera réalisée sur la plateforme MI-LXC (https://github.com/flesueur/mi-lxc), pour laquelle il faudra télécharger une VM Virtualbox **avant** le TD1.2 "Découverte de MI-LXC" (lien à venir, il faudra arriver en séance avec Virtualbox installé et le .ova de MI-LXC déjà téléchargé, l'installation et la déouverte de la VM seront ensuite le programme de la séance).


## Programme prévisionnel

* S1 :
  * CM1 introduction "C'est quoi internet ?" et panorama du cours
  * TD1.1 Wargame shell [Overthewire Bandit](https://overthewire.org/wargames/bandit/)
  * TD1.2 Découverte [MI-LXC](https://github.com/flesueur/mi-lxc)
* S2 :
  * CM2 Cryptographie et sécurité des communications
  * TD2.1 Cryptogaphie JdR
  * TD2.2 Apache/CMS
* S3 :
  * CM3 DNS (en ligne : [S. Bortzmeyer](https://www.iletaitunefoisinternet.fr/post/1-dns-bortzmeyer/))
  * TD3.1 DNS(SEC)
  * TD3.2 CA ACME
* S4 :
  * CM4 Mail (en ligne : [B. Sonntag](https://www.iletaitunefoisinternet.fr/post/7-email-sonntag/))
  * TD4.1 SMTP, POP, IMAP
  * TD4.2 SPF, DKIM, Spam, webmail
* S5 :
  * CM5 Firewall
  * TD5.1 IPTables
  * TD5.2 Architecture réseau
* S6 :
  * CM6 Administration centralisée
  * TD6.1 DHCP
  * TD6.2 SSH, Ansible

## Pour les curieux

Quelques bonnes références :
* l'ensemble des présentations sur le site [http://iletaitunefoisinternet.fr/](http://iletaitunefoisinternet.fr/) sont de grande qualité
* Stéphane Bortzmeyer : [Blog](https://www.bortzmeyer.org/), [Twitter](https://twitter.com/bortzmeyer), [Mastodon](https://mastodon.gougere.fr/@bortzmeyer)
* Cécile Morange : [Blog](https://blog.ataxya.net/), [Twitter](https://twitter.com/AtaxyaNetwork/)

## Évaluation

La note de l'UE est composée de :
* CC : chaque TD donne lieu à un compte-rendu à déposer sur Moodle (page du cours, zone de dépôt prévue), 2 comptes-rendus seront évalués
* DS : en fin de période
