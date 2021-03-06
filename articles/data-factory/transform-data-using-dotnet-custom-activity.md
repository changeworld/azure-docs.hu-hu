---
title: Egyéni tevékenységek használata egy folyamatban
description: Ismerje meg, hogyan hozhat létre egyéni tevékenységeket, és hogyan használhatja őket egy Azure Data Factory folyamat során.
services: data-factory
ms.service: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: 4913152125b0fafd74db575f835d53fa992b075e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260578"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Egyéni tevékenységek használata Azure Data Factory-folyamatban

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-use-custom-activities.md)
> * [Aktuális verzió](transform-data-using-dotnet-custom-activity.md)

A Azure Data Factory-folyamatokban két típusú tevékenység használható.

- Adatáthelyezési [tevékenységek](copy-activity-overview.md) a [támogatott forrás-és fogadó adattárak](copy-activity-overview.md#supported-data-stores-and-formats)közötti adatátvitel céljából.
- [Adatátalakítási tevékenységek](transform-data.md) az adatok számítási szolgáltatásokkal, például az Azure HDInsight, a Azure batch és a Azure Machine learning használatával történő átalakításához.

Ha olyan adattárból/adattárba szeretne áthelyezni, amely nem támogatja a Data Factory, illetve az adatok átalakítását vagy feldolgozását olyan módon, amelyet a Data Factory nem támogat, létrehozhat egy **egyéni tevékenységet** a saját adatáthelyezési vagy átalakítási logikával, és felhasználhatja a tevékenységet egy folyamaton belül. Az egyéni tevékenység a testreszabott kód logikáját futtatja **Azure batch** virtuális gépek készletén.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tekintse meg a következő cikkeket, ha új Azure Batch szolgáltatás:

* A Azure Batch szolgáltatás áttekintéséhez [Azure batch alapjai](../batch/batch-technical-overview.md) .
* A [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) parancsmaggal hozzon létre egy Azure batch-fiókot (vagy) [Azure Portal](../batch/batch-account-create-portal.md) a Azure batch-fiók Azure Portal használatával történő létrehozásához. A parancsmag használatával kapcsolatos részletes utasításokért lásd: a [PowerShell használata Azure batch-fiók kezeléséhez](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) .
* [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) parancsmag Azure batch készlet létrehozásához.

## <a name="azure-batch-linked-service"></a>Társított szolgáltatás Azure Batch

A következő JSON definiál egy minta Azure Batch társított szolgáltatást. Részletekért lásd: [Azure Data Factory által támogatott számítási környezetek](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 A Azure Batch társított szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [számítási társított szolgáltatások](compute-linked-services.md) című cikket.

## <a name="custom-activity"></a>Egyéni tevékenység

A következő JSON-kódrészlet egy egyszerű egyéni tevékenységgel rendelkező folyamatot határoz meg. A tevékenység definíciója a Azure Batch társított szolgáltatásra mutató hivatkozást tartalmaz.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

Ebben a példában a HelloWorld. exe a resourceLinkedService használt Azure Storage-fiók customactv2/HelloWorld mappájában tárolt egyéni alkalmazás. Az egyéni tevékenység elküldi ezt az egyéni alkalmazást a Azure Batch végrehajtásához. A parancsot lecserélheti bármely előnyben részesített alkalmazásra, amely végrehajtható a Azure Batch Pool-csomópontok cél operációs rendszerén.

A következő táblázat ismerteti a tevékenységre jellemző tulajdonságok nevét és leírását.

| Tulajdonság              | Leírás                              | Kötelező |
| :-------------------- | :--------------------------------------- | :------- |
| név                  | A folyamatban szereplő tevékenység neve     | Igen      |
| leírás           | A tevékenység működését leíró szöveg  | Nem       |
| type                  | Egyéni tevékenység esetén a tevékenység típusa **Egyéni**. | Igen      |
| linkedServiceName     | Társított szolgáltatás Azure Batch. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk.  | Igen      |
| command               | A végrehajtandó egyéni alkalmazás parancsa. Ha az alkalmazás már elérhető a Azure Batch készlet csomóponton, a resourceLinkedService és a folderPath kihagyható. Megadhatja például a `cmd /c dir`parancsot, amelyet a Windows batch-készlet csomópont natív módon támogat. | Igen      |
| resourceLinkedService | Azure Storage-beli társított szolgáltatás az egyéni alkalmazást tároló Storage-fiókhoz | nem&#42;       |
| folderPath            | Az egyéni alkalmazás mappájának és az összes függőségének elérési útja<br/><br/>Ha az almappákban tárolt függőségek vannak – vagyis a *folderPath* alatt lévő hierarchikus mappák struktúrájában – a rendszer jelenleg összefoglalja a mappa struktúráját, amikor a fájlok Azure Batchba másolódnak. Ez azt is megtörténik, hogy minden fájl egyetlen mappába van másolva, és nincs almappa. A viselkedés megkerüléséhez vegye fontolóra a fájlok tömörítését, a tömörített fájl másolását, majd a kívánt helyen lévő egyéni kóddal való kicsomagolását. | nem&#42;       |
| referenceObjects      | Meglévő társított szolgáltatások és adatkészletek tömbje. A hivatkozott társított szolgáltatásokat és adatkészleteket a rendszer JSON formátumban adja át az egyéni alkalmazásnak, így az egyéni kód hivatkozhat a Data Factory erőforrásaira | Nem       |
| extendedProperties    | Felhasználó által definiált tulajdonságok, amelyek JSON formátumban adhatók át az egyéni alkalmazásnak, így az egyéni kód további tulajdonságokat is hivatkozhat | Nem       |
| retentionTimeInDays | Az egyéni tevékenységhez elküldött fájlok megőrzési ideje. Az alapértelmezett érték 30 nap. | Nem |

&#42;A tulajdonságokat `resourceLinkedService` és `folderPath` mindkettőt meg kell adni, vagy mindkettőt el kell hagyni.

> [!NOTE]
> Ha a társított szolgáltatásokat referenceObjects egyéni tevékenységként adja át, akkor jó biztonsági gyakorlatnak kell átadnia egy Azure Key Vault engedélyezett társított szolgáltatást (mivel nem tartalmaz biztonságos karakterláncokat), és közvetlenül a kulcsból kéri le a hitelesítő adatokat a titkos név használatával. Tároló a kódból. [Itt](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) talál egy példát, amely a AKV-kompatibilis társított szolgáltatásra hivatkozik, lekéri a hitelesítő adatokat a Key Vaultból, majd a kódban hozzáfér a tárolóhoz.

## <a name="custom-activity-permissions"></a>Egyéni tevékenység engedélyei

Az egyéni tevékenység beállítja a Azure Batch automatikus felhasználói fiókot a *nem rendszergazdai hozzáférésre a feladat hatókörével* (az alapértelmezett automatikus felhasználó specifikációja). Az automatikus felhasználói fiók engedélyezési szintje nem módosítható. További információ: [feladatok futtatása a Batch felhasználói fiókjaiban | Automatikus felhasználói fiókok](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Parancsok végrehajtása

Az egyéni tevékenységek használatával közvetlenül is végrehajthat egy parancsot. A következő példa a "Echo Hello World" parancsot futtatja a cél Azure Batch készlet csomópontjain, és kiírja a kimenetet az stdout-ra.

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>Objektumok és tulajdonságok átadása

Ez a minta azt mutatja be, hogy a referenceObjects és a Extendedproperties példányt paraméterként segítségével hogyan adhat át Data Factory objektumokat és felhasználó által definiált tulajdonságokat az egyéni alkalmazáshoz.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {          
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

A tevékenység végrehajtásakor a referenceObjects és a Extendedproperties példányt paraméterként a PéldaAlkalmazás. exe ugyanazon végrehajtási mappájába telepített következő fájlokban tárolódik:

- `activity.json`

  Az egyéni tevékenység Extendedproperties példányt paraméterként és tulajdonságait tárolja.

- `linkedServices.json`

  A referenceObjects tulajdonságban definiált társított szolgáltatások tömbjét tárolja.

- `datasets.json`

  A referenceObjects tulajdonságban definiált adatkészletek tömbjét tárolja.

A következő mintakód bemutatja, hogyan érheti el a PéldaAlkalmazás. exe a szükséges információkat a JSON-fájlokból:

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Végrehajtás kimenetének beolvasása

A folyamat futtatását a következő PowerShell-parancs használatával indíthatja el:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Ha a folyamat fut, a következő parancsokkal tekintheti meg a végrehajtás kimenetét:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

Az egyéni alkalmazás **StdOut** -és **stderr** a rendszer a **adfjobs** -tárolóba menti az Azure Storage társított szolgáltatásban, amelyet a feladat GUID azonosítójának létrehozásakor definiált Azure batch társított szolgáltatáshoz. A tevékenység futtatási kimenetének részletes elérési útját a következő kódrészletben látható módon érheti el:

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Ha az StdOut. txt fájlt szeretné használni az alárendelt tevékenységekben, a "\@tevékenység (" MyCustomActivity ") kifejezésben megtekintheti az StdOut. txt fájl elérési útját. output. outputs [0]".

> [!IMPORTANT]
> - A Activity. JSON, a linkedServices. JSON és az adatkészletek. JSON a Batch feladat futásidejű mappájában tárolódik. Ebben a példában a Activity. JSON, a linkedServices. JSON és az adatkészletek. JSON fájl `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` elérési úton van tárolva. Szükség esetén külön kell megtisztítani őket.
> - A saját üzemeltetésű Integration Runtime használó társított szolgáltatások esetében a helyi Integration Runtime titkosítja a bizalmas adatokat, például a kulcsokat vagy a jelszavakat, így biztosítva, hogy a hitelesítő adatok az ügyfél által meghatározott magánhálózati környezetben maradnak. Bizonyos bizalmas mezők hiányoznak, amikor az egyéni alkalmazás kódja erre hivatkozik. Ha szükséges, használja a SecureString-t a Extendedproperties példányt paraméterként-ben a társított szolgáltatás hivatkozásának használata helyett.

## <a name="pass-outputs-to-another-activity"></a>Kimenetek továbbítása egy másik tevékenységbe

Egyéni értékeket is elküldhet az egyéni tevékenység kódjából Azure Data Factoryba. Ezt úgy teheti meg, hogy az alkalmazásból `outputs.json`ba írja őket. Data Factory átmásolja `outputs.json` tartalmát, és hozzáfűzi a tevékenység kimenetéhez a `customOutput` tulajdonság értékeként. (A méretkorlát 2 MB.) Ha az alsóbb rétegbeli tevékenységek `outputs.json` tartalmát szeretné felhasználni, az értéket a `@activity('<MyCustomActivity>').output.customOutput`kifejezés használatával szerezheti be.

## <a name="retrieve-securestring-outputs"></a>SecureString-kimenetek lekérése

A *SecureString*típusként kijelölt bizalmas tulajdonságértékek a jelen cikk néhány példájában láthatók a Data Factory felhasználói felület figyelés lapján.  A folyamat tényleges végrehajtása során azonban a *SecureString* tulajdonság a `activity.json` fájlban JSON-ként van szerializálva egyszerű szövegként. Például:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Ez a szerializálás nem igazán biztonságos, és nem biztonságos. A cél az, hogy rámutasson Data Factoryre a figyelés lapon lévő érték maszkolásához.

Ha a *SecureString* típusú tulajdonságokat egy egyéni tevékenységből szeretné elérni, olvassa el a `activity.json` fájlt, amely ugyanabba a mappába kerül, mint a. EXE, deszerializálja a JSON-t, majd elérheti a JSON-tulajdonságot (Extendedproperties példányt paraméterként = > [propertyName] = > érték).

## <a name="compare-v2-v1"></a>A v2 egyéni tevékenység és az 1. verzió (egyéni) DotNet-tevékenység összehasonlítása

A Azure Data Factory 1. verziójában egy (egyéni) DotNet-tevékenységet valósít meg egy .NET-es függvénytár-projekt létrehozásával egy olyan osztállyal, amely megvalósítja az `IDotNetActivity` felület `Execute` metódusát. A társított szolgáltatások, adatkészletek és kiterjesztett tulajdonságok egy (egyéni) DotNet-tevékenység JSON-tartalmában való átadása a végrehajtási metódusnak nagy mértékben beírt objektumokként történik. Az 1. verzió működésével kapcsolatos részletekért lásd: [(egyéni) a DotNet az 1. verzióban](v1/data-factory-use-custom-activities.md). Ennek a megvalósításnak a megvalósítása miatt az 1. verziójú DotNet-hibakód a .NET-keretrendszer 4.5.2-es verzióját célozza meg. Az 1. verziójú DotNet-tevékenységet a Windows-alapú Azure Batch Pool-csomópontokon is el kell végezni.

A Azure Data Factory v2 egyéni tevékenységben nem szükséges .NET-felületet megvalósítani. Mostantól közvetlenül futtathat parancsokat, parancsfájlokat és saját egyéni kódot, amely végrehajtható fájlként van lefordítva. A megvalósítás konfigurálásához adja meg a `Command` tulajdonságot a `folderPath` tulajdonsággal együtt. Az egyéni tevékenység feltölti a végrehajtható fájlt és annak függőségeit `folderpath` és végrehajtja a parancsot.

A (referenceObjects-ben definiált) társított szolgáltatások, adatkészletek, valamint a Data Factory v2 egyéni tevékenység JSON-adattartalmában definiált kiterjesztett tulajdonságok a végrehajtható fájl JSON-fájlként érhetők el. A szükséges tulajdonságokat egy JSON-szerializáló használatával érheti el, ahogy az a fenti PéldaAlkalmazás. exe-kódban is látható.

A Data Factory v2 egyéni tevékenységben bevezetett változások esetében megírhatja az egyéni kód logikáját az előnyben részesített nyelven, és a Azure Batch által támogatott Windows-és Linux-operációs rendszereken is végrehajthatja.

Az alábbi táblázat a Data Factory v2 egyéni tevékenység és az Data Factory 1. verzió (egyéni) DotNet-tevékenység közötti különbségeket ismerteti:

|Különbségek      | Egyéni tevékenység      | 1\. verzió (egyéni) DotNet-tevékenység      |
| ---- | ---- | ---- |
|Egyéni logika meghatározása      |Végrehajtható fájl biztosításával      |.NET DLL implementálása      |
|Az egyéni logika végrehajtási környezete      |Windows vagy Linux      |Windows (.NET-keretrendszer 4.5.2)      |
|Parancsfájlok végrehajtása      |Támogatja a parancsfájlok közvetlen végrehajtását (például "cmd/c echo Hello World") a Windows rendszerű virtuális gépen      |Implementáció szükséges a .NET DLL-ben      |
|Adatkészlet szükséges      |Optional      |A tevékenységek láncolásához és az információk továbbításához szükséges      |
|Információk átadása a tevékenységtől az egyéni logikáig      |ReferenceObjects (LinkedServices és adatkészletek) és Extendedproperties példányt paraméterként (egyéni tulajdonságok)      |Extendedproperties példányt paraméterként (egyéni tulajdonságok), bemeneti és kimeneti adatkészletek      |
|Információk beolvasása az egyéni logikában      |Elemzi a tevékenység. JSON, linkedServices. JSON és adatkészletek. JSON fájlt, amely ugyanabban a mappában található a végrehajtható fájlban.      |.NET SDK-n keresztül (.NET frame 4.5.2)      |
|Naplózás      |Írás közvetlenül az STDOUT-ba      |A naplózó implementálása a .NET DLL-ben      |

Ha az 1. verziójú (egyéni) DotNet-tevékenységhez már létezik .NET-kód, módosítania kell a kódot, hogy az az egyéni tevékenység aktuális verziójával működjön. Módosítsa a kódot a következő magas szintű irányelvek követésével:

  - Módosítsa a projektet egy .NET-osztály könyvtárából egy Console-alkalmazásra.
  - Indítsa el az alkalmazást a `Main` metódussal. A `IDotNetActivity` felület `Execute` metódusa már nem szükséges.
  - A társított szolgáltatások, adatkészletek és tevékenységek olvasása és elemzése JSON-szerializáló, és nem erős módon beírt objektumokként. Adja át a szükséges tulajdonságok értékeit a fő egyéni kód logikájának. Példaként tekintse meg az előző PéldaAlkalmazás. exe-kódot.
  - A naplózó objektum már nem támogatott. A végrehajtható fájl kimenete nyomtatható a konzolon, és az StdOut. txt fájlba lesz mentve.
  - A Microsoft. Azure. Management. DataFactories NuGet csomagra már nincs szükség.
  - Fordítsa le a kódot, töltse fel a végrehajtható fájlt és annak függőségeit az Azure Storage-ba, és adja meg az elérési utat a `folderPath` tulajdonságban.

Az Data Factory 1. verziójában leírt végpontok közötti DLL-és folyamat-minta teljes mintája az egyéni [tevékenységek Azure Data Factory-folyamatokban](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) való átírása Data Factory egyéni tevékenységként: [Data Factory egyéni tevékenység mintája](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Azure Batch automatikus méretezése

Létrehozhat egy Azure Batch készletet is az **autoscale** funkcióval. Létrehozhat például egy 0 dedikált virtuális géppel rendelkező Azure batch-készletet és egy, a függőben lévő feladatok számán alapuló autoskálázási képletet.

A minta képlet a következő viselkedést éri el: a készlet első létrehozásakor 1 virtuális géppel kezdődik. $PendingTasks metrika meghatározza a futó + aktív (várólistán lévő) állapotú feladatok számát. A képlet megkeresi a függőben lévő feladatok átlagos számát az utolsó 180 másodpercben, és ennek megfelelően beállítja a TargetDedicated. Biztosítja, hogy a TargetDedicated soha ne haladja meg a 25 virtuális gépet. Így az új feladatok elküldésekor a készlet automatikusan növekszik, és a feladatok elvégzése után a virtuális gépek egyszer sem lesznek elérhetők, és az automatikus skálázás csökkenti ezeket a virtuális gépeket. a startingNumberOfVMs és a maxNumberofVMs igényeihez igazítható.

Autoskálázási képlet:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

A részletekért lásd: [számítási csomópontok automatikus méretezése egy Azure batch készletben](../batch/batch-automatic-scaling.md) .

Ha a készlet az alapértelmezett [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)használja, a Batch szolgáltatás 15-30 percet is igénybe vehet, hogy az egyéni tevékenység futtatása előtt előkészítse a virtuális gépet. Ha a készlet eltérő autoScaleEvaluationInterval használ, a Batch szolgáltatás autoScaleEvaluationInterval + 10 percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást:

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Struktúra tevékenysége](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-tevékenység](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [Batch-végrehajtási tevékenység Machine Learning](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
