# M3102 - DUT2 Info - Services réseaux

_François Lesueur ([francois.lesueur@univ-ubs.fr](mailto:francois.lesueur@univ-ubs.fr))_

Cette page recense les séances du module M3102 "Services réseaux". L'objectif est d'expliquer le fonctionnement d'Internet en tant que réseau de réseaux, Internet est donc notamment l'union des systèmes d'information de tous les acteurs. Séance après séance, vous allez créer un SI minimal (DNS, HTTP, Mail, etc.) interconnectable avec le reste du monde et ainsi constater qu'Internet est le résultat de chaque acteur/AS qui offre et consomme des services.

Ce module se situe entre M2102 (Couches OSI, de la modulation à IP, avec un peu de routage/adressage IP) et M4101C. Il dure 6 semaines, avec chaque semaine 1 séance de cours (1h30) et 2 séances de TD (2*1h30) (attention, pour des raisons d'edt, la séance de cours a en fait lieu à la fin de la semaine précédent les TD). Le contenu sera détaillé au fur et à mesure de la période.

Une large part des séances pratiques sera réalisée sur la plateforme MI-LXC (https://github.com/flesueur/mi-lxc), pour laquelle il faudra télécharger une VM Virtualbox **avant** le TD1.1 "Découverte de MI-LXC" : [.ova à télécharger ici](https://filesender.renater.fr/?s=download&token=2f121a18-f94d-45d1-a079-f68229ebdfa9). Il faudra arriver en séance avec Virtualbox installé et le .ova de MI-LXC déjà téléchargé, l'installation et la découverte de la VM seront ensuite le programme de la séance TD1.1.


## Programme

* S1 :
  * [CM1](cm1.md) Introduction "C'est quoi internet ?" et panorama du cours
  * [TD1.1](td1.1-milxc.md) Découverte MI-LXC
<!--  * [TD1.2](td1.2-shell.md) Wargame shell -->
* S2 :
  * [CM2](cm2-crypto.md) Cryptographie et sécurité des communications (complément en ligne : [Section "Bases de la crypto"](https://github.com/flesueur/csc/blob/master/cours.md#bases-de-la-crypto))
  * [TD2.1](td2.1-crypto.md) Cryptographie JdR
  * [TD2.2](td2.2-apache.md) Apache/CMS
* S3 :
  * [CM3](cm3-dns.md) DNS (complément en ligne : [S. Bortzmeyer](https://www.iletaitunefoisinternet.fr/post/1-dns-bortzmeyer/))
  * [TD3.1](td3.1-dns.md) DNS
<!--  * TD3.2 CA ACME -->
* S4 :
  * CM4 Mail (complément en ligne : [B. Sonntag](https://www.iletaitunefoisinternet.fr/post/7-email-sonntag/))
  * TD4.1 SMTP, POP, IMAP
  * TD4.2 SPF, DKIM, Spam, webmail
* S5 :
  * CM5 Firewall (complément en ligne : [Filtrage et surveillance réseau](https://github.com/flesueur/srs/blob/master/cm3-filtrage.md))
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
