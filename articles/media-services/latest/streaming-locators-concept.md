---
title: Streaming-lokátorok a Azure Media Servicesban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogy milyen a folyamatos átviteli lokátorok, és hogyan használják őket a Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 3a9568e1a0307cd1713c511ef42c065424306548
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302882"
---
# <a name="streaming-locators"></a>Streamelési lokátor

Ahhoz, hogy a kimeneti adategységben található videók elérhetők legyenek az ügyfelek számára lejátszásra, létre kell hozni egy [streamelési lokátort](https://docs.microsoft.com/rest/api/media/streaminglocators), majd streamelési URL-címeket. URL-cím létrehozásához összefűzni kell a streaming Endpoint Host nevét és a folyamatos átviteli lokátor elérési útját. Egy .NET-példáért tekintse meg a [streamelési lokátor beszerzését](stream-files-tutorial-with-api.md#get-a-streaming-locator) bemutató cikket.

Az **adatfolyam-kereső** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint az **adatfolyam-kereső** azonnal érvényes az API-hívások létrehozása után, és addig tart, amíg meg nem történik a törlés, hacsak nem konfigurálja a nem kötelező kezdési és befejezési időpontokat. 

**Adatfolyam-kereső**létrehozásakor meg kell adnia egy **eszköz** nevét és egy **streaming Policy** -nevet. További információkért tekintse át a következők témaköröket:

* [Adategységek](assets-concept.md)
* [Streamelési szabályzatok](streaming-policy-concept.md)
* [Tartalomkulcs-szabályzatok](content-key-policy-concept.md)

Megadhatja a kezdő és a záró időpontot is a folyamatos átviteli lokátorban, így a felhasználó csak a következő időpontok közötti tartalmat fogja lejátszani (például 5/1/2019 – 5/5/2019).  

## <a name="considerations"></a>Megfontolások

* A **streaming-lokátorok** nem frissíthető. 
* A DateTime típusú **adatfolyam-kereső** tulajdonságai mindig UTC formátumban jelennek meg.
* A Media Service-fiókhoz korlátozott számú szabályzatot kell terveznie, és újra fel kell használni őket a streaming-lokátorok számára, amikor ugyanazok a beállítások szükségesek. További információ: [kvóták és korlátozások](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Folyamatos átviteli lokátorok létrehozása  

### <a name="not-encrypted"></a>Nincs titkosítva

Ha a fájlt a-The-Clear (nem titkosított) formátumban szeretné továbbítani, állítsa be az előre definiált tiszta folyamatos átviteli szabályzatot: "Predefined_ClearStreamingOnly" (a .NET-ben használhatja a PredefinedStreamingPolicy. ClearStreamingOnly enumerálást).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Titkosított 

Ha titkosítania kell a tartalmat a CENC titkosítással, állítsa a szabályzatot "Predefined_MultiDrmCencStreaming" értékre. A Widevine-titkosítás egy kötőjel-adatfolyamra lesz alkalmazva, és a PlayReady zökkenőmentes. A kulcs a konfigurált DRM-licencek alapján lesz továbbítva a lejátszási ügyfeleknek.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Ha a HLS streamet a CBCS (FairPlay) használatával is titkosítani szeretné, használja a következőt: "Predefined_MultiDrmStreaming".

> [!NOTE]
> A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="associate-filters-with-streaming-locators"></a>Szűrők hozzárendelése adatfolyam-keresővel

Lásd [: szűrők: társítsa a folyamatos átviteli lokátorokat](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Szűrés, rendelés, oldal adatfolyam-kereső entitásai

Lásd: [Media Services entitások szűrése, rendezése és lapozása](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>A streaming-lokátorok listázása az eszköz neve szerint

A következő műveletek végrehajtásával lekérheti a streaming-keresőket a társított eszköz neve alapján:

|Nyelv|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|parancssori felület|[az AMS Asset List-streaming-Locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Lásd még

* [Adategységek](assets-concept.md)
* [Streamelési szabályzatok](streaming-policy-concept.md)
* [Tartalomkulcs-szabályzatok](content-key-policy-concept.md)
* [Oktatóanyag: videók feltöltése, kódolása és továbbítása a .NET-tel](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Következő lépések

[Folyamatos átviteli lokátor létrehozása és URL-címek összeállítása](create-streaming-locator-build-url.md)
