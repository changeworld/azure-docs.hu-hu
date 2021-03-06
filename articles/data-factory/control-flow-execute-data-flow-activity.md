---
title: Adatfolyam-tevékenység
description: Az adatfolyamatok végrehajtása egy adatfeldolgozó-folyamaton belül.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 01/02/2020
ms.openlocfilehash: d0b9c59852175b91b4bf799a366ae5124fa0ae42
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644790"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Adatfolyam-tevékenység Azure Data Factory

Az adatfolyam tevékenységgel átalakíthatja és áthelyezheti az adatait a leképezési adatfolyamatok használatával. Ha még nem ismeri az adatfolyamatokat, tekintse meg az [adatáramlás leképezése – áttekintés](concepts-data-flow-overview.md) című témakört.

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
adatfolyam | A végrehajtandó adatfolyamra mutató hivatkozás | DataFlowReference | Igen
integrationRuntime | Az a számítási környezet, amelyen az adatfolyam fut. Ha nincs megadva, a rendszer az Azure Integration Runtime automatikus feloldását fogja használni | IntegrationRuntimeReference | Nem
számítás. coreCount | A Spark-fürtben használt magok száma. Csak akkor adható meg, ha az Azure Integration Runtime automatikus feloldása használatban van | 8, 16, 32, 48, 80, 144, 272 | Nem
számítás. computeType | A Spark-fürtben használt számítási típus. Csak akkor adható meg, ha az Azure Integration Runtime automatikus feloldása használatban van | "Általános", "ComputeOptimized", "MemoryOptimized" | Nem
előkészítés. linkedService | Ha SQL DW-forrást vagy-fogadót használ, a rendszer a alapszintű előkészítéshez használt Storage-fiókot használja. | Linkedservicereference sématulajdonsággal | Csak akkor, ha az adatfolyam beolvas vagy ír egy SQL DW-t
előkészítés. folderPath | Ha SQL DW-forrást vagy-fogadót használ, a mappa elérési útja a blob Storage-fiókban | Sztring | Csak akkor, ha az adatfolyam beolvas vagy ír egy SQL DW-t

![Adatfolyam végrehajtása](media/data-flow/activity-data-flow.png "Adatfolyam végrehajtása")

### <a name="data-flow-integration-runtime"></a>Adatfolyam-integrációs modul

Válassza ki, hogy melyik Integration Runtime szeretné használni az adatfolyam tevékenységének végrehajtásához. Alapértelmezés szerint a Data Factory az Azure Integration Runtime automatikus feloldását fogja használni négy munkavégző maggal, és nincs élettartam (TTL). Ez az IR általános célú számítási típussal rendelkezik, és ugyanabban a régióban fut, mint a gyár. Létrehozhat saját Azure-integrációs modulokat, amelyek meghatározott régiókat, számítási típusokat, alapszámokat és ÉLETTARTAMot határoznak meg az adatfolyam tevékenységének végrehajtásához.

A folyamatok végrehajtásához a fürt egy olyan fürt, amely a végrehajtás megkezdése előtt több percet vesz igénybe. Ha nem ad meg TTL-értéket, a rendszer ezt az indítási időt igényli minden folyamat futtatásához. Ha a TTL értéket adja meg, a meleg fürt a legutóbbi végrehajtás után megadott időre aktív marad, ami rövidebb indítási időt eredményez. Ha például 60 perc ÉLETTARTAMa van, és óránként egyszer futtat egy adatfolyamot, a fürtön marad aktív. További információ: [Azure Integration Runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> Az adatfolyam-tevékenység Integration Runtime kiválasztása csak a folyamat *aktivált végrehajtására* vonatkozik. A folyamat hibakeresése az adatfolyamatokkal a hibakeresési munkamenetben megadott fürtön fut.

### <a name="polybase"></a>PolyBase

Ha egy Azure SQL Data Warehouse fogadóként vagy forrásként használ, ki kell választania egy átmeneti helyet a Batch-köteg betöltéséhez. A Base lehetővé teszi a kötegek tömeges betöltését az adatsorok egymásba helyezése helyett. A Base drasztikusan csökkenti a betöltési időt az SQL DW-ben.

## <a name="parameterizing-data-flows"></a>Parameterizing-adatfolyamok

### <a name="parameterized-datasets"></a>Paraméteres adatkészletek

Ha az adatfolyam paraméteres adatkészleteket használ, állítsa be a paraméterek értékét a **Beállítások** lapon.

![Adatfolyam paramétereinek végrehajtása](media/data-flow/params.png "Paraméterek")

### <a name="parameterized-data-flows"></a>Paraméteres adatfolyamatok

Ha az adatfolyam paraméteres, állítsa be az adatfolyam paramétereinek dinamikus értékeit a **Parameters (paraméterek** ) lapon. Az ADF-folyamat kifejezésének nyelvét (csak karakterlánc-típusok esetében) vagy az adatfolyam kifejezésének nyelvét használhatja dinamikus vagy literális paraméterérték hozzárendeléséhez. További információ: adatfolyam- [Paraméterek](parameters-data-flow.md).

![Példa az adatforgalom paraméterének végrehajtására](media/data-flow/parameter-example.png "Példa paraméterre")

### <a name="parameterized-compute-properties"></a>Paraméteres számítási tulajdonságok.

Ha az Azure Integration Runtime automatikus feloldása és a számítási. coreCount és számítások értékeit adja meg, parametrizálja az alapvető darabszámot vagy a számítási típust.

![Példa az adatforgalom paraméterének végrehajtására](media/data-flow/parameterize-compute.png "Példa paraméterre")

## <a name="pipeline-debug-of-data-flow-activity"></a>Adatáramlási tevékenység folyamatának hibakeresése

Egy adatáramlási tevékenységgel futtatott hibakeresési folyamat végrehajtásához az adatfolyam-hibakeresési módot a felső sávon található **adatfolyam-hibakeresési** csúszka használatával kell bekapcsolni. A hibakeresési mód lehetővé teszi az adatfolyamok aktív Spark-fürtön való futtatását. További információ: [hibakeresési mód](concepts-data-flow-debug-mode.md).

![Hibakeresés gomb](media/data-flow/debugbutton.png "Hibakeresés gomb")

A hibakeresési folyamat az aktív hibakeresési fürtön fut, nem az adatáramlási tevékenység beállításaiban megadott integrációs futtatókörnyezeti környezettel. A hibakeresési mód indításakor kiválaszthatja a hibakeresés számítási környezetét.

## <a name="monitoring-the-data-flow-activity"></a>Az adatfolyam tevékenységének figyelése

Az adatfolyam-tevékenység speciális figyelési felülettel rendelkezik, ahol megtekintheti a particionálást, a szakasz időpontját és az adatvonal-információkat. Nyissa meg a figyelés panelt a **műveletek**területen található szemüvegek ikon használatával. További információ: [az adatfolyamatok figyelése](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Adatfolyam-tevékenységek eredményeinek használata egy későbbi tevékenységben

Az adatfolyam tevékenység az egyes fogadók számára írt sorok számát és az egyes forrásokból beolvasott sorokra vonatkozó mérőszámokat jeleníti meg. Ezek az eredmények a tevékenység futtatási eredményének `output` szakaszában lesznek visszaadva. A visszaadott metrikák az alábbi JSON formátumban jelennek meg.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Ha például a "sink1" nevű fogadóba írt sorok számát szeretné lekérni egy "dataflowActivity" nevű tevékenységben, használja a `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`.

Ha le szeretné kérni a fogadóban használt "source1" nevű forrásból beolvasott sorok számát, használja a `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`.

> [!NOTE]
> Ha a fogadó nulla sorból áll, akkor nem jelenik meg a mérőszámokban. A létezés ellenőrizhető a `contains` függvény használatával. `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` például megvizsgálhatja, hogy a sorok sink1-e.

## <a name="next-steps"></a>Következő lépések

Lásd: Data Factory által támogatott vezérlési flow-tevékenységek: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
