---
title: 'Rövid útmutató: Text Analytics ügyféloldali kódtára | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval összekapcsolhatók az alkalmazások az Azure Cognitive Services Text Analytics API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/12/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 1f5658c6fa52caa67de1f60c50048014dd77af13
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371321"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Gyors útmutató: az Text Analytics ügyféloldali kódtár használata

Ismerkedjen meg az Text Analytics ügyféloldali kódtár használatába. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.

A következő műveletek végrehajtásához használja a Text Analytics ügyféloldali függvénytárat:

* Hangulatelemzés
* Nyelvfelismerés
* Entitások felismerése
* Kulcskifejezések kinyerése

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * A Text Analytics API legújabb előzetes verziója `3.0-preview`, amely egy nyilvános előzetes verziót tartalmaz a továbbfejlesztett [Hangulatelemzés](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) és az [elnevezett entitás-felismeréshez](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). A legújabb stabil verzió a `2.1`.
>    * Ügyeljen arra, hogy csak az Ön által használt verzió utasításait kövesse.
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Éles környezetekben javasolt a kötegelt aszinkron módszerek használata a teljesítmény és a méretezhetőség érdekében. Tekintse meg az alábbi dokumentációt.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Ez a rövid útmutató csak az Text Analytics ügyféloldali kódtár `3.0-preview` verziójára vonatkozik, amely a továbbfejlesztett [Hangulatelemzés](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) és a [megnevezett entitások felismerésére](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)szolgáló nyilvános előzetes verziót tartalmaz.
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Éles környezetekben javasolt a kötegelt aszinkron módszerek használata a teljesítmény és a méretezhetőség érdekében. Tekintse meg az alábbi dokumentációt.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * A Text Analytics API legújabb előzetes verziója `3.0-preview`, amely egy nyilvános előzetes verziót tartalmaz a továbbfejlesztett [Hangulatelemzés](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) és az [elnevezett entitás-felismeréshez](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). A legújabb stabil verzió a `2.1`.
>    * Ügyeljen arra, hogy csak az Ön által használt verzió utasításait kövesse.
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Éles környezetekben javasolt a kötegelt aszinkron módszerek használata a teljesítmény és a méretezhetőség érdekében. Tekintse meg az alábbi dokumentációt.
> * A [böngészőben](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)a Text Analytics ügyféloldali kódtár ezen verzióját is futtathatja.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * A Text Analytics API legújabb előzetes verziója `3.0-preview`, amely egy nyilvános előzetes verziót tartalmaz a továbbfejlesztett [Hangulatelemzés](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) és az [elnevezett entitás-felismeréshez](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). A legújabb stabil verzió a `2.1`.
>    * Ügyeljen arra, hogy csak az Ön által használt verzió utasításait kövesse.
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Éles környezetekben javasolt a kötegelt aszinkron módszerek használata a teljesítmény és a méretezhetőség érdekében. Tekintse meg az alábbi dokumentációt. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>További nyelvi támogatás

Ha erre a lapra kattintott, valószínűleg nem jelenik meg egy rövid útmutató a kedvenc programozási nyelvén. Ne aggódjon, további gyors útmutatók érhetők el. A táblázat segítségével megtalálhatja a programozási nyelvhez megfelelő mintát.

| Nyelv | Elérhető verzió | 
|----------|------------------------|
| Ruby     | [2,1-es verzió](ruby-sdk.md) | 
| Indítás       | [2,1-es verzió](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megoldás megismerése](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [A Text Analytics áttekintése](../overview.md)
* [Hangulat elemzése](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
* [Nyelv felismerése](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Nyelvi felismerés](../how-tos/text-analytics-how-to-language-detection.md)
