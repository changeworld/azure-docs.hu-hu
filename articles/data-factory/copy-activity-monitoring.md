---
title: Másolási tevékenység figyelése
description: Tudnivalók a másolási tevékenységek végrehajtásának figyeléséről Azure Data Factoryban.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 6494352bf957af83b45488493bf12a094c730c09
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79125757"
---
# <a name="monitor-copy-activity"></a>Másolási tevékenység figyelése

Ez a cikk azt ismerteti, hogyan lehet figyelni a másolási tevékenységek végrehajtását Azure Data Factoryban. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="monitor-visually"></a>Vizuális megfigyelés

Miután létrehozta és közzétett egy folyamatot Azure Data Factoryban, társíthatja egy triggerhez, vagy manuálisan elindíthatja az ad hoc futtatást. Az összes folyamatot a Azure Data Factory felhasználói élményben natív módon figyelheti. Ismerkedjen meg a Azure Data Factory monitorozással a [vizuálisan figyelő Azure Data Factory](monitor-visually.md).

A másolási tevékenység futtatásának figyeléséhez nyissa meg a következőt: **& monitor** felhasználói felületét. A **figyelés** lapon megjelenik a folyamat-futtatások listája, és kattintson a **folyamat neve** hivatkozásra a folyamat futtatása során futó tevékenységek listájának eléréséhez.

![Másolási tevékenység futtatásának figyelése](./media/copy-activity-overview/monitor-pipeline-run.png)

Ezen a szinten megtekintheti a másolási tevékenység bemenetét, kimenetét és hibáit (ha a másolási tevékenység sikertelen lesz), valamint a statisztikát, például az időtartamot/állapotot. A másolási tevékenység neve melletti **részletek** gombra kattintva részletes információkat adhat meg a másolási tevékenység végrehajtásáról. 

![Másolási tevékenység futtatásának figyelése](./media/copy-activity-overview/monitor-copy-activity-run.png)

Ebben a grafikus figyelési nézetben a Azure Data Factory megadja a másolási tevékenység végrehajtásával kapcsolatos információkat, beleértve az olvasási/írási kötetet, a forrásról a fogadóba másolt fájlok számát/sorait, az átviteli sebességet, a másolási forgatókönyvre alkalmazott konfigurációkat, a másolási tevékenység lépéseit pedig a megfelelő időtartamokkal és részletekkel együtt. Tekintse meg [ezt a táblázatot](#monitor-programmatically) minden lehetséges metrika és részletes leírása alapján. 

Bizonyos helyzetekben, amikor másolási tevékenységet futtat Data Factoryban, a másolási tevékenység figyelése nézet tetején a **"Performance tuning tippek"** láthatók a példában látható módon. A tippekből megtudhatja, hogy az ADF milyen szűk keresztmetszetet azonosít az adott másolási futtatáshoz, és javaslatot tesz arra, hogy mit kell módosítani a másolási teljesítmény növelése érdekében. További információ az [automatikus teljesítmény-hangolási tippekről](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

Az alsó **végrehajtási adatok és időtartamok** a másolási tevékenység lépéseit ismertetik, ami különösen hasznos a másolási teljesítmény hibaelhárításához. A másolási Futtatás szűk keresztmetszete a leghosszabb időtartamú. Tekintse meg a [másolási tevékenység teljesítményének hibaelhárítása](copy-activity-performance-troubleshooting.md) című témakört, amely az egyes szakaszok és a részletes hibaelhárítási útmutatót mutatja be.

**Példa: másolás az Amazon S3-ból Azure Data Lake Storage Gen2**

![A másolási tevékenység futtatási részleteinek figyelése](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Programozott figyelése

A másolási tevékenység végrehajtási részleteit és a teljesítmény jellemzőit a rendszer a **másolási tevékenység futtatási eredmény** > **kimenet** szakaszában is visszaadja, amely a felhasználói felület figyelési nézetének megjelenítésére szolgál. A következő lista az esetleg visszaadott tulajdonságok teljes listáját tartalmazza. Csak a másolási forgatókönyvre vonatkozó tulajdonságokat fogja látni. További információ a tevékenységek figyeléséről általában programozott módon: Azure-beli adat- [előállító programozott figyelése](monitor-programmatically.md).

| Tulajdonság neve  | Leírás | Kimeneti egység |
|:--- |:--- |:--- |
| dataRead | A forrásból beolvasott adatok tényleges mennyisége. | Int64 érték bájtban |
| dataWritten | A fogadóba írt/elkötelezett adatok tényleges csatlakoztatása. A méret különbözhet a `dataRead` méretétől, mivel az egyes adattár az adatok tárolási módját mutatja. | Int64 érték bájtban |
| filesRead | A fájl alapú forrásból beolvasott fájlok száma. | Int64 típusú érték (egység) |
| filesWritten | A fájl alapú fogadóba írt/véglegesített fájlok száma. | Int64 típusú érték (egység) |
| sourcePeakConnections | A másolási tevékenység futtatása során a forrás adattárban létesített egyidejű kapcsolatok maximális száma. | Int64 típusú érték (egység) |
| sinkPeakConnections | A fogadó adattárhoz a másolási tevékenység futtatása során létesített egyidejű kapcsolatok maximális száma. | Int64 típusú érték (egység) |
| rowsRead | A forrásból beolvasott sorok száma (bináris másolás esetén nem alkalmazható). | Int64 típusú érték (egység) |
| rowsCopied | A fogadóba másolt sorok száma (bináris másolás esetén nem alkalmazható). | Int64 típusú érték (egység) |
| rowsSkipped | A kihagyott inkompatibilis sorok száma. A nem kompatibilis sorok kihagyását a `enableSkipIncompatibleRow` True értékre állításával engedélyezheti. | Int64 típusú érték (egység) |
| copyDuration | A másolás futtatásának időtartama. | Int32 érték másodpercben |
| Átviteli sebesség | Adatátviteli sebesség. | Lebegőpontos szám (Kbit/s) |
| sourcePeakConnections | A másolási tevékenység futtatása során a forrás adattárban létesített egyidejű kapcsolatok maximális száma. | Int32 érték (nincs egység) |
| sinkPeakConnections| A fogadó adattárhoz a másolási tevékenység futtatása során létesített egyidejű kapcsolatok maximális száma.| Int32 érték (nincs egység) |
| sqlDwPolyBase | Azt jelzi, hogy a rendszer az adatok másolásakor használja-e a SQL Data Warehouse. | Logikai |
| redshiftUnload | Azt jelzi, hogy a rendszer az ELTÁVOLÍTÁSt használja-e az adatok Vöröseltolódásból történő másolásakor. | Logikai |
| hdfsDistcp | Azt határozza meg, hogy a rendszer DistCp használ-e az adatok HDFS-ből való másolásakor. | Logikai |
| effectiveIntegrationRuntime | A tevékenység futtatásához használt integrációs modul (IR) vagy futtatókörnyezet a következő formátumban: `<IR name> (<region if it's Azure IR>)`. | Szöveg (karakterlánc) |
| usedDataIntegrationUnits | A hatékony integrációs adategységek másolása során. | Int32 érték |
| usedParallelCopies | A hatékony parallelCopies másolása során. | Int32 érték |
| redirectRowPath | A `redirectIncompatibleRowSettings` tulajdonságban konfigurált blob Storage-beli kihagyott inkompatibilis sorok naplójának elérési útja. Lásd: [hibatűrés](copy-activity-overview.md#fault-tolerance). | Szöveg (karakterlánc) |
| executionDetails | További részletek a másolási tevékenység lépésein, valamint a megfelelő lépéseken, időtartamokon, konfigurációkon stb. Nem javasoljuk, hogy elemezze ezt a szakaszt, mert megváltozhat. Ha szeretné jobban megismerni, hogy miként segíti a másolási teljesítmény megismerését és hibakeresését, tekintse meg a [vizuális figyelés](#monitor-visually) című szakaszt. | Tömb |
| perfRecommendation | Teljesítmény-finomhangolási tippek másolása. A részletekért lásd a [teljesítmény-hangolási tippeket](copy-activity-performance-troubleshooting.md#performance-tuning-tips) . | Tömb |

**Példa**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Következő lépések
A másolási tevékenység egyéb cikkekben talál:

\- [másolási tevékenység áttekintése](copy-activity-overview.md)

a [másolási tevékenység teljesítményének](copy-activity-performance.md) \-