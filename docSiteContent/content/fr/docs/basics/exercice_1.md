---
title: Exercice 1
linktitle: Exercice 1
type: book
date: "2019-05-05T00:00:00+01:00"
# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 4
---

Dans cet exercice, vous allez essayer d'utiliser FMT pour lire un scénario, le résoudre, lire une de ses sorties, et exporter la solution.

Pour faire cet exercice, **vous devez avoir installé FMT sur votre ordinateur et avoir téléchargé les fichiers d'exemples depuis le dépôt GitHub de FMT**.

{{< cta cta_text="💾 Télécharger et installer FMT" cta_link="../../download_install" cta_new_tab="true" >}}

{{< cta cta_text="💾 Télécharger les fichiers d'exemples de FMT" cta_link="https://downgit.github.io/#/home?url=https://github.com/gcyr/FMT/tree/master/Examples" cta_new_tab="true" >}}

## Objectifs de l'exercice

Utilisez FMT pour :

1. Lire le modèle Woodstock `TWD_land` dans les fichiers d'exemples en sélectionnant son scénario nommé `LP`.
	- N'oubliez pas de sélectionner le solveur `CLP` lors de la lecture du modèle.
2. Résolvez le modèle en
	- Construisant son graphe complet pour 10 périodes
	- Définissant l'objectif et les contraintes du modèle
	- Utilisant la fonction `FMTModel.initialSolve()` pour le résoudre.
3. Lire les valeurs de la sortie `OSUPREC` du modèle en
	- Obtenant une liste des sorties du modèle avec la fonction `FMTModel.getOuputs()`.
	- Effectuant une boucle `for` pour sélectionner la bonne sortie avec la fonction `FMToutput.getName()`.
	- Imprimer les valeurs de la sortie avec la fonction `FMTModel.getVariabilities(FMToutput)`.
4. Exportez le calendrier optimisé du modèle dans un fichier `.txt` en
	- Créant un nouvel objet `FMTScheduleParser.`.
	- Récupérant la solution du modèle à chaque période avec une liste en utilisant `LPModel.getSolution(period)`.
	- Transformant la liste en un vecteur
	- Écriture du vecteur dans un fichier `.txt` avec `FMTScheduleParser.write()`.

**Vous pouvez utiliser les informations des pages précédentes de la documentation pour vous aider.**

Lorsque vous avez terminé, ou si vous êtes bloqué.e, **vous pouvez afficher la correction de cet exercice pour du code Python en cliquant sur le bouton "Révéler la correction" ci-dessous**.

## Correction

{{< spoiler text="Révéler la correction" >}}

```python
#%% CHARGEMENT DE FMT

# Ici, on charge FMT directement, comme s'il avait été installé avec pip.

from FMT import Models
from FMT import Parser
from FMT import Version

#%% CHARGEMENT DU MODÈLE WOODSTOCK

# Création d'un objet pour lire le modèle (parser)
modelParser = Parser.FMTmodelparser()

# Definition du chemin menant au modèle Woodstock
# Le modèle Woodstock est fait de pleins de petits fichiers différents
# On pointe vers le fichier .pri, qui contient la location de tous les petits fichiers du modèle.
# WARNING : FMT a du mal avec les espaces dans les chemins. Essayez d'avoir un
# chemin sans espaces.
pathToWoodstockModel = "D:/TempCode/FMT/FMT/Examples/Models/TWD_land/TWD_land.pri"

# On définit le/les scénarios que l'on va lire
# Les scénarios sont des ensembles de contraites et objectifs,
# ou bien un planning déjà fait que l'on va simuler.
scenariosToSelect = ["LP"]

# On lit le model grâce au parser. Celui-ci va renvoyer une liste de modèles
# qu'il a détécté. Dans notre cas, on n'en aura qu'un seul.
listOfModelsParsed = modelParser.readproject(pathToWoodstockModel, scenariosToSelect)

# On charge le modèle parmis la liste des modèles renvoyés
# Pour le charger correctement, il faut décrire le type de modèle que c'est, ainsi que des paramètres importants.
# Ici, le modèle sera un modèle linéraire spatialement référencé (LP),
# et on utilisera le solveur CLP pour le résoudre.
LPModel = Models.FMTlpmodel(listOfModelsParsed[0], Models.FMTsolverinterface.CLP)

#%% RÉSOLUTION DU MODÈLE

# Construction des périodes du modèle
for period in range(1, 11):
    print(LPModel.buildperiod())

# Récupération des contraintes et de l'objectif
constraintsOfModel = LPModel.getconstraints()
objectiveOfModel = constraintsOfModel.pop(0)

# Définition des contraintes et de l'objectif
for constraint in constraintsOfModel:
    print(LPModel.setconstraint(constraint))
print(LPModel.setobjective(objectiveOfModel))

# Résolution du modèle
LPModel.initialsolve()

#%% INTERROGATION DE OSUPREC

# On peut lire OSUPREC avant que le modèle soit résolu, mais les valeurs ne sont pas intéréssantes si c'est le cas.
outputWanted = "OSUPREC"

# On récupère les sorties du modèle
outputsOfModel = LPModel.getoutputs()
# On fait une boucle autour de la liste des sorties pour selecitonner celle qui a le bon nom
for output in outputsOfModel:
    print(output.getname())
    if output.getname() == outputWanted:
    	# On affiche les mesures de "OSUPREC" dans le terminal
        print(LPModel.getvariabilities([output]))
        # On sauvegarde les valeurs de OSUPREC pour les mettre au propre ensuite
        osuprecValues = LPModel.getvariabilities([output])
  
# Ici, c'est un petit code qui permet d'afficher les sorties
# en s'adaptant au fait que getvariabilities() renvoie leur valeur
# minimum, maximum et moyenne  
print("\n\n ###############################")
print("Values of " + outputWanted + " :\n")
print("PERIOD\t\tMINIMUM\t\tMEAN\t\tMAXIMUM")
for period in range(1, 11):
    print(str(period) + "\t\t\t" + str(round(osuprecValues["L"+outputWanted][period-1], 2))
          + "\t\t" + str(round(osuprecValues["M"+outputWanted][period-1], 2))
          + "\t\t" + str(round(osuprecValues["U"+outputWanted][period-1], 2)))

#%% ECRITURE DU PLANNING (cedule)

# On déclare un objet permettant d'écrire le programme/planning/cédule
scheduleParser = Parser.FMTscheduleparser()

# On récupère la solution du modèle
modelSolution = list()
for period in range(1, 11):
    modelSolution.append(LPModel.getsolution(period))
    
# On écrit le programme avec le scheduleParser
modelSolutionAsVector = [[i] for i in modelSolution]
scheduleParser.write(modelSolution, r"D:/TempCode/FMT/FMT/Examples/programme.txt")
```

{{< /spoiler >}}