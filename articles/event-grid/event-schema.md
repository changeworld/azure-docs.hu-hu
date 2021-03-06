---
title: Azure Event Gridi esemény sémája
description: Az összes eseményhez tartozó tulajdonságokat és sémát ismerteti. Az események öt kötelező karakterlánc-tulajdonságot és egy kötelező adatobjektumot foglalnak magukban.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 35cea2e6df311d2f4071686c21c8e4c36477abc1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244835"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Gridi esemény sémája

Ez a cikk az összes eseményhez tartozó tulajdonságokat és sémákat ismerteti. Az események öt kötelező karakterlánc-tulajdonságot és egy kötelező adatobjektumot foglalnak magukban. A tulajdonságok minden közzétevőtől származó eseményre jellemzőek. Az adatobjektumhoz az egyes közzétevők jellemző tulajdonságai tartoznak. A rendszertémakörök esetében ezek a tulajdonságok az erőforrás-szolgáltatóra jellemzőek, például az Azure Storage vagy az Azure Event Hubs.

Az eseményforrás az eseményeket egy tömbben Azure Event Gridba küldi, amely több eseményvezérelt objektummal is rendelkezhet. Amikor eseményeket küld egy Event Grid-témakörbe, a tömb legfeljebb 1 MB méretű lehet. A tömbben lévő összes esemény 64 KB-ra (általános rendelkezésre állás) vagy 1 MB-ra (előzetes verzió) korlátozódik. Ha egy esemény vagy a tömb mérete nagyobb, mint a méretkorlát, a 413-as **számú válasz túl nagy**lesz.

> [!NOTE]
> A 64 KB-ig terjedő méretű események általánosan elérhetők (GA) szolgáltatói szerződés (SLA). A legfeljebb 1 MB méretű esemény támogatása jelenleg előzetes verzióban érhető el. Az 64 KB-nál nagyobb számú esemény díja 64 KB. 

Event Grid küldi az eseményeket egy olyan tömbben lévő előfizetőknek, amely egyetlen eseménnyel rendelkezik. Ez a viselkedés a jövőben változhat.

A Event Grid eseményhez és az egyes Azure-közzétevők adattartalmahoz tartozó JSON-sémát az [Event Schema Store](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane)-ban találja.

## <a name="event-schema"></a>Eseményséma

Az alábbi példa az összes esemény-közzétevő által használt tulajdonságokat mutatja be:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Az Azure Blob Storage-eseményekhez közzétett séma például a következő:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Esemény tulajdonságai

Minden esemény a következő legfelső szintű adatértékekkel rendelkezik:

| Tulajdonság | Típus | Kötelező | Leírás |
| -------- | ---- | -------- | ----------- |
| témakör | sztring | Nem, de ha belefoglalt, akkor pontosan meg kell egyeznie a Event Grid témakör Azure Resource Manager AZONOSÍTÓjának. Ha nem szerepel, Event Grid az eseményre Pecsétel. | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Event Grid megadja ezt az értéket. |
| subject | sztring | Igen | Közzétevő által megadott elérési út az esemény tárgya számára. |
| eventType | sztring | Igen | Az eseményforrás egyik regisztrált eseménytípus. |
| eventTime | sztring | Igen | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| id | sztring | Igen | Az esemény egyedi azonosítója. |
| data | objektum | Nem | Az erőforrás-szolgáltatóhoz tartozó esemény-adatértékek. |
| dataVersion | sztring | Nem, de a rendszer üres értékkel fogja lepecsételni őket. | Az adatobjektum séma-verziója. A közzétevő határozza meg a séma verzióját. |
| metadataVersion | sztring | Nem kötelező, de ha szerepel, pontosan meg kell egyeznie a Event Grid sémával `metadataVersion` pontosan (jelenleg csak `1`). Ha nem szerepel, Event Grid az eseményre Pecsétel. | Az esemény metaadatainak séma-verziója. Event Grid a legfelső szintű tulajdonságok sémáját határozza meg. Event Grid megadja ezt az értéket. |

Az adatobjektum tulajdonságainak megismeréséhez tekintse meg az esemény forrását:

* [Azure-előfizetések (felügyeleti műveletek)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Médiaszolgáltatások](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Erőforráscsoportok (felügyeleti műveletek)](event-schema-resource-groups.md)
* [Szolgáltatásbusz](event-schema-service-bus.md)
* [Azure-jelző](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Az egyéni témakörök esetében az esemény-közzétevő határozza meg az adatobjektumot. A legfelső szintű adatnak ugyanazokkal a mezőkkel kell rendelkeznie, mint a szabványos erőforrás-definíciós események.

Az események egyéni témakörökbe való közzétételekor olyan témákat hozhat létre az eseményekhez, amelyek megkönnyítik az előfizetők számára, hogy megismerjék, hogy érdeklik-e az esemény. Az előfizetők a tulajdonos használatával szűrhetik és irányítják az eseményeket. Gondolja át, hogy hol történt az esemény, így az előfizetők az elérési út szakaszai alapján szűrhetik az útvonalat. Az elérési út lehetővé teszi az előfizetők számára az események szűk vagy széles körű szűrését. Ha például egy három szegmens elérési utat ad meg, mint a tárgy `/A/B/C`, az előfizetők az első szegmens alapján szűrhetik a `/A`, hogy az események széles körét kapják meg. Ezek az előfizetők olyan eseményeket kapnak, mint a `/A/B/C` vagy a `/A/D/E`. Más előfizetők a `/A/B` alapján szűrhetik az események szűkebb körét.

Előfordulhat, hogy a tárgya több részletet is igényel, hogy mi történt. A **Storage-fiókok** közzétevője például a tulajdonos `/blobServices/default/containers/<container-name>/blobs/<file>`t adja meg, amikor egy fájlt hozzáadnak egy tárolóhoz. Az előfizető az elérési út alapján szűrheti az `/blobServices/default/containers/testcontainer`, hogy lekérje az adott tároló összes eseményét, de a Storage-fiókban ne legyenek más tárolók. Az előfizető a `.txt` utótaggal is szűrheti vagy átirányíthatja a szöveget, hogy csak szöveges fájlokkal működjön.

## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
