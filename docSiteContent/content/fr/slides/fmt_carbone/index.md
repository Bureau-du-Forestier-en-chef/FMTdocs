---
title: Planification forestière à l’aide du Machine Learning
summary: Comprendre ce que FMT est, et ce qu'il fait.
authors: ["Guillaume Cyr,ing.f.,M.Sc."]
tags: ["Machine learning","FORAC","carbon"]
categories: []
slides:
  # Choose a theme from https://github.com/hakimel/reveal.js#theming
  theme: white
  # Choose a code highlighting style (if highlighting enabled in `params.toml`)
  #   Light style: github. Dark style: dracula (default).
  highlight_style: obsidian
---

# Planification forestière à l’aide du Machine Learning

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

---

## Problématique

- Notre bilan carbone est difficile à obtenir rapidement:
  - Pour tous les réservoirs de l'écosystème:
    - Carbone du sol
    - Biomasse souterraine
    - Litière
    - Bois mort
    - Biomasse aérienne
  - La productivité nette de l'écosystème

---

## Problématique

- La localisation des activités sylvicoles pouvant améliorer le bilan carbone est un enjeux complexe:
  - Le calcul du bilan de carbone forestier pour un modèle de planification est complexe
  - L’optimisation spatialement explicit de la localisation travaux sylvicoles peut être très difficile à réaliser avec un modèle mathématique classique


---

## Productivité nette de l’écosystème


{{< figure src="nep1.jpg" width="30%" height="30%" >}}


---

## Productivité nette de l’écosystème


{{< figure src="nep2.jpg" width="30%" height="30%" >}}


---

## Problématique: Productivité nette de l’écosystème ?


{{< figure src="nep3.jpg" width="30%" height="30%" >}}


---

## Problématique: États des réservoirs ?


{{< figure src="pool1.jpg" width="30%" height="30%" >}}


---

## Problématique: États des réservoirs ?


{{< figure src="pool2.jpg" width="30%" height="30%" >}}


---

## Problématique: États des réservoirs ?


{{< figure src="pool3.jpg" width="30%" height="30%" >}}

---

## Problématique:…Pour chaque période

{{< figure src="gcbm1.jpg" width="auto" height="auto" >}}

---

## Objectif

- Obtention du bilan de carbone rapidement:
  - Tous les réservoirs de l’écosystème:
    - Carbone du sol
    - Biomasse sous-terraine
    - Litière
    - Bois mort
    - Biomasse aérienne
  - La productivité nette de l’écosystème

---

## Données d'apprentissage

{{< figure src="gcbm2.jpg" width="auto" height="auto" >}}

---

## Apprentissage réalisé

{{< figure src="ml1.jpg" width="auto" height="auto" >}}

---

## Utilisation de la méthode

{{< figure src="m1.jpg" width="auto" height="auto" >}}


---

## Utilisation de la méthode

{{< figure src="m2.jpg" width="auto" height="auto" >}}


---

## Concrètement

  - Nouvelle syntaxe pour la section yield
  - Voir dans examples/Models/TWD_land/Scenarios/Predictors
  - Développement d'un nouveau type de yield "*YM"
 

---

## Modèle pour les réservoirs

  <pre><code class="fmt" data-line-numbers="3|4|5|6|7|9|10|13|14|15|16|17">
  {
  Intrants:
    - GFI = Volume de feuillu intolérant
    - GFT = Volume de feuillu tolérant
    - GF = Volume feuillu total
    - GR = Volume résineu total
  }
  *YM ? ? ?
  AG_Biomass_C,BG_Biomass_C,Deadwood_C,Litter_C,Soil_C _PRED(pools_carbon,GFI,GFT,GF,GR)
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

## Modèle pour la productivité nette de l'écosystème

  <pre><code class="fmt" data-line-numbers="3|4|5|6|7|9|10|13|14|15|16|17">
  {
  Intrants:
    - GFI = Volume de feuillu intolérant
    - GFT = Volume de feuillu tolérant
    - GF = Volume feuillu total
    - GR = Volume résineu total
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

  <pre><code class="fmt" data-line-numbers="4|5|8|9|12|13">

  ;Inventaire de la productivité nette de l'écosystème en (c)
  *OUTPUT NEP_Total
  *SOURCE ? ? ? _INVENT NEP

  ;Inventaire de la biomasse anérienne et souterraine en (c)
  *OUTPUT Biomasse_Total
  *SOURCE ? ? ? _INVENT AG_Biomass_C + ? ? ? _INVENT BG_Biomass_C

  ;Inventaire du carbone du sols en (c)
  *OUTPUT Sols_Total
  *SOURCE ? ? ? _INVENT Soil_C

  </code></pre>

---



## Problématique : Optimisation spatialement explicit
  - Pratiquement impossible avec nos modèles
  - Utilisations d’un heuristique pour placer la récolte
  - Difficile à obtenir à moins d’implémenter une heuristique:
    - Simulated Annealing
    - Tabou Search
  - Utilisation de la programmation mixte intégrale
  - On peut donc difficilement répondre à la question du ou doit t’on faire t'elle ou t'elle traitement pour améliorer notre bilan de carbone


---

## Objectif : Meilleur bilan

{{< figure src="se1.jpg" width="auto" height="auto" >}}


---

## Objectif : Meilleur bilan

{{< figure src="se2.jpg" width="auto" height="auto" >}}


---

## Objectif : Meilleur bilan et respect des contraintes

{{< figure src="se3.jpg" width="auto" height="auto" >}}


---

## Objectif : Meilleur bilan et respect des contraintes

{{< figure src="se4.jpg" width="auto" height="auto" >}}

---

## Apprentissage

{{< figure src="a1.jpg" width="auto" height="auto" >}}

---

## Apprentissage

{{< figure src="a2.jpg" width="auto" height="auto" >}}

---

## Des Questions?

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

<script src="https://code.jquery.com/jquery-3.5.0.js"></script>
<script>
$( "li" ).addClass( "fragment" );
// $( "p" ).addClass( "fragment" );
</script>
