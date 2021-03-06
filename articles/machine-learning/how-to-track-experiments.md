---
title: Log ML-kísérletek & metrikák
titleSuffix: Azure Machine Learning
description: Figyelje az Azure ML-kísérleteit, és figyelje a futtatási metrikákat a modell létrehozási folyamatának növelése érdekében. Vegyen fel naplózást a betanítási parancsfájlba, és tekintse meg a Futtatás naplózott eredményeit.  Használja a Run. log, a Run. start_logging vagy a ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: 0c77e9d0aa4f44f33b1345a6021fc0378459ee85
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79296965"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Azure ML-kísérletek futtatásának és metrikáinak monitorozása
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A kísérletek és a figyelési futtatási metrikák nyomon követésével növelheti a modell létrehozásának folyamatát. Ebből a cikkből megtudhatja, hogyan adhat hozzá naplózási kódot a betanítási parancsfájlhoz, elküldheti a kísérlet futtatását, figyelheti a futtatást, és megvizsgálhatja az eredményeket Azure Machine Learning.

> [!NOTE]
> A Azure Machine Learning a betanítás során más forrásokból is naplózhat adatokat, például automatizált gépi tanulási futtatásokat vagy a betanítási feladatot futtató Docker-tárolót. Ezek a naplók nincsenek dokumentálva. Ha problémákat tapasztal, és felveszi a kapcsolatot a Microsoft ügyfélszolgálatával, előfordulhat, hogy a hibaelhárítás során ezeket a naplókat is használni tudja.

> [!TIP]
> A jelen dokumentumban található információk elsősorban olyan adatszakértők és fejlesztők számára készültek, akik figyelni szeretnék a modell betanítási folyamatát. Ha Ön olyan rendszergazda, aki az Azure Machine learning erőforrás-felhasználásának és eseményeinek figyelését érdekli, például a kvótákat, a befejezett képzések futtatását vagy az elkészült modell üzembe helyezését, tekintse meg a [figyelés Azure Machine learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>A nyomon követett elérhető metrikák

A következő metrikák hozzáadhat egy Futtatás betanítási kísérlet során. A futtatások nyomon követésére szolgáló részletes lista megtekintéséhez tekintse meg a [futtatási osztály referenciájának dokumentációját](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Típus| Funkce Pythonu | Megjegyzések|
|----|:----|:----|
|Skaláris értékek |Függvény:<br>`run.log(name, value, description='')`<br><br>Példa:<br>Run.log ("pontossággal", 0,95) |Napló egy numerikus vagy karakterlánc értéket, a Futtatás a megadott névvel. Ennek a mutatónak tárolhatók a kísérletben a futtatási rekord a naplózási a metrika a futtató okoz.  Az azonos metrika futtató, az eredmény egy adott mérőszám vektor fontolóra belül több alkalommal bejelentkezhet.|
|Listák|Függvény:<br>`run.log_list(name, value, description='')`<br><br>Példa:<br>Run.log_list ("pontosságú", [a 0.6-os, 0,7, 0.87]) | Jelentkezzen a Futtatás a megadott nevű értékek listáját.|
|Sor|Függvény:<br>`run.log_row(name, description=None, **kwargs)`<br>Példa:<br>Run.log_row ("Keresztül X Y", x = 1, y = 0,4) | A *log_row* használatával a kwargs-ben leírtak szerint több oszloppal rendelkező mérőszámot hoz létre. Minden elnevezett paraméter generálja egy oszlop megadott értékkel.  a *log_row* egy tetszőleges rekord naplózására, vagy egy hurokban többször is meghívható egy teljes tábla létrehozásához.|
|Tábla|Függvény:<br>`run.log_table(name, value, description='')`<br><br>Példa:<br>Run.log_table ("Keresztül X Y", {"x": [1, 2, 3], "y": [a 0.6-os, 0,7, 0.89]}) | Jelentkezzen a szótárobjektum a Futtatás a megadott névvel. |
|Képek|Függvény:<br>`run.log_image(name, path=None, plot=None)`<br><br>Példa:<br>`run.log_image("ROC", plot=plt)` | Jelentkezzen egy képet a futtatási rekord. Be-vagy képfájl a matplotlib log_image használatával a Futtatás rajz.  Ezek a lemezképek lesz látható, és a futtatási rekord összehasonlítható.|
|Futtatás címkézése|Függvény:<br>`run.tag(key, value=None)`<br><br>Példa:<br>Run.tag ("kijelölt", "yes") | A Futtatás egy karakterláncot és egy opcionális karakterláncértéket a címkékkel.|
|Töltse fel a fájl vagy könyvtár|Függvény:<br>`run.upload_file(name, path_or_stream)`<br> <br> Példa:<br>Run.upload_file ("best_model.pkl", ". / model.pkl") | Töltse fel egy fájlt a futtatási rekord. Futtatások automatikusan rögzítheti a megadott kimeneti könyvtár, amely alapértelmezés szerint a fájl ". / kimenete" legtöbb Futtatás típusok esetében.  Upload_file csak akkor használja további fájlokat szeretne feltölteni, vagy nincs megadva egy kimeneti könyvtárat. Javasoljuk `outputs` hozzáadását a névhez, hogy az a kimenet könyvtárba legyen feltöltve. A futtatási rekordhoz társított összes fájlt megtekintheti a következő néven: `run.get_file_names()`|

> [!NOTE]
> Metrikák fejlécekké, a listákat, a sorok és a táblák rendelkezhet típusa: lebegőpontos, egész szám vagy karakterlánc.

## <a name="choose-a-logging-option"></a>Naplózási lehetőség kiválasztása

Ha szeretné nyomon követni vagy figyelése is futtathatja a kísérletet, hozzá kell adnia a naplózása, amikor a Futtatás küld kódot. A futtatási küldésének aktiválásához módjai a következők:
* __Run. start_logging__ – naplózási függvények hozzáadása a képzési parancsfájlhoz, és az interaktív naplózási munkamenet elindítása a megadott kísérletben. a **start_logging** egy interaktív futtatást hoz létre, amely olyan forgatókönyvekben használható, mint például a jegyzetfüzetek. A munkamenet során naplózott összes metrikák kerülnek a futtatási rekordját a kísérletben.
* __ScriptRunConfig__ – adja hozzá a naplózási funkciókat a betanítási parancsfájlhoz, és töltse be a teljes parancsfájl-mappát a futtatással.  A **ScriptRunConfig** a parancsfájlok futtatásához szükséges konfigurációk beállításának osztálya. Ezzel a beállítással adhat hozzá figyelés kódja befejezéséről értesítést, vagy egy vizuális widget figyelése lekéréséhez.

## <a name="set-up-the-workspace"></a>A munkaterület beállítása
Mielőtt hozzáadná a naplózás és a egy kísérlet elküldése, be kell állítania a munkaterületen.

1. Töltse be a munkaterület. A munkaterület-konfiguráció beállításával kapcsolatos további tudnivalókért lásd a [munkaterület konfigurációs fájlját](how-to-configure-environment.md#workspace).

[! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? Name = load_ws)]


## <a name="option-1-use-start_logging"></a>1\. lehetőség: Start_logging használata

a **start_logging** egy interaktív futtatást hoz létre, amely olyan forgatókönyvekben használható, mint például a jegyzetfüzetek. A munkamenet során naplózott összes metrikák kerülnek a futtatási rekordját a kísérletben.

Az alábbi példában egy egyszerű sklearn Ridge modell helyileg a helyi Jupyter notebook betanítja. A kísérletek különböző környezetekben történő elküldésével kapcsolatos további tudnivalókért lásd: [számítási célok beállítása a modell betanításához Azure Machine learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets).

### <a name="load-the-data"></a>Az adatok betöltése

Ez a példa a cukorbetegség-adathalmazt, egy jól ismert kis adatkészletet használ, amely a scikit-Learn szolgáltatáshoz tartozik. Ez a cella betölti az adatkészletet, és felosztja véletlenszerű betanítási és tesztelési készletekbe.

[! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? Name = load_data)]

### <a name="add-tracking"></a>Nyomkövetés hozzáadása
A Azure Machine Learning SDK használatával vegyen fel kísérlet-követést, és töltsön fel egy megőrzött modellt a kísérlet futtatása rekordba. Az alábbi kód felveszi a címkéket, naplók, a, és feltölti egy modellfájl futtassa a kísérletet.

[! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? Name = create_experiment)]

A parancsfájl ```run.complete()```tel végződik, amely a futtatást befejezettként jelöli meg.  Ezt a funkciót általában interaktív notebook olyan forgatókönyvekben használatos.

## <a name="option-2-use-scriptrunconfig"></a>2\. lehetőség: ScriptRunConfig használata

A [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) a parancsfájlok futtatásához szükséges konfigurációk beállításának osztálya. Ezzel a beállítással adhat hozzá figyelés kódja befejezéséről értesítést, vagy egy vizuális widget figyelése lekéréséhez.

Ebben a példában a fent sklearn Ridge alapmodell tartalmazó gyűjteménnyel bővítjük. Egy egyszerű paraméter Ismétlés az ismétlés metrikákat rögzítheti a modell és a kísérlet alatt fut, a betanított modellek az alfa értékeknek keresztül hajtja végre. A példában helyben fut egy felhasználó által felügyelt környezetben. 

1. Hozzon létre egy képzési parancsfájlt `train.py`.

   [! code-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. A `train.py` parancsfájl hivatkozik `mylib.py`, amely lehetővé teszi a Ridge-modellben használandó alfa-értékek listájának lekérését.

   [! code-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Állítsa be a felhasználó által felügyelt helyi környezetben.

   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = user_managed_env)]


4. Küldje el a ```train.py``` parancsfájlt a felhasználó által felügyelt környezetben való futtatáshoz. Ez a teljes parancsfájl-mappa betanításra kerül, beleértve a ```mylib.py``` fájlt is.

   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? név = src)] [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? név = Futtatás)]




## <a name="manage-a-run"></a>Futtatás kezelése

A [képzések indítása, figyelése és megszakítása című](how-to-manage-runs.md) cikk kiemeli a kísérletek kezeléséhez kapcsolódó Azure Machine learning munkafolyamatokat.

## <a name="view-run-details"></a>Futtatás részletei nézet

### <a name="view-activequeued-runs-from-the-browser"></a>Aktív/várólistán lévő futtatások megtekintése a böngészőben

A modellek betanításához használt számítási célok egy megosztott erőforrás. Így előfordulhat, hogy egy adott időpontban több futtatási sor van vagy aktív. Ha meg szeretné tekinteni a futtatásokat egy adott számítási célra a böngészőben, kövesse az alábbi lépéseket:

1. A [Azure Machine learning Studióban](https://ml.azure.com/)válassza ki a munkaterületet, majd a lap bal oldalán kattintson a __számítás__ elemre.

1. Válassza a __betanítási fürtök__ lehetőséget a betanításhoz használt számítási célok listájának megjelenítéséhez. Ezután válassza ki a fürtöt.

    ![Válassza ki a betanítási fürtöt](./media/how-to-track-experiments/select-training-compute.png)

1. Válassza a __futtatások__lehetőséget. Megjelenik a fürtöt használó futtatások listája. Egy adott Futtatás részleteinek megtekintéséhez használja a __Run (Futtatás__ ) oszlopban található hivatkozást. A kísérlet részleteinek megtekintéséhez használja a __kísérlet__ oszlopban található hivatkozást.

    ![A betanítási fürt futtatásának kiválasztása](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > A futtatások tartalmazhatnak alárendelt futtatásokat, így egy betanítási feladatok több bejegyzést is eredményezhetnek.

A Futtatás befejezése után már nem jelenik meg ezen a lapon. A befejezett futtatásokkal kapcsolatos információk megtekintéséhez látogasson el a Studio __kísérletek__ szakaszára, és válassza ki a kísérletet, majd futtassa a parancsot. További információ: a [lekérdezés futtatásának mérőszámai](#queryrunmetrics) szakasz.

### <a name="monitor-run-with-jupyter-notebook-widget"></a>A monitor futtatása a Jupyter notebook widgettel
Ha a **ScriptRunConfig** metódust használja a futtatások elküldéséhez, tekintse meg a Futtatás folyamatát egy [Jupyter widgettel](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). A futtatás elküldéséhez hasonlóan a vezérlő aszinkron módon működik, és 10-15 másodpercenként élő állapotfrissítést biztosít a feladat befejeződéséig.

1. A Jupyter widget tekintse meg a Futtatás befejeződik várakozás közben.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Képernyőkép a Jupyter notebook widget](./media/how-to-track-experiments/run-details-widget.png)

   A munkaterületen megjelenő megjelenítésre mutató hivatkozást is kaphat.

   ```python
   print(run.get_portal_url())
   ```

2. **[Automatikus gépi tanulás futtatásához]** A diagramok egy korábbi futtatásból való elérése. Cserélje le a `<<experiment_name>>`t a megfelelő kísérlet nevére:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![A Machine Learning automatikus Jupyter notebook widget](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Ha meg szeretné tekinteni a folyamat további részleteit, kattintson arra a folyamatra, amelyet fel szeretne venni a táblázatban, és a diagramok egy előugró ablakban jelennek meg a Azure Machine Learning Studióban.

### <a name="get-log-results-upon-completion"></a>Naplóeredmények lekérése a befejezéskor

Modell betanítása és figyelési fordulnak elő a háttérben, hogy a várakozás közben egyéb feladatokat is futtathat. Azt is, amíg a modell több kód futtatása előtt képzési befejeződéséig. A **ScriptRunConfig**használatakor a ```run.wait_for_completion(show_output = True)``` segítségével megtekintheti, hogy mikor fejeződik be a modell betanítása. A ```show_output``` jelző részletes kimenetet biztosít. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>A lekérdezés futtatása a metrikák

```run.get_metrics()```használatával megtekintheti a betanított modell mérőszámait. Most már beszerezheti az összes naplózott határozza meg a legjobb modellt a fenti példában a metrikákat.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>A kísérlet megtekintése a munkaterületen a [Azure Machine learning Studióban](https://ml.azure.com)

Amikor egy kísérlet befejezését követően, megnyithatja a futtatási rekord rögzített kísérletet. Az előzményeket a [Azure Machine learning studióból](https://ml.azure.com)érheti el.

Navigáljon a kísérletek lapra, és válassza ki a kísérletet. A kísérlet futtatása irányítópultra kerül, ahol megtekintheti az egyes futtatásokhoz naplózott mérőszámokat és diagramokat. Ebben az esetben azt naplózza, MSE és az alfa értékeknek.

  ![Részletek futtatása a Azure Machine Learning Studióban](./media/how-to-track-experiments/experiment-dashboard.png)

Egy adott Futtatás részletezésével megtekintheti a kimeneteit vagy naplóit, vagy letöltheti az elküldött kísérlet pillanatképét, így megoszthatja a kísérlet mappáját másokkal.

### <a name="viewing-charts-in-run-details"></a>Diagramok részleteinek megtekintése

A naplózási API-k többféle módon rögzíthetik a különböző típusú metrikákat a Futtatás során, és megtekinthetik őket diagramként Azure Machine Learning Studióban.

|A naplózott érték|Példakód| A portál megtekintése|
|----|----|----|
|Napló numerikus értékek tömbje| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Single-variable vonaldiagram|
|Az azonos nevű metrika ismételten használt egyetlen numerikus érték naplózása (hasonló belül egy "for" ciklus)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Single-variable vonaldiagram|
|Jelentkezzen egy sor 2 numerikus oszlopok ismételten|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Kétváltozós vonaldiagram|
|A numerikus oszlopok 2 naplótábláját|`run.log_table(name='Sine Wave', value=sines)`|Kétváltozós vonaldiagram|


## <a name="example-notebooks"></a>Példa notebookok
A következő notebookok a jelen cikk fogalmait bemutatása:
* [használati útmutató – azureml/képzés/vonat – jegyzetfüzeten belül](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [használati útmutató – azureml/képzés/helyi betanítás](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [használati útmutató – azureml/Track-and-monitor-kísérletek/naplózás – API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Próbálja ki a következő lépések megtudhatja, hogyan használható az Azure Machine Learning SDK Pythonhoz készült:

* Megtudhatja, hogyan regisztrálhat a legjobb modellt, és hogyan helyezheti üzembe az oktatóanyagban, hogyan [taníthatja be a rendszerképek besorolási modelljét Azure Machine learning](tutorial-train-models-with-aml.md).

* Megtudhatja, hogyan [taníthat PyTorch-modelleket Azure Machine learning](how-to-train-pytorch.md)használatával.
