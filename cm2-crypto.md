CM2 Cryptographie et sécurité des communications - Notes de cours
=================================================================


À la rencontre d'Alice, Bob et Ernest
=====================================

Comment permettre à Alice et Bob de communiquer de manière *sûre* sur un canal *non sûr* ?

* Alice et Bob veulent communiquer, Ernest écoute (attaque passive) ou altère (attaque active) les échanges
* Le medium non sûr :
  * Un messager qui peut se faire capturer
  * Un réseau de câbles téléphoniques
  * Internet (routage multi-saut multi-acteurs, écoute possible sur la route + par des attaquants qui arriveraient à dévier)
* Exemples de positions *MitM - Man in the Middle* :
  * La DSI de l'UBS sur eduroam
  * Orange sur les livebox
  * Lumen sur du transatlantique
  * Autre transitaire fournissant le service au serveur visé
  * Attaquant sur le réseau local (attention aux LP Cyber qui jouent par là...)
  * ...
* L'objectif de la crypto : communiquer de manière *sûre* sur un medium *non sûr*
* Aujourd'hui, (très) peu de services réseau sans crypto


Communiquer de manière sûre ?
=============================

1. **Confidentialité** : chiffrement
2. **Intégrité** : signature/scellement
3. **Authentification** : preuve de possession de matériel crypto
4. **Non-répudiation** : signature


La cryptographie moderne
========================

La cryptographie asymétrique :

* Chaque acteur a une paire de clés publique/privée reliées mathématiquement
* Chiffrer = utiliser la clé publique de l'interlocuteur, déchiffrer = utiliser sa propre clé privée (tout le monde peut nous parler, nous seuls pouvons comprendre)
* Signer = utiliser sa propre clé privée, vérifier = utiliser la clé publique de l'interlocuteur (nous seuls pouvons signer, tout le monde peut vérifier)
* Exemples : RSA, ECDSA

La cryptographie symétrique :

* Chaque paire d'acteurs a une clé secrète
* Cette clé secrète est utiliser pour chiffrer/déchiffrer et sceller/vérifier
* Exemple : AES, HMAC

Le hachage :

* Empreinte de taille fixe à sens unique (non inversible)
* Hachage crypto doit être robuste aux attaques (collision, pré-images)
* Exemples SHA-256, SHA3-512

Quelle taille de clé ? [KeyLength](https://www.keylength.com/)


La mise en œuvre
================

L'obtention des clés : les PKI - Public Key Infrastructure
----------------------------------------------------------

* La difficulté majeure côté usage : obtenir la bonne clé. Comment l'obtenir de manière sûre quand le medium est non sûr ?
* Le risque : parler chiffré mais pas avec le bon interlocuteur (ie, avec Ernest)
* Les PKI sont des moyens d'associer des clés publiques (donc pour crypto asymétrique) à des identifiants (nom de personne, nom DNS, ...)
* Quelques modèles :
  * Centralisé CA (autorités de certification) / PKIX
  * Distribué PGP
  * DANE/TLSA dans DNSSEC
  * Diffus style keybase.io
  * L'échange direct / uniquement en out-of-band (donc pas vraiment de PKI)
  * La mise en contact


La négociation : les protocoles cryptographiques
------------------------------------------------

* Pour communiquer, il faut un standard : un protocole
* Quelques protocoles cryptographiques : TLS, SSH, S/MIME, PGP, ...
* Généralement pour la mise en place d'une communication en crypto hybride :
  * Échange de clés symétriques en crypto asymétrique
  * Puis communication *payload* en crypto symétrique
* Exemple des suites cryptographiques TLS ("ECDHE-RSA-AES128-GCM-SHA256")


Exemple : le modèle HTTPS
-------------------------

* Objectif : un tunnel chiffré entre le client (navigateur) et le serveur
* Pour cela il nous faut :
  * Un protocole : TLS
  * La (bonne) clé publique du serveur : Certificat
* Un certificat = (clé publique, nom) signé par un tiers. Ici :
  * clé publique = celle du serveur, il a la clé privée qui va avec
  * nom = nom d'hôte DNS (par exemple www.univ-ubs.fr)
  * tiers = une autorité de certification (par exemple Let's Encrypt)
  * => On a bien une association entre un nom et une clé, ici certifiée par un tiers
* Le client doit valider la signature de la CA :
  * Il lui faut donc vérifier avec la clé publique de la CA
  * Les clés publiques des CA reconnues par les clients sont pré-installées dans les navigateurs, pas de magie, il faut une *ancre de confiance* dans le logiciel


Ce qu'on va faire en TD
=======================

* TD2.1 : Crypto asymétrique
* TD3.2 : CA
