---
title: Les bases de FMT
summary: Comprendre ce que FMT est, et ce qu'il fait.
authors: []
tags: []
categories: []
slides:
  # Choose a theme from https://github.com/hakimel/reveal.js#theming
  theme: white
  # Choose a code highlighting style (if highlighting enabled in `params.toml`)
  #   Light style: github. Dark style: dracula (default).
  highlight_style: obsidian
  diagram: true
---

# Les bases de FMT

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/gcyr/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)


---

## Qu'est-ce que FMT ?


- librairie open source
- objets et fonctions
- en C++, R ou Python

---

## A quoi sert FMT ?

- planification forestière
  - comment récolter des forêts pour maximiser des objectifs (e.g. rendement) ?
  - sous certaines contraintes (e.g. legislation, technologie, etc.)

---

## En pratique, a quoi ca sert ?

- Lecture de modèles Woodstock
- Simulation de paysages forestiers
- Optimisation / Ré-optimisation de modèles
- Planification (en utilisant des événements stochastiques)
- Résolution des heuristiques d'agrégation
- Planification des blocs de récolte

---

## Comment est-ce que ça marche ?

- code source en c++
- compilation en Python et R

{{% fragment %}}
<!-- svg diagram created with draw.io using file > embed > svg for the html code. -->
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="611px" viewBox="-0.5 -0.5 611 151" style="max-width:100%;max-height:151px;"><defs/><g><rect x="210" y="0" width="180" height="40" fill="rgb(255, 255, 255)" stroke="rgb(0, 0, 0)" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 178px; height: 1px; padding-top: 20px; margin-left: 211px;"><div style="box-sizing: border-box; font-size: 0px; text-align: center;" data-drawio-colors="color: rgb(0, 0, 0); "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: normal; overflow-wrap: normal;"><div style="display: inline-block"><span id="L-L-1-2" class="edgeLabel L-LS-1' L-LE-2"></span></div><div style="display: inline-block"><span id="L-L-1-3" class="edgeLabel L-LS-1' L-LE-3"></span></div><div style="display: inline-block"><span id="L-L-1-4" class="edgeLabel L-LS-1' L-LE-4"></span></div><div style="display: inline-block">Interface de programmation (C++)</div></div></div></div></foreignObject><text x="300" y="24" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">Interface de programmation (C+...</text></switch></g><rect x="0" y="110" width="180" height="40" fill="rgb(255, 255, 255)" stroke="rgb(0, 0, 0)" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 178px; height: 1px; padding-top: 130px; margin-left: 1px;"><div style="box-sizing: border-box; font-size: 0px; text-align: center;" data-drawio-colors="color: rgb(0, 0, 0); "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: normal; overflow-wrap: normal;">R</div></div></div></foreignObject><text x="90" y="134" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">R</text></switch></g><rect x="210" y="110" width="180" height="40" fill="rgb(255, 255, 255)" stroke="rgb(0, 0, 0)" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 178px; height: 1px; padding-top: 130px; margin-left: 211px;"><div style="box-sizing: border-box; font-size: 0px; text-align: center;" data-drawio-colors="color: rgb(0, 0, 0); "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: normal; overflow-wrap: normal;">Python 2</div></div></div></foreignObject><text x="300" y="134" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">Python 2</text></switch></g><rect x="430" y="110" width="180" height="40" fill="rgb(255, 255, 255)" stroke="rgb(0, 0, 0)" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 178px; height: 1px; padding-top: 130px; margin-left: 431px;"><div style="box-sizing: border-box; font-size: 0px; text-align: center;" data-drawio-colors="color: rgb(0, 0, 0); "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: normal; overflow-wrap: normal;">Python 3</div></div></div></foreignObject><text x="520" y="134" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">Python 3</text></switch></g><path d="M 90 110 L 300 40" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 300 110 L 300 40" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 520 110 L 300 40" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" pointer-events="stroke"/></g><switch><g requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"/><a transform="translate(0,-5)" xlink:href="https://www.diagrams.net/doc/faq/svg-export-text-problems" target="_blank"><text text-anchor="middle" font-size="10px" x="50%" y="100%">Text is not SVG - cannot display</text></a></switch></svg>
{{% /fragment %}}

---

## Pourquoi FMT ?

{{% fragment %}}
Projet du Bureau du Forestier en Chec du Québec.
{{% /fragment %}}

- 2017 : Besoin d'automatiser l'utilisation de modèles Woodstocks
- 2018 : Besoin de relier Woodstock à GCBM pour estimations de carbone
- 2019 à aujourd'hui : Augmentation des performances et fonctionalités


---

## Le futur

- Interface utilisateurice (GUI)
- Optimisation spatialement explicite
- Meilleus *outputs* (sorties)

---

## Comment ca, du code interactif ?

Reveal.js permet de mettre en valeur des lignes par intermitence. Ici, la 2, puis la 8, puis la 13. Fantastique pour parler du code au fur et à mesure.

<pre><code class="R" data-line-numbers="2|8|13">
library(FMT) # Charge FMT dans R
if (new(FMTversion)$hasfeature("OSI")) # Vérifie si FMT a été compilé avec OSI en créant un nouvel objet FMTversion, et en utilisant la fonction hasfeature().
{
  # Créer un objet parser pour lire les fichiers du modèle
  newmodelparser <- new(FMTmodelparser)
  # Lit le projet avec le parser en spécifiant que l'on veut lire le scénario "LP" (il peut y avoir différents scénarios dans un seul modèle)
  modelslist <- newmodelparser$readproject("Models/TWD_land/TWD_land.pri", c("LP"), TRUE, TRUE, TRUE)
  model_scenario_lp <- modelslist[[1]]
  # On récupère le nom du modèle
  print(model_scen_lp$getname())
} else { # Si le modèle n'as pas été compilé avec OSI, on affiche une erreur
  print("FMT needs to be compiled with OSI")
}
</code></pre>

---

## La suite

[Objets, parsing et interrogation](../objets_parsing_interrogation)

^ Lien vers la présentation suivante, si besoin.


<script src="https://code.jquery.com/jquery-3.5.0.js"></script>
<script>
$( "li" ).addClass( "fragment" );
// $( "p" ).addClass( "fragment" );
</script>