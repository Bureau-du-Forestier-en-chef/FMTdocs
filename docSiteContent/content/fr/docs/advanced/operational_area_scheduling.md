---
title: Planification des Compartiments d'Organisation Spatiale (COS)
linktitle: Planification des COS
type: book
date: "2019-05-05T00:00:00+01:00"
# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 3
---

## Les COS et la planification spatiale des coupes

Un COS (*Compartiment d'Organisation Spatiale*) est une aire de forêt que l'on peut utiliser pour agréger des zones de coupes forestières dans l'espace. Dans Woodstock, elle est représentée de manière spatialement référencée par un groupe d'aires ou des peuplements qui partagent un numéro de COS commun.

{{< figure src="docs/Figure 1- explication COS.png" >}}

L'agrégation des coupes forestières a été pendant un temps vue d'un mauvais œil dans certains pays à cause des vastes étendues de forêts coupées continues qu'elle pouvait générer. Cependant, elle est aujourd'hui considérée à nouveau dans le but de minimiser l'étendue des zones de coupes au niveau du paysage, ainsi que la quantité de chemins forestiers générés. Le prix des coupes en est également réduit par une réduction des déplacements et de la complexité lors des interventions forestières.

FMT propose ainsi différentes fonctions qui permettent de dériver un calendrier d'ouverture et de fermeture de COS qui respecte plusieurs contraintes spatiales données quant à la taille des zones de coupes. Ainsi, le calendrier va fermer des COS trop proches lorsque cela pourrait causer des tailles de coupes trop grandes si elles étaient choisies, ou bien forcer de grandes coupes en restreignant les COS disponibles pour la coupe.

{{< figure src="docs/Figure 2 - Explication calendrier.png" >}}

Ce calendrier d'ouverture et de fermeture de COS va ainsi permettre de communiquer les contraintes spatiales aux modèles de programmation linéaire basés sur des fichiers Woodstock que FMT utilise (voir [les bases de FMT](../../basics/spatially_referenced_optimization/)). FMT va alors pouvoir générer une nouvelle solution pour un modèle avec l'ajout de ce calendrier. Bien évidemment, lorsque l'on ajoute des contraintes additionnelles au modèle, cela a généralement pour cause de réduire la quantité de bois qui est récoltable dans la solution optimale. Ainsi, l'ajout de ce calendrier d'ouvertures et de fermetures de COS va réduire le bois récolté dans la nouvelle solution générée par FMT.

L'idée est alors de trouver un calendrier "optimal" qui va réduire le moins possible la quantité de bois récoltable dans la solution du modèle. On peut le faire par le biais du processus itératif suivant :

- On calcule la solution du modèle sans calendrier.
- On génère un premier calendrier d'ouverture et de fermeture de COS qui respecte les contraintes spatiales données.
	- Ce calendrier est simplement "réalisable"; il n'est pas forcément optimisé, mais respecte simplement toutes les contraintes.
- On ajoute le calendrier au modèle, et on recalcule la solution du modèle.
- On compare la quantité de bois récolté dans la première solution (sans calendrier) avec cette nouvelle solution (avec calendrier). On obtient le % de réduction de bois récoltable dont le calendrier est responsable.
	- Le but est alors d'optimiser le calendrier pour réduire ce pourcentage autant que possible.
- On réalise ensuite la boucle suivante (appellée un *greedy algorithm*) avec autant d'itérations que nécessaire :
	- On change le calendrier des COS de manière aléatoire tout en faisant en sorte qu'il continue de respecter toutes les contraintes spatiales
	- On insère le calendrier dans le modèle et l'on recalcule la solution
	- Si le calendrier a réduit le pourcentage de réduction de bois récoltable, on garde la modification aléatoire pour l'itération suivante
		- Sinon, on annule cette modification aléatoire et l'on en cherche une autre

{{< figure src="docs/Figure 3 - Greedy Algorithm COS.png" >}}

{{% callout note %}}
Il est possible de résoudre le problème de l'optimisation du calendrier avec des méthodes plus complexes, comme celle du *mixed integer programming* (MIP) pour obtenir une solution vraiment optimale.
Cependant, ces algorithmes peuvent demander beaucoup trop de calcul pour des modèles de programmation linéaire qui concernent des paysages forestiers complexes. Dans ce cas-là, le *greedy algorithm* est utile pour éviter des temps de calcul trop longs.
{{% /callout %}}

## L'implémentation des COS dans des fichiers Woodstock

La syntaxe des fichiers Woodstock n'est pas faite pour permettre de l'optimisation via des contraintes spatiales. Cependant, le système des COS utilisé par FMT peut être mis en place dans des fichiers Woodstock par le biais d'un *yield*.

Par exemple, il est possible de créer un *yield* nommé `youvert` qui décrit si un peuplement est disponible à la récolte ou non. Ce *yield* peut alors être assigné à un numéro de COS présent dans un thème, et va donc s'appliquer à tous les peuplements qui sont associés à ce numéro de COS via ce même thème. Il peut ensuite servir à restreindre certaines actions (e.g., des coupes) au sein du modèle. 

Dans la section `YIELD` du modèle woodstock, ce *yield* qui indique quand un COS est ouvert ou fermé aura une valeur pour chacune des périodes du modèle. Cet enchainement de valeurs représente donc le calendrier d'ouverture ou de fermeture des COS. En pratique, la section YIELD qui contient le calendrier formulé par FMT prendra la forme suivante (ici, le yield `youvert` indique si un COS est ouvert ou fermé) :

{{< figure src="docs/Dessin_Yield_FR.png" >}}

Pour ce qui est d'une action dans le modèle comme une coupe, le *yield* peut être utilisé de la manière suivante dans la section ACTION du modèle pour restreindre l'action à des COS ouverts :

{{< figure src="docs/Dessin_Yield_Action_FR.png" >}}

## Fonctions utilisées et paramètres nécessaires

FMT propose plusieurs fonctions qui permettent de créer un calendrier optimisé d'ouverture et de fermeture de COS dans FMT. Ces fonctions nécessitent différents paramètres qui seront décrits ici.

{{% callout warning %}}
Cette documentation ne traitera pas du problème de la définition des COS, que l'on appelle le *clustering*. Idéalement, définir les COS (c.-à-d. attribuer des numéros de COS a des peuplements) devrait se faire en même temps qu'optimiser le calendrier; mais cela représente une opération très complexe. Ainsi, le clustering est généralement fait en amont de l'optimisation du calendrier, en regroupant des strates similaires et proches dans l'espace.

Ainsi, **pour pouvoir utiliser les fonctions suivantes, les COS doivent déjà avoir été insérés dans les fichiers du modèle sous forme de numéros de COS associés aux peuplements par des masques**. Voir la [section précédente](#limplémentation-des-cos-dans-des-fichiers-woodstock) pour plus d'information.
{{% /callout %}}

### Création des COS au sein d'un modèle de programmation linéaire dans FMT

Dans FMT, une fois qu'un modèle linéaire a été créé en se basant sur des fichiers woodstock (voir [les bases de FMT](../../basics/spatially_referenced_optimization/)), il est possible de créer la structure des COS au sein du modèle (voir sections précédentes) par le biais de la fonction `Heuristics.FMToperatingareascheme()`.

La fonction `Heuristics.FMToperatingareascheme()` sert à construire un seul COS au sein du modèle, et doit donc être répétée pour chacun des COS qui doit être créé. Elle nécessite différents paramètres. Ces paramètres servent à donner des contraintes sur comment et quand un COS peut être ouvert dans le calendrier.

Vous trouverez un exemple de cette création dans la [section suivante](#exemple-de-script).

{{% callout warning %}}
Le calendrier d'ouverture et de fermeture des COS que FMT pourra créer par la suite est très sensible à ces différents paramètres. Si ceux-ci sont mal indiqués, FMT pourrait être incapable de trouver une solution réalisable au modèle.
{{% /callout %}}

- Le premier paramètre contient le COS à créer en soi. Celui-ci peut être construit à l'aide de la fonction `Heuristics.FMToperatingarea()`, qui prend elle-même deux paramètres : le premier est le masque qui va décrire quels peuplements seront contenus dans ce COS, et le second est le `neihgbors perimeter`, ou *périmètre des voisins*. Ce second paramètre décrit le ratio du périmètre du COS que celui-ci doit avoir en commun avec un autre COS pour que les deux soient considérés comme voisins. Par exemple, si le périmètre des voisins est de 0.5, alors 50% du périmètre du COS doit être partagé avec un autre COS pour qu'ils soient considérés comme voisins.
- Le deuxième paramètre est le `opening time`, ou le temps d'ouverture. Il définit combien de temps le COS doit rester ouvert lorsqu'il est ouvert sur une période. Par exemple, si le temps d'ouverture est de 5, alors le COS sera toujours ouvert pour 5 périodes à la fois.
- Le troisième paramètre est le `return time`, ou temps de retour. Il correspond au nombre de périodes de temps qui doivent être respectés avant que le COS soit réouvert dans le futur. Par exemple, si le temps de retour est de 5, alors il doit y avoir au moins 5 périodes de temps entre deux ouvertures du COS.
- Le quatrième paramètre est le `repetition pattern`, ou la répétition du patron de récolte. Il est supérieur ou égal à 1, et il va effectivement forcer le COS à suivre plusieurs fois un patron d'ouvertures/fermetures dans le temps qui correspond à une alternance entre son temps d'ouverture et de fermeture. Par exemple, pour un temps d'ouverture de 2, un temps de fermeture de 3 et une valeur de répétition du patron de récolte de 2, une fois le COS ouvert, il suivra automatiquement le calendrier "ouvert, ouvert, fermé, fermé, fermé, ouvert, ouvert, fermé, fermé, fermé", ce qui correspondant à 2 répétitions de ses paramètres d'ouverture et de fermeture.
- Le cinquième paramètre est la `green up period`, ou période de verdissement. Elle correspond au délai que doit attendre le COS pour être ouvert à nouveau si un COS voisin est récolté. Les COS voisins sont en retour trouvés grâce au périmètre des voisins donné plus tôt.
- Le sixième paramètre est la `starting period`, ou période de départ. Elle correspond à la période à partir de laquelle le COS peut commencer à être ouvert dans le modèle.

Tous les COS créés par la fonction `Heuristics.FMToperatingareascheme()` doivent idéalement être mis au sein d'une liste, qui sera donnée en paramètre à une autre fonction par la suite.

### Détermination des COS voisins pour utiliser des contraintes spatiales avancées

Il est possible que vous ayez un fichier shapefile contenant la position dans l'espace de chacun des COS.

Si c'est le cas, il est alors possible de déterminer quels COS sont voisins en lisant le fichier shapefile, et en comparant les numéros des polygones correspondant aux COS dans ce fichier shapefile avec ceux qui se trouvent dans les fichiers du modèle linéaire. Cela permet d'utiliser la contrainte spatiale déterminée par le paramètre `green up period` décrit dans la [section précédente](#création-des-cos-au-sein-dune-modèle-de-programmation-linéaire-dans-fmt).

{{% callout warning %}}
Pour utiliser les fonctions suivantes, **les premiers attributs présents dans le fichier shapefile/vectoriel doivent correspondre aux thèmes présents dans les fichiers Woodstock de votre modèle**.

Le fichier shapefile ou vectoriel peut contenir d'autres attributs par la suite; mais sans ces premiers attributs correspondants aux thèmes des fichiers Woodstock, les fonctions de FMT ne pourront pas reconnaitre quel polygone correspond à quel COS, et elles ne fonctionneront pas.
{{% /callout %}}

Pour cela, il est possible d'utiliser la fonction `getschemeneighbors()` d'un objet `FMTareaparser` créé auparavant, et de lui fournir la liste de COS crée par le biais de `Heuristics.FMToperatingareascheme()` (voir [section précédente](#création-des-cos-au-sein-dune-modèle-de-programmation-linéaire-dans-fmt)) ainsi que le fichier shapefile (ou un autre type de fichier vectoriel) qui contient les COS. Il suffit ensuite d'indiquer quels sont les champs du fichier shapefile qui contiennent les informations liées à l'âge des COS et leur aire. Il est possible d'utiliser des paramètres optionnels afin de ne pas considérer les COS avec des aires trop petites.

En se faisant, la fonction `getschemeneighbors()` renverra une liste de COS similaire à celle créée par `Heuristics.FMToperatingareascheme()`; cependant, pour chacun des COS, le vecteur indiquant la liste de leurs voisins spatiaux sera rempli.

Cela devrait prendre la forme suivante :

```python
# On créer l'objet FMTareaparser
areap = Parser.FMTareaparser()
# On indique le chemin du fichier shapefile
shapefileLocation = "./spatialCompartments.shp"
# On utilise la fonction sur une liste de COS deja crée avec Heuristics.FMToperatingareascheme() auparavant, operaeas
# L'objet "modelthemes" contient les themes du modèle
# La liste sera mise a jour avec des COS qui contiennent leur vecteur de voisins remplis selon leur position dans l'espace décrite dans le fichier vectoriel spatialCompartments.shp donné en argument
opeareas = areap.getschemeneighbors(opeareas,modelthemes,shapefileLocation,"AGE","SUPERFICIE")
```

Pour plus d'informations, voir la [page doxygen de `FMTareaparser` et la section sur `getschemeneighbors`](../../../../doxygen/html/classParser_1_1FMTareaparser.html#a000283e41dfb73733984a4292b6dfa4a)

### Création d'un *output node* lié aux actions influencées par le calendrier des COS.

Afin de pouvoir commencer à optimiser le calendrier des COS (voir [section suivante](#lancement-du-greedy-algorithm-et-récupération-du-calendrier-optimisé)), il faut fournir à FMT différentes informations quand aux actions et aux peuplements qui seront affectés par les COS. Pour ce faire, FMT a besoin des `FMToutputnode` liés aux actions qui seront visées par les ouvertures et fermeture de COS (par exemple, un type de coupes qui ne peuvent pas se faire trop proches les une des autres). Ces objets sont seulement utilisés parce qu'ils sont les plus utiles pour cette étape, et non pas parce qu'ils sont les seuls qui peuvent donner ces informations à FMT.

Les objets `FMToutputnode` contiennent les *output nodes* du modèle linéaire. Ces derniers sont composés de 4 éléments : un masque (pour définir les peuplements considérés par l'*output node*), une action, un yield, et un paramètre qui peut être utilisé pour modifier le yield. Par exemple, un *output node* peut concerner 80% du volume brut récolté par les coupes totales dans tous les peuplements via la mention `? ? ? coupetotale volumebrut * 0.88`. Les *output nodes* sont indiqués dans le fichier `.out` de la formulation Woodstock. Plusieurs *output nodes* peuvent alors être combiné pour donner un seul output.

Afin de fournir ces `FMToutputnode` à FMT, deux méthodes sont disponibles :
1. Il est possible de créer un nouvel objet `FMToutputnode` qui contiendra toutes les actions visées par les COS en faisant un agrégat de ces actions. Pour ce faire, il suffit d'indiquer un nom d'agrégat d'action à toutes les actions visées via la fonction `push_aggregate()` associées aux objets `FMTaction`; puis, à fournir ce nom d'agrégats au constructeur de `FMToutputnode`. Si ces actions concernent tous les peuplements possibles, le constructeur poura alors prendre la forme `FMToutputnode(Core.FMTmask(("? "*len(themes))[:-1],themes),Agg_name)`, avec `Agg_name` contenant le nom de l'agrégat des actions visées par les COS.
2. Il est aussi possible de récupérer un `FMToutputnode` qui est contenu dans l'un des `FMToutput` du modèle directement via la fonction `getnodes()` associée aux objets `FMToutput`.

Un exemple de la deuxième méthode est montré dans [l'exemple de script](#exemple-de-script) plus bas.

### Lancement du *greedy algorithm* et récupération du calendrier optimisé

La dernière étape est de lancer le *greedy algorithm* pour créer un calendrier optimisé.

Pour ce faire, il suffit de créer un objet qui va contenir la "tâche" du greedy algorithm, et de donner cette tâche à une fonction de FMT qui s'occupe de lancer les tâches parallèles. Ces deux étapes sont essentielles, car le *greedy algorithm* de FMT utilise des tâches en parallèle afin de réaliser les différentes itérations de l'algorithme aussi rapidement que possible.

La création de l'objet contenant la tâche de l'algorithme se fait via la fonction `Parallel.FMTopareaschedulertask()`. La fonction nécessite 7 paramètres :

- Le `FMTlpmodel` qui contient le modèle linéaire pour lequel on veut faire le calendrier des COS
- Un objet contenant la liste de COS (voir la [section précédente](#cr%C3%A9ation-des-cos-au-sein-dune-mod%C3%A8le-de-programmation-lin%C3%A9aire-dans-fmt))
- Le *output node* créé dans la [section précédente](#cr%C3%A9ation-dun-noeud-li%C3%A9s-aux-actions-influenc%C3%A9es-par-le-calendrier-des-cos)
- Une chaine de caractères (*string*) qui indique où les fichiers créés par la fonction (qui contiennent des informations sur le calendrier optimisé final) seront copiés
- Une chaine de caractères (*string*) indiquant le nom de l'output lié au statut ouvert ou fermé des COS (par exemple `youvert`; voir sections précédentes)
- Un nombre indiquant le nombre maximum d'itérations que le *greedy algorithm* peut réaliser avant de s'arrêter (voir sections précédentes)
- Un nombre indiquant le temps maximal (en secondes) que le *greedy algorithm* peut prendre avant de s'arrêter; l'algorithme s'arrettera si ce temps ou si le nombre d'itérations maximal est atteint 

Une fois que l'objet contenant la tâche est créé avec cette fonction, il doit être passé à la fonction `Parallel.FMTtaskhandler()`. Cette fonction peut accepter un second paramètre qui indique le nombre de cœurs du processeur de l'ordinateur que la fonction utilisera pour réaliser les opérations en parallèle. La fonction retournera un objet `FMTtaskhandler`, qui peut ensuite lancer l'algorithme pour de bon à l'aide de la fonction `conccurentrun()` de l'objet.

Il est aussi possible de ne pas lancer le *greedy algorithm*, mais d'obtenir un premier calendrier non optimisé, mais simplement "réalisable". Pour cela, il suffit d'utiliser la fonction `getoperatingareaschedulerheuristics()` d'un objet `FMTlpmodel` en lui fournissant un objet contenant la liste des COS et un *output node*, ainsi que le nombre de calendriers non optimisés que l'on veut obtenir. Un exemple est montré dans [ce script](https://github.com/Bureau-du-Forestier-en-chef/FMT/blob/bb5aedacd33178f479769ea77d000307e134e3f3/Examples/Python/Operatingareascheduling.py) aux lignes 34-35.


## Exemple de script

Un exemple de script très simple pour créer un calendrier de COS **non optimisé** en python est disponible [à cette adresse](https://github.com/Bureau-du-Forestier-en-chef/FMT/blob/3ebc304ca488bf10039b1843e6e31fc197493d47/Examples/Python/Operatingareascheduling.py). Ce script ne fait que générer une première version non optimisée d'un calendrier pour les COS, mais qui est simplement réalisable en fonction des contraintes données.

Le script suivant est une autre version de ce script qui est intégralement commenté, et qui lance à la fin le *greedy algorithm* pour obtenir un calendrier optimisé de COS.

```python
# Ici, on charge FMT directement, comme s'il avait été installé avec pip.
from FMT import Models
from FMT import Parser
from FMT import Core
from FMT import Heuristics
from FMT import Version                  


if __name__ == "__main__":
	# On vérifie que FMT possède bien les fonctions nécessaires pour ce script
	if Version.FMTversion().hasfeature("OSI"):
		# Definition du chemin menant au modèle woodstock
		primarylocation = "../Models/TWD_land/TWD_land.pri"
		# Création d'un objet pour lire le modèle (parser)
		modelparser = Parser.FMTmodelparser()
		# On lit le model grâce au parser.
		models = modelparser.readproject(primarylocation,["LP"])
		# On désactive le log pour ce modèle (optionel)
		models[0].setquietlogger()
		# On charge le modèle parmis la liste des modèles renvoyés
		optimizationmodel=Models.FMTlpmodel(models[0],Models.FMTsolverinterface.CLP)
		# On récupère les thèmes du modèle
		themes = optimizationmodel.getthemes()
		# On créé une liste que l'on va remplir avec les opératings areas
		opareas = []
		# Pour chacun des numéros de COS présent dans les thèmes, on créer une COS que l'on met dans la liste
		for attribute in themes[2].getattributes("?"):
			mask = ["?" for theme in themes]
			mask[2] = attribute
			# On créer le COS avec le masque qui sélectionne les peuplements avec les numéros de COS
			# On utilise un opening time de 2, un return time de 1, une reptition de 4, une green up period de 0, et une starting period de 1.
			opareas.append(Heuristics.FMToperatingareascheme(Heuristics.FMToperatingarea(Core.FMTmask(mask,themes),0),2,1,4,0,1))
		# Les lignes suivantes vont trouver une solution initiale au modèle, sans calendrier de COS. Voir bases de FMT.
		for period in range(0,10):
				print(optimizationmodel.buildperiod())
		allconstraints = optimizationmodel.getconstraints()
		objective = allconstraints.pop(0)
		for constraint in allconstraints:
				optimizationmodel.setconstraint(constraint)
		optimizationmodel.setobjective(objective)
		optimizationmodel.initialsolve()
		# On récupère les output nodes du modèle nécessaires pour créer un calendrier de COS grace aux actions qu'ils contiennent
		nodeofoutput = None
		for output in optimizationmodel.getoutputs():
				if "OSUPREC" == output.getname():
					  nodeofoutput = output.getnodes(optimizationmodel.getarea(),optimizationmodel.getactions(),optimizationmodel.getyields())[0]
					  break;
		# On finit en définissant les paramètres nécessaires pour lancer le greedy algorithm : l'endroit ou mettre les outputs, et le temps maximal et le nombre d'itérations maximales pour l'algorithme.
		outputsLocation = "./Outputs/outputCalendrier"
		maximumIterations = 30
		maximumTime = 1209600 # Temps maximum en seconde; ici, cela correspond à deux semaines.
        # On créer l'objet qui contient la tâche à lancer avec le greedy algorithm
        maintask = Parallel.FMTopareaschedulertask(optimizationmodel, opeareas, outputsLocation, "YOUVERT", maximumIterations, maximumTime)
        # On met la tâche au sein d'un objet qui gère le lancement des tâches en parallèle dans FMT
        handler = Parallel.FMTtaskhandler(maintask,self.thr)
        # On active l'enregistrement du log de la tâche
        handler.settasklogger()
        # on lance la tâche du greedy algorithm en parralèle
        handler.conccurentrun()
	else:
		print("FMT needs to be compiled with OSI")
```