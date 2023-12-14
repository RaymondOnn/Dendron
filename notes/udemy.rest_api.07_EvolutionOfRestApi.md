---
id: 2a4ckpfs5dv9oaevpp0qhu3
title: 07_EvolutionOfRestApi
desc: ''
updated: 1699966247330
created: 1699964466782
---

7:15 / 7:20
Up next
8. Introduction to RESTful API
Transcript
L'évolution des extraterrestres apparaît à la fin de cette conférence.

Vous devriez être en mesure d'expliquer l'idée derrière les appels de procédure de redémarrage et vous devriez être en mesure de répondre

à la question, pourquoi l'arrestation et Jason est-il devenu si populaire pour la création d'applications?

Considérez ce morceau de code écrit en norges.

Lorsque ce code est lancé sur un ordinateur, il conduit à la création d'un processus qui a le code

de fonction et une fonction appelée une autre fonction.

Le code de la fonction carte est exécuté et les données de la fonction principale sont renvoyées à la fonction

appelante.

Tout ce traitement se produit dans le cadre du processus commun et ces appels ne conduisent à

aucun type d'activité réseau.

Ceci est un exemple d'appels de procédure locale.

Regardons maintenant les appels de procédure à distance ou RBC.

Il y a un client et un site serveur côté serveur.

Il existe une procédure ou une fonction que le client peut appeler à l'aide du mécanisme APC.

Le client peut appeler la fonction ou la procédure sur le serveur comme s'il s'agissait d'une fonction locale.

Ainsi, l'objectif du mécanisme RTC est de rendre les appels distants aussi simples que les appels locaux.

En d'autres termes, pour le client, la fonction distante semble être une fonction locale et

un code côté client ressemble beaucoup aux appels de procédure locale.

Revenons à la définition d'une Appia et l'API est un mécanisme RTC.

L'API d'arrestation est un mécanisme APC qui suit les principes de conception de l'arrestation.

Rest représente la représentation du transfert d'État.

Vous en apprendrez plus sur les API de risque dans la prochaine conférence, puis vous approfondirez les principes de conception

pour créer moins d'applications dans les sections suivantes.

Un petit peu sur l'histoire des mécanismes RTC en 1991 CORBA qui signifie architecture Broecker Common Object

Request a été introduit.

C'était le premier mécanisme RTC pratique.

La partie la plus intéressante était qu'elle était prise en charge par plusieurs langues.

En d'autres termes, vous pouvez écrire sur votre serveur et voir, puis avoir plusieurs clients appelant les procédures du

serveur en utilisant d'autres langages tels que Fortran ou COBOL en 1998.

Donc, qui signifie Simple Object Access Protocol, a été introduit.

Ce mécanisme RBOC utilisait exclusivement XML, mais ces deux mécanismes étaient complexes, difficiles à utiliser et fortement

dépendants des formats de message standard.

À l'origine, ces mécanismes RTC étaient conçus pour les applications de classe entreprise et non pour les services Web

Internet référencés au RTC ou à Internet.

En 2000 seulement, les développeurs d'applications Web ont créé des services Web à l'aide de XML ou de SCDP.

Cela s'appelait le plan ou XML ou boîte.

Ils ont également utilisé SoPE pour les services Web.

Le défi à résoudre était qu'il y avait deux bâtiments, des structures de messagerie standard côté

client et côté serveur, ce qui rendait le processus de développement d'application très lourd.

Puis est venu HTP Jason, qui était similaire à HTP XML, sauf que la demande et la réponse pour ces services Web

n'étaient pas au format JSON.

Et puis vint le S&P Restabilize.

Ce sont les services Web ou API construits en suivant les pratiques de conception reposantes.

À ce jour, le SCDP, les API RESTful ou les services Web RESTful sont devenus la norme de facto pour les

API.

Une question évidente que vous pouvez vous poser à ce stade est la suivante: pourquoi la solution est-elle devenue une norme de facto

alors qu'il y a tant de choix pour la création de services Web ou d'applications?

Il y a plusieurs raisons.

Tout d'abord, il existe un ensemble commun de principes de conception que toutes les autres API doivent suivre.

Par conséquent, le développeur d'une application n'a pas à repartir de zéro.

Ils adoptent simplement ces principes de conception qui facilitent la création d'applications.

La communauté des développeurs d'applications a rassemblé les meilleures pratiques éprouvées pour créer et gérer des applications de

repos.

L'API Rest n'est liée à aucun format de données spécifique, comme le savon est lié à l'eczéma.

Ce n'est pas le cas avec l'API rest.

Arrest API peut recevoir et répondre avec des données dans n'importe quel format.

Enfin, la simplicité de la création d'applications de repos le rend très attrayant et il n'y a pas de norme en tant

que telle.

Il existe ces directives que vous suivez pour créer des applications de repos.

En conséquence, les développeurs Rustaveli n'ont pas à se soucier des incompatibilités entre le client et le serveur et

comme il n'y a pas de norme.

Vous pouvez reposer les applications avec n'importe quel format de données, presque toutes les API modernes sont construites à l'aide de repos.

Et Jason, passons en revue les raisons pour lesquelles Reste est et est devenu si populaire.

XML est intrinsèquement complexe.

Il est destiné à la consommation de la machine et a ensuite vu l'utilisation record de normes complexes.

Les applications écrites avec SUB-STANDARD étaient très difficiles à gérer car les normes évoluaient continuellement

et exigeaient que les applications changent.

Alors que Gissen est très simple, il est destiné à la consommation humaine.

Vous pouvez facilement lire le document Jizan et comprendre le contenu de ce document.

L'autre raison était que Gissen est supporté nativement dans les plates-formes Web, par exemple, JavaScript et le

navigateur.

Cela signifiait donc que vous pouviez exécuter Jason Apitz directement depuis le navigateur.

L'avènement de la technologie mobile pour cela a agi comme un catalyseur dans l'adoption de repos et Jason pour les services

Web.

En 2007, Apple a lancé son iPhone.

Au départ, les applications de l'iPhone utilisaient les services Web, conçus pour les applications basées sur un

navigateur de bureau.

Et peu de temps après, les développeurs ont réalisé que la transmission de XML sur le téléphone utilise trop de CPU et de mémoire.

Ils ont également réalisé que les gros messages XML utilisaient trop de bande passante sur les appareils

mobiles.

Ces deux raisons ont conduit à de mauvaises performances des applications sur iPhone.

Une autre chose qui s'est produite est que l'utilisation intensive de l'axonal a également conduit à une vidange plus rapide de la batterie

sur l'appareil mobile.

Et la batterie est l'une des ressources les plus critiques sur les appareils mobiles.

Et pour ces raisons, les développeurs ont abandonné le XML au profit de Jason.

Il est temps de résumer.

Dans cette conférence, vous apprendrez pourquoi le repos et Jason est devenu si populaire pour la création de services Web ou d'applications.

La raison en est qu'il existe un ensemble commun de principes de conception qui peuvent être adoptés par le développeur

d'API pour lancer rapidement ses applications.

Deuxièmement, il existe une grande communauté de développeurs qui ont rassemblé un ensemble de bonnes pratiques prêtes à être

utilisées pour créer et gérer des applications de repos.

C'est la simplicité et la flexibilité qu'offrent les applications de repos.