---
title: A gyorsítótárazási szabályokkal Azure CDN gyorsítótárazási viselkedés szabályozása | Microsoft Docs
description: A CDN gyorsítótárazási szabályainak használatával globálisan és feltételekkel is beállíthatja vagy módosíthatja az alapértelmezett gyorsítótár-lejárati viselkedést, például egy URL-útvonalat és egy fájlkiterjesztést.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: magattus
ms.openlocfilehash: ddd7dc7e1245c2a77e866a454bf6bfa3c1f16f88
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278141"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Azure CDN gyorsítótárazási viselkedés szabályozása gyorsítótárazási szabályokkal

> [!NOTE] 
> A gyorsítótárazási szabályok csak a **Verizon Azure CDN standard** csomagból és a Akamai-profilokból **Azure CDN standard** csomagból érhetők el. A **Microsoft** -profilokból Azure CDN a [standard szintű szabályok motorját](cdn-standard-rules-engine-reference.md) kell használnia a Verizon-profilokkal való **Azure CDN premiumhoz** , a **felügyeleti portálon** a hasonló funkciókhoz a [Verizon Premium Rules motort](cdn-rules-engine.md) kell használnia.
 
Az Azure Content Delivery Network (CDN) két módszert kínál a fájlok gyorsítótárazásának szabályozására: 

- Gyorsítótárazási szabályok: Ez a cikk azt ismerteti, hogyan használhatja a Content Delivery Network (CDN) gyorsítótárazási szabályait az alapértelmezett gyorsítótár-lejárati viselkedés globális és egyéni feltételekkel történő beállításához vagy módosításához, például egy URL-cím elérési útját és a fájlkiterjesztés használatát. Az Azure CDN két gyorsítótárazási szabálytípust biztosít:

   - Globális gyorsítótárazási szabályok: Beállíthat egy globális gyorsítótárazási szabályt mindegyik végponthoz a profiljában, amely a végpontra küldött összes kérelmet érinti. A globális gyorsítótárazási szabály felülbírálja az összes HTTP-gyorsítótárazási irányelv fejlécét, ha be van állítva.

   - Egyéni gyorsítótárazási szabályok: Beállíthat egy vagy több globális gyorsítótárazási szabályt minden egyes végponthoz a profiljában. Az egyéni gyorsítótárazási szabályok meghatározott elérési utaknak és fájlkiterjesztéseknek felelnek meg, a feldolgozásuk sorrendben történik, és felülbírálják a globális gyorsítótárazási szabályt, ha az be van állítva. 

- Lekérdezési karakterlánc gyorsítótárazása: beállíthatja, hogy a Azure CDN hogyan kezelje a lekérdezési karakterláncokat tartalmazó kérelmek gyorsítótárazását. További információ: [Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal](cdn-query-string.md). Ha a fájl nem gyorsítótárazható, a lekérdezési karakterlánc gyorsítótárazási beállításának nincs hatása a gyorsítótárazási szabályok és az alapértelmezett CDN-viselkedés alapján.

További információ az alapértelmezett gyorsítótárazási viselkedésről és a gyorsítótárazási direktíva fejlécekről: [Hogyan működik a gyorsítótárazás](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Azure CDN gyorsítótárazási szabályok elérése

1. Nyissa meg a Azure Portal, válasszon ki egy CDN-profilt, majd válasszon ki egy végpontot.

2. A bal oldali ablaktáblán, a Beállítások alatt válassza a **Gyorsítótárszabályok** lehetőséget.

   ![CDN-gyorsítótárszabályok gomb](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Megjelenik a **Gyorsítótárszabályok** lap.

   ![CDN-gyorsítótárazási szabályok lap](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Gyorsítótárazási viselkedés beállításai
Globális és egyéni gyorsítótárazási szabályok esetén a következő **gyorsítótárazási viselkedési** beállításokat adhatja meg:

- **Gyorsítótár megkerülése**: ne gyorsítótárazza és ne hagyja figyelmen kívül a forrás által megadott gyorsítótár-direktíva fejléceket.

- **Felülbírálás**: figyelmen kívül hagyja a forrás által megadott gyorsítótár időtartamát; használja helyette a gyorsítótár megadott időtartamát. Ez a művelet nem bírálja felül a Cache-Control: no-cache beállítást.

- **Ha hiányzik, állítsa be**a (z) a kiindulási forrásként megadott cache-direktíva fejléceket, ha vannak ilyenek; Ellenkező esetben használja a gyorsítótár megadott időtartamát.

![Globális gyorsítótárszabályok](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Egyéni gyorsítótárszabályok](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Gyorsítótár lejárati időtartama
Globális és egyéni gyorsítótárazási szabályok esetén a gyorsítótár lejárati időtartamát napokban, órában, percben és másodpercben adhatja meg:

- A **felülbíráláshoz** és a **gyorsítótárazási viselkedés** **hiánya beállításhoz adja meg** az érvényes gyorsítótárazási időtartamot 0 másodperc és 366 nap közötti tartományban. 0 másodperces érték esetén a CDN gyorsítótárazza a tartalmat, de az összes kérelmet újra kell érvényesíteni a forrás-kiszolgálóval.

- A **gyorsítótár megkerülése** beállításnál a gyorsítótár időtartama automatikusan 0 másodpercre van beállítva, és nem módosítható.

## <a name="custom-caching-rules-match-conditions"></a>Az egyéni gyorsítótárazási szabályok megfelelnek a feltételeknek

Az egyéni gyorsítótárazási szabályok esetében két egyeztetési feltétel érhető el:
 
- **Elérési út**: ez az állapot megegyezik az URL-cím elérési útjával, a tartománynév nélkül, és támogatja a helyettesítő karakteres szimbólumot (\*). Például: _/myfile.html_, _/My/Folder/*_ , és _/My/images/*. jpg_. A maximális hossz 260 karakter.

- **Kiterjesztés**: Ez a feltétel megegyezik a kért fájl fájlkiterjesztés-fájljával. Megadhatja a megfelelő vesszővel tagolt fájlkiterjesztések listáját. Például: _. jpg_, _. mp3_vagy _. png_. A bővítmények maximális száma 50, a kiterjesztések maximális száma pedig 16. 

## <a name="global-and-custom-rule-processing-order"></a>Globális és egyéni szabályok feldolgozási sorrendje
A globális és az egyéni gyorsítótárazási szabályok feldolgozása a következő sorrendben történik:

- A globális gyorsítótárazási szabályok elsőbbséget élveznek az alapértelmezett CDN-gyorsítótárazási viselkedéssel szemben (HTTP-gyorsítótár – direktíva fejléc beállításai). 

- Az egyéni gyorsítótárazási szabályok elsőbbséget élveznek a globális gyorsítótárazási szabályokkal szemben, ha azok érvényesek. Az egyéni gyorsítótárazási szabályok feldolgozása felülről lefelé történik. Vagyis ha egy kérelem mindkét feltételnek megfelel, a lista alján található szabályok elsőbbséget élveznek a lista tetején található szabályokkal szemben. Ezért a listában alacsonyabbra kell helyeznie a konkrét szabályokat.

**Példa**:
- Globális gyorsítótárazási szabály: 
   - Gyorsítótárazási viselkedés: **felülbírálás**
   - Gyorsítótár lejárati időtartama: 1 nap

- Egyéni gyorsítótárazási szabály #1:
   - Egyeztetési feltétel: **elérési út**
   - Egyezési érték: _/Home/*_
   - Gyorsítótárazási viselkedés: **felülbírálás**
   - Gyorsítótár lejárati időtartama: 2 nap

- Egyéni gyorsítótárazási szabály #2:
   - Egyeztetési feltétel: **bővítmény**
   - Egyezés értéke: _. html_
   - Gyorsítótárazási viselkedés: **állítsa be, ha hiányzik**
   - Gyorsítótár lejárati időtartama: 3 nap

Ha ezek a szabályok be vannak állítva, a _&lt;Endpoint hostname&gt;_ . azureedge.net/Home/index.html eseményindítók egyéni gyorsítótárazási szabályt #2, amely a következőre van beállítva: **Ha hiányzik** és 3 nap van beállítva. Ezért ha az *index. html* fájl `Cache-Control` vagy `Expires` a HTTP-fejléceket, azok tiszteletben vannak; Ellenkező esetben, ha ezek a fejlécek nincsenek beállítva, a rendszer 3 napig gyorsítótárazza a fájlt.

> [!NOTE] 
> A szabályok módosítása előtt gyorsítótárazott fájlok megőrzik a forrás gyorsítótárának időtartamára vonatkozó beállítást. A gyorsítótár időtartamának alaphelyzetbe állításához el kell [törölni a fájlt](cdn-purge-endpoint.md). 
>
> Azure CDN konfiguráció módosítása hosszabb időt is igénybe vehet a hálózaton keresztül történő propagáláshoz: 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A Verizon-profiloktól **Azure CDN standard** esetén a propagálás általában 10 percen belül elvégezhető.  
>

## <a name="see-also"></a>Lásd még

- [A gyorsítótárazás működése](cdn-how-caching-works.md)
- [Oktatóanyag: Azure CDN gyorsítótárazási szabályainak beállítása](cdn-caching-rules-tutorial.md)
