---
layout: post
lang: fr-LU
title:  "Ma première application en Europe"
date:   2022-02-17 13:16:00
categories: ReactNative
---


**Salut Dev, comment vas-tu?** Aujourd'hui je viens partager une très belle expérience que j'ai pu vivre dans ma nouvelle aventure.

<img src="/assets/images/pages/covid-check-lu-banner.jpg">

Ici au Luxembourg il y a un certificat de vaccination (UEDCC - Digital Covid Certificate of the European Union) qui est exigé dans les établissements (restaurants, bars, spectacles, etc...) et chaque moment de la pandémie et/ou type d'environnement a des effets différents des règles.

Les règles s'appellent 3G, 2G et 2G+ (Par ordre de difficulté).

Pour valider cela, le gouvernement devait créer une première application hors ligne pour lire le QRCode de chaque certificat et valider que le certificat a une signature numérique valide et que les dates et les doses de vaccination, de test covid et/ou de certificat de récupération concordent avec le règle valable pour cette période et cet établissement.

Eh bien, parfois, j'ai remarqué que l'application gouvernementale se fermait d'elle-même ou plantait au point de ne pas faire les validations, ce qui obligeait les établissements à nous libérer de toute façon.

Cela dit, j'ai décidé de comprendre comment les validations de ces règles fonctionnaient au sein de l'APP qui a été faite en ionique, pour essayer de reproduire exactement les mêmes validations en utilisant React Native.

**et voilà**

Les validations consistent en :
- Base45
- CBOR
- COSE
- Signature PEM


Et pour couronner le tout, quelques fonctionnalités supplémentaires que l'application officielle ne possède pas, à savoir :
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