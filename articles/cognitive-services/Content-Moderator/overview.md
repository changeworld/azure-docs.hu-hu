---
title: Mi az az Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Megismerheti, hogyan használható a Content Moderator a felhasználók által létrehozott tartalmakban található nem megfelelő anyagok nyomon követésére, megjelölésére, értékelésére és szűrésére.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: c1ff74bc358f167612b2bfe05ca47e21203d8702
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973492"
---
# <a name="what-is-azure-content-moderator"></a>Mi az az Azure Content Moderator?

Az Azure Content Moderator egy olyan kognitív szolgáltatás, amely potenciálisan sértő, kockázatos vagy egyéb módon nemkívánatos anyagok számára ellenőrzi a szöveget, a képeket és a videó tartalmát. Ha ez az anyag megtalálható, a szolgáltatás a megfelelő címkéket (jelzőket) alkalmazza a tartalomra. Az alkalmazás ezután kezelheti a megjelölt tartalmakat, így betarthatja az előírásokat, és fenntarthatja a felhasználók számára kívánt környezetet. A [moderálási API](#moderation-apis) -k című szakaszban további információt talál arról, hogy mit jeleznek a különböző tartalmi jelzők.

## <a name="where-its-used"></a>Hol használják

A szoftverfejlesztőknek vagy a csapatoknak például a következő néhány alkalmazási helyzetben érdemes használniuk a Content Moderatort:

- Online piactér, amely mérsékelt termékkatalógusokat és más, felhasználó által létrehozott tartalmat mutat be.
- A nagyvállalatok által generált játék-összetevőkkel és csevegési szobákkal rendelkező vállalatok.
- A közösségi üzenetkezelési platformok, amelyek a felhasználók által hozzáadott képeket, szövegeket és videókat vesznek igénybe.
- Vállalati média-vállalatok, amelyek központosított moderálást valósítanak meg a tartalmukhoz.
- A K-12 oktatási megoldás-szolgáltatók olyan tartalmat szűrnek ki, amely nem felel meg a diákoknak és a pedagógusoknak.

> [!NOTE]
> Nem használhatja a Content Moderatort a gyermekek illegális kiaknázására szolgáló rendszerképek észlelésére. A minősített szervezetek azonban használhatják a [PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") -t az ilyen típusú tartalmak megjelenítésére.

## <a name="what-it-includes"></a>Mit tartalmaz?

A Content Moderator szolgáltatás számos webszolgáltatás API-t tartalmaz, amelyek REST-hívásokkal és .NET SDK-val is elérhetőek. Ezek között az emberi felülvizsgálati eszköz is szerepel, amely lehetővé teszi, hogy emberi felülvizsgálók segítsék a szolgáltatást és végezzenek finomhangolást a moderálási funkción.

## <a name="moderation-apis"></a>Moderálási API-k

A Content Moderator szolgáltatás olyan moderálási API-kat tartalmaz, amelyek potenciálisan nem megfelelő vagy kifogásolt anyagok tartalmának ellenőrzését is tartalmazzák.

![Content Moderator moderálási API-k diagramjának letiltása](images/content-moderator-mod-api.png)

Az alábbi táblázat a moderálási API-k különböző típusait ismerteti.

| API-csoport | Leírás |
| ------ | ----------- |
|[**Szövegmoderálás**](text-moderation-api.md)| Megvizsgálja a sértő tartalom, a szexuálisan explicit vagy szuggesztív tartalom, a káromkodás és a személyes adatokat szövegét.|
|[**Egyéni kifejezéslista**](try-terms-list-api.md)| A szöveget a beépített kifejezésekkel együtt a kifejezések egyéni listájában vizsgálja. Az egyéni listákkal a saját tartalomházirendjének megfelelően tilthatja le vagy engedélyezheti a tartalmakat.|  
|[**Képmoderálás**](image-moderation-api.md)| Kiszűri a felnőtteknek szóló és kényes tartalmakat ábrázoló képeket, észleli a képeken található szövegeket az optikai karakterfelismerés (OCR) segítségével, valamint arcokat ismer fel.|
|[**Egyéni képlisták**](try-image-list-api.md)| Egyéni képlista alapján szűri a képeket. Egyéni képlistákkal szűrheti ki az olyan gyakran ismétlődő tartalmakat, amelyeket nem szeretne ismét besorolni.|
|[**Videomoderálás**](video-moderation-api.md)| Felnőtteknek szóló és kényes tartalmakat keres a videókban, és az ilyen tartalmakhoz időjelölőket ad vissza.|

## <a name="review-apis"></a>API-k áttekintése

A felülvizsgálati API-k lehetővé teszik a moderálási folyamat és az emberi felülvizsgálók integrálását. A [feladatok](review-api.md#jobs) [, a felülvizsgálatok](review-api.md#reviews)és a [munkafolyamat](review-api.md#workflows) -műveletek használatával létrehozhat és automatizálhat humán-in-the-loop-munkafolyamatokat a [felülvizsgálati eszközzel](#the-review-tool) (alább).

> [!NOTE]
> A munkafolyamat API még nem érhető el a .NET SDK-ban, de a REST-végponttal is használható.

![Content Moderator felülvizsgálati API-k diagramjának letiltása](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>A felülvizsgálati eszköz

A Content Moderator szolgáltatás magában foglalja a webalapú [felülvizsgálati eszközt](Review-Tool-User-Guide/human-in-the-loop.md)is, amely az emberi Moderátorok által feldolgozható tartalmi felülvizsgálatokat üzemelteti. Az emberi bevitel nem a szolgáltatás betanítása, hanem a szolgáltatás és az emberi felülvizsgálati csapatok együttes munkája lehetővé teszi a fejlesztők számára, hogy a hatékonyság és a pontosság közötti megfelelő egyensúlyt megtalálják. A felülvizsgálati eszköz egy felhasználóbarát kezelőfelületet is biztosít számos Content Moderator erőforráshoz.

![A Content Moderator emberi felülvizsgálati eszköz kezdőlapja](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes Cognitive Services esetében, a Content Moderator szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft adatkezelési szabályzatait. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>Következő lépések

A Content Moderator szolgáltatás használatának megkezdéséhez kövesse az alábbi témakörben található útmutatást a [webes kipróbálás Content moderator](quick-start.md).