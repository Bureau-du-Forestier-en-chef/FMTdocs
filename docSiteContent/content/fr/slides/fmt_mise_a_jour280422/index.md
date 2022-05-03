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

# FMT où en somme nous?

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

---

## C'est quoi FMT?

- F: Forest
- M: Management
- T: Tool


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
  6["Re-planification"];
  7["Base de données"];
  8["BFEC-GCBM"];
  9["Planification"];
  10["Spatialisation"];
  11["BFECOPT"];
  12["Analyses Diverses"];
  13["Autres"];
  14["Tests"];
  15["Machine Learning"];
</div>

---

## Comment on test FMT?

<div class=mermaid>
pie title Les tests de FMT
  "Syntaxe" : 3.5
  "Unités spatiale" : 5.3
  "Planification" : 61.4
  "Re-planification" : 5.3
  "Spatialisation" : 7.0
  "Machine Learning" : 7.0
  "Presolve" : 3.5
  "Analyses autres" : 7.0
</div>

---

## L'évolution des versions

<div class=mermaid>
flowchart LR;
1 --> 6;
6 --> 7;
7 --> 8;
8 --> 9;
1 --> 11 --> 6;
1 --> 2 --> 6;
2 --> 7;
2 --> 8;
2 --> 9;
1 --> 3 --> 6;
3 --> 7;
3 --> 8;
3 --> 9;
1 --> 4 --> 9;
1 --> 10 --> 8;
10 --> 9;
1 --> 5 --> 9;
1["Master"]
2["Re-planification"]
3["BFEcopt"]
4["Machine Learning"]
5["type II"]
6["0.9.1"]
7["0.9.2"]
8["0.9.3"]
9["0.9.4 en développement"]
10["Presolve"]
11["Autres"]
</div>

---

## Les nouveautés 0.9.4

- Presolve
  - Réduit la taille du modèle à un modèle équivalent
  - Diminue la vitesse de construction
  - Diminue la vitesse de résolution
- Type II
  - Diminue la taille de la matrice
  - Diminue la vitesse de construction et de résolution
  - Diminue l'utilisation de la mémoire de l'ordinateur par le solveur
- Syntaxe carbone
  - Permet d'intégrer les réservoirs et flux de carbone à même les yields
  - Permet d'obtenir un bilan de carbone spatialement référencé et spatialement explicit

---

## [La Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)
<div class=mermaid>
    stateDiagram
    Code --> Doxygen(78%)
    Doxygen(78%) --> Présentations
    state Doxygen(78%) {
      direction LR
      C++ --> Python
      C++ --> R
    }
    state Présentations {
      Formation --> Replanification
      Formation --> ClubFMT
      Formation --> Carbone
      Autres
    }
    Doxygen(78%) --> GitHub
    state GitHub {
      Fonctionnement
      Installation
      MachineLearning
      Spatialisation
    }
</div>


---

## Documentation à venir

- Github:
  - Re-plannification
  - Syntaxe:
    - Spatialisation
    - Carbone (Yields)
    - Re-plannification
- Continuer à documenter les fonctions (78%)

---

## Les travaux

<div class=mermaid>
gantt
dateFormat  YYYY-MM-DD
title Les travaux dans FMT
excludes weekends
section Interface
Programmation          :done,des1,2021-12-15,2022-03-07 
Test Phase 1           :     des2,2022-02-25,2022-03-11
Test Phase 2           :     des3,2022-03-16,2022-03-30
Test Phase 3           :active,des4,2022-05-03,2022-05-16
Corrections            :     des5, 2022-03-11,2022-03-20
Parallélisation BFEcopt/Planification           :     des6, 2022-03-11,2022-04-28
Mise à jour de la cache :     des8, 2022-04-20,2022-05-20
Migration du code          :     crit,des9, 2022-05-02,2022-05-09
Ajout playback et parallisation :     des10, 2022-05-20,2022-06-03
Ajout de BFECopt       :     des11, 2022-06-03,2022-06-09
Ajout de la re-planification :des12, 2022-06-09,2022-06-30
section Formations
ClubFMThé            :done,des1,2021-12-15,2022-02-30
Formation Re-planification :active,des2,2022-03-22,2022-06-15
section Machine Learning
Phase 1.2 (Développement de syntaxe carbone)            :done,des1,2021-12-15,2022-04-15
Mise en place d'une présentation                        :     des2,2022-02-15,2022-04-08
Phase 2.1 (Spatialisation de la récolte)               :active,des3,2021-12-15,2022-06-30
section Générale
Presolve                                                :done,des1,2021-12-15,2022-04-01
Migration à Visual Studio 2022                          :crit,active,des2,2022-04-15,2022-05-20
Débogage                                                :       des3,2021-12-15,2022-06-30

</div>

---

## Les futurs projets potentiels

- Re-planification spatialement explicite
  - Permettre d'obtenir un effet de spatialisation à travers la re-planification
  - Utilisation d'évènements stochastique spatiaux
  - Inclure les enjeux opérationel
  - Changement climatique
- Type I
  - Corriger des comportements indésirable des CPF (existant depuis des années)
  - Respect des stratégiques de A à Z par le solveur
- Lien avec SELES
  - Passer les hypothèses de nos modèles à SELES
- Permettre l'optimisation du bilan de carbone
- Explorer les modèles de prédiction qu'on pourrait intégrer directement
  - Biomasse
  - Facteur de productivité en fonction du scénario Rcp

---

## Des Questions?

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

<script src="https://code.jquery.com/jquery-3.5.0.js"></script>
<script>
$( "li" ).addClass( "fragment" );
// $( "p" ).addClass( "fragment" );
</script>


