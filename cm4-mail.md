CM4 Mail - Notes de cours
=========================

Programme
=========

* Les protocoles du mail : SMTP, IMAP, POP
* La lutte contre le spam : Antispam, SPF, DKIM, DMARC
* Les acteurs et l'évolution des forces

Les principes généraux
======================

* Un domaine = une zone de mail
* Une adresse mail : idenfiant@domaine
* Des boîtes à lettres / mailbox : espace disque au nom de 'identifiant' pour chaque compte mail sur le serveur appartenant à 'domaine'
* Le chemin d'un mail :
  * MUA - Mail User Agent : client mail (ex Thunderbird, Webmail PHP)
  * MSA - Mail Submission Agent : serveur SMTP d'envoi (ex Postfix)
  * MTA* - Mail Transfert Agent : succession de serveurs SMTP (ex Postfix)
  * MDA - Mail Delivery Agent : Dépôt par un MTA dans une boîte à lettres (ex Dovecot, fdm, sieve)
  * MUA : client mail en réception IMAP/POP (ex Thunderbird, Webmail PHP)


Quelques packages
=================

* Zimbra : une suite complète SMTP/IMAP/POP/Webmail, avec par exemple Postfix côté SMTP
* MS Exchange Server _on-premises_ : une suite complète également, basée sur une pile logicielle Microsoft
* MS Exchange Server _Cloud_, Google suite : hébergement distant d'une suite complète


Éléments techniques
===================

Un ensemble de protocoles en mode texte, le plus souvent utilisés en version TLS. Debug avec `netcat` ou `openssl s_client` (éventuellement avec -starttls).

Envoi
----

SMTP : deux rôles, un (presque) même protocole :

* Submission agent
  * TCP, écoute sur 587 (clair), 465 (TLS)
  * Reçoit les mails depuis un client MUA
  * Les transfère au MTA
* Transfer agent
  * TCP, écoute sur le port 25
  * Reçoit des mails d'un MSA ou d'un autre MTA
  * Les transfère à un MTA ou un MDA

Consultation
------------

* IMAP : Protocole de consultation avec stockage et organisation côté serveur
* POP : Protocole de consultation avec stockage et organisation côté client (et suppression côté serveur)


Architecture
============

* Sur les postes de travail : les MUA, clients SMTP et IMAP
* Sur un serveur opéré par/pour l'organisation locale :
  * Un MSA pour les envois *depuis* les utilisateurs locaux (au sens réseau ou authentification, à contrôler !), qui ensuite relaie au MTA
  * Un MTA pour les envois *vers* les utilisateurs locaux, qui écoute donc sur l'extérieur, et dépose les mails
* Comment un MSA/MTA trouve-t-il le MTA du domaine cible :
  * Champ MX dans la zone DNS
  * Un mail envoyé à `toto@acme.org` signifie :
    * le MTA côté émetteur doit trouver le serveur MX de `acme.org` -> requête DNS
    * lui envoyer le mail
    * le serveur cible le dépose dans la boîte de son utilisateur local `toto`
* On a ainsi un système fédéré :
  * chaque organisation/domaine s'interconnecte avec les autres, tout le monde peut (en théorie, cf section spam) s'intégrer dans le système et opérer un domaine et ses serveurs SMTP
  * mais chaque organisation/domaine est responsable de sa gestion interne


Quelques aspects de sécurité
============================

**Le mail est un système peu sécurisé**

Ce qui est sécurisé
-------------------

* On soumet ses mails en SMTP TLS : chiffré du MUA au MSA
* Le MSA est généralement local au MTA : pas de communication réseau
* Les serveurs SMTP discutent de plus en plus en TLS : chiffré entre MTA, mais pas toujours
* On relève ses mails en IMAP TLS : chiffré du MUA au serveur IMAP

Ce qui n'est pas sécurisé
-------------------------

* Pas chiffré de bout en bout mais par parties : chaque serveur intermédiaire accède au clair (Allo Google ?)
* Le chiffrement entre serveurs SMTP n'est qu'opportuniste :
  * Cela nécessite que le SMTP client demande du TLS
  * Si un certificat valide est présenté, très bien
  * Si un certificat non valide est présenté (auto-signé), la plupart l'acceptent encore (il n'y a pas d'interaction utilisateur possible pour accepter/refuser une exception de sécurité)
  * Si le serveur dit "je ne fais de TLS !", le client reste en clair
  * Cela laisse toute la marge de manœuvre nécessaire à un attaquant
  * Conclusion : soit il n'y a pas d'attaquant et la communication sera probablement bien chiffrée (mais son chiffrement était quasi-inutile), soit il y a un attaquant et la communication sera probablement pas/mal chiffrée
* L'authenticité des messages ! Pas de preuve de l'adresse de l'émetteur, les identités sont usurpables par SMTP spoofing


Un peu de lutte contre le SPAM
------------------------------

Le spam est un fléau qui menace l'utilisabilité du mail et, indirectement par les contre-mesures mises en place, le caractère ouvert et fédéré du système mail.

Les filtres :

* Filtrage côté SMTP destination ou côté client MUA
* Analyse du texte, de PJ, d'IP source (bases de réputation)
* À la fois sur le contenu et sur l'éventuelle répétition sur plusieurs connexions (campagne)
* Parfois avec de l'apprentissage
* Risque de faux positifs
* Aboutissent à un système qui responsabilise les SMTP émetteurs : si vous spammez, vos utilisateurs légitimes seront filtrés
* Mais si votre SMTP est classé spam par Google ou MS, vous avez perdu
* Ex SpamAssassin


Le triptyque SPF/DKIM/DMARC
===========================

L'usurpation facile d'identité et le besoin de ne pas trop participer aux vagues de spams a amené SPF, DKIM et DMARC comme bonnes pratiques aujourd'hui.

SPF
---

* Un domaine possède son infrastructure, ses serveurs SMTP
* Les mails se prétendant d'adresses "@CeDomaine.org" devraient logiquement venir de ces SMTP
* => Annoncer dans la zone DNS "CeDomaine.org" un enregistrement SPF déclarant les serveurs autorisés à envoyer en son nom
* Le MSA de "CeDomaine.org" doit authentifier ses utilisateurs, s'assurer qu'il n'autorise à envoyer pour ce domaine que les utilisateurs légitimes. On limite ainsi les usurpations et l'usage de notre nom pour des spams
* Quand un MTA tiers reçoit un mail "@CeDomaine.org", il fait une requête DNS :
  - soit il provient du MTA autorisé et il peut l'accepter
  - soit il provient d'une autre IP et il devrait le refuser
* Sauf que...
  - SPF pas toujours bien configuré
  - Gestion pas toujours simple des tiers autorisés (prestataires de mailings commerciaux, mails automatiques, ...)
  - Certains réseaux forcent à utiliser l'envoi par leur SMTP local (ex UBS), y compris pour des mails envoyés avec une adresse d'expéditeur externe
  - Et des problèmes avec les mailing lists
* Côté réception, on a jamais réussi à filtrer systématiquement sur le SPF, c'est resté une entrée non éliminatoire du filtre anti-spam
* Il faut l'implémenter en envoi, mais ne pas être contraignant en réception...

DKIM
----

* Un domaine possède son infrastructure, ses serveurs SMTP
* Les mails se prétendant d'adresses "@CeDomaine.org" devraient logiquement venir de ces SMTP
* => Générer une paire de clés cryptographiques, signer avec la clé privée sur ces serveurs, annoncer dans la zone DNS "CeDomaine.org" un enregistrement DKIM déclarant la clé publique autorisée à signer
* Le MSA de "CeDomaine.org" doit authentifier ses utilisateurs, s'assurer qu'il n'autorise à envoyer pour ce domaine que les utilisateurs légitimes. On limite ainsi les usurpations et l'usage de notre nom pour des spams
* Quand un MTA tiers reçoit un mail "@CeDomaine.org", il fait une requête DNS :
  - il obtient la clé publique DKIM pour "@CeDomaine.org"
  - soit la signature est valide et il peut l'accepter
  - soit la signature n'est pas valide et il devrait le refuser
* Sauf que...
  - DKIM pas toujours bien configuré
  - Gestion pas toujours simple des tiers autorisés (prestataires de mailings commerciaux, mails automatiques, ...)
  - Certains réseaux forcent à utiliser l'envoi par leur SMTP local (ex UBS), y compris pour des mails envoyés avec une adresse d'expéditeur externe
  - Et des problèmes avec les mailing lists
* Côté réception, on a jamais réussi à filtrer systématiquement sur le DKIM, c'est resté une entrée non éliminatoire du filtre anti-spam
* Il faut l'implémenter en envoi, mais ne pas être contraignant en réception...

DMARC
-----

* Constat que SPF et DKIM sont durs à contraindre
* Le destinataire est en fait mal placé pour refuser systématiquement les échecs SPF/DKIM
* Permettre à l'émetteur de déclarer sa politique, dire à quel point son usage de SPF/DKIM est complet et ce que le destinataire devrait faire en cas d'échec
* Ajouter, en plus, une boucle de rétro-action en cas d'échec SPF/DKIM (adresse mail pour les rapports d'échecs, charge à l'émetteur de distinguer ce qui est un échec normal (une usurpation, donc) et ce qui est une erreur de configuration)
* Un nouvel enregistrement DNS qui dit :
  - S'il y a du SPF/DKIM
  - Si un échec doit être ignoré (en cours de déploiement), suspect (donc entrée pour antispam) ou rejet du mail
* Sauf que...
  - Peu de domaines à ma connaissance osent aller jusqu'à demander le rejet, mais au moins c'est possible
  - Toujours les difficultés avec les mailing-lists, les redirections, ... bref, l'historique de l'usage du mail
* Il faut l'implémenter également

_De la difficulté à faire évoluer une infrastructure fédérée_


Les acteurs
===========

* Plein de petits (chaque domaine peut être autonome, puisque fédéré)
* Quelques très gros (Google, Microsoft)
* Les très gros ont un avantage sur le corpus pour les antispam
* Plus le temps passe, plus il devient difficile d'être un petit :
  - De nombreuses règles pour limiter le spam complexifient l'administration, ce qui est bien mais augmente le coût
  - Les gros acteurs poussent vers l'usage de meilleures pratiques, ce qui est bien mais augmente le coût
  - Les choix des gros acteurs impactent tous les acteurs. Aujourd'hui, le seuil pour maintenir son autonomie mail est de ne *jamais* être classé spam par Google/Microsoft, sinon on a perdu (les clients partent)
  - La lutte contre le spam, légitime, renforce ainsi les plus gros acteurs, qui ont donc un double intérêt dans cette lutte contre le spam...


Ce qu'on va faire en TD
=======================

* TD4.1 Mail
