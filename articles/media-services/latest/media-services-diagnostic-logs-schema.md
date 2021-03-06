---
title: Azure Media Services diagnosztikai naplók sémái – Azure
description: Ez a cikk a Azure Media Services diagnosztikai naplók sémáit mutatja be.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 37baed076ca074c1d558af36649e90959a0034c9
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750870"
---
# <a name="diagnostic-logs-schemas"></a>Diagnosztikai naplók sémái

[Azure monitor](../../azure-monitor/overview.md) lehetővé teszi a metrikák és diagnosztikai naplók figyelését, amelyek segítenek megérteni az alkalmazások teljesítményét. Nyomon követheti Media Services diagnosztikai naplóit, és riasztásokat és értesítéseket hozhat létre az összegyűjtött metrikák és naplók számára. Naplókat küldhet az [Azure Storage](https://azure.microsoft.com/services/storage/)-ba, továbbíthatja őket az [Azure Event Hubsba](https://azure.microsoft.com/services/event-hubs/), és exportálhatja őket [log Analyticsba](https://azure.microsoft.com/services/log-analytics/), vagy külső szolgáltatásokhoz is használhatja őket.

Részletes információ: [Azure monitor metrikák](../../azure-monitor/platform/data-platform.md) és [Azure monitor diagnosztikai naplók](../../azure-monitor/platform/platform-logs-overview.md).

Ez a cikk Media Services diagnosztikai naplók sémáit ismerteti.

## <a name="top-level-diagnostic-logs-schema"></a>Legfelső szintű diagnosztikai naplók sémája

A legfelső szintű diagnosztikai naplók sémájának részletes ismertetését lásd: [támogatott szolgáltatások, sémák és kategóriák az Azure diagnosztikai naplóihoz](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Kulcs kézbesítési naplójának sémája

### <a name="properties"></a>Tulajdonságok

Ezek a tulajdonságok a Key Delivery log sémára vonatkoznak.

|Név|Leírás|
|---|---|
|keyId|A kért kulcs azonosítója.|
|KeyType|A következő értékek egyike lehet: "Clear" (nincs titkosítás), "FairPlay", "PlayReady" vagy "Widevine".|
|policyName|A házirend Azure Resource Manager neve.|
|tokenType|A jogkivonat típusa.|
|statusMessage|Az állapotjelző üzenet.|

### <a name="examples"></a>Példák

A kulcs kézbesítési kérések sémájának tulajdonságai.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="next-steps"></a>Következő lépések

[Media Services metrikák és diagnosztikai naplók figyelése](media-services-metrics-diagnostic-logs.md)
