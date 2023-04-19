---

title: Planification avec Machine Learning (Mise à jour 2023)
summary: Comprendre ce que FMT est, et ce qu'il fait.
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

# Planification forestière à l’aide du Machine Learning (Mise à jour 2023)

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

---


## Les problématiques

- Notre bilan carbone est difficile à obtenir rapidement
  - Pour tous les réservoirs de l'écosystème
  - Pour la productivité nette de l'écosystème
- La localisation des activités sylvicoles pouvant améliorer le bilan carbone est un enjeu complexe
  - Le calcul du bilan de carbone forestier pour un modèle de planification est complexe
  - L’optimisation spatialement explicite de la localisation travaux sylvicoles peut être très difficile à réaliser avec un modèle mathématique classique

---

## Les objectifs

- Obtention du bilan de carbone rapidement (Article #1)
  - Tous les réservoirs de l’écosystème
  - La productivité nette de l’écosystème

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
         Volume_de_feuillue_tolérant
         Volume_de_feuillue_intolérant
         Volume_de_résineux
       }
       Intrants --> Réservoir
      state Réservoir {
       Biomasse_aérienne
       Biomasse_sousterraine
       Bois_mort
       Carbone_du_sol
       Litière
     }
      Intrants --> Flux
     State Flux {
        Productivitée_nette_de_l'écosystème
        }
   }
   IA_par_pixel --> Entrainement
   State Entrainement {
     direction LR
     CPF2328 --> Global
     CPF2328 --> Pessière
     CPF2328 --> Sapnière
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

## Les objectifs

- Génération de planification spatialement explicite (Article #2)
  - Maximisant le bilan carbone

---

## 2. Planification spatial par IA
<div class=mermaid>
    stateDiagram
    direction LR
    Modèle --> FMTsamodel
    state FMTsamodel {
       direction LR
       state Intrants {
         direction LR
         (i)Spatiaux --> Age
         (i)Spatiaux --> Opérabilité
         (i)Non_spatiaux --> Taille_des_blocs
         (i)Non_spatiaux --> Délai_dadjacence
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
Test et validation avec modèles réel		     :active,des15,2022-10-01,2023-05-01
section IA Planification spatiale
Test et extension à la planification référencé          :     done,des9, 2022-02-01,2022-05-01
Mise en place d'un plan :     done,des10, 2021-12-01,2022-02-01
Lien FMT avec Python avec exemple       :     done,des11, 2022-01-01,2022-04-01
Modèle IA de spatialisation minétique:		done,des12, 2022-04-01,2022-10-01
Modèle IA d'évaluation de planification spatialisé           :crit,des13,2022-09-01,2023-03-01
Modèle IA de spatialisation (avec carbone)           :crit,des14,2023-02-01,2023-08-01
Écriture de l'article planification forestière	:des17,2023-04-01,2023-08-01
</div>

---


## Concrètement

  - Nouvelle syntaxe pour la section yield
  - Voir dans examples/Models/TWD_land/Scenarios/Predictors
  - Développement d'un nouveau type de yield "*YM"

---

## Modèle pour la productivité nette de l'écosystème

  <pre><code class="fmt" data-line-numbers="3|4|5|6|7|9|10|13|14|15|16|17" style="font-size:120%">
  {
  Intrants:
    - GFI = Volume de feuillus intolérants
    - GFT = Volume de feuillus tolérants
    - GF = Volume feuillu total
    - GR = Volume résineux total
  }
  *YM ? ? ?
  NEP _PRED(nep_carbon,GFI,GFT,GF,GR)
  {
  Extrants:
    - AG_Biomass_C = Biomasse aérienne
    - BG_Biomass_C = Biomasse souterraine
    - Deadwood_C = Bois mort
    - Litter_C = Littière
    - Soil_C = Sol

  }
  </code></pre>

---

## Suivi des variables

  <pre><code class="fmt" data-line-numbers="4|5|8|9|12|13" style="font-size:120%">

  ;Inventaire de la productivité nette de l'écosystème en (c)
  *OUTPUT NEP_Total
  *SOURCE ? ? ? _INVENT NEP

  ;Inventaire de la biomasse anérienne et souterraine en (c)
  *OUTPUT Biomasse_Total
  *SOURCE ? ? ? _INVENT AG_Biomass_C + ? ? ? _INVENT BG_Biomass_C

  ;Inventaire du carbone du sol en (c)
  *OUTPUT Sols_Total
  *SOURCE ? ? ? _INVENT Soil_C

  </code></pre>

---

<div class=mermaid>
gantt
dateFormat  YYYY-MM-DD
tickInterval month
axisFormat %b%d
title Validation
excludes weekends
section Échelle Global
Apprentissage (29 UA)          :done,des1,2023-01-09,2023-01-23 
Tests avec spatialisations          :     done,des2,2023-03-01,2023-04-01
Analyse des résultats           :     done,des3,2023-04-01,2023-04-17
Présentation          :     active,des4,2023-05-01,2023-05-02
section Échelle des régions écologique
Apprentissage (29 UA) :done,des5,2023-01-02,2023-01-16
Tester l'IA (08665) : done,des6,2023-03-06,2023-03-27
section Optimisation du NEP
Tester avec 02751 : done,des7,2023-04-03,2023-04-24
</div>

---

## GCBM vs IA (02751)

{{< figure src="NEP.jpg" width="100%" height="auto" >}}

---

## GCBM vs IA (02751)

{{< figure src="Cardone_du_sol.jpg" width="100%" height="auto" >}}

---

## Constats IA vs GCBM

   - Le modèle d'IA par région écologique performe le moins bien
      - Sous estime le carbone des réservoirs
      - Sous estime le NEP
   - le modèle d'IA Global est le meilleur
   - Les scénarios spatialement explicite se collent à GCBM
      - Les scénarios non spatiaux performe moins bien
      - L'objetif du projet était l'utilisation avec des modèles spatialement explicite
---

## Pourquoi ces différences?
   - Les résultats de l'IA diffère de GCBM car:
      - IA suppose le feu comme perturbation historique
      - GCBM utilise plus d'intrants
         - Température annuelle moyenne
         - Précipitation
         - Transitions après perturbation
   - Le modèle par région écologique performe moins bien car:
      - Manque de données par modèle
      - Les régions écologiques sont difficiles à identifier dans les modèles
      - Superficie orpheline dans les modèles
   
---

## Optimisation du NEP (02751)

{{< figure src="NEP_GLOBAL.jpg" width="100%" height="auto" >}}

---

## Constats

  - Le scénario MAX NEP à le meilleur bilan carbone
  - Le scénario MAX AAM stock plus de carbone dans les différents réservoirs

---


## Conclusion

- On peut maintenant obtenir un bilan de carbone facilement pour:
  - Une solution spatiale explicite et spatialement référencée
  - Les cinqs principaux réservoirs de l'écosystème
  - Le flux de productivité nette de l'écosystème
  - Calcul du bilan avec perturbations naturelles en utilisant la replanification
- On peut maintenant utiliser le Machine Learning pour:
  - Générer des intrants au calcul (Courbes de productions, Paramètres économique)
- La génération de solution spatiale avec IA:
   - Nous permettra de faire de l'optimisation spatialement explicite
   - Utilisation du projet de fin d'étude à B. Forest
   - Replanification spatialement explicite

---

## Les prochaines étapes

- Validation de l'impact de l'optimisation du NEP sur
  - Le choix des travaux sylvicoles
  - La productivité
  - Les rotations
- Faire d'autres analyses (UA du sud)
- Faire un scénario de maximisation des puits de carbone
- Générer une base de donnée contenant les actions:
  - Feux
  - Récupération après feux
- Tester le tout en replanification

---

## Des Questions?

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

<script src="https://code.jquery.com/jquery-3.5.0.js"></script>
<script>
$( "li" ).addClass( "fragment" );
// $( "p" ).addClass( "fragment" );
</script>
