---
title: Mi a Bing Web Search API?
titleSuffix: Azure Cognitive Services
description: A Bing Web Search API egy RESTful szolgáltatás, amely azonnali választ ad a felhasználói lekérdezésekre. A keresés könnyen konfigurálható, hogy weboldalakra, képekre, videókra, hírekre, fordításokra és egyéb találatokra is kiterjedjen. A találatok JSON formátumban érkeznek, és a keresési relevancia, valamint a Bing Web Search-előfizetései alapján állnak össze.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 12/05/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: a7b2627b5837a124ebbcd76783bb49679cbe6313
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650282"
---
# <a name="what-is-the-bing-web-search-api"></a>Mi a Bing Web Search API?

A Bing Web Search API egy RESTful szolgáltatás, amely azonnali választ ad a felhasználói lekérdezésekre. A keresés könnyen konfigurálható, hogy weboldalakra, képekre, videókra, hírekre, fordításokra és egyéb találatokra is kiterjedjen. A Bing Web Search a keresési relevancia és a Bing Web Search-előfizetések alapján biztosítja az eredményeket JSON-ként.

Ez az API optimális az olyan alkalmazásokhoz, amelyek hozzáférést biztosítanak a felhasználók keresési lekérdezései szempontjából releváns tartalmakhoz. Ha olyan alkalmazást fejleszt, amelyhez csak a találatok adott típusa szükséges, érdemes lehet inkább a [Bing Image Search API-t](../Bing-Image-Search/overview.md), a [Bing Video Search API-t](../Bing-Video-Search/search-the-web.md) vagy a [Bing News Search API-t](../Bing-News-Search/search-the-web.md) használnia. A Bing Search API-k teljes listájáért lásd a [Cognitive Services API-kat](https://docs.microsoft.com/azure/cognitive-services) ismertető témakört.

Szeretné látni működés közben? Próbálja ki a [Bing Web Search API bemutatóanyagát](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Szolgáltatások  

Bing Web Search nem csak az azonnali válaszokhoz biztosít hozzáférést. Emellett további funkciókat és funkciókat is biztosít, amelyek segítségével testre szabhatja a felhasználók keresési eredményeit.

| Funkció | Leírás |
|---------|-------------|
| [Keresési kifejezések valós idejű felkínálása](../bing-autosuggest/get-suggested-search-terms.md) | A Bing Autosuggest API a begépelés során javaslatokat tesz a keresési kifejezésekre, így könnyebbé teszi az alkalmazások használatát. |
| [Találatok szűrése és korlátozása a tartalom típusa alapján](filter-answers.md) | Keresési eredmények szűrőkkel és lekérdezési paraméterekkel történő testre szabása és finomítása a weboldalakra, képekre, videókra, a biztonságos keresésre és egyebekre vonatkozóan. |
| [Találatok kiemelése unicode-karakterekhez](hit-highlighting.md) | Nem kívánt unicode-karakterek azonosítása és eltávolítása a találatokból, mielőtt azok megjelennének a találatok kiemelése funkciót használó felhasználóknak. |
| [Találatok helyi szűkítése ország, régió és/vagy piac alapján](supported-countries-markets.md) | A Bing Web Search több mint három tucat országot vagy régiót támogat. A funkció használatával a találatok leszűkíthetők egy adott országra/régióra vagy piacra. |
| [Keresési metrikák elemzése a Bing Statistics használatával](bing-web-stats.md) | A Bing Statistics egy fizetős előfizetés, amely a meghívásmennyiségek, a leggyakoribb lekérdezési sztringek, a földrajzi eloszlás és egyéb szempontok elemzését végzi. |

## <a name="workflow"></a>Munkafolyamat

A Bing Web Search API bármely olyan programnyelvből meghívható, amely képes HTTP-lekérdezéseket kezelni és JSON-válaszokat elemezni. A szolgáltatás a [REST API](quickstarts/python.md) vagy a [Bing Web Search ügyféloldali kódtárak](./quickstarts/client-libraries.md)használatával érhető el.

1. [Hozzon létre egy Azure-erőforrást](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Bing Search API-k számára. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).  
2. Küldjön egy [kérést a Bing Web Search API-ra](quickstarts/python.md).
3. Elemezze a JSON-választ.

## <a name="next-steps"></a>Következő lépések

* A [Python rövid útmutatója](./quickstarts/client-libraries.md?pivots=programming-language-python) alapján végezze el az első hívást a Bing Web Search API-ra.  
* [Hozzon létre egy egyoldalas webalkalmazást](tutorial-bing-web-search-single-page-app.md).
* Tekintse át a [Web Search API 7-es verziójának referenciadokumentációját](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).  
* Tekintse át részletesebben a Bing Web Search [használati és megjelenítési követelményeit](UseAndDisplayRequirements.md).  
