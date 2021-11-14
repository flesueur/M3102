TD1.1 Découverte MI-LXC
=======================

Ce TP sera réalisé dans la VM MI-LXC disponible [ici](https://filesender.renater.fr/?s=download&token=2f121a18-f94d-45d1-a079-f68229ebdfa9). Avant de lancer la VM, il peut être nécessaire de diminuer la RAM allouée. Par défaut, la VM a 3GO : si vous avez 4GO sur votre machine physique, il vaut mieux diminuer à 2GO, voire 1.5GO pour la VM (la VM devrait fonctionner de manière correcte toujours).

MI-LXC simule un internet minimaliste que nous utiliserons tout au long de la matière. Ce TD couvre la découverte de l'environnement existant et les premiers éléments qui seront nécessaires pour l'étendre par la suite. Dans la suite de la matière, vous installerez et configurerez un réseau et des services qui s'interconnecteront avec cet existant.

> Pour les curieux, le code de MI-LXC, qui sert à générer cette VM automatiquement, est disponible avec une procédure d'installation documentée [ici](https://github.com/flesueur/mi-lxc)

> La VM peut être affichée en plein écran. Si cela ne fonctionne pas, il faut parfois changer la taille de fenêtre manuellement, en tirant dans l'angle inférieur droit, pour que VirtualBox détecte que le redimensionnement automatique est disponible. Il y a une case adéquate (taille d'écran automatique) dans le menu écran qui doit être cochée. Si rien ne marche, c'est parfois en redémarrant la VM que cela peut se déclencher. Mais il *faut* la VM en grand écran.

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



Manipulation et navigation dans l'infrastructure pré-existante
==============================================================

Découverte du réseau
--------------------

Vous devez vous connecter à la VM en root/root. MI-LXC est déjà installé et l'infrastructure déployée, il faut avec un terminal aller dans le dossier `/root/mi-lxc`. Commencez par afficher le plan du réseau avec `./mi-lxc.py print`. Les rectangles sont les machines et les ovales les switchs réseau. L'infrastructure déployée simule un internet minimaliste :

* un réseau de réseaux indépendants
* chaque réseau propose des services internes et expose des services externes utilisés par d'autres réseaux
* des services globaux nécessaires à l'interconnexion de l'ensemble sont déjà en place (cœur de réseau, racine DNS, ...)

Au milieu du plan, le cœur de réseau est constitué de 2 opérateurs nommés transit-a et transit-b (munis chacun d'une machine et d'un switch). D'autres organisations sont ensuite connectées à ces opérateurs afin de former globalement cet internet. Ces organisations sont classiquement construites avec une machine <organisation>-router en entrée de réseau, un switch interne et d'autres machines branchées sur ce switch interne.

> Question 1 : À partir de l'image de la topologie accessible [ici](https://github.com/flesueur/mi-lxc/blob/master/doc/topologie.png), délimitez les réseaux (les AS) et essayez de préciser leur rôle dans le système global. Vous pouvez vous aider du listing présent dans le fichier `doc/MI-IANA.fr.txt`.


Utilisation
-----------

Pour démarrer l'infrastructure, tapez `./mi-lxc.py start`. Ensuite pour vous connecter à une machine :

* `./mi-lxc.py display isp-a-home` : pour afficher le bureau de la machine isp-a-home qui peut vous servir de navigateur web à l'intérieur du réseau de MI-LXC (en tant qu'utilisateur debian)
* `./mi-lxc.py attach target-dmz` : pour obtenir un shell sur la machine target-dmz (en tant qu'utilisateur root)

Toutes les machines ont les deux comptes suivants : debian/debian et root/root (login/mot de passe).

> Question 2 : Depuis la machine isp-a-home, ouvrez un navigateur pour vous connecter à `http://www.target.milxc`. Vous accédez à une page Dokuwiki. Savez-vous sur quel serveur cette page est hébergée ?

> Question 3 : Depuis la machine isp-a-home, ouvrez un navigateur pour vous connecter au site web de l'UBS. Cela fonctionne-t-il ? Cette page est-elle hébergée dans l'infrastructure MI-LXC ?

> Question 4 : Ouvrez un shell sur la machine target-dmz (commande attach, donc). Installez le package nano grâce à l'outil `apt` et vérfifiez que vous pouvez maintenant éditer des fichiers avec nano.

> Dans la VM et sur les machines MI-LXC, vous pouvez donc installer des logiciels supplémentaires. Par défaut, vous avez mousepad pour éditer des fichiers de manière graphique.

> Si la souris reste bloquée dans une fenêtre de display, appuyez sur CTRL+SHIFT pour la libérer. Un tuto vidéo de démarrage est proposé [ici](https://mi-lxc.citi-lab.fr/data/media/tuto.mp4).



Extension de l'infrastructure
=============================

MI-LXC permet le prototypage rapide d'une infrastructure. Nous allons maintenant ajouter un AS à l'infrastructure existante.

Le déroulement va être le suivant :

* Déclarer un numéro d'AS, une plage d'adresses IP et un nom de domaine pour cette nouvelle organisation
* Créer cet AS minimaliste dans MI-LXC
* Ajouter un autre hôte à cet AS
* Modifier ce nouvel hôte

Déclaration d'un nouvel AS
-------------------------------

Le fichier `doc/MI-IANA.fr.txt` représente l'annuaire de l'IANA. Vous pouvez y trouver un numéro d'AS libre ainsi qu'une plage d'IP libre. Les IPv4 routables sont attribuées dans l'espace 100.64.0.0/10 (réservé au CG-NAT et donc normalement sans risque de conflit local).

Vous pouvez aussi en profiter pour prévoir un nom de domaine en .milxc, qui sera utilisé lors d'une prochaine séance.

**Faîtes valider ce plan par l'enseignant**

> Si vous n'êtes pas très à l'aise avec le choix de numéros d'AS et de plages d'IP, vous pouvez utiliser les paramètres de l'AS 9 "Evil" (cet AS n'est pas présent dans l'infrastructure fournie, ses paramètres sont donc libres).

Création de cet AS dans MI-LXC
-----------------------------------

Un AS est représenté par un groupe d'hôtes. La première étape est ainsi de déclarer ce nouveau groupe dans le fichier de topologie globale `global.json`. Ajoutez-y un groupe simple en partant d'un modèle existant. Par exemple, le groupe existant "milxc", proche de vos besoins, est défini de la manière suivante :

```
"milxc": {
  "templates":[{"template":"as-bgp", "asn":"8", "asdev":"eth1", "neighbors4":"100.64.0.1 as 30","neighbors6":"2001:db8:b000::1 as 30",
  "interfaces":[
    {"bridge":"transit-a", "ipv4":"100.64.0.40/24", "ipv6":"2001:db8:b000::40/48"},
    {"bridge":"milxc-lan", "ipv4":"100.100.20.1/24", "ipv6":"2001:db8:a020::1/48"}
  ]
}]}
```

Le champ _template_ décrit le template du groupe, ici ce sera également un as-bgp. Les champs _asn, asdev, neighbors4, neighbors6_ et _interfaces_ doivent être ajustés :

* _asn_ est le numéro d'AS, tel que déclaré dans `MI-IANA.fr.txt`
* _asdev_ est l'interface réseau qui sera relié au réseau _interne_ de l'organisation (celle qui a les IP liées à l'AS, ce sera eth1 pour vous comme dans l'exemple)
* _neighbors4_ sont les pairs BGP4 pour le routage IPv4 (au format _IP\_du\_pair as ASN\_du\_pair_)
* _neighbors6_ sont les pairs BGP6 pour le routage IPv6 (optionnel, au format _IP\_du\_pair as ASN\_du\_pair_)
* _interfaces_ décrit les interfaces réseau du routeur de cet AS (malgré l'indentation trompeuse, c'est bien un paramètre du template as-bgp). Pour chaque interface, il faut spécifier son bridge, son ipv4 et son ipv6 (optionnelle) de manière statique ici. Dans cet exemple :
  * _transit-a_ est le bridge opéré par l'opérateur Transit-A, s'y connecter permet d'aller vers les autres AS, il faut utiliser une IP libre dans son réseau 100.64.0.40/24 et cette interface sera l'interface externe _eth0_
  * _milxc-lan_ est le bridge interne de votre nouvelle organisation, on y associe une IP de son AS. Son nom doit **impérativement** commencer par le nom du groupe + "-", ici "milxc-", et ne pas être trop long (max 15 caractères, contrainte de nommage des interfaces réseau niveau noyau)

Pour intégrer votre nouvel AS, il faudra donc choisir à quel point de transit le connecter et avec quelle IP. Un `./mi-lxc.py print` vous donne une vue générale des connexions et IP utilisées (tant que le JSON est bien formé...). Il faut également déclarer ce nouveau pair de l'autre côté du tunnel BGP (ici, ce routeur du groupe "milxc" est par exemple listé dans les pairs BGP du groupe "transit-a", défini plus haut dans le fichier : il *faut* mettre à jour la liste des voisins de transit-a !).

Une fois ceci défini, un `./mi-lxc.py print` pour vérifier la topologie, puis `./mi-lxc.py create` permet de créer la machine routeur associée à cet AS (ce sera un Alpine Linux). L'opération create est paresseuse, elle ne crée que les conteneurs non existants et sera donc rapide.

> **DANGER ZONE** On va détruire un conteneur et uniquement un. Si vous faîtes une fausse manipulation, vous risquez de détruire l'infra complète et de mettre ensuite 15 minutes à tout reconstruire, ce n'est pas le but. Donc spécifiez bien le nom du conteneur à détruire et, si vous êtes dans une VM, ça peut être le moment de faire un snapshot...

À ce moment, par contre, le pair BGP (l'autre bout du tunnel BGP mis à jour dans le JSON, par exemple le conteneur transit-a-router) ne connaît pas encore ce nouveau routeur (rappel, vous avez dû modifier sa configuration dans `global.json`). Il faut le détruire et le re-générer : `./mi-lxc.py destroy transit-a-router` (détruit _uniquement_ le conteneur transit-a-router) puis `./mi-lxc.py create` pour le re-générer.

On peut enfin faire un `./mi-lxc.py start` et vérifier le bon démarrage.

> Attention, pour des raisons de gestion des IP et des routes, étonnamment, il n'y a pas de façon simple pour que le routeur puisse lui-même initier des communications. C'est-à-dire que si tout fonctionne bien il sera démarré, aura de bonnes tables de routage, mais pour autant ne pourra pas ping en dehors du subnet du transitaire. C'est le comportement attendu et donc vérifier la connectivité du routeur ne peut pas se faire comme ça. On verra ensuite comment vérifier cela depuis un poste interne et nous utiliserons, sur le routeur ou ses voisins BGP, les commandes `birdc show route all` et `birdc show protocols` pour inspecter les tables de routage et vérifier l'établissement des sessions BGP.

> Question 5 : Quels ajouts avez-vous fait dans `global.json` ?

> Question 6 : Vérifiez les sorties de `birdc show route all` et `birdc show protocols` de chaque côté de la connexion que vous avez rajoutée. Dans protocols, tout doit être "Established". Mettez des screenshots des sorties "protocols" dans votre compte-rendu.


Ajout d'un hôte dans le nouvel AS
--------------------------------------

Nous allons maintenant ajouter un nouvel hôte dans cet AS. Si le groupe a été nommé "acme" dans global.json, il faut créer le dossier `groups/acme` pour l'accueillir. Dans ce dossier nous allons avoir un fichier `local.json` qui décrit la topologie interne du groupe.

> Si vous explorez les autres groupes, vous verrez qu'en plus du `local.json`, il y a un sous-dossier par machine pour le provisionning de chacun de ces hôtes. Ce sont les "recettes" de création de chaque hôte pré-existant. Vous n'aurez pas à procéder comme cela de votre côté mais les recettes des autres hôtes pourront parfois vous aider durant de prochaines séances.

Un exemple de `local.json` minimal (groups/gozilla/local.json) :
```
{
  "comment":"Gozilla AS",
  "containers": {
    "infra":
        {"container":"infra",
          "interfaces":[
            {"bridge":"lan", "ipv4":"100.83.0.2/16", "ipv6":"2001:db8:83::2/48"}
          ],
          "gatewayv4":"100.83.0.1",
          "gatewayv6":"2001:db8:83::1",
          "templates":[{"template":"nodhcp", "domain":"gozilla.milxc", "ns":"100.100.100.100"}]}
  }
}
```

Ce JSON définit :

* qu'il y a un conteneur qui s'appelle infra
* qu'il a une interface réseau branchée sur le bridge _gozilla-lan_ avec les IP spécifiées (le préfixe _groupname-_ est automatiquement ajouté au nom de bridge écrit dans ce JSON)
* que sa passerelle IPv4 est 100.83.0.1
* qu'il utilise un template (nous ne détaillerons pas, il vous faudra utiliser le même) qui désactive le DHCP et fixe le domaine et le serveur DNS

Une fois tout ceci fait, on peut faire `./mi-lxc.py print` pour vérifier que le JSON est bien formé et que la topologie est conforme. Un `./mi-lxc.py create` créera ce conteneur, puis `./mi-lxc.py start` le lancera (inutile d'avoir stoppé les autres avant).

> Question 7 : Quel contenu dans `local.json` ?


Modification de cet hôte
-----------------------------

Maintenant que cet hôte est créé, nous allons le modifier. Il s'agit d'une Debian basique sur laquelle il est donc possible d'ajouter des paquets, les configurer, etc. Installez le paquet nano et vérifiez son fonctionnement. Ouvrez enfin un display sur cette nouvelle machine et vérifiez que vous pouvez naviguer avec Firefox vers `http://www.target.milxc` et vers un site internet externe à MI-LXC.

Enfin, vous pouvez éteindre proprement MI-LXC en tapant `./mi-lxc.py stop` et en arrêtant la VM (proprement également). Vos changements sont persistants : rallumez la VM, redémarrez MI-LXC et vérifiez que nano est toujours disponible sur votre nouvelle machine.

> Question 8 : Montrez avec une capture d'écran que l'installation a bien fonctionné.


Organisation pour la suite de la matière
----------------------------------------

L'infrastructure pré-existante suit le paradigme de l'_infrastructure-as-code_, c'est à dire que la topologie, les installations et les configurations sont _programmées_ (les json que vous avez manipulé ainsi que les `provision.sh`, par exemple dans `groups/target/dmz/provision.sh`). Cela permet de sauvegarder/versionner les recettes et de facilement regénérer des hôtes en cas de mauvaise manipulation. La contrepartie est de programmer les configurations plutôt que de juste les faire.

A priori, vous n'utiliserez pas ces fonctionnalités (ce n'est en tous cas pas exigé). Vos configurations seront persistantes mais vous ne pourrez donc pas facilement revenir sur des erreurs de manipulation sur les hôtes rajoutés. Au-delà des comptes-rendus, vous avez donc tout intérêt à documenter vos actions car vous allez progressivement développer votre AS durant les 6 semaines de la matière, en nécessitant parfois des fonctionnalités mises en place dans un TD précédent.

**Votre compte-rendu doit être déposé sur Moodle en fin de séance au format PDF uniquement**
