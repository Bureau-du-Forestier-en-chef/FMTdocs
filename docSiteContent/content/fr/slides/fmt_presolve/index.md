---
title: Le presolve et le postsolve dans FMT
summary: Comprendre le presolve et le postsolve
authors: ["Guillaume Cyr,ing.f.,M.Sc."]
tags: ["FMT","Presolve"]
categories: []
slides:
  # Choose a theme from https://github.com/hakimel/reveal.js#theming
  theme: white
  # Choose a code highlighting style (if highlighting enabled in `params.toml`)
  #   Light style: github. Dark style: dracula (default).
  highlight_style: vs
  diagram: true
---

# Presolve et Postsolve

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

---

## Le processus

<div class=mermaid>
journey
    title Planification sans presolve
    section Avant résolution
      Construction du problème: 1: FMT
    section Résolution
      Interior point: 1: Solveur
      Simplex: 1: Solveur
    section Après Résolution
      Utilisation du modèle: 5: FMT
</div>

---

## Le processus

<div class=mermaid>
journey
    title Planification avec presolve
    section Avant résolution
      Presolve: 5: FMT
      Construction du problème: 5: FMT
    section Résolution
      Interior point: 5: Solveur
      Simplex: 5: Solveur
    section Après Résolution
      Postsolve: 5: FMT
      Utilisation du modèle: 5: FMT
</div>

---

## Le presolve impact

<div class=mermaid>
    stateDiagram
    Presolve
    state Presolve {
      direction LR
      Temps --> Construction
      Temps --> Résolution
      Taille --> Problème
    }
</div>

---

## Comment?

<div class=mermaid>
graph TD;
  1-->2;
  1-->3;
  2-->5;
  2-->9
  2-->8;
  2-->4;
  2-->6;
  2-->7;
  3-->4;
  3-->6;
  3-->7;
  1["Presolve"];
  2["Réduire le nombre"];
  3["Simplifier"];
  4["Thèmes"];
  5["Yields"];
  6["Actions"];
  7["Transitions"];
  8["Contraintes"];
  9["Développements"]
</div>

---

## Thèmes

<div class=mermaid>
flowchart LR
    B{"Un seul attribut?"} -- Oui -->C["Élimine le thème"]
    B -- Non -->D{"Attributs statiques?"}
    D -- Oui -->C
    D -- Non -->E{"Attribut inutile?"}
    subgraph Réduction du nombre d'éléments
    E -- Oui -->F["Éliminer l'attribut"]
    F --> E
    E -- Non -->G["Aggrégat inutile?"]
    G -- Oui -->H["Éliminer l'aggrégat"]
    H --> G
    end
    G -- Non -->I["Nouveau thème"]
</div>

---

## Yields

<div class=mermaid>
flowchart LR
    B{"Contrôle l'opérabilité?"} -- Oui -->C["Conserve le yield"]
    B -- Non -->D{"Masque sur attribut inutile?"}
    D -- Oui -->E["Élimine le yield"]
    D -- Non -->C
</div>

---

## Actions

<div class=mermaid>
flowchart LR
    B{"Aucune opérabilité?"} -- Oui -->C["Élimine l'action"]
    B -- Non -->D{"Ligne d'opérabilité inutile?"}
    subgraph Réduction du nombre d'éléments
    D -- Oui -->E{"Éliminer cette opérabilité"}
    E --> D
    D -- Non -->F{"Opérabilité équivalente?"}
    F -- Oui -->G{"Regroupement des opérabilités"}
    G --> F
    F -- Non -->H["Nouvelle action"]
    end
</div>

---

## Transitions

<div class=mermaid>
flowchart LR
    B{"Aucune action?"} -- Oui -->C["Élimine la transition"]
    B -- Non -->D{"Ligne de source inutile?"}
    subgraph Réduction du nombre d'éléments
    D -- Oui -->E{"Éliminer cette source et ses targets"}
    E --> D
    D -- Non -->F{"Source équivalente?"}
    F -- Oui -->G{"Regroupement des sources"}
    G --> F
    F -- Non -->H["Nouvelle transition"]
    end
</div>

---

## Développements

<div class=mermaid>
flowchart LR
    B{"Développement avec attributs inutilisés?"} -- Oui -->C["Regroupement avec un autre développement"]
    B -- Non -->D["Conserve le développement"]
    C --> B
</div>

---

## Contraintes

<div class=mermaid>
flowchart LR
    B{"RHS de 0?"} -- Oui -->C["Output d'actions?"]
    C -- Oui -->D["Transformer la contrainte en yields et opérabilité d'action et l'éliminer"]
    C -- Non -->E["Output d'inventaire?"]
    E -- Oui -->D
    E -- Non -->F["Conserver la contrainte"]
</div>

---

## Postsolve

<div class=mermaid>
graph TD;
  1-->2;
  1-->3;
  3-->4;
  3-->5;
  3-->6;
  3-->7;
  3-->8;
  1["Postsolve"];
  2["Réajustement du Graph"];
  3["Réattribution"];
  4["Thèmes"];
  5["Actions"];
  6["Transitions"];
  7["Outputs"];
  8["Contraintes"];
</div>

---

## Exemple

{{< figure src="presolveex.jpg" width="auto" height="auto" >}}
- Le présolve est réalisé sur le scénario nommé Globalreplanning
  - 10 itérations de presolve sont réalisées
  - Le modèle possède 11299 développements, l’original (506 de plus)
  - Le modèle possède 13 thèmes, l’original (7 de plus)
  - Le modèle possède 276 yields, l’original (89 de plus)
  - Le modèle possède 14 actions, l’original (19 de plus)
  - Le modèle possède 14 transitions, l’original (19 de plus)
  - Le modèle possède 1103 outputs, l’original (1074 de plus)
  - Le modèle possède 130 contraintes, l’original (9 de plus)

---

## Des Questions?

{{< figure src="LogoFMT.png" width="40%" height="auto" >}}

[Github](https://github.com/Bureau-du-Forestier-en-chef/FMT) | [Documentation](https://bureau-du-forestier-en-chef.github.io/FMTdocs/)

<script src="https://code.jquery.com/jquery-3.5.0.js"></script>
<script>
$( "li" ).addClass( "fragment" );
// $( "p" ).addClass( "fragment" );
</script>


