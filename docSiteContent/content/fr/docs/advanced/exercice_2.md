---
title: Exercice 2
linktitle: Exercice 2
type: book
date: "2019-05-05T00:00:00+01:00"
# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 2
---

Dans cet exercice, vous allez essayer d'utiliser FMT pour répondre à différentes questions concernant un modèle stratégique et un modèle tactique, par le biais d'un processus de replanification.

Ainsi, cet exercice vous apprendra à :

- [x] Réaliser un script de replanification
- [x] Faire une replanification avec plusieurs modèles, en variant [les mots clés spéciaux de FMT](../replanning/#les-nouveaux-mots-clés-associés-à-la-re-planification)
- [x] Analyser les résultats des replanifications pour répondre à différentes questions

Pour faire cet exercice, **vous devez avoir installé FMT sur votre ordinateur et avoir téléchargé les fichiers d'exemple depuis le dépôt GitHub de FMT**.

{{< cta cta_text="💾 Télécharger et installer FMT" cta_link="../../download_install" cta_new_tab="true" >}}

{{< cta cta_text="💾 Télécharger les fichiers d'exemples de FMT" cta_link="https://downgit.github.io/#/home?url=https://github.com/gcyr/FMT/tree/master/Examples" cta_new_tab="true" >}}

**Vous devez également avoir installé Python sur votre ordinateur**. Si cela n'est pas fait, [référez-vous à cette page](../../download_install/importing_in_python/).

## 1ère partie : Création d'un script de replanification

{{% callout note %}}
Si vous ne connaissez pas beaucoup le langage Python, ou bien si vous n'avez vraiment pas le temps, vous pouvez sauter cette section et utiliser le script `Replanner.py` qui se trouve dans le dossier "Examples" que vous avez téléchargé, dans le dossier `/Python/replanning`.
{{% /callout %}}

Le processus de replanification implique souvent de lancer de nombreux modèles, et de nombreuses fois (pour faire des itérations).

Il est ainsi très utile de creer un script Python qui peut être appellé par le biais d'une console, d'un terminal, ou d'une invite de commande.

Encore mieux : il est possible de créer un script Python qui lira des arguments qui lui seront fournis lorsqu'il sera appelé avec l'invite de commande. Cela permettra de réutiliser ce script pour différentes replanifications.

Essayez alors de créer en langage Python le script suivant :

- Importation de `Models`, `Parser`, `Core`, `Version` et `Parallel` depuis `FMT`
- Lecture des arguments suivants (il est recommandé d'utiliser le module [`argparse`](https://docs.python.org/3/library/argparse.html))
	- `-pri` qui indique la localisation du fichier primaire des modèles qui seront utilisés
	- `-str` qui indique le nom du scénario stratégique
	- `-tac` qui indique le nom du scénario tactique
	- `-sto` qui indique le nom du scénario stochastique
	- `-out` qui indique une liste d'outputs d'intéret, séparés par une virgule, à extraire des résultats des modèles
	- `-fol` qui indique le dossier où les sorties du script seront mises
	- `-rep` qui indique le nombre d'itérations à faire
	- `-len` qui indique le nombre de périodes du modèle stratégique
	- `-rel` qui indique le nombre de périodes sur lesquelles la replanification va se faire
	- `-thr` qui indique le nombre de coeurs de CPU à utiliser pour faire des itérations en parralèle

Ensuite, le script devra :

- Lire le modèle via le fichier primaire
- Lire les différents scénarios
- Créer le modèle stratégique/global en utilisant le nom de scénario fournis par `-str`
	- Faire en sorte que le modèle global se fasse sur la durée fournie par `-len`
	- Faire en sorte que le modèle global utilise le nombre de threads indiqués par `-thr` pour la résolution en parallèle
- Créer le modèle stochastique en utilisant le nom de scénario fournis par `-sto`
	- Faire en sorte que le modèle stochastique se fasse sur la durée de une période
- Créer le modèle tactique en utilisant le nom de scénario fournis par `-tac`
	- Faire en sorte que le modèle tactique se fasse sur la durée de une période
	- Faire en sorte que le modèle global utilise le nombre de threads indiqués par `-thr` pour la résolution en parallèle
- Ajouter les outputs selectionnés par l'argument `-out` dans une liste depuis les outputs des modèles
- Préparer la tâche de replanning par le biais de la fonction `Parallel.FMTreplanningtask()` en fournissant à la fonction :
	- Le modèle stratégique
	- Le modèle stochastique
	- Le modèle tactique
	- La liste des outputs voulus
	- Le dossier où les outputs seront mis
	- Le format des outputs (ici, `.csv`)
	- [Tous les autres arguments nécéssaires à la fonction](../../../../doxygen/html/classParallel_1_1FMTreplanningtask.html)
- Lancer la tâche de replanning par le biais de la fonction `Parallel.FMTtaskhandler()`

Lorsque vous avez terminé, ou si vous êtes bloqué.e, **vous pouvez afficher la correction de cet exercice pour du code Python en cliquant sur le bouton "Révéler la correction" ci-dessous**.

{{< spoiler text="Révéler la correction" >}}
```python
from FMT import Models
from FMT import Parser
from FMT import Core
from FMT import Version
from FMT import Parallel
import os,argparse


if __name__ == "__main__":
    ap = argparse.ArgumentParser()
    ap.add_argument("-pri", "--primary", required=True,help="Fichier primaire")
    ap.add_argument("-str", "--strategic", required=True,help="Nom du scenario strategique")
    ap.add_argument("-tac", "--tactic", required=True,help="Nom du scenario tactique")
    ap.add_argument("-sto", "--stochastic", required=True,help="Nom du scenario stochastique")
    ap.add_argument("-out", "--outputs", required=True,help="Liste d'outputs separe d'une virgule")
    ap.add_argument("-fol", "--folder", required=True,help="Dossier de sortie")
    ap.add_argument("-rep", "--replicates", required=True,help="Nombre de replicas")
    ap.add_argument("-len", "--strategiclength", required=True,help="Nombre de periode du strategique")
    ap.add_argument("-rel", "--replanninglength", required=True,help="Nombre de periode de re-plannification")
    ap.add_argument("-thr", "--threads", required=True,help="Nombre de threads")
    ap.add_argument("-com","--compresstime", nargs="+",help="Compress time sous la forme 20,5 40,8")
    args = vars(ap.parse_args())
    if Version.FMTversion().hasfeature("OSI"):
        modelparser = Parser.FMTmodelparser()
        path = args["primary"]
        scenarios = [args["strategic"],args["stochastic"],args["tactic"]]
        models = modelparser.readproject(path, scenarios)
        globalmodel = Models.FMTlpmodel(models[0], Models.FMTsolverinterface.CLP)
        globalmodel.setparameter(Models.FMTintmodelparameters.LENGTH,int(args["strategiclength"]))
        globalmodel.setparameter(Models.FMTintmodelparameters.NUMBER_OF_THREADS,1)
        if "com" in args:
            for firstperiod,value in [tuple(int(cmpt.split(",")[0]),float(cmpt.split(",")[1])) for cmpt in args["com"]]:
                globalmodel.setcompresstime(firstperiod,int(args["strategiclength"]),value)
        stochastic = Models.FMTnssmodel(models[1],0)
        stochastic.setparameter(Models.FMTintmodelparameters.LENGTH, 1)
        localmodel = Models.FMTlpmodel(models[2], Models.FMTsolverinterface.CLP)
        localmodel.setparameter(Models.FMTintmodelparameters.LENGTH, 1)
        localmodel.setparameter(Models.FMTintmodelparameters.NUMBER_OF_THREADS,1)
        selectedoutputs=[]
        for output in globalmodel.getoutputs():
            if output.getname() in args["outputs"].split(","):
                selectedoutputs.append(output)
        outputlocation = args["folder"]
        layersoptions=["SEPARATOR=SEMICOLON"]
        replanningtask=Parallel.FMTreplanningtask(globalmodel,stochastic, localmodel, selectedoutputs, outputlocation, "CSV", layersoptions,int(args["replicates"]),int(args["replanninglength"]),0.5, Core.FMToutputlevel.totalonly)
        handler = Parallel.FMTtaskhandler(replanningtask,int(args["threads"]))
        handler.setquietlogger()
        handler.conccurentrun()
    else:
        print("FMT needs to be compiled with OSI")
```
{{< /spoiler >}}

## 2ème partie : Création des scénarios pour la replanification

### Préparation

Après avoir téléchargé les fichiers d'exemples de FMT (voir plus haut), ouvrez le dossier `Models/TWD_land/Scenarios/Globalex1`.

Ce scénario `Globalex1` est un scénario que nous utiliserons comme [modèle stratégique ou modèle global](../replanning#le-modèle-stratégique).

**Prenez le temps d'observer le contenu de ses fichiers**; en particulier, jetez un oeil à ses actions, ses objectifs et ses contraintes.

**Faites de même avec le modèle `Localex1`**, que nous utiliserons comme [modèle tactique ou modèle local](../replanning#le-modèle-tactique).

### Question 1

Ouvrez ensuite une invite de commande de Windows, ou bien un Powershell. Utilisez la commande `cd` pour vous rendre dans le dossier `Examples/Python/replanning`. Puis, utilisez la commande suivante pour lancer la replannification :

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex1 -tac Localex1 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT -fol replanning_EX1 -rep 10 -len 20 -rel 20 -thr 2
```

Cette commande indique au script créé dans la première partie que nous voulons :

- Utiliser le modèle qui se trouve au chemin `../../Models/TWD_land/TWD_land.pri`
- Utiliser le scénario `Globalex1` comme modèle stratégique
- Utiliser le scénario `Localex1` comme modèle tactique
- Utiliser le scénario `Globalnofire` comme modèle stocastique
- Créer des tableaux de sorties pour les outputs `OVOLREC`, `OSUPREC`, `OSUPPL`, et `VOLINVENT`
- Mettre ces tableaux dans le dossier `replanning_EX1`
- Faire 10 itérations
- Solutionner le modèle stratégique sur 20 périodes
- Faire la replannification sur 20 périodes
- Utiliser 2 coeurs de CPU pour faire des itérations en parallèle

Une fois que la commande s'est éxécutée, regardez les sorties présentes dans le dossier `Examples/Python/replanning/replanning_EX1`.

**Avec une tolérance à la rupture de stock de 5%, quelle est la probabilité de maintenir `OVOLREC` jusqu’à la période 10 dans le modèle tactique ?**

{{< spoiler text="Révéler la correction" >}}
Pour regarder la probabilité de maintenir `OVOLREC` au-dessus d'une baisse de 5%, il suffit de regarder le fichier `outputsdrift.csv`. Celui-ci nous indique la probabilité que les différents outputs sélectionnés restent, dans le modèle tactique, au-dessus ou en-dessous d'un certain éloignement vis-à-vis du modèle stratégique.

On peut donc ouvrir le fichier, et se rendre jusqu'aux lignes qui concernent `OVOLREC` et un drift de `0.05` (5%), à savoir les lignes 462-481.

En regardant ces lignes, on peut voir qu'à partir de la période 7, `OVOLREC` a une probabilité de `0` de ne pas baisser de plus de 5% par rapport au modèle stratégique (troisième colonne `LowerProbability`); cette probabilité reste à 0 pour toutes les périodes suivantes. Ainsi, il semble donc que la probabilité de maintenir `OVOLREC` au-dessus d'une baisse de 5% jusqu'à la période 10 est de 0.

On peut représenter ces valeurs avec un graphe via les données de `outputsdrift.csv` :

{{< figure src="docs/grapheExercice2_replanning_Exercice1.png" >}}

{{< /spoiler >}}

### Question 2

À présent, dupliquez le dossier `Globalex1` et renommez-le `Globalex2` (supprimez le dossier existant s'il existe déjà).

**Modifiez ensuite le fichier `TWD_land.opt` de `Globalex2` afin de forcer le scénario `Globalex2` à ne pas dépasser 20 000 m³ pour l'output `OVOLREC`**.

{{< spoiler text="Révéler la correction" >}}
Le contenu de `TWD_land.opt` du scénario `Globalex2` doit alors être :

```

*OBJECTIVE
_MAXMIN oVolRec 1.._LENGTH

*EXCLUDE
aCaribou 1.._LENGTH
afire    1.._LENGTH
arecup    1.._LENGTH

*CONSTRAINTS

oVolRec<= 20000 1.._LENGTH

_NDY(oVolRec) 1.._LENGTH


*FORMAT MOSEK

```
{{< /spoiler >}}

Lancez ensuite une replannification avec le modèle stratégique `Globalex2` et le modèle tactique `Localex1`, avec la commande suivante :

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex2 -tac Localex1 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT -fol replanning_EX2 -rep 10 -len 20 -rel 20 -thr 2
```

Une fois que la commande s'est éxécutée, regardez les sorties présentes dans le dossier `Examples/Python/replanning/replanning_EX2`.

**Avec une tolérance à la rupture de stock de 5%, quelle est la probabilité de maintenir `OVOLREC` jusqu’à la période 10 dans le modèle tactique ?**

**Y-as-t-il un gain vis-à-vis de la question 1 ?**

{{< spoiler text="Révéler la correction" >}}
En regardant les résultats de `outputsdrift.csv`, on peut voir que `OVOLREC` garde une probabilité de `1` de ne pas diminuer de plus de 5% tout du long des 20 périodes. Ainsi, il y a une probabilité maximale que `OVOLREC` se maintiennent au-dessus de 5% de baisse dans le modèle tactique `Localex1`, lorsque l'on utilise le modèle stratégique `Globalex2`.

On a donc un gain clair vis-à-vis de la question 1, pour laquelle `OVOLREC` ne pouvait pas se maintenir au-dessus d'une baisse de 5% à partir de la 7ième période. Cela est surement dû au fait que l'on a forcé `Globalex2` à ne pas récolter trop, ce qui a rendu le fait de maintenir ce niveau dans `Localex1` plus facile.
{{< /spoiler >}}

### Question 3

À présent, dupliquez le dossier `Localex1` et renommez-le `Localex3` (supprimez le dossier existant s'il existe déjà).

**Modifiez ensuite le fichier `TWD_land.opt` de `Localex3` afin de forcer ce modèle tactique à suivre la solution du modèle stratégique avec un poids de 100, avec le mot clé [`_SETGLOBALSCHEDULE(poids)`](../replanning/#_setglobalscheduleratio)**.

{{< spoiler text="Révéler la correction" >}}
Le contenu de `TWD_land.opt` du scénario `Localex3` doit alors être :

```

*OBJECTIVE
_MAX ovolrec 1.._LENGTH _SETGLOBALSCHEDULE(100)

*EXCLUDE
aCaribou 1.._LENGTH
afire    1.._LENGTH
arecup    1.._LENGTH

*CONSTRAINTS

oVolRec<= 40272 1.._LENGTH _SETFROMGLOBAL(1)


*FORMAT MOSEK

```
{{< /spoiler >}}

Lancez ensuite une replannification avec le modèle stratégique `Globalex1` et le modèle tactique `Localex3`, avec la commande suivante :

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex1 -tac Localex3 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT -fol replanning_EX3 -rep 10 -len 20 -rel 20 -thr 2
```

Une fois que la commande s'est éxécutée, regardez les sorties présentes dans le dossier `Examples/Python/replanning/replanning_EX3`.

**Avec une tolérance à la rupture de stock de 5%, quelle est la probabilité de maintenir `OVOLREC` jusqu’à la période 10 dans le modèle tactique ?**

**Y-as-t-il un gain vis-à-vis de la question 1 ?**

{{< spoiler text="Révéler la correction" >}}
En regardant les résultats de `outputsdrift.csv` dans `Examples/Python/replanning/replanning_EX3`, on peut voir que `OVOLREC` a une probabilité de `1` de ne pas diminuer de plus de 5% par rapport au modèle stratégique à la période 8; mais que cette probabilité passe à `0` à partir de la période 9, et reste à `0` pendant toutes les autres périodes. Ainsi, il est certain que `OVOLREC` ne se maintiendra pas au-dessus d'une baisse de 5% jusqu'a la période 10.

Cependant, on peut quand même observer un léger gain par rapport à la question 1; la probabilité que `OVOLREC` ne diminue pas de plus de 5% descendait à `0` dès la période 7 dans ces résultats. En poussant le modèle tactique à suivre la solution du modèle stratégique avec le mot clé `_SETGLOBALSCHEDULE(poids)`, on a donc gagné deux périodes de stabilité en plus !
{{< /spoiler >}}

### Question 4

À présent, dupliquez le fichier `TWD_land._opt` de `Localex1` dans le dossier `Localex4` (supprimez le fichier existant s'il existe déjà).

**Modifiez ensuite le fichier `TWD_land.opt` de `Localex4` afin de forcer le modèle tactique à suivre les tables de réplicas (`PEUPLEMENT1.csv` et `PEUPLEMENT2.csv`) pour la récolte dans les types de peuplement1 et peuplement2 (utilisez [`_REPLICATE()`](../replanning/#_replicateoutput))**. En ce faisant, vous devez donc forcer le modèle tactique à faire un certain niveau de récolte dans des entités spatiales particulières du modèle (les peuplements 1 et 2) en vous basant sur des fichiers que vous fournissez au modèle (`PEUPLEMENT1.csv` et `PEUPLEMENT2.csv`).

{{< spoiler text="Révéler la correction" >}}
Le contenu de `TWD_land.opt` du scénario `Localex4` doit alors être :

```

*OBJECTIVE
_MAX ovolrec - _Penalty(_All) 1.._LENGTH

*EXCLUDE
aCaribou 1.._LENGTH
afire    1.._LENGTH
arecup    1.._LENGTH

*CONSTRAINTS

oVolRec<= 40272 1.._LENGTH _SETFROMGLOBAL(1)

oVolRecPEUPLEMENT1 <= 0 1.._LENGTH _Goal(G1,9999) _REPLICATE(PEUPLEMENT1.csv)
oVolRecPEUPLEMENT1 >= 0 1.._LENGTH _Goal(G2,9999) _REPLICATE(PEUPLEMENT1.csv)
oVolRecPEUPLEMENT2 <= 0 1.._LENGTH _Goal(G3,9999) _REPLICATE(PEUPLEMENT2.csv)
oVolRecPEUPLEMENT2 >= 0 1.._LENGTH _Goal(G4,9999) _REPLICATE(PEUPLEMENT2.csv)

*FORMAT MOSEK

```
{{< /spoiler >}}

Lancez ensuite une replannification avec le modèle stratégique `Globalex1` et le modèle tactique `Localex4`, avec la commande suivante :

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex1 -tac Localex4 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT,OVOLRECPEUPLEMENT1,OVOLRECPEUPLEMENT2 -fol replanning_EX4 -rep 10 -len 20 -rel 20 -thr 2
```

Une fois que la commande s'est éxécutée, **rendez vous dans le fichier `Localex4.csv` du dossier `replanning_EX4`, et observez les résultats des outputs `OVOLRECPEUPLEMENT1` et `OVOLRECPEUPLEMENT2`**. En particulier, vous pouvez creer une table pivot dans un logiciel comme Microsoft Excel afin d'obtenir un graphe montrant l'évolution de ces mesures à travers les périodes de temps, avec une courbe pour chacune des itérations.

En même temps, ouvrez les fichiers `PEUPLEMENT1.csv` et `PEUPLEMENT2.csv`, et observez leur contenu. Chaque colonne correspond à des valeurs que le modèle tactique devait suivre pour une itération, avec chaque ligne contenant une valeur pour une période donnée.

**En comparant le contenu de `Localex4.csv` et de `PEUPLEMENT1.csv` et `PEUPLEMENT2.csv`, qu'observez vous ?**

{{< spoiler text="Révéler la correction" >}}
On peut voir que le modèle tactique à en effet suivi les niveaux de récolte définit dans les fichiers `PEUPLEMENT1.csv` et `PEUPLEMENT2.csv` pour l'output `OVOLREC` dans les peuplements 1 et deux.

En particulier, on peut voir que `OVOLRECPEUPLEMENT1` descend à 0 pour la période 4 dans toutes les itérations, comme prévu dans `PEUPLEMENT1.csv`. On peut aussi voir que l'itération 4 garde une valeur de `OVOLRECPEUPLEMENT1` à 10 la plupart du temps, alors que les autres itérations gardent une valeur de 20.

{{< figure src="docs/grapheExercice2_replanning_Exercice4.png" >}}


On peut observer une baisse à 0 pour `OVOLRECPEUPLEMENT2` à la période 9 dans toutes les itérations, comme prévu dans `PEUPLEMENT2.csv`.

{{< figure src="docs/grapheExercice2_replanning_Exercice4-2.png" >}}
{{< /spoiler >}}

### Question 5

À présent, dupliquez le dossier `Localex1` et renommez-le `Localex5` (supprimez le dossier existant s'il existe déjà).

**Modifiez ensuite le fichier `TWD_land.opt` de `Localex5` afin de forcer ce modèle tactique à ignorer la contrainte de plantation de 90 à 100ha à partir de la période (ou bien de ne la suivre que durant la période 1) en utilisant le mot clé [`_REIGNORE(période)`](../replanning/#_reignoreperiod)**.

{{< spoiler text="Révéler la correction" >}}
Le contenu de `TWD_land.opt` du scénario `Localex5` doit alors être :

```

*OBJECTIVE
_MAX ovolrec - _Penalty(_All) 1.._LENGTH

*EXCLUDE
aCaribou 1.._LENGTH
afire    1.._LENGTH
arecup    1.._LENGTH

*CONSTRAINTS

oVolRec<= 40272 1.._LENGTH _SETFROMGLOBAL(1)

osuppl >= 90 1 _Goal(G1,9999) _REIGNORE(2)
osuppl <= 100 1 _Goal(G2,9999) _REIGNORE(2)

*FORMAT MOSEK
```
{{< /spoiler >}}

Lancez ensuite une replannification avec le modèle stratégique `Globalex1` et le modèle tactique `Localex5`, avec la commande suivante :

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex1 -tac Localex5 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT -fol replanning_EX5 -rep 10 -len 20 -rel 20 -thr 2
```

Une fois que la commande s'est éxécutée, **comparez l'évolution de l'output du niveau de plantations `OSUPPL` dans le modèle tactique `Localex1` (dans le dossier `replanning_EX1` créé à la question 1) à l'évolution de `OSUPPL` dans le modèle tactique `Localex5` (dans le dossier `replanning_EX5`)**. Comme précédemment, vous pouvez utiliser une table pivot dans un logiciel comme Microsoft Excel afin d'obtenir un graphe montrant l'évolution de ces mesures à travers les périodes de temps, avec une courbe pour chacune des itérations.

**Quelles différences observez-vous entre `Localex1` et `Localex5` ? ?**

{{< spoiler text="Révéler la correction" >}}
On peut voir que le modèle tactique `Localex1` arrive à soutenir le niveau de `OSUPPL` pendant un long moment de manière stable, comme montré sur ce graphe :


{{< figure src="docs/grapheExercice2_replanning_Exercice5.png" >}}


En comparaison, dans `Localex5`, on peut voir que le niveau de `OSUPPL` est beaucoup plus variable et instable. Il atteind rapidement des valeurs de 0, à cause du fait que les contraintes du modèle le concernant sont ignorées après la période 2, comme montré sur ce graphe :

{{< figure src="docs/grapheExercice2_replanning_Exercice5-2.png" >}}

Ainsi, il semble que le fait d'ignorer les contraintes concernant `OSUPPL` à bien fonctionné.
{{< /spoiler >}}

### Question 6

À présent, dupliquez le dossier `Localex1` et renommez-le `Localex6` (supprimez le dossier existant s'il existe déjà).

**Modifiez ensuite le fichier `TWD_land.opt` de `Localex6` afin de forcer ce modèle tactique à réaliser de 100% du niveau de récolte (`OVOLREC`) du modèle stratégique, avec une variabilité de 1% autorisée pour le dépassement de la valeur (donc au moins 100%, et au plus 101%) avec le mot clé [`_SETFROMGLOBAL(poids)`](../replanning/#_setfromglobalratio)**.

{{< spoiler text="Révéler la correction" >}}
Le contenu de `TWD_land.opt` du scénario `Localex6` doit alors être :

```

*OBJECTIVE
_MAX ovolrec - _Penalty(_All) 1.._LENGTH

*EXCLUDE
aCaribou 1.._LENGTH
afire    1.._LENGTH
arecup    1.._LENGTH

*CONSTRAINTS

oVolRec<= 40272 1.._LENGTH _SETFROMGLOBAL(1)

osuprec <= 40272 1.._LENGTH _Goal(G1,9999) _SETFROMGLOBAL(1.0)
osuprec >= 40272 1.._LENGTH _Goal(G2,9999) _SETFROMGLOBAL(0.99)

*FORMAT MOSEK

```
{{< /spoiler >}}

Lancez ensuite une replannification avec le modèle stratégique `Globalex1` et le modèle tactique `Localex6`, avec la commande suivante :

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex1 -tac Localex6 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT -fol replanning_EX6 -rep 10 -len 20 -rel 20 -thr 2
```

Une fois que la commande s'est éxécutée, regardez les sorties présentes dans le dossier `Examples/Python/replanning/replanning_EX6`.

**Comparez à présent la probabilité de maintenir une récolte similaire à l'exercice 1 (avec `Localex1`) en regardant le contenu de `outputsdrift.csv` dans `replanning_EX1` et `replanning_EX6`.**

**Quelles différences observez-vous ?**

{{< spoiler text="Révéler la correction" >}}
On observe que la probabilité de maintient de `OVOLREC` dans le modèle tactique au-dessus d'une diminution de 5% par rapport au modèle stratégique est relativement similaire entre `Localex1` et `Localex6`; si ce n'est que `Localex6` à une valeur plus basse de cette probabilité à la période 7, quand celle-ci chute vers 0.
{{< /spoiler >}}

### Question 7

À présent, dupliquez le dossier `Globalex1` et renommez-le `Globalex7` (supprimez le dossier existant s'il existe déjà).

**Modifiez ensuite le fichier `TWD_land.opt` de `Globalex7` afin de forcer le scénario `Globalex7` à réaliser au moins 99% du niveau de récolte `OVOLREC` du modèle tactique**.

{{< spoiler text="Révéler la correction" >}}
Le contenu de `TWD_land.opt` du scénario `Globalex7` doit alors être :

```

*OBJECTIVE
_MAXMIN oVolRec - _Penalty(_All) 1.._LENGTH

*EXCLUDE
aCaribou 1.._LENGTH
afire    1.._LENGTH
arecup    1.._LENGTH

*CONSTRAINTS

osuprec <= 40272 1.._LENGTH _Goal(G1,9999) _SETFROMLOCAL(1.0)
osuprec >= 40272 1.._LENGTH _Goal(G2,9999) _SETFROMLOCAL(0.99)

_NDY(oVolRec) 1.._LENGTH


*FORMAT MOSEK

```
{{< /spoiler >}}

Lancez ensuite une replannification avec le modèle stratégique `Globalex7` et le modèle tactique `Localex1`, avec la commande suivante :

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex7 -tac Localex1 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT -fol replanning_EX7 -rep 10 -len 20 -rel 20 -thr 2
```

Une fois que la commande s'est éxécutée, regardez les sorties présentes dans le dossier `Examples/Python/replanning/replanning_EX7`.

**Observez à présent comment se compare le maintient du niveau des plantations (output `OSSUPL`) entre ceux de la question 1, et ceux de cette replannification en regardant les fichiers `outputsdrift.csv` de `replanning_EX1` et `replanning_EX7`**.

**Comment se comparent le maintient du niveau de `OSSUPL` par rapport à la question 1 ?**

{{< spoiler text="Révéler la correction" >}}
On peut remarquer que pour la question 1 et cette question, `OSSUPL` garde une probabilité très basse de se maintenir au dela d'une grande diminution dès les premières périodes lorsque l'on compare le modèle tactique et stratégique.

Cependant, même pour des valeurs de drifts très hautes (e.g. 50%), on peut voir que `OSSUPL` à tendance à avoir une plus haute probabilité de grandement diminuer sa valeur avec `Globalex7` plutot qu'avec `Globalex1`.
{{< /spoiler >}}