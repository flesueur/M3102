CM6 Wrap-up + Questions/Réponses- Notes de cours
================================================

Wrap-up
=======

Ce que nous avons vu :
* Internet est un réseau de réseaux autonomes : les AS
* BGP permet d'établir le routage logique entre ces AS :
  * Des liens locaux bas-niveau, connexions directes, permettent l'échange des paquets entre voisins. C'est entre ces voisins directs que l'on parle BGP, pour annoncer notre AS ainsi que les AS joignables via nous
  * Chaque AS découvre ainsi quels autres préfixes sont joignables en multi-saut
  * Le routage annoncé/paramétré via BGP permet d'établir le routage global, au niveau logique, permettant de communiquer avec l'ensemble du réseau IP
  * À ce niveau, nous obtenons donc une interconnexion IP globale
  * Résilience niveau interconnexion IP : BGP est auto-réparant, une route inaccessible sera immédiatement remplacée par une autre possible, si elle existe (et Internet est conçu de manière maillée)
* DNS permet de nommer les objets sur Internet, typiquement les sites web ou adresses mail, par le système des domaines
  * Un système arborescent, disjoint de la hiérarchie des adresses IP
  * Une partie "serveurs faisant autorité", qui hébergent chacun le contenu de la zone dont ils sont responsables et répondent aux requêtes sur cette zone
  * Une partie "serveurs de résolution/résolveurs", sollicités par les clients, qui vont parcourir les serveurs faisant autorité pour répondre au client
  * Résilience niveau service DNS : Les serveurs faisant autorité doivent avoir un miroir (au moins), situé dans un autre AS, si possible très disjoint
* SMTP permet d'échanger des mails entre domaines distincts
  * Pour émettre un mail, un client (Thunderbird, webmail, ...) transmet cette tâche à son serveur SMTP (chez son FAI, chez son opérateur de mail, ...)
  * Ce serveur SMTP est ensuite responsable de transmettre au serveur SMTP destination
  * Les serveurs de réception de `@domain.tld` sont enregistrés en champ MX dans la zone DNS de `domain.tld`
  * Le serveur côté réception stocke dans son spool
  * Résilience niveau SMTP : Les serveurs émetteurs réessaient pendant quelques jours + Il faut enregistrer plusieurs MX au niveau DNS
* IMAP(/POP) permettent de relever ses mails auprès du serveur
  * SMTP est en charge de déposer (enfin presque... mais on va simplifier) sur le serveur responsable de la boîte mail du destinataire
  * IMAP permet ensuite la relève par le destinataire
  * Résilience niveau IMAP : Moins critique, mais on fera du miroir / des sauvegardes
* Architecture globale et locale
  * Globalement, à l'échelle d'internet, on a une architecture physique maillée, distribuée, hétérogène, variée et une architecture logique à plat permettant la connectivité totale
  * Localement, à l'échelle d'un SI, on a une architecture physique arborescente, qui remonte souvent vers un (ou deux) points, moins hétérogène, plus structurée et une architecture logique segmentée pour limiter la connectivité interne
* Tous les échanges aujourd'hui sont chiffrés
  * Cryptographie hybride : crypto asymétrique + crypto symétrique
  * Besoin d'obtenir/valider les clés publiques des interlocuteurs : notion de PKI (autorité de certification, PGP, DANE/TLSA, Keybase, ...)
  * Il faut 1/s'assurer que l'on chiffre avec la bonne personne (PKI) et 2/chiffrer


Questions/Réponses
==================

(pour l'instant, je fais semblant de ne pas connaître les questions...)


Retours
=======

Questionnaire rapide et anonyme (3 questions : ce qui vous a plu, ce qui vous a déplu, suggestions d'améliorations) [ici](https://framaforms.org/retour-m3102-services-reseaux-1637759395). Clôture dimanche 12/12, merci !
