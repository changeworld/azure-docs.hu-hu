---
title: A Speech SDK memóriahasználat nyomon követése – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Speech Service SDK számos programozási nyelvet támogat a beszédfelismerés és a szöveg közötti átalakításhoz, valamint a beszéd fordításához. Ez a cikk az SDK-ba beépített memória-kezelési eszközöket ismerteti.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456431"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>A Speech SDK memóriahasználat használatának nyomon követése

A Speech SDK egy olyan natív programkódon alapul, amelyet több programozási nyelvre terveztek az együttműködési rétegek sorozatán keresztül. Az egyes nyelvspecifikus leképezések idiomatically megfelelő funkciókat biztosítanak az objektumok életciklusának kezeléséhez. Emellett a Speech SDK a memória-kezelési eszközöket is tartalmazza, amelyekkel nyomon követheti az erőforrás-használatot az objektumok naplózásával és az objektumok korlátaival. 

## <a name="how-to-read-object-logs"></a>Az objektumok naplófájljainak olvasása

Ha [a beszédfelismerési SDK naplózása engedélyezve van](how-to-use-logging.md), a rendszer a címkék követésével teszi lehetővé a korábbi objektumok megfigyelését. Ezek a címkék a következők: 

* `TrackHandle` vagy `StopTracking` 
* Az objektum típusa
* Az objektum típusát nyomon követő objektumok aktuális száma, valamint a nyomon követett aktuális szám.

Íme egy példa napló: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Figyelmeztetési küszöbérték beállítása

Lehetősége van figyelmeztetési küszöbértéket létrehozni, és ha túllépi a küszöbértéket (feltéve, hogy a naplózás engedélyezve van), a rendszer figyelmeztető üzenetet naplóz. A figyelmeztető üzenet tartalmazza a létezésben lévő összes objektum számát a darabszámmal együtt. Ez az információ a problémák jobb megismeréséhez használható. 

A figyelmeztetési küszöbérték engedélyezéséhez meg kell adni egy `SpeechConfig` objektumon. Ezt az objektumot az új felismerő létrehozásakor ellenőrzi a rendszer. A következő példákban tegyük fel, hogy létrehozott egy `config`nevű `SpeechConfig` példányt:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> Ennek a tulajdonságnak az alapértelmezett értéke 10 000.

## <a name="set-an-error-threshold"></a>Hiba küszöbértékének beállítása 

A Speech SDK használatával beállíthatja, hogy az objektumok maximális száma egy adott időpontban engedélyezett legyen. Ha ez a beállítás engedélyezve van, a maximális szám megadását követően az új felismerő objektumok létrehozásának kísérletei sikertelenek lesznek. A meglévő objektumok továbbra is működni fognak.

Példa a következőre:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

A hiba küszöbértékének engedélyezéséhez meg kell adni egy `SpeechConfig` objektumon. Ezt az objektumot az új felismerő létrehozásakor ellenőrzi a rendszer. A következő példákban tegyük fel, hogy létrehozott egy `config`nevű `SpeechConfig` példányt:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> Ennek a tulajdonságnak az alapértelmezett értéke egy `size_t` adattípusra vonatkozó platform-specifikus maximális érték. Egy tipikus felismerés 7 és 10 belső objektum között lesz használatban.

## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési szolgáltatás próbaverziós előfizetésének beszerzése](get-started.md)
* [Megtudhatja, hogyan ismerheti fel a beszédfelismerést mikrofon használatával](quickstarts/speech-to-text-from-microphone.md)