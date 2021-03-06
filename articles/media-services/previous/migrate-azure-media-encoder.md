---
title: Migrálás Azure Media Encoderról Media Encoder Standardra | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan lehet áttelepíteni a Azure Media Encoderról a Media Encoder Standard Media Processor szolgáltatásba.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: f8fe1b13db6473e80f0d7cdc638b775a0c8062c7
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513501"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Áttelepítés Azure Media Encoderról Media Encoder Standard

Ez a cikk azt ismerteti, hogyan lehet áttelepíteni a régi Azure Media Encoder (AME) adathordozó-processzorról (amely kivonásra kerül) a Media Encoder Standard Media Processor szolgáltatásba. A nyugdíjazási dátumokért tekintse meg ezt a [régi összetevőket](legacy-components.md) ismertető témakört.

A fájlok az AME-val való kódolásakor az ügyfelek általában egy elnevezett előre beállított karakterláncot (például `H264 Adaptive Bitrate MP4 Set 1080p`) használnak. A Migrálás érdekében a kódot frissíteni kell, hogy a **Media Encoder standard** adathordozó-processzort a ame helyett használja, és az egyik egyenértékű [rendszer-beállításkészlet](media-services-mes-presets-overview.md) , például a `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Áttelepítés Media Encoder Standardre

Az alábbi példa egy C# tipikus mintakód, amely az örökölt adathordozó-processzort használja. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

A Media Encoder Standardt használó frissített verzió.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Speciális forgatókönyvek 

Ha a saját sémája alapján hozta létre saját kódolási beállításkészletét az AME-hoz, akkor a [Media Encoder standard egyenértékű sémával](media-services-mes-schema.md)rendelkezik. Ha kérdése van, hogy miként képezhető le a régebbi beállítások az új kódolóhoz, lépjen kapcsolatba velünk mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Ismert különbségek 

Media Encoder Standard robusztusabb, megbízhatóbb, jobb teljesítményű, és jobb minőségű kimenetet eredményez, mint az örökölt AME-kódoló. Továbbá: 

* Media Encoder Standard különböző elnevezési konvencióval rendelkező kimeneti fájlokat hoz létre, mint az AME.
* A Media Encoder Standard összetevőket, például a [bemeneti fájl metaadatait](media-services-input-metadata-schema.md) és a [kimeneti fájl (oka) metaadatokat](media-services-output-metadata-schema.md)tartalmazó fájlokat hoz létre.

## <a name="next-steps"></a>Következő lépések

* [Örökölt összetevők](legacy-components.md)
* [Díjszabási oldal](https://azure.microsoft.com/pricing/details/media-services/#encoding)
