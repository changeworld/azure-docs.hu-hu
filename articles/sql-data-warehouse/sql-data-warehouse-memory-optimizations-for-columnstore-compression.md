---
title: A oszlopcentrikus index teljesítményének javítása
description: Csökkentse a memória követelményeit, vagy növelje a rendelkezésre álló memóriát, hogy maximalizálja a sorok számát az egyes sorcsoport belül.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 11c0a168e4b2e8eac03eaebd37b208446082d1b4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269288"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>A oszlopcentrikus sorcsoport-minőségének maximalizálása

A sorcsoport minőségét a sorcsoport sorainak száma határozza meg. A rendelkezésre álló memória növelésével maximalizálható, hogy a oszlopcentrikus-indexek hány sort tömörítenek az egyes sorcsoport.  Ezekkel a módszerekkel javíthatja a tömörítési sebességet és a lekérdezési teljesítményt a oszlopcentrikus indexek esetében.

## <a name="why-the-rowgroup-size-matters"></a>A sorcsoport méretének okai
Mivel a oszlopcentrikus-indexek egy táblázatot vizsgálnak az egyes sorcsoportokba való tömörítéséhez oszlopainak vizsgálatával, az egyes sorcsoport sorainak maximális száma növeli a lekérdezési teljesítményt. Ha a sorcsoportokba való tömörítéséhez nagy számú sort tartalmaz, az adattömörítés javítja azt, ami azt jelenti, hogy a lemezből kevesebb adatok olvashatók be.

További információ a sorcsoportokba való tömörítéséhez: [Oszlopcentrikus indexek útmutató](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Sorcsoportokba való tömörítéséhez-cél mérete
A legjobb lekérdezési teljesítmény érdekében a cél a sorcsoport sorok számának maximalizálása egy oszlopcentrikus indexben. A sorcsoport legfeljebb 1 048 576 sort tartalmazhat. A sorok maximális száma nem lehet sorcsoport. A oszlopcentrikus indexek jó teljesítményt érnek el, ha a sorcsoportokba való tömörítéséhez legalább 100 000 sor van.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>A sorcsoportokba való tömörítéséhez a tömörítés során kivágásra kerülhet

Tömeges betöltés vagy oszlopcentrikus index újraépítése során előfordulhat, hogy nem áll rendelkezésre elegendő memória az egyes sorcsoport kijelölt sorok tömörítéséhez. Ha van memória-nyomás, a oszlopcentrikus indexek kivágja a sorcsoport méretét, így a oszlopcentrikus tömörítése sikeres lehet. 

Ha nincs elegendő memória ahhoz, hogy legalább 10 000 sort tömörítenek az egyes sorcsoport, a rendszer hibát generál.

A tömeges betöltéssel kapcsolatos további információkért lásd: [tömeges betöltés fürtözött oszlopcentrikus indexbe](https://msdn.microsoft.com/library/dn935008.aspx#Bulk ).

## <a name="how-to-monitor-rowgroup-quality"></a>A sorcsoport minőségének figyelése

A DMV sys. dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys. dm_db_column_store_row_group_physical_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql) tartalmazza a View definition Matching SQL db), amelyek hasznos információkat tesznek elérhetővé, például a sorok számát a sorcsoportokba való tömörítéséhez-ban, valamint a vágás okát, ha a vágás megtörtént. A következő nézetet praktikus módon is létrehozhatja a DMV lekérdezéséhez, hogy információkat kapjon a sorcsoport-vágásról.

```sql
create view dbo.vCS_rg_physical_stats
as 
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]                             
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]                                          
)
select *
from cte;
```

A trim_reason_desc megadja, hogy a sorcsoport-e (trim_reason_desc = NO_TRIM azt jelenti, hogy a vágás és a sorcsoport nem optimális minőségű). A következő vágási okok a sorcsoport idő előtti kivágását jelzik:
- BULKLOAD: Ez a vágási ok akkor használatos, ha a terhelés sorainak bejövő kötege kevesebb, mint 1 000 000 sor volt. A motor tömörített sorcsoport-csoportokat hoz létre, ha több mint 100 000 sor van beszúrva (a különbözeti tárolóba való behelyezés helyett), de a Trim ok BULKLOAD állítja be. Ebben az esetben érdemes lehet növelni a Batch-terhelést, hogy több sort tartalmazzon. A particionálási séma újraértékelésével győződjön meg arról, hogy az nem túl részletes, mert a Sorcsoportok nem terjedhetnek ki a partíciós határokra.
- MEMORY_LIMITATION: a 1 000 000 sorral rendelkező sorcsoport létrehozásához a motornak bizonyos mennyiségű munkamemóriát kell megadnia. Ha a betöltési munkamenet rendelkezésre álló memóriája kisebb, mint a szükséges munkamemória, a sorcsoport idő előtt le lesz vágva. A következő szakaszokban megtudhatja, hogyan becsülheti meg a szükséges memóriát, és hogyan foglalhat le memóriát.
- DICTIONARY_SIZE: Ez a vágási ok azt jelzi, hogy a sorcsoport-levágás történt, mert legalább egy karakterlánc-oszlop széles és/vagy magas kardinális sztringekkel rendelkezik. A szótár mérete legfeljebb 16 MB a memóriában, és ha eléri ezt a korlátot, a rendszer tömöríti a sort. Ha ezt a helyzetet választja, érdemes elkülöníteni a problémás oszlopot egy különálló táblába.

## <a name="how-to-estimate-memory-requirements"></a>A memória követelményeinek becslése

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Egy sorcsoport tömörítéséhez szükséges maximális memória körülbelül

- 72 MB +
- \#sorok \* \#oszlopok \* 8 bájt +
- \#sorok \* \#rövid karakterlánc-oszlopok \* 32 bájt +
- \#hosszú karakterlánc-oszlopok \* 16 MB a tömörítési szótárhoz

ahol a rövid karakterlánc-oszlopok karakterlánc adattípusokat használnak < = 32 bájt és hosszú karakterlánc típusú oszlopokban, a > 32 bájtos karakterlánc-adattípusokat használnak.

A hosszú karakterláncok tömörítve lettek a szöveg tömörítésére szolgáló tömörítési módszerrel. Ez a tömörítési módszer *szótárt* használ a szöveges mintázatok tárolásához. A szótár maximális mérete 16 MB. A sorcsoport minden hosszú sztring oszlopához csak egy szótár van.

A oszlopcentrikus memória követelményeinek részletes ismertetését lásd a videó [SQL Analytics skálázás: konfiguráció és útmutatás](https://channel9.msdn.com/Events/Ignite/2016/BRK3291)című témakörben.

## <a name="ways-to-reduce-memory-requirements"></a>A memória-követelmények csökkentésének módjai

A következő módszerekkel csökkentheti a sorcsoportokba való tömörítéséhez tömörítéséhez szükséges memóriát a oszlopcentrikus indexek szolgáltatásban.

### <a name="use-fewer-columns"></a>Kevesebb oszlop használata
Ha lehetséges, tervezze meg a táblázatot kevesebb oszloppal. Ha egy sorcsoport a oszlopcentrikus tömörítve van, a oszlopcentrikus-index külön tömöríti az egyes oszlopok szegmenseit. Ezért a sorcsoport tömörítéséhez szükséges memória-követelmények növelik az oszlopok számát.


### <a name="use-fewer-string-columns"></a>Kevesebb karakterlánc-oszlop használata
A karakterlánc típusú adattípusok oszlopai több memóriát igényelnek, mint a numerikus és a dátum adattípusok. A memóriára vonatkozó követelmények csökkentése érdekében fontolja meg az egyedkapcsolati táblák karakterlánc-oszlopainak eltávolítását, és a kisebb dimenziós táblákban való elhelyezését.

További memória-követelmények a karakterláncok tömörítéséhez:

- A karakterlánc-adattípusok legfeljebb 32 karakterből állhatnak a 32 további bájtok értékkel.
- A több mint 32 karaktert tartalmazó karakterlánc-adattípusokat a rendszer a szótárak módszereit használva tömöríti.  A sorcsoport minden oszlopa további 16 MB-ra is felhasználhatja a szótár összeállítását.

### <a name="avoid-over-partitioning"></a>A túlzott particionálás elkerülése

A oszlopcentrikus indexek egy vagy több sorcsoportokba való tömörítéséhez hoznak létre. Az Azure szinapszis Analyticsben az adattárházak esetében a partíciók száma gyorsan növekszik, mivel az elosztott adatforgalom és az egyes eloszlások particionálva vannak. Ha a tábla túl sok partíciót tartalmaz, előfordulhat, hogy nem áll rendelkezésre elegendő sor a sorcsoportokba való tömörítéséhez kitöltéséhez. A sorok hiánya nem hoz létre memóriát a tömörítés során, de olyan sorcsoportokba való tömörítéséhez vezet, amelyek nem érik el a legjobb oszlopcentrikus-lekérdezési teljesítményt.

A túlzott particionálás elkerülésének egy másik oka, hogy a sorok terhelését egy particionált tábla oszlopcentrikus indexére kell betölteni. A terhelés során számos partíció fogadhatja a bejövő sorokat, amelyeket a memóriában tartanak, amíg az egyes partíciók nem tömörítik a megfelelő sorokat. A túl sok partíció további memóriát hoz létre.

### <a name="simplify-the-load-query"></a>Leegyszerűsíti a betöltési lekérdezést

Az adatbázis megosztja a lekérdezéshez tartozó memória-hozzáférést a lekérdezésben szereplő összes operátor között. Ha egy betöltési lekérdezés összetett rendezéseket és illesztéseket tartalmaz, a tömörítéshez rendelkezésre álló memória csökken.

Tervezze meg a betöltési lekérdezést úgy, hogy csak a lekérdezés betöltésére koncentráljon. Ha átalakításokat kell futtatnia az adatokon, futtassa őket külön a betöltési lekérdezésből. Például megadhatja az adathalom-tábla adatkészletét, futtathatja az átalakításokat, majd betöltheti az előkészítési táblát a oszlopcentrikus indexbe. Először is betöltheti az adatkészleteket, majd a MPP rendszer használatával átalakíthatja az adattípusokat.

### <a name="adjust-maxdop"></a>MAXDOP módosítása

Minden eloszlás párhuzamosan tömöríti a sorcsoportokba való tömörítéséhez a oszlopcentrikus, ha az eloszlásban több CPU-mag is elérhető. A párhuzamossághoz további memória-erőforrások szükségesek, ami memória-és sorcsoport tisztítást eredményezhet.

A memória terhelésének csökkentése érdekében a MAXDOP lekérdezési mutatóval kényszerítheti a betöltési műveletet, hogy soros módban fusson az egyes eloszlásokon belül.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Több memória foglalásának módjai

A DWU mérete és a felhasználói erőforrás osztály együttesen határozzák meg, hogy mekkora memória érhető el a felhasználói lekérdezésekhez. A betöltési lekérdezések memória-engedélyezésének növeléséhez növelje a DWU számát, vagy növelje az erőforrás osztályt.

- A DWU növeléséhez lásd: [Hogyan méretezési teljesítmény?](quickstart-scale-compute-portal.md)
- Ha módosítani szeretné egy lekérdezés erőforrás osztályát, tekintse meg a [felhasználói erőforrás osztályának módosítása](resource-classes-for-workload-management.md#change-a-users-resource-class)című témakört.

## <a name="next-steps"></a>Következő lépések

Az SQL Analytics teljesítményének növelésével kapcsolatos további lehetőségekért tekintse meg a [teljesítmény áttekintését](sql-data-warehouse-overview-manage-user-queries.md).
