---
title: Modul Twin – Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurálás külön modulon keresztül.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5c23b9ef280a4a4e3458d279ecf060d2e3d50295
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992144"
---
# <a name="module-twin-json-schema"></a>Modul Twin JSON-sémája

A Event Grid on IoT Edge integrálódik a IoT Edge ökoszisztémával, és támogatja a témakörök és előfizetések létrehozását a Twin modulon keresztül. Azt is jelenti, hogy az összes témakör és esemény-előfizetés aktuális állapota a (z) Twin modul jelentett tulajdonságaira mutat.

> [!WARNING]
> A IoT Edge ökoszisztémájának korlátai miatt a következő JSON-példa összes tömb eleme JSON-karakterláncként lett kódolva. Lásd a következő példában szereplő `EventSubscription.Filter.EventTypes` és `EventSubscription.Filter.AdvancedFilters` kulcsokat.

## <a name="desired-properties-json"></a>Kívánt tulajdonságok JSON

* A témakörök szakaszban szereplő egyes kulcs-érték párok értéke pontosan ugyanazt a JSON-sémát tartalmazza, amelyet az API-hoz `Topic.Properties` használ a témakörök létrehozásakor.
* A **EventSubscriptions** szakaszban szereplő egyes kulcs-érték párok értéke pontosan ugyanazt a JSON-sémát tartalmazza, amelyet az API `EventSubscription.Properties` használ a témakörök létrehozásakor.
* Ha törölni szeretne egy témakört, állítsa annak értékét `null`re a kívánt tulajdonságokban.
* Az esemény-előfizetések a kívánt tulajdonságok használatával történő törlése nem támogatott.

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    }
}
```

## <a name="reported-properties-json"></a>Jelentett tulajdonságok JSON

A modul következő részében található jelentett tulajdonságok szakasz az alábbi információkat tartalmazza:

* A modul tárolójában található témakörök és előfizetések készlete
* A kívánt témakörök/esemény-előfizetések létrehozásakor előforduló hibák
* Minden rendszerindítási hiba (például a kívánt tulajdonságok JSON-elemzése sikertelen volt)

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    },
    "errors": {
        "bootupMessage": "",
        "bootupException": "",
        "topics": {},
        "eventSubscriptions": {
            "twinTopic1EventGridUserTopicSub": "HttpStatusCode='BadRequest' ErrorCode='InvalidDestination' Message='EventSubscription.Properties.Destination failed validation. Reason: EndpointUrl must target the /api/events API of Azure Event Grid in the cloud..'"
        }
    }
}
```
