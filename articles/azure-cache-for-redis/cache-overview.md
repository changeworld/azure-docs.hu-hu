---
title: Mi az az Azure Cache for Redis?
description: Ismerje meg az Azure cache-t, amely lehetővé teszi a gyorsítótárazást, a tartalom gyorsítótárazását, a felhasználói munkamenetek gyorsítótárazását, a feladatok és a Message Queuing Redis, valamint az elosztott tranzakciókat.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: 38936000e426d560237295105b5456429d9ae16d
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126370"
---
# <a name="azure-cache-for-redis-description"></a>Az Azure cache Redis leírása

A Redis készült Azure cache memóriában tárolt adattárakat biztosít a nyílt forráskódú szoftverek [Redis](https://redis.io/)alapján. Gyorsítótárként való használat esetén a Redis javítja a háttérbeli adattárakban nagy mértékben támaszkodó rendszerek teljesítményét és méretezhetőségét. A teljesítmény javítása a gyakran használt adatoknak az alkalmazáshoz közel található gyors tárterületre másolásával történik. Az Azure cache for Redis esetében ez a gyors tárterület a memóriában található, nem pedig a lemezről egy adatbázisból.

A Redis készült Azure cache memóriában tárolt adatstruktúra-tárolóként, elosztott, nem összehasonlítható adatbázisként és egy Message brokerként is használható. A Redis-motor kis késésének és nagy átviteli sebességének köszönhetően javul az alkalmazás teljesítménye.

Az Azure cache for Redis hozzáférést biztosít egy biztonságos, dedikált Redis cache-hez. A Redis készült Azure cache-t a Microsoft felügyeli, amely az Azure-on belül fut, és az Azure-on belüli és kívüli alkalmazások számára is elérhető.

## <a name="using-azure-cache-for-redis"></a>Az Azure cache használata a Redis

Az Azure cache for Redis az alkalmazások teljesítményének növelésével javítja az alkalmazás-architektúrák általános mintáit. A leggyakoribbak többek között a következők:

| Mintázat      | Leírás                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Az adatbázisok gyakran túl nagyok a gyorsítótárba való közvetlen betöltéshez. Gyakori a [gyorsítótár-](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) feltöltési minta használata, hogy csak szükség esetén töltse be az adatgyorsítótárat a gyorsítótárba. Ha a rendszer módosítja az adatmódosítást, a rendszer frissíti a gyorsítótárat is, amelyet aztán más ügyfelek számára terjesztenek. Emellett a rendszer beállíthat egy lejárati időpontot, vagy egy kizárási házirend használatával aktiválhatja az adatfrissítéseket a gyorsítótárba.|
| [Tartalmak gyorsítótárazása](cache-aspnet-output-cache-provider.md) | Számos weblap olyan sablonokból jön létre, amelyek statikus tartalmat, például fejléceket, lábléceket és szalagcímeket használnak. Ezek a statikus elemek gyakran nem változnak. A memórián belüli gyorsítótár használata gyors hozzáférést biztosít a háttérben lévő adattárolókkal összehasonlítva a statikus tartalmakhoz. Ez a minta csökkenti a feldolgozási időt és a kiszolgáló terhelését, így a webkiszolgálók rugalmasabbak lesznek. Lehetővé teszi a terhelések kezeléséhez szükséges kiszolgálók számának csökkentését. A Redis készült Azure cache biztosítja a Redis kimeneti gyorsítótár-szolgáltatóját, hogy támogassa ezt a mintát a ASP.NET.|
| [Felhasználói munkamenetek gyorsítótárazása](cache-aspnet-session-state-provider.md) | Ezt a mintát gyakran használják a bevásárlókocsi-és más felhasználói előzményi adatként, amelyeket a webalkalmazások felhasználói cookie-kkal kívánnak rendelni. Ha túl sok információt tárol cookie-kban, csökkenhet a teljesítmény, mivel a cookie mérete nő, és a rendszer minden kérés esetén átadja és ellenőrzi a cookie-t. Egy tipikus megoldás a cookie-t használja kulcsként az adatbázisban tárolt adatlekérdezéshez. A memóriában tárolt gyorsítótár, például a Redis-hez készült Azure cache használatával az adatok egy felhasználóhoz való hozzárendelése sokkal gyorsabb, mint a teljes körű, kapcsolatban álló adatbázisokkal való interakció. |
| Feladatok és üzenetek üzenetsor-kezelése | Az alkalmazások gyakran vesznek fel feladatokat egy várólistába, ha a kérelemhez társított műveletek végrehajtása időt vesz igénybe. A már futó műveletek várólistára kerülnek, és gyakran egy másik kiszolgáló végzi a feldolgozást.  Ezt a késleltetési módot a tevékenységek üzenetsorba való helyezésének nevezik. A Redis készült Azure cache elosztott várólistát biztosít a minta alkalmazásban való engedélyezéséhez.|
| Elosztott tranzakciók | Az alkalmazásokhoz időnként szükség van egy adott háttérbeli adattárolóra vonatkozó parancsok sorozatára, hogy egyetlen atomi műveletként fussanak. Minden parancsnak sikeresnek kell lennie, vagy mindegyiket vissza kell állítani a kezdeti állapotba. A Redis-hez készült Azure cache egyetlen [tranzakcióként](https://redis.io/topics/transactions)támogatja a parancsok kötegének végrehajtását. |

## <a name="azure-cache-for-redis-offerings"></a>Azure cache a Redis ajánlatokhoz

A Redis készült Azure cache a következő szinteknél érhető el:

| Szint | Leírás |
|---|---|
Alapszintű | Egy csomópontos gyorsítótár. Ez a szint több memóriát támogat (250 MB-53 GB), és ideális fejlesztési, tesztelési és nem kritikus fontosságú számítási feladatokhoz. Az alapszintű csomag nem rendelkezik szolgáltatásszint-szerződéssel (SLA) |
| Standard | Egy két csomópontos, elsődleges/másodlagos, az Azure által felügyelt, magas rendelkezésre állást biztosító SLA-val (99,9%) rendelkező replikált gyorsítótár |
| Premium | A prémium szint a nagyvállalati használatra kész szint. A prémium szintű gyorsítótárak több funkciót támogatnak, és nagyobb átviteli sebességgel, valamint gyorsabb válaszidőkkel rendelkeznek. A prémium szintű gyorsítótárakat nagyobb teljesítményű hardvereken helyezik üzembe, és az alapszintű vagy a standard szintnél jobb teljesítményt biztosítanak. Ez az előny azt jelenti, hogy az azonos méretű gyorsítótár esetében a standard szinthez képest magasabb lesz a prémium szintű kapacitás. |

> [!TIP]
> A mérettel, az átviteli sebességgel és a sávszélességgel kapcsolatos további információkért tekintse meg az [Azure cache for Redis – gyakori kérdések](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)című témakört.
>

A gyorsítótárat a létrehozása után akár magasabb szinten is méretezheti. Az alacsonyabb szintre való vertikális leskálázás nem támogatott. A részletes skálázási utasításokért lásd: az [Azure cache skálázása Redis](cache-how-to-scale.md) és [a skálázási műveletek automatizálása](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Szolgáltatások összehasonlítása

Az [Azure cache for Redis díjszabási](https://azure.microsoft.com/pricing/details/cache/) oldala az egyes szintek részletes összehasonlítását tartalmazza. Az alábbi táblázat a szintek által támogatott néhány funkciót írja le:

| Funkció leírása | Premium | Standard | Alapszintű |
| ------------------- | :-----: | :------: | :---: |
| [Szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis-fürt](cache-how-to-premium-clustering.md) |✔|-|-|
| [Biztonság a tűzfalszabályok használatával](cache-configure.md#firewall) |✔|✔|✔|
| Titkosítás átvitel közben |✔|✔|✔|
| [Továbbfejlesztett biztonság és elkülönítés VNettel](cache-how-to-premium-vnet.md) |✔|-|-|
| [Importálás és exportálás](cache-how-to-import-export-data.md) |✔|-|-|
| [Ütemezett frissítések](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Georeplikáció](cache-how-to-geo-replication.md) |✔|-|-|
| [Újraindítás](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Következő lépések

* [ASP.net-webalkalmazás](cache-web-app-howto.md) – rövid útmutató Hozzon létre egy egyszerű ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.
* [.Net](cache-dotnet-how-to-use-azure-redis-cache.md) gyors útmutató Hozzon létre egy .NET-alkalmazást, amely egy Azure cache-t használ a Redis.
* [.Net Core](cache-dotnet-core-quickstart.md) gyors útmutató Hozzon létre egy .NET Core-alkalmazást, amely egy Azure cache-t használ a Redis.
* [Node. js](cache-nodejs-get-started.md) rövid útmutató Hozzon létre egy egyszerű Node. js-alkalmazást, amely egy Azure cache-t használ a Redis.
* [Java](cache-java-get-started.md) rövid útmutató Hozzon létre egy egyszerű Java-alkalmazást, amely egy Azure cache-t használ a Redis.
* [Python](cache-python-get-started.md) rövid útmutató Hozzon létre egy olyan Python-alkalmazást, amely egy Azure cache-t használ a Redis.
