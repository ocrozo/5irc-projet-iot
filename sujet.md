# Projet IoT – CPE Lyon – 2018-2019

*Auteurs: N. Lebedev, A. Massouri, O. Carrillo*

## 1. Introduction

Dans ce projet nous nous proposons de créer un prototype d'un système IoT pour un domaine d'application* (cf Annexe) choisi en s'appuyant sur les plateformes matérielles et logicielles existantes. Nous suivrons la démarche d'intégration pour mettre l'accent sur les thèmes propres à la spécialité R&T : techniques de transmission et de traitement des données, radiocommunications, protocoles MAC, ordonnancement, partage des ressources, QoS, routage.

Les trois principales composantes d'un système IoT sont :
  + Capteurs – émetteurs radio
  + Passerelles – récepteurs radio
  + Cloud

Pour remplir chacune de ces fonctionnalités, on utilisera les plateformes de base couplées à des cartes mezzanine et des “clicks”:
  + Les plateformes de base sont les systèmes embarqués de type micro-ordinateurs disponibles dans le commerce : Arduino, Genuino, Beaglebone.
  + Les cartes mezzanine que l'on peut connecter aux passerelles via les socles intégrés, les “shield” de différents format et standard
  + Les “click” sont les capteurs et les cartes de communication radio, - transmetteurs.

Le choix du matériel mis à disposition est dicté par la relative facilité de son intégration. L'accès aux fonctionnalités de ces plateformes se fait via les APIs qui ne nécessitent pas de programmation embarquée bas niveau en C ou microC ou encore Assembleur, et les bibliothèques haut niveau en Python, C, C++ avec les exemples d'utilisation suffisent pour prototyper rapidement.

## 2. Objectifs

Les objectifs du projet s'articulent autour des points suivants :

  1. Comprendre les enjeux, les perspectives et les contraintes de l'IoT.
  2. S'initier aux problématiques IoT et s'approprier d'une solution clé en main du commerce.
  3. Utiliser le matériel mis à disposition (les kits de développement, cartes et modules radio,...),  les logiciels et les outils afin de créer un prototype d'un système IoT pour répondre à un besoin d'un domaine d'application... qui reste à déterminer ou à imaginer... par vous!

**But final**: l'acquisition des compétences par chaque groupe sur une chaîne complète des blocs capteur/sans-fils/plateforme et passerelle/cloud, et la réalisation d'une maquette/prototype.

## 3. Planning

Chaque groupe doit réaliser cette châine complète : capteur – passerelle – cloud.
Chaque équipe disposera du matériel suivant :
  + Capteur :
    - 1  Arduino Genuino Mega
    - N clicks capteurs
    - 1 click module sans fil
  + Passerelle BBB – BeagleBone Black, Source: livre “Exploring Beaglebone”.
  http://exploringbeaglebone.com/

## 4. Evaluation

Chaque équipe restituera son travail sous forme de partage de compétences à l'aide de sa maquette via un tutoriel, une démo, voire un mini-TP pour l'ensemble de la promo et le jury.

Les supports pour la restitution choisie devront être mis en ligne sur le git du projet créé pour votre équipe.
Pour créer le dépôt git pour votre équipe veillez suivre le lien suivant:
https://classroom.github.com/g/T-2u-FDX

## Annexe. Domaines d'application :

* **Ville connectée** : transport et gestion des flux (feux routiers, péages, ...), infrastructure des smart grids (électricité, gaz, eau, …), compteurs connectés, relais connectés,  environnement, avertissements de la population via les appareils connectés, agrégation des données, distribution et restitution des données sous forme visuelle, sonore, sensorielle.
+ **Environnement** : contrôle et transmission des paramètres météo (température, humidité, pression atmosphérique), de la pollution au CO2, aux gases toxiques.
+ **Maison connectée** : alerte incendie avec la détection de fumée ou de feu.
+ **e-Santé et e-Sport** : suivi des patients à domicile, surveillance des personnes agées, suivi non-intrusif du mode de vie, des activités vitales, de la prise des médicaments.
+ **Détection de mouvements**, de chutes, assistance aux personnes, suivi et rappels.
+ **Industrie connectée** : gestion des chaines de production et des automates.
+ **Sécurité et surveillance** : contrôle d'accès par badge, franchissement du périmètre et calcul du nombre de passages, retro-action et demande d'authentification via la saisie d'un code sur une appli smartphone ou web,...
+ ...votre idée ?!

