---
title: Exportálja, vagy a munkaterület adatainak törlése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan exportálhatja vagy törölheti a munkaterületet a Azure Machine Learning Studio, CLI, SDK és hitelesített REST API-k használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 4abef0146b4bf0cfaa254d196b0ca68f0d8ac883
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218291"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportálja, vagy a Machine Learning szolgáltatás munkaterület adatainak törlése

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure Machine Learning exportál, vagy törölje a munkaterület adatainak hitelesített REST API-val. Ez a cikk bemutatja, hogyan.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Munkaterület adatfelügyelet

A Azure Machine Learning által tárolt termékbeli adatok a Azure Machine Learning Studio, a CLI, az SDK és a hitelesített REST API-k használatával exportálhatók és törlésre használhatók. Az adatvédelem az Azure Portalon keresztül elérhető telemetriai adatokat. 

Azure Machine Learning a személyes adatok a futtatási előzmények dokumentumaiban lévő felhasználói adatokból és a szolgáltatással kapcsolatos egyes felhasználói interakciók telemetria állnak.

## <a name="delete-workspace-data-with-the-rest-api"></a>A REST API-val a munkaterület adatainak törlése

Annak érdekében, hogy az adatok törléséhez a következő API-hívások a HTTP DELETE művelet is végezhető. Ezek a kérelemben `Authorization: Bearer <arm-token>` fejléctel rendelkeznek, ahol a `<arm-token>` az `https://management.core.windows.net/` végpont HRE hozzáférési jogkivonata.  

Ha meg szeretné tudni, hogyan kérheti le ezt a tokent, és hogyan hívhat meg Azure-végpontokat, tekintse meg a további tudnivalók az [ml-erőforrások és az](how-to-manage-rest.md) [Azure REST API](https://docs.microsoft.com/rest/api/azure/)  

Az alábbi példákban cserélje le a {} szövegét a társított erőforrást meghatározó példányok neveire.

### <a name="delete-an-entire-workspace"></a>Egy teljes munkaterület törlése

Ez a hívás használatával egy teljes munkaterület törlése.  
> [!WARNING]
> Összes adat törlődik, és a munkaterület többé nem lesz használható.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Modell törlése

Ez a hívás segítségével modelleket és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Egyéni modellek törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Eszközök törlése

Ez a hívás használja az eszközök és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Egyes eszközök törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Rendszerképek törlése

Ez a hívás segítségével képeket és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Az egyéni lemezképek törlése:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Szolgáltatások törlése

Ez a hívás használatával services és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Az egyes szolgáltatások is törölve:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>A REST API-val szolgáltatás az adatok exportálása

Annak érdekében, hogy az adatok exportálása a következő API-hívások a HTTP GET művelet végrehajtásával lehessen elvégezni. Ezek a kérelemben `Authorization: Bearer <arm-token>` fejléctel rendelkeznek, ahol `<arm-token>` a végpont HRE hozzáférési jogkivonata `https://management.core.windows.net/`  

Ha meg szeretné tudni, hogyan kérheti le ezt a tokent, és hogyan hívhat meg Azure-végpontokat, tekintse meg a további tudnivalók az [ml-erőforrások és az](how-to-manage-rest.md) [Azure REST API dokumentáció](https://docs.microsoft.com/rest/api/azure/)   

Az alábbi példákban cserélje le a {} szövegét a társított erőforrást meghatározó példányok neveire.

### <a name="export-workspace-information"></a>Munkaterületek adatainak exportálása

Ez a hívás használatával minden munkaterületek listájának lekérése:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

Által egy adott munkaterület információ szerezhető be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Számítási adatainak exportálása

Az összes számítási munkaterülethez csatlakoztatott tárolók lekérheti:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

Egyetlen számítási célt információ kérhetők le:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

### <a name="export-run-history-data"></a>Exportálás futtatási előzményadatokat

Ez a hívás használatával minden kísérletek és az adataik listájának lekérése:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Egy adott kísérletet az összes futó szerezhető be:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Futtatási előzmények, elemek lekérheti:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

A kísérlet összes futtatási metrikák szerezhető be:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Egyszeri futtatás metrika szerezhető be:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}

### <a name="export-artifacts"></a>Összetevők exportálása

Ez a hívás használatával összetevők és az elérési utak listájának lekérése:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}

### <a name="export-notifications"></a>Értesítések exportálása

Ez a hívás használatával tárolt feladatok listájának lekérése:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Egyetlen feladat értesítéseire való regisztrációt szerezhető be:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Adattárak exportálása

Ez a hívás használatával adattárak listáját:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Egyes adattárak szerezhető be:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Modellek exportálása

Ez a hívás segítségével modelleket és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Egyéni modellek szerezhető be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Eszközök exportálása

Ez a hívás használja az eszközök és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Egyes eszközök által szerezhető be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Képek exportálása

Ez a hívás segítségével képeket és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Egyéni rendszerképek szerezhető be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Exportálás szolgáltatása

Ez a hívás használatával services és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Egyes szolgáltatások szerezhető be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

### <a name="export-pipeline-experiments"></a>Folyamat kísérletek exportálása

Az egyes kísérletek szerezhető be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Folyamat Gráfok exportálása

Az egyes diagramok szerezhető be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Folyamat modulok exportálása

Modulok által szerezhető be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Folyamat-sablonok exportálása

Sablonok szerezhető be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Folyamat adatforrások exportálása

Adatforrások szerezhető be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Eszközök törlése a tervezőben

A tervezőben, ahol létrehozta a kísérletet, töröljön egyedi eszközöket:

1. Ugrás a tervezőbe

    ![Eszközök törlése](./media/how-to-export-delete-data/delete-experiment.png)

1. A listából válassza ki a törölni kívánt folyamathoz tartozó egyéni piszkozatot.

1. Válassza a **Törlés** elemet.

### <a name="delete-datasets-in-the-designer"></a>Adatkészletek törlése a tervezőben

A tervezőben lévő adatkészletek törléséhez használja a Azure Portal vagy Storage Explorer a csatlakoztatott Storage-fiókok eléréséhez, és ott törölje az adatkészleteket. Az adatkészletek tervezőben való regisztrációjának törlése csak a tárolóban lévő hivatkozási pontot távolítja el.

## <a name="export-data-in-the-designer"></a>Adatexportálás a tervezőben

A tervezőben, amelyben létrehozta a kísérletet, exportálja a hozzáadott adatait:

1. A bal oldalon válassza az **adatkészletek**lehetőséget.

1. Válassza ki az exportálandó adatkészletet a listából.

    ![Az adatletöltés](./media/how-to-export-delete-data/unregister-dataset.png)
