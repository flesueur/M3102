CM1 Introduction - Notes de cours
=================================

C'est quoi internet ?
=====================

* Des protocoles pour l'interopérabilité : BGP, DNS, SMTP, HTTP, ...
* Des organisations qui orchestrent : IETF, ICANN (dont IANA), RIR RIPE/NANOG/etc.
* Des acteurs : des GAFA/BATX aux utilisateurs, en passant par les milliers de petits hébergeurs
* Des lieux : points de peering, datacenters
* Du matériel : des ordinateurs, des routeurs, des câbles/fibres [J. Mau](https://www.iletaitunefoisinternet.fr/post/3-infra-maud/)
* Des logiciels : du firmware de routeur, des services de cœur (BGP, DNS), des serveurs et clients à chaque bout de la chaîne
* Quelques *killer app* : email, web
* Des usages : messagerie, réseau social, encyclopédie
* D'un réseau militaire (ARPANET) à un outil de partage de connaissances sans frontières (ou de ciblage publicitaire et de commande de sushis, selon les opinions...)


Comment est structuré internet ?
================================

Un réseau de réseaux :

* Acentré (pas de chef, pas de décision unique sans consensus des acteurs indépendants, pas de point de défaillance unique (SPOF)). *Est-ce toujours aussi vrai ? De la panne Facebook aux clouds*
* Structuré autour de la notion d'AS (systèmes autonomes, environ 100 000) qui forment le découpage de premier niveau
* Chaque AS (exemple : RENATER, Orange) est ensuite sous-divisé en interne
* Les AS s'interconnectent entre eux et le protocole BGP assurent la glu entre ces AS
* Un bon thread qui explique cela [ici](https://twitter.com/AtaxyaNetwork/status/1445096685286350849) : chaque AS est une île, BGP sert à construire les ponts, l'objectif étant de permettre de transmettre des messages d'une île à une autre île éloignée, par le passage donc de plusieurs îles et ponts intermédiaires.


Internet, le routage, les services, les couches OSI
===================================================

De manière *très* synthétique :

* Chaque lien physique entre 2 machines/routeurs (fibre, câble, sans-fil) : un protocole couche 2
* Internet c'est la connectivité IP (Internet Protocol) globale couche 3 de bout en bout
* Au dessus d'IP, on retrouve classiquement TCP et UDP :
  * Au-dessus de TCP : HTTP, SMTP, ...
  * Au-dessus d'UDP : DNS, WebRTC et autres protocoles audio/vidéo, ...
* (ICMP, qui véhicule notamment les paquets ping, est de même niveau que IP)


Et pourquoi "Internet" pour un cours "Services réseaux" ?
=========================================================

Parce que tout simplement, comme on va le voir pendant 6 semaines, les services réseaux que l'on déploie dans un SI ont, pour beaucoup (HTTP pour le web, SMTP pour le mail, DNS pour le nommage par exemple), vocation à s'interfacer avec le reste d'internet. Internet est un réseau de réseaux, il n'héberge pas de services en tant que tel, il n'y a pas de "zone de serveurs" : on ne fait qu'aller interagir avec les services hébergés dans un des autres réseaux d'internet. Et donc mettre en place des services réseaux, c'est souvent offrir des services sur internet.


Panorama du cours
=================

* Introduction "C'est quoi internet ?" (aujourd'hui)
* Un peu de sécurité des communications (crypto)
* Du web (HTTP)
* Du DNS
* De l'autorité de certification
* Du mail
* Du firewall et de l'archi réseau
* De la gestion centralisée ou à distance : DHCP, SSH, Ansible


Les TODO
========

* Venir en TD avec son laptop. Combien faut-il en prévoir en complément ?
* Télécharger l'[ova de MI-LXC](https://filesender.renater.fr/?s=download&token=adb51140-dae2-4cc6-ba1c-15cd1f91c913) et installer VirtualBox ou VMWare **avant le premier TD** !
