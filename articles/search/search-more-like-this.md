---
title: moreLikeThis (előzetes verzió) lekérdezési funkció
titleSuffix: Azure Cognitive Search
description: Ismerteti a moreLikeThis (előzetes verzió) szolgáltatást, amely az Azure Cognitive Search REST API előzetes verzióiban érhető el.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873811"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (előzetes verzió) az Azure Cognitive Search

> [!IMPORTANT] 
> Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs portál vagy .NET SDK-támogatás.

`moreLikeThis=[key]` egy lekérdezési paraméter a [keresési dokumentumok API](https://docs.microsoft.com/rest/api/searchservice/search-documents) -ban, amely a dokumentum kulcsában megadott dokumentumhoz hasonló dokumentumokat keres. Ha `moreLikeThis`re vonatkozó keresési kérelmet küld, a rendszer egy lekérdezést hoz létre az adott dokumentumból kinyert keresési kifejezésekkel, amelyek a legmegfelelőbb dokumentumot írják le. A rendszer ezután a generált lekérdezést használja a keresési kérelem elvégzéséhez. Alapértelmezés szerint az összes kereshető mező tartalma megtekinthető, mínusz a `searchFields` paraméterrel megadott korlátozott mezők. A `moreLikeThis` paraméter nem használható a keresési paraméterrel, `search=[string]`.

Alapértelmezés szerint a rendszer a legfelső szintű kereshető mezők tartalmát veszi figyelembe. Ha inkább konkrét mezőket szeretne megadni, használhatja a `searchFields` paramétert. 

[Összetett típusban](search-howto-complex-data-types.md)nem használhatók a kereshető almezők `MoreLikeThis`.

## <a name="examples"></a>Példák

Az alábbi példák a gyors üzembe helyezési pontról származó szállodákat használják [: hozzon létre keresési indexet a Azure Portal](search-get-started-portal.md).

### <a name="simple-query"></a>Egyszerű lekérdezés

A következő lekérdezés megkeresi azokat a dokumentumokat, amelyek leírás mezőjének legtöbbje hasonlít a forrásdokumentum mezőjéhez, ahogy azt a `moreLikeThis` paraméter adja meg:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

Ebben a példában a kérelem a következőhöz hasonló szállodákat keres: `HotelId` 29.
A HTTP GET használata helyett a HTTP POST használatával is meghívhat `MoreLikeThis`t:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Szűrők alkalmazása

`MoreLikeThis` kombinálható más gyakori lekérdezési paraméterekkel, például a `$filter`okkal. A lekérdezés például csak olyan szállodákra korlátozható, amelyek kategóriája "költségvetés", és ahol a minősítés magasabb, mint 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Mezők kiválasztása és az eredmények korlátozása

Az `$top` választóval korlátozható, hogy hány eredményt kell visszaadni egy `MoreLikeThis` lekérdezésben. Emellett a mezőket `$select`is kiválaszthatja. Itt az első három Hotel van kiválasztva az AZONOSÍTÓval, a névvel és a minősítéssel együtt: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>Következő lépések

A szolgáltatással való kísérletezéshez bármilyen webes tesztelési eszközt használhat.  Javasoljuk, hogy ehhez a gyakorlathoz a Poster-t használja.

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search REST API-k megismerése a Poster használatával](search-get-started-postman.md)
