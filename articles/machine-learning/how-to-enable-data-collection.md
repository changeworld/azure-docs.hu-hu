---
title: Adatok gyűjtése az üzemi modelleken
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan gyűjthet Azure Machine Learning bemeneti modell adatait az Azure Blob Storage-ban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3c481a2e12d83e865025cd90e59e0eba572ad9a5
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771393"
---
# <a name="collect-data-for-models-in-production"></a>Adatok gyűjtése a termelési modellekhez

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> A Azure Machine Learning monitoring SDK hamarosan megszűnik. Az SDK továbbra is megfelelő azoknak a fejlesztőknek, akik jelenleg az SDK használatával figyelik az adateltolódást a modellekben. Az új ügyfelek esetében azonban javasoljuk, hogy az egyszerűsített [adatfigyelést Application Insights](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)használatával használja.

Ez a cikk bemutatja, hogyan gyűjtheti be a bemeneti modell adatait Azure Machine Learningból. Azt is bemutatja, hogyan helyezheti üzembe a bemeneti adatokat egy Azure Kubernetes szolgáltatásbeli (ak-beli) fürtön, és hogyan tárolhatja a kimeneti adatokat az Azure Blob Storage-ban.

A gyűjtemény engedélyezése után az összegyűjtött adatok segítenek a következőkben:

* [Figyelje az adateltolódásokat](how-to-monitor-data-drift.md) , mivel a termelési adatként bekerül a modellbe.

* Jobb döntéseket hozhat a modell újratanítása vagy optimalizálása során.

* A modell újratanítása az összegyűjtött adatokkal.

## <a name="what-is-collected-and-where-it-goes"></a>A begyűjtött és a hová kerül

A következő adatokat gyűjthetjük össze:

* Bemeneti adatok modellezése egy AK-fürtben üzembe helyezett webszolgáltatásokból. A hanghangok, a képek és a videók gyűjtése *nem* történik meg.
  
* Modellek előrejelzése éles bemeneti adatok használatával.

>[!NOTE]
> Ezen az adathalmazon jelenleg nem része az előösszesítésnek és az előszámításoknak.

A rendszer menti a kimenetet a blob Storage-ban. Mivel az adatait hozzáadja a blob Storage-hoz, kiválaszthatja kedvenc eszközét az elemzés futtatásához.

A blob kimeneti adatelérési útja a következő szintaxist követi:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> A 0.1.0 A16 verziónál korábbi Pythonhoz készült Azure Machine Learning SDK verziójában a `designation` argumentum neve `identifier`. Ha a kódot egy korábbi verzióval fejlesztette ki, azt ennek megfelelően kell frissítenie.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://aka.ms/AMLFree) a virtuális gép létrehozásának megkezdése előtt.

- Telepíteni kell egy AzureMachine learning-munkaterületet, egy helyi könyvtárat, amely tartalmazza a parancsfájlokat, valamint a Pythonhoz készült Azure Machine Learning SDK-t. A telepítésének megismeréséhez tekintse meg [a fejlesztési környezet konfigurálása](how-to-configure-environment.md)című témakört.

- Szüksége van egy betanított gépi tanulási modellre, amelyet AK-ra kell telepíteni. Ha nem rendelkezik modellel, tekintse meg a [betanítási modell besorolása](tutorial-train-models-with-aml.md) című oktatóanyagot.

- Szüksége van egy AK-fürtre. További információ a létrehozásáról és telepítéséről: a [telepítés és a hol](how-to-deploy-and-where.md).

- [Állítsa be a környezetet](how-to-configure-environment.md) , és telepítse a [Azure Machine learning monitoring SDK](https://aka.ms/aml-monitoring-sdk)-t.

## <a name="enable-data-collection"></a>Az adatgyűjtés engedélyezése

Az adatgyűjtést a Azure Machine Learning vagy más eszközökön keresztül üzembe helyezett modelltől függetlenül is engedélyezheti.

Az adatgyűjtés engedélyezéséhez a következőket kell tennie:

1. Nyissa meg a pontozási fájlt.

1. Adja hozzá a [következő kódot](https://aka.ms/aml-monitoring-sdk) a fájl elejéhez:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Az adatgyűjtési változók deklarálása a `init` függvényben:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    A *correlationId* egy opcionális paraméter. Nem kell használni, ha a modell nem igényli. A *correlationId* használata megkönnyíti a többi adattal, például a *LoanNumber* vagy a *Vevőkód*használatával való leképezést.
    
    A rendszer később az *azonosító* paramétert használja a mappa struktúrájának létrehozásához a blobban. Felhasználhatja a feldolgozott adatokból származó nyers adatok megkülönböztetésére is.

1. Adja hozzá a következő sornyi kódot a `run(input_df)` függvényhez:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Az adatgyűjtést *nem* lehet automatikusan **true** értékre állítani, ha a szolgáltatást az AK-ban helyezi üzembe. Frissítse a konfigurációs fájlt, ahogy az az alábbi példában is látható:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    A szolgáltatás figyelésének Application Insights a konfiguráció módosításával is engedélyezheti:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Új rendszerkép létrehozásához és a Machine learning-modell üzembe helyezéséhez tekintse meg a [hogyan kell üzembe helyezni és hol](how-to-deploy-and-where.md).

Ha már van olyan szolgáltatás, amelynek függőségei a környezet fájljában és a pontozási fájlban vannak telepítve, az alábbi lépéseket követve engedélyezze az adatgyűjtést:

1. Lépjen [Azure Machine learning](https://ml.azure.com).

1. Nyissa meg a munkaterületet.

1. Válassza a **központi telepítések** > a szolgáltatás > **Szerkesztés** **lehetőséget** .

   ![A szolgáltatás szerkesztése](././media/how-to-enable-data-collection/EditService.PNG)

1. A **Speciális beállítások**területen válassza a modell-adatgyűjtés **engedélyezése**lehetőséget.

    [az adatgyűjtés ![kiválasztása](./media/how-to-enable-data-collection/CheckDataCollection.png)](././media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   A szolgáltatás állapotának nyomon követéséhez a **AppInsights diagnosztika engedélyezése** lehetőséget is választhatja.

1. A módosítások alkalmazásához válassza a **frissítés** elemet.

## <a name="disable-data-collection"></a>Adatgyűjtés letiltása

Bármikor leállíthatja az adatgyűjtést. A Python-kód vagy a Azure Machine Learning használatával tiltsa le az adatgyűjtést.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>1\. lehetőség – adatgyűjtés letiltása a Azure Machine Learningban

1. Jelentkezzen be [Azure Machine Learningba](https://ml.azure.com).

1. Nyissa meg a munkaterületet.

1. Válassza a **központi telepítések** > a szolgáltatás > **Szerkesztés** **lehetőséget** .

   [![válassza a szerkesztés lehetőséget](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. A **Speciális beállítások**területen törölje a **modell adatgyűjtésének engedélyezése**beállítást.

    [az adatgyűjtési jelölőnégyzet ![törlése](./media/how-to-enable-data-collection/UncheckDataCollection.png)](././media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. A módosítás alkalmazásához válassza a **frissítés** elemet.

Ezeket a beállításokat a munkaterületen is elérheti [Azure Machine Learningban](https://ml.azure.com).

### <a name="option-2---use-python-to-disable-data-collection"></a>2\. lehetőség – az adatgyűjtés letiltása a Python használatával

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Az adatai ellenőrzése és elemzése

A blob Storage-ban összegyűjtött adatok elemzéséhez kiválaszthatja a kívánt eszközt.

### <a name="quickly-access-your-blob-data"></a>BLOB-adatai gyors elérése

1. Jelentkezzen be [Azure Machine Learningba](https://ml.azure.com).

1. Nyissa meg a munkaterületet.

1. Válassza a **Storage** lehetőséget.

    [![válassza a tárolás lehetőséget](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Kövesse a blob kimeneti adatelérési útját a következő szintaxissal:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>A modell adatai elemzése Power BI használatával

1. Töltse le és nyissa meg [Power bi Desktop](https://www.powerbi.com).

1. Válassza **az adatlekérdezés** lehetőséget, és válassza az [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources)lehetőséget.

    [![Power BI blob beállítása](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Adja meg a Storage-fiók nevét, és adja meg a Storage-kulcsát. Ezt az információt a blobban található **beállítások** > **hozzáférési kulcsok** lehetőség kiválasztásával érheti el.

1. Válassza ki a **modell** adattárolót, és válassza a **Szerkesztés**lehetőséget.

    [![Power BI-navigátor](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. A lekérdezés-szerkesztőben kattintson a **Name (név** ) oszlopban a Storage-fiók hozzáadása lehetőségre.

1. Adja meg a modell elérési útját a szűrőben. Ha csak egy adott évből vagy hónapból származó fájlokra szeretne rákeresni, egyszerűen bontsa ki a szűrő elérési útját. Ha például csak a márciusi adatmegjelenítést szeretné megkeresni, használja a szűrő elérési útját:

   /modeldata/\<subscriptionid >/\<resourcegroupname >/\<workspacename >/\<webszolgáltatásnév >/\<modelname >/\<modelversion >/\<megjelölése >/\<év >/3

1. A **név** értékek alapján szűrheti a megfelelő adatokat. Ha a jóslatokat és a bemeneti adatokat tárolta, mindegyikhez létre kell hoznia egy lekérdezést.

1. A fájlok egyesítéséhez kattintson a **tartalom** oszlop fejléce melletti lefelé mutató dupla nyílra.

    [![Power BI tartalom](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Kattintson az **OK** gombra. Az adatelőre betöltött sorok.

    [![Power BI fájlok egyesítése](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Válassza **a Bezárás és alkalmaz**lehetőséget.

1. Ha hozzáadta a bemeneteket és az előrejelzéseket, a táblákat a rendszer automatikusan **kérelemazonosító** -értékek alapján rendezi.

1. Megkezdheti az egyéni jelentések összeállítását a modell adatain.

### <a name="analyze-model-data-using-azure-databricks"></a>A modell adatai elemzése Azure Databricks használatával

1. Hozzon létre egy [Azure Databricks munkaterületet](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

1. Nyissa meg a Databricks-munkaterületet.

1. A Databricks munkaterületen válassza az **adatok feltöltése**lehetőséget.

    [![az Databricks-feltöltési lehetőség kiválasztása](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Válassza az **új tábla létrehozása** lehetőséget, és válassza ki az **egyéb adatforrásokat** > **Azure Blob Storage** > **CREATE TABLE in notebook**.

    [![Databricks tábla létrehozása](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Frissítse az adatai helyét. Például:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks beállítása](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Az adatai megtekintéséhez és elemzéséhez kövesse a sablon lépéseit.
