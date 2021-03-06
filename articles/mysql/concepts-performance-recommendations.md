---
title: Teljesítménnyel kapcsolatos javaslatok – Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL Performance ajánlási funkcióját ismerteti
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: f957459cf20a018ae53ba6ec90fb478dd0c69044
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770900"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Teljesítménnyel kapcsolatos javaslatok Azure Database for MySQL

**A következőkre vonatkozik:** Azure Database for MySQL 5,7

A teljesítményre vonatkozó javaslatok szolgáltatás elemzi az adatbázisokat, hogy testreszabott javaslatokat hozzon létre a jobb teljesítmény érdekében. A javaslatok létrehozásához az elemzés különböző adatbázis-jellemzőket vizsgál, beleértve a sémát. Engedélyezze a [lekérdezési tárolót](concepts-query-store.md) a kiszolgálón a teljesítményre vonatkozó javaslatok szolgáltatás teljes körű kihasználásához. Ha a teljesítmény sémája ki van kapcsolva, a lekérdezés-tároló bekapcsolása lehetővé teszi a performance_schema és a szolgáltatáshoz szükséges teljesítmény-séma eszközök egy részhalmazát. A teljesítményre vonatkozó javaslat megvalósítása után tesztelje a teljesítményt a változások hatásának kiértékeléséhez.

## <a name="permissions"></a>Engedélyek

A Teljesítménnyel kapcsolatos javaslatok funkcióval futtatott elemzéshez **Tulajdonos** vagy **Közreműködő** jogosultság szükséges.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok

A [Teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) funkció elemzi a szerveren futó számítási feladatokat a potenciálisan javítható teljesítményű indexek azonosításához.

Nyissa meg a **teljesítményre vonatkozó javaslatokat** a MySQL-kiszolgáló Azure Portal lapjának menüsorának **intelligens teljesítmény** szakaszában.

![A Teljesítménnyel kapcsolatos javaslatok kezdőlapja](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Válassza **az elemzés lehetőséget, és válasszon** egy adatbázist, amely megkezdi az elemzést. A munkaterheléstől függően az elemzés több percet is igénybe vehet. Amikor az elemzés elkészült, a portálon megjelenik egy értesítés. Az elemzés az adatbázis mélyreható vizsgálatát végzi. Javasoljuk, hogy az elemzést az időszakon kívüli időszakok alatt végezze el.

A **javaslatok** ablak a javaslatok listáját jeleníti meg, valamint a kapcsolódó lekérdezés azonosítóját, amely a javaslatot generálta. A lekérdezés AZONOSÍTÓjának használatával a [MySQL. query_store](concepts-query-store.md#mysqlquery_store) nézetet használhatja a lekérdezés további megismeréséhez.

![Teljesítményre vonatkozó javaslatok – új oldal](./media/concepts-performance-recommendations/performance-recommendations-result.png)

A javaslatok nem lesznek automatikusan alkalmazva. A javaslat alkalmazásához másolja a lekérdezés szövegét, és futtassa azt a választott ügyfélről. A javaslat kiértékeléséhez ne felejtse el tesztelni és figyelni.

## <a name="recommendation-types"></a>Javaslatok típusai

Jelenleg csak az *indexek létrehozásával* kapcsolatos javaslatok támogatottak.

### <a name="create-index-recommendations"></a>Tárgymutató-javaslatok létrehozása

Az indexelési javaslatok alapján új indexek *hozhatók létre* , amelyek felgyorsítják a számítási feladatok leggyakrabban futtatott vagy időigényes lekérdezéseit. Ehhez az ajánlási típushoz engedélyezni kell a [query Store](concepts-query-store.md) -t. A lekérdezési tároló gyűjti a lekérdezési adatokat, és megadja az elemzés által a javaslat végrehajtásához használt részletes lekérdezési futtatókörnyezetet és gyakorisági statisztikát.

## <a name="next-steps"></a>Következő lépések
- További információ a Azure Database for MySQL [figyeléséről és hangolásáról](concepts-monitoring.md) .