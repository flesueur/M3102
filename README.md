# M3102 - DUT2 Info - Services réseaux

_François Lesueur ([francois.lesueur@univ-ubs.fr](mailto:francois.lesueur@univ-ubs.fr))_

Cette page recense les séances du module M3102 "Services réseaux". L'objectif est d'expliquer le fonctionnement d'Internet en tant que réseau de réseaux, Internet est donc notamment l'union des systèmes d'information de tous les acteurs. Séance après séance, vous allez créer un SI minimal (DNS, HTTP, Mail, etc.) interconnectable avec le reste du monde et ainsi constater qu'Internet est le résultat de chaque acteur/AS qui offre et consomme des services.

Ce module se situe entre M2102 (Couches OSI, de la modulation à IP, avec un peu de routage/adressage IP) et M4101C. Il dure 7 semaines, avec chaque semaine 1 séance de cours (1h30) et 2 séances de TD (2*1h30) (attention, pour des raisons d'edt, la séance de cours a en fait lieu à la fin de la semaine précédent les TD).

Une large part des séances pratiques sera réalisée sur la plateforme MI-LXC (https://github.com/flesueur/mi-lxc), pour laquelle il faudra télécharger une VM Virtualbox **avant** le TD1 "Découverte de MI-LXC" : [.ova à télécharger ici](https://flesueur.irisa.fr/mi-lxc/images/milxc-debian-amd64-1.4.2pre3.ova), nous utiliserons la version 1.4.2pre3. Il faudra arriver en séance avec Virtualbox installé et le .ova de MI-LXC déjà téléchargé, l'installation et la découverte de la VM seront ensuite le programme de la séance TD1.


## Programme

* S19 :
  * [CM1](cm1.md) Introduction "C'est quoi internet ?" et panorama du cours
* S20 :
  * [TD1](td1-milxc.md) Découverte MI-LXC
  * [CM2](cm2-crypto.md) Cryptographie et sécurité des communications (complément en ligne : [Section "Bases de la crypto"](https://github.com/flesueur/csc/blob/master/cours.md#bases-de-la-crypto))
<!--  * [TD1.2](td1.2-shell.md) Wargame shell -->
* S21/S22 :
  * [TD2](td2-crypto.md) Cryptographie JdR
  * [TD3](td3-passwords.md) Mots de passe
* S22 :
  * [TD4](td4-apache.md) Apache/CMS/Tunnels
  * [CM3](cm3-dns.md) DNS (complément en ligne : [S. Bortzmeyer](https://www.iletaitunefoisinternet.fr/post/1-dns-bortzmeyer/))
* S23 :
  * [TD5](td5-dns.md) DNS
  * [CM4](cm4-mail.md) Mail (complément en ligne : [B. Sonntag](https://www.iletaitunefoisinternet.fr/post/7-email-sonntag/))
* S24 :
  * [TD6](td6-mail.md) SMTP, POP, IMAP
  * [CM5](cm5-archi.md) Architecture réseau et firewall (complément en ligne : [ANSSI](https://www.ssi.gouv.fr/administration/guide/definition-dune-architecture-de-passerelle-dinterconnexion-securisee/) (chapitre 2))
* S25 :
  * [TD7](td7-archi.md) Segmentation réseau et IPTables
  * [CM6](cm6-wrapup.md) Révisions, questions/réponses

<!--  * TD4.2 SPF, DKIM, Spam, webmail -->
<!-- * S5 :
  * [CM5](cm5-archi.md) Architecture réseau et firewall (complément en ligne : [ANSSI](https://www.ssi.gouv.fr/administration/guide/definition-dune-architecture-de-passerelle-dinterconnexion-securisee/) (chapitre 2))
  * [TD5.1](td5.1-archi.md) Segmentation réseau et IPTables
* S6 :
  * [TD6.1](td6.1-tunnels.md) Tunnels et bonus -->

## Pour les curieux

Quelques bonnes références :
* l'ensemble des présentations sur le site [http://iletaitunefoisinternet.fr/](http://iletaitunefoisinternet.fr/) sont de grande qualité
* Stéphane Bortzmeyer : [Blog](https://www.bortzmeyer.org/), [Twitter](https://twitter.com/bortzmeyer), [Mastodon](https://mastodon.gougere.fr/@bortzmeyer)
* Cécile Morange : [Blog](https://blog.ataxya.net/), [Twitter](https://twitter.com/AtaxyaNetwork/)

## Évaluation

La note de l'UE est composée de :
* CC : chaque TD donne lieu à un compte-rendu à déposer sur Moodle (page du cours, zone de dépôt prévue), 2 comptes-rendus seront évalués
* DS : en fin de période
