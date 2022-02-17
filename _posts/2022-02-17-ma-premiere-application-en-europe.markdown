---
layout: post
lang: fr-LU
title:  "Ma première application en Europe"
date:   2022-02-17 13:16:00
categories: ReactNative
---


**Salut Dev, comment vas-tu?** Aujourd'hui je viens partager une très belle expérience que j'ai pu vivre dans ma nouvelle aventure.

<img src="/assets/images/pages/covid-check-lu-banner.jpg">

J'ai récemment déménagé du Brésil au Luxembourg, et j'ai eu de grandes expériences et je veux partager avec vous celle qui m'a aidé en tant que portefeuille qui résout un vrai problème.

Ici au Luxembourg il y a un certificat de vaccination **(UEDCC - Digital Covid Certificate of the European Union)** qui est exigé dans les établissements (restaurants, bars, spectacles, etc...) et à chaque instant de la pandémie et/ou type de l'environnement ont des règles différentes.

Les règles s'appellent 3G, 2G et 2G+ (Par ordre de difficulté), voir les règles en détail sur ces liens :
- https://guichet.public.lu/fr/actualites/2021/decembre/28-app-covidcheck.html
- https://covid19.public.lu/en/covidcheck.html#:~:text=The%202G%2B%20system,CoV%2D2%20rapid%20antigen%20test

Pour valider cela, le gouvernement devait créer une **App Offline First** pour lire le QRCode de chaque certificat et valider cela :
- Le certificat a une signature numérique valide
- Le certificat n'a pas été révoqué (Annulé)
- Le certificat n'a pas été falsifié
- C'est dans la règle actuelle

Eh bien, j'ai parfois remarqué que l'application gouvernementale se fermait d'elle-même ou plantait au point de ne pas faire les validations, ce qui obligeait les établissements à nous libérer sans faire les validations nécessaires.

Cela dit, j'ai décidé de comprendre comment les validations de ces règles fonctionnaient au sein de l'APP qui a été faite en ionique, pour essayer de reproduire exactement les mêmes validations en utilisant React Native.

**et voilà**

La première étape a été de comprendre le fonctionnement de l'App, et avec un peu de temps j'ai pu constater que :
- L'application a été écrite en ionique, ce qui m'a aidé, car j'ai beaucoup utilisé ionique dans le passé.
- En lisant le code JS généré, j'ai pu identifier les validations qui se font dans :
    -Base45
    - CBOR
    - COSE
    - Signature PEM
- Il existe une API officielle accessible uniquement avec une clé .p12 qui nous donne :
    - Certificats à clé publique pour valider la signature des certificats de vaccination
    - Liste des certificats révoqués
        - Crypté en SHA256

Avec toutes ces informations, j'ai commencé à concevoir la nouvelle mise en page de l'application et à rédiger toutes ces validations obligatoires. Enfin, nous avons pu atteindre le résultat souhaité.

Et en plus de cela, j'ai ajouté quelques fonctionnalités que l'application officielle n'a pas, à savoir :
- 5 langues disponibles
- Enregistrer mes certificats hors ligne

Voir le résultat final sur: https://pedroentringer.dev/app/covid-check-lu/
Mise en page initiale: https://www.figma.com/file/FgWcvLqgUQyMF77909GxQM/CovidCheck---Lu?node-id=0%3A1

<div class="splash projects">
    <div class="content">
        <span class="title">Publication</span>
        <span class="description">Cette application ne peut pas être publiée car elle utilise des données sensibles liées à la santé, selon Apple et Google.</span>
    </div>
</div>