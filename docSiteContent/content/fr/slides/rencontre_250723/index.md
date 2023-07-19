---

title: Planification avec Machine Learning (Mise à jour 2023)
summary: Comprendre le projet d'IA
authors: ["Guillaume Cyr,ing.f.,M.Sc."]
tags: ["Machine learning","FORAC","carbon"]
categories: []
slides:
  # Choose a theme from https://github.com/hakimel/reveal.js#theming
  theme: white
  # Choose a code highlighting style (if highlighting enabled in `params.toml`)
  #   Light style: github. Dark style: dracula (default).
  highlight_style: vs
  diagram: true

---

# FMT et développements, mise à jour été 2023

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

---

## Plan de la présentation

- Les problématiques 
- Présentation de FMT
- Génération de calendriers de COS
- Replanification 
- Intélligence artificielle (machine learning)
- Chronologie des travaux entamés
- Planification 0-6 mois
- Planification 1-5 ans
- Questions


---

## Les problématiques

- Notre bilan carbone est difficile à obtenir rapidement
  - Pour tous les réservoirs de l'écosystème
  - Pour la productivité nette de l'écosystème
- La localisation des activités sylvicoles pouvant améliorer le bilan carbone est un enjeu complexe
  - Le calcul du bilan de carbone forestier pour un modèle de planification est complexe
  - L’optimisation spatialement explicite de la localisation des travaux sylvicoles peut être très difficile à réaliser avec un modèle mathématique classique



---

## Qu'est-ce que FMT ?


- librairie open source
- objets et fonctions
- en C++, R ou Python

<div class=mermaid>
graph TD;
  2-->14;
  1-->2-->5;
  1-->3-->6;
  1-->4-->7;
  3-->8;
  3-->12;
  3-->15;
  4-->13;
  5-->9;
  5-->10;
  5-->11;
  1["FMT API"];
  2["C++"];
  3["Python"];
  4["Cran R"];
  5["Interface"];
  6["Re-planification"];
  7["Base de données"];
  8["BFEC-GCBM"];
  9["Planification"];
  10["Spatialisation"];
  11["BFECOPT"];
  12["Analyses diverses"];
  13["Autre"];
  14["Tests"];
  15["Machine Learning"];
</div>

---

<div class=mermaid>
classDiagram
    FMTobject <|-- FMTmodel
    FMTmodel <|-- FMTsemodel
    FMTsemodel <|-- FMTsamodel
    FMTsemodel <|-- FMTsesmodel
    FMTmodel <|-- FMTsrmodel
    FMTsrmodel <|-- FMTlpmodel
    FMTsrmodel <|-- FMTnssmodel
</div>

---

## A quoi sert FMT ?

- planification forestière
  - Comment récolter des forêts pour maximiser des objectifs (e.g. rendement) ?
  - Sous certaines contraintes (e.g. legislation, technologie, etc.)

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

- Code source en c++
- Compilation en Python et R

{{% fragment %}}
<!-- svg diagram created with draw.io using file > embed > svg for the html code. -->
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="611px" viewBox="-0.5 -0.5 611 151" style="max-width:100%;max-height:151px;"><defs/><g><rect x="210" y="0" width="180" height="40" fill="rgb(255, 255, 255)" stroke="rgb(0, 0, 0)" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 178px; height: 1px; padding-top: 20px; margin-left: 211px;"><div style="box-sizing: border-box; font-size: 0px; text-align: center;" data-drawio-colors="color: rgb(0, 0, 0); "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: normal; overflow-wrap: normal;"><div style="display: inline-block"><span id="L-L-1-2" class="edgeLabel L-LS-1' L-LE-2"></span></div><div style="display: inline-block"><span id="L-L-1-3" class="edgeLabel L-LS-1' L-LE-3"></span></div><div style="display: inline-block"><span id="L-L-1-4" class="edgeLabel L-LS-1' L-LE-4"></span></div><div style="display: inline-block">Interface de programmation (C++)</div></div></div></div></foreignObject><text x="300" y="24" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">Interface de programmation (C+...</text></switch></g><rect x="0" y="110" width="180" height="40" fill="rgb(255, 255, 255)" stroke="rgb(0, 0, 0)" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 178px; height: 1px; padding-top: 130px; margin-left: 1px;"><div style="box-sizing: border-box; font-size: 0px; text-align: center;" data-drawio-colors="color: rgb(0, 0, 0); "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: normal; overflow-wrap: normal;">R</div></div></div></foreignObject><text x="90" y="134" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">R</text></switch></g><rect x="210" y="110" width="180" height="40" fill="rgb(255, 255, 255)" stroke="rgb(0, 0, 0)" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 178px; height: 1px; padding-top: 130px; margin-left: 211px;"><div style="box-sizing: border-box; font-size: 0px; text-align: center;" data-drawio-colors="color: rgb(0, 0, 0); "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: normal; overflow-wrap: normal;">Python 2</div></div></div></foreignObject><text x="300" y="134" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">Python 2</text></switch></g><rect x="430" y="110" width="180" height="40" fill="rgb(255, 255, 255)" stroke="rgb(0, 0, 0)" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 178px; height: 1px; padding-top: 130px; margin-left: 431px;"><div style="box-sizing: border-box; font-size: 0px; text-align: center;" data-drawio-colors="color: rgb(0, 0, 0); "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: normal; overflow-wrap: normal;">Python 3</div></div></div></foreignObject><text x="520" y="134" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">Python 3</text></switch></g><path d="M 90 110 L 300 40" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 300 110 L 300 40" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 520 110 L 300 40" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" pointer-events="stroke"/></g><switch><g requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"/><a transform="translate(0,-5)" xlink:href="https://www.diagrams.net/doc/faq/svg-export-text-problems" target="_blank"><text text-anchor="middle" font-size="10px" x="50%" y="100%">Text is not SVG - cannot display</text></a></switch></svg>
{{% /fragment %}}

---

## Pourquoi FMT ?

{{% fragment %}}
Projet du Bureau du Forestier en Chef du Québec.
{{% /fragment %}}

- 2017 : Besoin d'automatiser l'utilisation de modèles Woodstocks
- 2018 : Besoin de relier Woodstock à GCBM pour estimations de carbone
- 2019 à aujourd'hui : Augmentation des performances et fonctionalités

---
## Planification des Compartiments d'Organisation Spatiale (COS) BFECOpt/FMT
[Documentation COS](https://bureau-du-forestier-en-chef.github.io/FMTdocs/fr/docs/advanced/operational_area_scheduling/)

{{< figure src="Figure3COS.png" width="90%" height="auto" >}}

---

## 1. Bilan carbone avec l'IA
<div class=mermaid>
    stateDiagram
    direction LR
    state IA_par_pixel {
       direction LR
       state Intrants {
         direction LR
         Dernières_perturbations
         Volume_de_feuillus_tolérants
         Volume_de_feuillus_intolérants
         Volume_de_résineux
       }
       Intrants --> Réservoir
      state Réservoir {
       Biomasse_aérienne
       Biomasse_souterraine
       Bois_mort
       Carbone_du_sol
       Litière
     }
      Intrants --> Flux
     State Flux {
        Productivité_nette_de_l'écosystème
        }
   }
   IA_par_pixel --> Entrainement
   State Entrainement {
     direction LR
     CPF2328 --> Global
     CPF2328 --> Pessière
     CPF2328 --> Sapinière
     CPF2328 --> Érablière
     }
   Entrainement --> Validation
   State Validation {
     direction LR
     Spatial --> 07152
     Non_Spatial --> 07152
     Spatial --> 02751
     Non_Spatial --> 02751
     Spatial --> 08665
     Non_Spatial --> 08665
     Spatial --> 08762
     Non_Spatial --> 08762
     Spatial --> 09751
     Non_Spatial --> 09751
     }
	 
</div>


---

## Planification spatiale par IA
<div class=mermaid>
    stateDiagram
    direction LR
    Modèle --> FMTsamodel
    state FMTsamodel {
       direction LR
       state Intrants {
         direction LR
         (i)Cellule --> Âge
         (i)Cellule --> Opérabilité
         (i)Globale --> Taille_des_blocs
         (i)Globale --> Délai_dadjacence
       }
       Intrants --> IA
      state Générateur {
       direction LR
       Calcul_2328 --> Apprentisage
     }
     State IA {
        Générateur --> Prédiction
        state Prédiction {
         (o)Spatiaux --> Blocs_de_coupes
         (o)Non_spatiaux --> faisabilité
         (o)Non_spatiaux --> Bilan_carbone
        }
   }
   IA --> Optimisation
   State Optimisation {
     Validation --> Amélioration
     }
 }
FMTsamodel --> Solution

</div>

---

## Les objectifs

- Obtention du bilan de carbone rapidement (Article #1)
  - Tous les réservoirs de l’écosystème
  - La productivité nette de l’écosystème
- Génération de planification spatialement explicite (Article #2)
  - Maximisant le bilan carbone
  
---

<div class=mermaid>
gantt
dateFormat  YYYY-MM-DD
title Projet FORAC
excludes weekends
section IA estimateur carbone
Script d'entrainement          :done,des1,2021-04-01,2021-09-01 
Script d'utilisation           :     done,des2,2021-04-01,2021-09-01
Sélection du modèle           :     done,des2,2021-04-01,2021-05-01
Rapport          :     done,des3,2021-09-01,2021-10-01
Installation FMT release           :done,des4,2021-10-01,2021-11-01
Pytorch à C++            :     done,des5, 2021-10-01,2021-12-01
Installation FMT debug          :     done,des6, 2021-12-01,2022-03-01
Implémentation :     done,des8, 2021-12-01,2022-03-01
Écriture de l'article estimateur de carbone		:des16,2022-09-01,2023-04-01
Test et validation avec modèles réels		     :active,des15,2022-10-01,2023-05-01
section IA Planification spatiale
Test et extension à la planification référencée          :     done,des9, 2022-02-01,2022-05-01
Mise en place d'un plan :     done,des10, 2021-12-01,2022-02-01
Lien FMT avec Python avec exemple       :     done,des11, 2022-01-01,2022-04-01
Modèle IA de spatialisation mimétique:		done,des12, 2022-04-01,2022-10-01
Modèle IA d'évaluation de planification spatialisée           :done,des13,2022-09-01,2023-03-01
Modèle IA de spatialisation (avec carbone)           :done,des14,2023-02-01,2023-08-01
Écriture de l'article planification forestière	:crit,des17,2023-04-01,2023-08-01

</div>
  


---


## Constats IA vs GCBM

- On peut maintenant obtenir un bilan de carbone facilement pour:
  - Une solution spatiale explicite et spatialement référencée
  - Les cinq principaux réservoirs de l'écosystème
  - Les émissions nettes de l'écosystème
- On peut maintenant utiliser le Machine Learning pour:
  - Générer des intrants au calcul (Courbes de production, Paramètres économiques)
- La génération de solution spatiale avec IA:
   - Nous permettra de faire de l'optimisation spatialement explicite
   - Utilisation du projet de fin d'étude à B. Forest
   - Replanification spatialement explicite



---

<div class=mermaid>
gantt
dateFormat  YYYY-MM-DD
title Travaux entamés
excludes weekends
section Replanification
Développement :done, 2023-01-23,2023-05-22
Support travaux (F.S, L-A.G, A.B-R) : ,2023-01-09,2023-12-27 
section Interface
Déploiement :done, 2023-01-09,2023-01-10
Présentation :done, 2023-03-13,2023-03-14
Documentation  :done, 2023-04-03,2023-04-10
Mise en place d'un comité FMT : ,2023-05-1,2023-11-07
BFEC-GCBM, mises à jour de FMT :en cours, 2023-07-10,2023-08-28
section Génération de calendriers de COS
Intégration des fonctions du chantier COS : en cours, 2023-04-17,2023-12-27
section IA
Modèles global et par sous-domaines d'intéligence artificielle  :done,2023-01-09,2023-05-01
Tests IA :en cours,2023-04-03,2023-08-28 
Entretien et débogage : ,2023-01-02,2023-12-27
Support : ,2023-01-02,2023-12-27

</div>


---

## Étapes de la replannification (0-6 mois)

<div class=mermaid>
gantt
dateFormat  YYYY-MM-DD
tickInterval month
axisFormat %b%d
title Planification 0-6 mois
excludes weekends
section Carbone
Utilisation de l'IA pour l'obtention d'un bilan carbone : ,2023-12-18,2024-01-01
section Analyses
Support et transfert aux analystes : , 2023-08-07,2024-01-01


</div>

[Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/fr/docs/advanced/replanning/)

---

## Étapes de l'IA (0-6 mois)

<div class=mermaid>
gantt
dateFormat  YYYY-MM-DD
tickInterval month
axisFormat %b%d
title Planification 0-6 mois
excludes weekends
section IA Carbone
Optimisation NEP, :active,    des1, 2023-05-26,2023-07-24
Optimisation des Stocks de Carbone	:active,    des1, 2023-05-26,2023-07-24
Optimisation AAM 2751 (scénario de référence) :active,    des1, 2023-06-26,2023-07-24
Optimisation stock de carbone forêt feuillue - UA 7152 :    des1, 2023-07-14,2023-09-28
Analyses des différents scénarios d'optimisation :    des1, 2023-08-17,2023-09-28
Apprentissage avec perturbations naturelles :    des1, 2023-09-04,2023-12-18
Développement pour opérationalisation du calcul : ,2023-09-04,2024-01-01
Test de l'opérationalisation : ,2023-10-06,2024-01-01

</div>

[Documentation Machine Learning](https://bureau-du-forestier-en-chef.github.io/FMTdocs/fr/slides/fmt_carbone/)

---

## Interface (0-6 mois)

<div class=mermaid>
gantt
dateFormat  YYYY-MM-DD
tickInterval month
axisFormat %b%d
title Planification 0-6 mois
excludes weekends
section SSP
Analyses de modèles de base : ,2023-08-17,2024-01-01
section Spatialisation
Ajout du Simulated Annealing : ,2023-10-16,2024-01-01
section Calendrier de COS
Intégration des fonctions du chantier COS : en cours, 2023-04-17,2023-12-27


</div>



---

## LES FUTURS PROJETS (0-5 ans)

- GCBM
	- Mise à jour de GCBM
	- Refonte des scripts en Python3
	- Intégration des produits du bois
	
- Spatialisation du calcul
	- Développement d'un indice caribou
	- Développement d'un indice de réseau routier
	- Visualisation de solutions alternatives
	
---
## LES FUTURS PROJETS (0-5 ans)
	
- Gestion des modèles de base
	- Automatisation des validations
	- Automatisation d'analyses préliminaires 
	
- Replanification spatialement explicite
	- Permettre d’obtenir un effet de spatialisation à travers la replanification
	- Utilisation d’évènements stochastiques spatiaux
	- Inclure les enjeux opérationels
	- Changements climatiques
	
- Continuer/faire un transfert de connaissances aux différents services
	


---

## Des Questions?

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

<script src="https://code.jquery.com/jquery-3.5.0.js"></script>
<script>
$( "li" ).addClass( "fragment" );
// $( "p" ).addClass( "fragment" );
</script>
