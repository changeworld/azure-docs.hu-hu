---
title: Várakozási tevékenység Azure Data Factory
description: A várakozási tevékenység szünetelteti a folyamat végrehajtását a megadott időszakra vonatkozóan.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: f9dd53fded06eec169219d00993620a0f2aa2bf0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678238"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Várakozási tevékenység végrehajtása a Azure Data Factoryban
Ha Wait tevékenységet használ egy folyamatban, akkor a folyamat kivárja a megadott időtartamot, és csak az után folytatja a további tevékenységek futtatását. 

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
név | A `Wait` tevékenység neve. | Sztring | Igen
type | **Várakozási**értékre kell beállítani. | Sztring | Igen
waitTimeInSeconds | Azon másodpercek száma, ameddig a folyamat várakozik a feldolgozás folytatása előtt. | Egész szám | Igen

## <a name="example"></a>Példa

> [!NOTE]
> Ez a szakasz JSON-definíciókat és PowerShell-parancsokat tartalmaz a folyamat futtatásához. A Data Factory-adatfolyamatok Azure PowerShell és JSON-definíciók használatával történő létrehozásával kapcsolatos részletes útmutatásért lásd [: oktatóanyag: az adatfeldolgozó létrehozása a Azure PowerShell használatával](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Várakozási tevékenységgel rendelkező folyamat
Ebben a példában a folyamat két tevékenységgel rendelkezik: **eddig** és **várni**. A várakozási tevékenység úgy van konfigurálva, hogy várjon egy másodpercig. A folyamat egy ciklusban futtatja a webes tevékenységet egy másodperces várakozási idővel az egyes futtatások között. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>További lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
