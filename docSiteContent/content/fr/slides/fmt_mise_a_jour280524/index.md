---
title: Les différents projets avec FMT
summary: Comprendre ce que FMT est, et ce qu'il fait.
authors: ["Guillaume Cyr,ing.f.,M.Sc."]
tags: ["FMT","FORAC","carbone","Machine Learning","Interface"]
categories: []
slides:
  # Choose a theme from https://github.com/hakimel/reveal.js#theming
  theme: white
  # Choose a code highlighting style (if highlighting enabled in `params.toml`)
  #   Light style: github. Dark style: dracula (default).
  highlight_style: vs
  diagram: true
---

# FMT où en somme-nous?

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

---

## C'est quoi FMT?

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
  6["Replanification"];
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

## Comment on test FMT (129 tests)?

<div class=mermaid>
pie title Les tests de FMT
  "Syntaxe" : 5.42
  "Unités spatiales" : 4.65
  "Planification" : 44.18
  "Écriture de modèles" : 10.85
  "Interface" : 3.87
  "Replanification" : 6.20
  "Spatialisation" : 9.3
  "Machine Learning" : 5.42
  "Presolve" : 2.32
  "Analyse de retrait" : 0.77
  "Analyses autre" : 6.98
</div>

---

## L'évolution des versions

<div class=mermaid>
flowchart LR;
13 --> 14 --> 19;
13 --> 15 --> 19;
13 --> 16 --> 19;
13 --> 17 --> 19;
13 --> 40 --> 19;
19 --> 20 --> 27;
19 --> 21 --> 27;
19 --> 22 --> 27;
19 --> 23 --> 27;
19 -->24 --> 27;
19 --> 25 --> 27;
19 -->26 --> 27;
27 --> 28 --> 39;
27 --> 29 --> 39;
27 --> 30 --> 39;
27 --> 31 --> 39;
27 --> 32 --> 39;
27 --> 33 --> 39;
27 --> 34 --> 39;
27 --> 35 --> 39;
27 --> 36 --> 39;
27 --> 37 --> 39;
27 --> 38 --> 39;
13["0.9.6"]
14["Développement SA spatialisation"]
15["Juxtaposition"]
16["Decision COS"]
17["Yield Distance"]
40["Atteinte 7m COS"]
19["0.9.7"]
20["Coverage"]
21["Mosek 10"]
22["Pas de choix FMTExcel"]
23["Keyword TSLA"]
24["Update Driver GDAL"]
25["Update Interface COS"]
26["Outils Rotation"]
27["0.9.8"]
28["Vitesse Presolve"]
29["Aléatoire replanification"]
30["Tests Stochastiques"]
31["Theme FMTExcel"]
32["VCPKG manifest mode GCBM"]
33["Rtools 4.4"]
34["Optimisation contraintes inventaires"]
35["Spatialisation aggrégation"]
36["Spatialisation séparation"]
37["Spatialisation Optimisation"]
38["Optimisation Replanification"]
39["0.9.9 en développement"]
</div>

---

## Les nouveautés 0.9.9
- FMTExcel
  - Chantier COS
  - Valideurs FMT (Rotations,Séries,non récolté)
- Spatialisation
  - Agrégation des actions
  - Division des actions
  - Optimisation du temps de résolution
- Replanification
  - +Vitesse
  - Stochastiques (mots clés)
  - Approche AWS
  - Meilleure gestion de la mémoire
  - Fichier .par utilisable par scénario

---

## Les travaux

<div class=mermaid>
gantt
dateFormat  YYYY-MM-DD
title Les travaux dans FMT
excludes weekends
section Interface
Modification BFECopt: done,des13,2023-05-01,2023-11-01
Modification spatialisation: done,des14, 2023-10-01,2023-12-01
Mise à jour interface: done,des15,2024-03-01,2024-05-01
Adaptation carbone: active,des2, 2024-06-01,2024-08-15
section Formations
Formation Replanification(2) :done,des2,2024-02-27,2024-03-15
section FMTExcel
Développement outil de validation						:done,des2,2023-09-01,2024-01-15
section Spatialisation
Optimisation de la vitesse								:done,des2, 2024-06-01,2024-06-10
Agrégation												:done,des2, 2024-01-15,2024-06-10
Division												:done,des2, 2024-06-01,2024-06-10
Statistique 											:active,des2, 2024-07-01,2024-08-01
section CC
Ajout Syntaxe Aléatoire									:done,des2, 2024-04-01,2024-06-01
Optimisation de la mémoire								:active,des2, 2024-05-15,2024-06-01
Optimisation de la vitesse								:crit,des2, 2024-06-01,2024-08-01
Optimisation des contraintes d'inventaires				:active,des2, 2024-05-01,2024-08-01
Migration AWS											:active,des2, 2024-05-01,2024-08-01
Cédule carbone											:active,des2, 2024-07-01,2024-07-15
section Carbone
Adaptation modèle TBE									:active,des2, 2024-09-01,2024-09-15
section Générale
Débogage                                                :       des3,2023-01-01,2024-10-30
</div>

---

## Intégration de Marc-Alexandre

- Objectifs
    - Libérer Guillaume des taches pouvant être délégués
    - Commencer à transmettre des connaissances liées a FMT
    - Permets de varier le travail de Marc-Alexandre
- Premier mandat d'exposer des fonctions dans le wrapper pour l'interface utilisateur
- Pourquoi?
    - Permets d'utiliser les méthodes sans briser le code existant
    - Permets une familiarisation en encapsulant la porté du code
    - Permets une entrée en la matière du code C++ et ses utilisations spécifiques comme les pointeurs 
- Travaux liés à la spatialisation, l'adaptation de la TBE et au carbone 

---

## Les futurs projets potentiels

- Diffusion du calcul (MJ)
- Retrait de superficie (MJ)
- Automatisation AWS
- Optimisation Spatialement explicite
  - Opérationalisation du calcul
- Replanification spatialement explicite
  - Permettre d'obtenir un effet de spatialisation à travers la replanification
  - Utilisation d'évènements stochastiques spatiaux
  - Inclure les enjeux opérationels
  - Changements climatique
- Permettre l'optimisation du bilan de carbone


---

## Des Questions?

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

<script src="https://code.jquery.com/jquery-3.5.0.js"></script>
<script>
$( "li" ).addClass( "fragment" );
// $( "p" ).addClass( "fragment" );
</script>


