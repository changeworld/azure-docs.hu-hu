---
title: Szűrők létrehozása az Azure Media Services .NET SDK-val
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, így az ügyfél használhatja őket stream konkrét szakaszokra datového proudu. A Media Services dinamikus jegyzékek érdekében, ami a szelektív hoz létre.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: cenkdin
ms.openlocfilehash: c60b223f91a151bf63cabc5e95816f2545022503
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016608"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Szűrők létrehozása Media Services .NET SDK-val 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

A 2,17-es verziótól kezdődően a Media Services lehetővé teszi az eszközökhöz tartozó szűrők megadását. Ezek a szűrők olyan kiszolgálóoldali szabályok, amelyek lehetővé teszik az ügyfelek számára a következő műveleteket: csak a videó egy szakaszának lejátszása (a teljes videó lejátszása helyett), vagy adja meg az ügyfél által kezelhető hang-és videó-kiadatások egy részhalmazát (a az eszközhöz társított összes kiadatás. Az eszközök szűrése az ügyfél által a megadott szűrő (k) alapján a videó streamre való továbbítására szolgáló **dinamikus jegyzékfájlon**keresztül érhető el.

A szűrőkkel és a dinamikus Jegyzéktel kapcsolatos részletesebb információkért lásd: a [dinamikus jegyzékfájlok áttekintése](media-services-dynamic-manifest-overview.md).

Ez a cikk bemutatja, hogyan használhatók a Media Services .NET SDK szűrők létrehozásához, frissítéséhez és törléséhez. 

Megjegyzés: Ha frissít egy szűrőt, akár két percet is igénybe vehet, amíg a streaming-végpont frissíti a szabályokat. Ha a tartalom a szűrő használatával lett kézbesítve (és gyorsítótárazva van a proxys és a CDN-gyorsítótárban), akkor a szűrő frissítése a Player meghibásodását eredményezheti. A szűrő frissítése után mindig törölje a gyorsítótárat. Ha ez a beállítás nem lehetséges, érdemes lehet egy másik szűrőt használni. 

## <a name="types-used-to-create-filters"></a>Szűrők létrehozásához használt típusok
Szűrők létrehozásakor a következő típusok használatosak: 

* **IStreamingFilter**.  Ez a típus a következő REST API szűrőn [](https://docs.microsoft.com/rest/api/media/operations/filter) alapul.
* **IStreamingAssetFilter**. Ez a típus a következő REST API [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter) alapul.
* **PresentationTimeRange**. Ez a típus a következő REST API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange) alapul.
* **FilterTrackSelectStatement** és **IFilterTrackPropertyCondition**. Ezek a típusok a következő REST API-k [FilterTrackSelect és FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect) alapulnak.

## <a name="createupdatereaddelete-global-filters"></a>Globális szűrők létrehozása/frissítése/olvasása/törlése
A következő kód bemutatja, hogyan használható a .NET az eszközök szűrőinek létrehozásához, frissítéséhez, olvasásához és törléséhez.

```csharp
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();
```

## <a name="createupdatereaddelete-asset-filters"></a>Eszköz-szűrők létrehozása/frissítése/olvasása/törlése
A következő kód bemutatja, hogyan használható a .NET az eszközök szűrőinek létrehozásához, frissítéséhez, olvasásához és törléséhez.

```csharp
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();

```


## <a name="build-streaming-urls-that-use-filters"></a>Szűrőket használó streaming URL-címek összeállítása
Az eszközök közzétételével és továbbításával kapcsolatos információkért lásd: [tartalom továbbítása az ügyfeleknek áttekintés](media-services-deliver-content-overview.md).

Az alábbi példák bemutatják, hogyan adhat hozzá szűrőket a folyamatos átviteli URL-címekhez.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) v4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) v3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Dinamikus jegyzékfájlok áttekintése](media-services-dynamic-manifest-overview.md)

