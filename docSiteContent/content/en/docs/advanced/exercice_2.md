---
title: Exercise 2
linktitle: Exercise 2
type: book
date: "2019-05-05T00:00:00+01:00"
# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 2
---

In this exercise, you will try to use FMT to answer different questions about a strategic model and a tactical model through a re-planning process.

Thus, this exercise will teach you to:

- [x] Create a re-planning script
- [x] Re-plan with multiple models, and varying [special FMT keywords](../replanning/#the-new-keywords-associated-with-re-planning)
- [x] Analyze re-planning results to answer different questions

To do this exercise, **you must have FMT installed on your computer, along with the example files from the FMT GitHub repository**.

{{< cta cta_text="💾 Download and install FMT" cta_link="../../download_install" cta_new_tab="true" >}}

{{< cta cta_text="💾 Download the FMT example files" cta_link="https://downgit.github.io/#/home?url=https://github.com/gcyr/FMT/tree/master/Examples" cta_new_tab="true" >}}

**You must also have Python installed on your computer**. If this is not done, [refer to this page](../../download_install/importing_in_python/).

## Part 1: Creating a re-planning script

{{% callout note %}}
If you don't know much about Python, or if you really don't have the time, you can skip this section and use the `Replanner.py` script which is in the "Examples" folder you downloaded, in the `/Python/replanning` folder.
{{% /callout %}}

The re-planning process often involves running many models, many times (to make replicas).

It is therefore very useful to create a Python script that can be called through a console, a terminal, or a command prompt.

Even better : it is possible to create a Python script that will read arguments given to it when it is called with the command prompt. This will allow to re-use this script for different re-planning scenarios or purposes.

Try to create the following script in Python language:

- Importing `Models`, `Parser`, `Core`, `Version` and `Parallel` from `FMT`.
- Read the following arguments (it is recommended to use the [`argparse` module](https://docs.python.org/3/library/argparse.html))
    - `-pri` which specifies the location of the primary file of the models that will be used
    - `-str` which indicates the name of the strategic scenario
    - `-tac` which indicates the name of the tactical scenario
    - `-sto` which indicates the name of the stochastic scenario
    - `-out` indicates a comma-separated list of output values of interest to be extracted from the model results
    - `-fol` which indicates the folder where the output files of the script will be put
    - `-rep` which indicates the number of replicas to do
    - `-len` which indicates the number of periods of the strategic model
    - `-rel` which indicates the number of periods over which the re-planning will be done
    - `-thr` which indicates the number of CPU cores to use for parallelization

Next, the script will need to:

- Read the model via the primary file
- Read the different scenarios
- Create the strategic/global model using the scenario name provided by `-str`.
    - Make the global model run for the duration provided by `-len`.
    - Make the global model use the number of threads given by `-thr` for parallel resolution
- Create the stochastic model using the scenario name provided by `-sto`.
    - Make the stochastic model run for one period
- Create the tactical model using the scenario name provided by `-tac`.
    - Make the tactical model run for one period
    - Make the global model use the number of threads specified by `-thr` for parallel resolution
- Add the outputs selected by the `-out` argument in a list from the model outputs
- Prepare the re-planning task through the `Parallel.FMTreplanningtask()` function by providing to the function :
    - The strategic model
    - The stochastic model
    - The tactical model
    - The list of desired outputs
    - The folder where the outputs will be put
    - The format of the outputs (here, `.csv`)
    - [All other arguments needed for the function](../../../doxygen/html/classParallel_1_1FMTreplanningtask.html)
- Run the rescheduling task through the `Parallel.FMTtaskhandler()` function

When you are done, or if you are stuck, **you can view the correction of this exercise for Python code by clicking on the "Reveal correction" button below**.

{{< spoiler text="Reveal correction" >}}
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

## Part 2: Creation of scenarios for re-planning

### Preparation

After downloading the FMT example files (see above), open the folder `Models/TWD_land/Scenarios/Globalex1`.

This `Globalex1` scenario is a scenario that we will use as [strategic model or global model](../replanning#the-strategic-model).

**Take time to look at the contents of its files**; in particular, look at its actions, objectives and constraints.

**Do the same with the `Localex1` model**, which we will use as the [tactical model or local model](../replanning#the-tactical-model)

### Question 1

Open a windows command prompt, or a powershell. Use the command `cd` to go to the `Examples/Python/replanning` folder. Then use the following command to start re-planning:

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex1 -tac Localex1 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT -fol replanning_EX1 -rep 10 -len 20 -rel 20 -thr 2
```

This command tells the script created in the first part that we want to :

- use the model found at `../../Models/TWD_land/TWD_land.pri`
- use the scenario `Globalx1` as a strategic model
- use the `Localex1` scenario as a tactical model
- use the `Globalnofire` scenario as a stocastic model
- create output tables for the `OVOLREC`, `OSUPREC`, `OSUPPL`, and `VOLINVENT` outputs
- put these tables in the `replanning_EX1` folder
- make 10 replicates
- solve the strategic model for 20 periods
- do the re-plannification on 20 periods
- use 2 CPU cores to make replicas in parallel

Once the command has run, look at the output in the `Examples/Python/replanning/replanning_EX1` folder.

**With a stock-out tolerance of 5%, what is the probability of holding `OVOLREC` until period 10 in the tactical model?**

{{< spoiler text="Reveal correction" >}}
To look at the probability of keeping `OVOLREC` above a 5% value drop, we just have to look at the `outputsdrift.csv` file. This file shows us the probability that the different outputs selected in the tactical model remain above or below a certain distance (drift) from the strategic model.

We can open the file, and go to the lines that concern `OVOLREC` and a drift of `0.05` (5%), namely lines 462-481.

Looking at these rows, we can see that from period 7 onwards, `OVOLREC` has a probability of `0` of not falling by more than 5% compared to the strategic model (third column `LowerProbability`); this probability remains at 0 for all subsequent periods. Thus, it appears that the probability of keeping `OVOLREC` above a 5% decline until period 10 is 0.

These values can be represented with a graph via the data in `outputsdrift.csv` :

{{< figure src="docs/grapheExercice2_replanning_Exercice1.png" >}}

{{< /spoiler >}}

### Question 2

Now duplicate the `Globalex1` folder and rename it `Globalex2` (delete the existing folder if it already exists).

**Then modify the `TWD_land.opt` file of `Globalex2` to force the `Globalex2` scenario not to exceed 20,000 m³ for the `OVOLREC` output**.

{{< spoiler text="Reveal correction" >}}
The content of `TWD_land.opt` of the `Globalex2` scenario should then be :

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

Then run a re-planning with the strategic model `Globalex2` and the tactical model `Localex1`, with the following command:

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex2 -tac Localex1 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT -fol replanning_EX2 -rep 10 -len 20 -rel 20 -thr 2
```

Once the command has run, look at the output in the `Examples/Python/replanning/replanning_EX2` folder.

**With a stock-out tolerance of 5%, what is the probability of holding `OVOLREC` until period 10 in the tactical model?**

**Is there a gain from question 1?**

{{< spoiler text="Reveal correction" >}}
Looking at the results of `outputsdrift.csv`, we can see that `OVOLREC` keeps a probability of `1` of not decreasing more than 5% throughout the 20 periods. Thus, there is a maximum probability that `OVOLREC` will stay above a 5% decline in the tactical model `Localex1`, when using the strategic model `Globalex2`.

We have a clear gain on question 1, for which `OVOLREC` could not stay above a 5% decline from the 7th period. This is probably due to the fact that `Globalex2` was forced not to harvest too much, which made it easier to maintain this level in `Localex1`.
{{< /spoiler >}}

### Question 3

Now duplicate the `Localex1` folder and rename it `Localex3` (delete the existing folder if it already exists).

**Then modify the `TWD_land.opt` file of `Localex3` to force this tactical model to follow the solution of the strategic model with a weight of 1000, using the keyword [`_SETGLOBALSCHEDULE(weight)`](../replanning/#_setglobalscheduleratio)**.

{{< spoiler text="Reveal correction" >}}
The content of `TWD_land.opt` of the `Localex3` scenario should then be :

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

Then run a re-planning with the strategic model `Globalex1` and the tactical model `Localex3`, with the following command:

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex1 -tac Localex3 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT -fol replanning_EX3 -rep 10 -len 20 -rel 20 -thr 2
```

Once the command has run, look at the output in the `Examples/Python/replanning/replanning_EX3` folder.

**With a stock-out tolerance of 5%, what is the probability of holding `OVOLREC` until period 10 in the tactical model?**

**Is there a gain from question 1?**

{{< spoiler text="Reveal correction" >}}
Looking at the results of `outputsdrift.csv` in `Examples/Python/replanning/replanning_EX3`, we can see that `OVOLREC` has a probability of `1` of not falling more than 5% from the strategic model in period 8; but that this probability changes to `0` from period 9 onwards, and remains at `0` for all other periods. Thus, it is certain that `OVOLREC` will not stay above a 5% decline until period 10.

However, we can still observe a slight gain from question 1; the probability that `OVOLREC` will not decrease by more than 5% dropped to `0` as early as period 7 in these results. By pushing the tactical model to follow the solution of the strategic model with the keyword `_SETGLOBALSCHEDULE(weight)`, we thus gained two more periods of stability!
{{< /spoiler >}}

### Question 4

Now duplicate the `TWD_land._opt` file from `Localex1` to the `Localex4` folder (delete the existing file if it already exists).

**Then modify the `TWD_land.opt` file in `Localex4` to force the tactical model to follow the replica tables (`PEUPLEMENT1.csv` and `PEUPLEMENT2.csv`) for harvesting in the stands peuplement1 and peuplement2 (use [`_REPLICATE()`](../replanning/#_replicateoutput)**. In doing so, you must force the tactical model to do a certain level of harvesting in particular spatial features of the model (peuplement1 and peuplement2) based on files you provide to the model (`PEUPLEMENT1.csv` and `PEUPLEMENT2.csv`).

{{< spoiler text="Reveal correction" >}}
The content of `TWD_land.opt` of the `Localex4` scenario should then be :

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

Then run a re-planning with the strategic model `Globalex1` and the tactical model `Localex4`, with the following command:

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex1 -tac Localex4 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT,OVOLRECPEUPLEMENT1,OVOLRECPEUPLEMENT2 -fol replanning_EX4 -rep 10 -len 20 -rel 20 -thr 2
```

Once the command has run, **go to the `Localex4.csv` file in the `EX4_Scheduling` folder, and look at the results of the `OVOLRECPEUPLEMENT1` and `OVOLRECPEUPLEMENT2` outputs**. In particular, you can create a pivot table in a program such as Microsoft Excel to obtain a graph showing the evolution of these measures over time, with a curve for each iteration.

At the same time, open the files `PEUPLEMENT1.csv` and `PEUPLEMENT2.csv`, and observe their contents. Each column corresponds to values that the tactical model was supposed to use for a replica, with each row containing a value for a given period.

**When comparing the contents of `Localex4.csv` and `PEUPLEMENT1.csv` and `PEUPLEMENT2.csv`, what do you observe?**

{{< spoiler text="Reveal correction" >}}
We can see that the tactical model has indeed followed the harvest levels defined in the `PEUPLEMENT1.csv` and `PEUPLEMENT2.csv` files for the output `OVOLREC` in stands 1 and 2.

In particular, we can see that `OVOLRECPEUPLEMENT1` drops to 0 for period 4 in all iterations, as expected in `PEUPLEMENT1.csv`. We can also see that iteration 4 keeps a value of `OVOLRECPEUPLEMENT1` at 10 most of the time, while the other iterations keep a value of 20.

{{< figure src="docs/grapheExercice2_replanning_Exercice4.png" >}}


We can observe a drop to 0 for `OVOLRECPEUPLEMENT2` at period 9 in all iterations, as expected in `PEUPLEMENT2.csv`.

{{< figure src="docs/grapheExercice2_replanning_Exercice4-2.png" >}}
{{< /spoiler >}}

### Question 5

Now duplicate the `Localex1` folder and rename it `Localex5` (delete the existing folder if it already exists).

**Then modify the `TWD_land.opt` file of `Localex5` to force this tactical model to ignore the 90-100ha planting constraint from period (or to follow it only in period 1) using the keyword [`_REIGNORE(period)`](../replanning/#_reignoreperiod)**.

{{< spoiler text="Reveal correction" >}}
The content of `TWD_land.opt` of the `Localex5` scenario should then be :

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

Then run a re-planning with the strategic model `Globalex1` and the tactical model `Localex5`, with the following command:

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex1 -tac Localex5 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT -fol replanning_EX5 -rep 10 -len 20 -rel 20 -thr 2
```

Once the command has run, **compare the evolution of the planting level output `OSUPPL` in the tactical model `Localex1` (in the `planning_EX1` folder created in question 1) with the evolution of `OSUPPL` in the tactical model `Localex5` (in the `planning_EX5` folder)**. As before, you can use a pivot table in a program like Microsoft Excel to obtain a graph showing the evolution of these measures over time, with a curve for each of the iterations.

**What differences do you observe between `Localex1` and `Localex5`?**

{{< spoiler text="Reveal correction" >}}
We can see that the tactical model `Localex1` manages to sustain the level of `OSUPPL` for a long time in a stable way, as shown on this graph:


{{< figure src="docs/grapheExercice2_replanning_Exercice5.png" >}}


In comparison, in `Localex5`, we can see that the level of `OSUPPL` is much more variable and unstable. It quickly reaches values of 0, due to the fact that the model constraints on it are ignored after period 2, as shown on this graph:

{{< figure src="docs/grapheExercice2_replanning_Exercice5-2.png" >}}

Hence, it seems that the local model did ignore the constraints `OSUPPL`.
{{< /spoiler >}}

### Question 6

Now duplicate the `Localex1` folder and rename it `Localex6` (delete the existing folder if it already exists).

**Then modify the `TWD_land.opt` file of `Localex6` to force this tactical model to achieve 100% of the harvest level (`OVOLREC`) of the strategic model, with a 1% variability allowed for exceeding the value (so at least 100%, and at most 101%) with the keyword [`_SETFROMGLOBAL(weight)`](../replanning/#_setfromglobalratio)**.

{{< spoiler text="Reveal correction" >}}
The content of `TWD_land.opt` of the `Localex6` scenario should then be :

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

Then run a re-plannibg with the strategic model `Globalex1` and the tactical model `Localex6`, with the following command:

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex1 -tac Localex6 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT -fol replanning_EX6 -rep 10 -len 20 -rel 20 -thr 2
```

Once the command has run, look at the output in the `Examples/Python/replanning/replanning_EX6` folder.

**Now compare the probability of maintaining a similar harvest as in Exercise 1 (with `Localex1`) by looking at the contents of `outputsdrift.csv` in `replanning_EX1` and `replanning_EX6`.**

**What differences do you observe?**

{{< spoiler text="Reveal correction" >}}
We observe that the probability of maintaining `OVOLREC` in the tactical model above a 5% decrease with respect to the strategic model is relatively similar between `Localex1` and `Localex6`; except that `Localex6` has a lower value of this probability at period 7, when it drops towards 0.
{{< /spoiler >}}

### Question 7

Now duplicate the `Globalex1` folder and rename it `Globalex7` (delete the existing folder if it already exists).

**Then modify the `TWD_land.opt` file of `Globalex7` to force the `Globalex7` scenario to achieve at least 99% of the `OVOLREC` harvest level of the tactical model**.

{{< spoiler text="Reveal correction" >}}
The content of `TWD_land.opt` of the `Globalex7` scenario should then be :

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

Then run a re-planning with the strategic model `Globalex7` and the tactical model `Localex1`, with the following command:

```shell
python Replanner.py -pri ../../Models/TWD_land/TWD_land.pri -str Globalex7 -tac Localex1 -sto Globalnofire -out OVOLREC,OSUPREC,OSUPPL,VOLINVENT -fol replanning_EX7 -rep 10 -len 20 -rel 20 -thr 2
```

Once the command has run, look at the output in the `Examples/Python/replanning/replanning_EX7` folder.

**Now observe how the planting levels (output `OSSUPL`) compare between those of question 1, and those of this re-planning by looking at the `outputsdrift.csv` files of `replanning_EX1` and `replanning_EX7`.**

**How does the maintenance of the level of `OSSUPL` compare to question 1?**

{{< spoiler text="Reveal correction" >}}
It can be seen that for question 1 and this question, `OSSUPL` keeps a very low probability of maintaining itself from a large decrease from the first periods when comparing the tactical and strategic model.

However, even for very high drift values (e.g. 50%), we can see that `OSSUPL` tends to have a higher probability of greatly decreasing its value with `Globalex7` than with `Globalex1`.
{{< /spoiler >}}