---
title: La mise à jour de la 1er période dans FMT
summary: Comprendre la mise à jour d'un FMTmodel
authors: ["Guillaume Cyr,ing.f.,M.Sc."]
tags: ["FMT"]
categories: []
slides:
  # Choose a theme from https://github.com/hakimel/reveal.js#theming
  theme: white
  # Choose a code highlighting style (if highlighting enabled in `params.toml`)
  #   Light style: github. Dark style: dracula (default).
  highlight_style: vs
  diagram: true
---

# Mise à jour d'un modèle

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

---

## Pourquoi la mise à jour dans les modèles?

<div class=mermaid>
graph TD;
  1-->2;
  1-->3;
  1["La carte n'est pas à jour"];
  2["Les données ne sont pas disponibles"];
  3["La période précédente de calcul n'est pas terminée"];
</div>

---

## Les désavantages de la mise à jour

- Peut causer des biais dans les analyses d'impact
  - Sous-estimer l'impact d'un retrait de superficie
  - Surestimer le retrait d'une balise de traitement sylvicole
- Ralentit le solveur mathématique 
  - Solutions multiples
- Certains solveurs ne peuvent pas résoudre le modèle
- Causer des erreurs de manipulation (ex: changer les bornes avec _GOAL)
- Causer des biais dans l'interprétation carbone du modèle
- Causer des erreurs d'interprétation

---

## La mise à jour non spatiale

<div class=mermaid>
graph TD;
  1-->2;
  1-->3;
  1-->4;
  1-->5;
  1-->6;
  2-->7;
  3-->7;
  4-->7;
  5-->7;
  6-->7;
  1["Résoudre le modèle (1 période ou +)"]
  2["Mettre à jour la section area"]
  3["Mettre à jour les contraintes"]
  4["Mettre à jour les actions"]
  5["Mettre à jour les transitions"]
  6["Mettre à jour les yields"]
  7["Réécrire le modèle"]
</div>

---

## À venir

- Mise à jour de la carte?
  - Perte en précision
  - Plus complexe
- Alternative
  - Mise à jour non spatiale avec tous les thèmes

---

## Des Questions?

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

<script src="https://code.jquery.com/jquery-3.5.0.js"></script>
<script>
$( "li" ).addClass( "fragment" );
// $( "p" ).addClass( "fragment" );
</script>


