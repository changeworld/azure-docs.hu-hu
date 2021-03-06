---
title: Adatraktár-egységek (DWU-EK) az Azure szinapszis Analyticsben (korábban SQL DW)
description: Javaslatok az adatraktár-egységek (DWU) ideális számának kiválasztásához az árak és a teljesítmény optimalizálásához, valamint az egységek számának módosításához.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7cd6a037f339f193f63cbe152f0ea9964679c231
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420495"
---
# <a name="data-warehouse-units-dwus"></a>Adatraktár-egységek (DWU)

Javaslatok az adatraktár-egységek (DWU) ideális számának kiválasztásához az árak és a teljesítmény optimalizálásához, valamint az egységek számának módosításához.

## <a name="what-are-data-warehouse-units"></a>Mik az adatraktár-egységek

Az [SQL-készlet](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) az [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse)használatakor kiépített analitikus erőforrások gyűjteményét jelöli. Az analitikai erőforrások a CPU, a memória és az IO kombinációja vannak meghatározva. Ez a három erőforrás az adatraktár-egységek (DWU) számítási skálázási egységei között van. A DWU a számítási erőforrások és a teljesítmény absztrakt, normalizált mértékét jelöli. A szolgáltatási szint módosítása megváltoztatja a rendszer számára elérhető DWU számát, ami viszont a teljesítmény és a szolgáltatás költségeit is módosítja.

A nagyobb teljesítmény érdekében növelheti az adatraktár-egységek számát. Kevesebb teljesítmény esetén csökkentse az adatraktár-egységeket. A tárolási és a számítási költségek számlázása külön történik, így az adatraktár-egységek módosítása nem befolyásolja a tárolási költségeket.

Az adatraktár-egységek teljesítménye a következő számítási feladatok mérőszámán alapul:

- A szabványos adattárház-lekérdezések gyors vizsgálata nagy mennyiségű sort vizsgál, majd összetett összesítést hajt végre. Ez a művelet I/O-és CPU-igényes.
- Az adattárház az Azure Storage-Blobokból vagy Azure Data Lakeokból származó adatok betöltésének gyorsasága. A művelet a hálózati és a CPU-igényes.
- A [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) t-SQL-parancs gyors másolása egy tábla másolásához. A művelet magában foglalja az adatok tárolásból való beolvasását, a berendezés csomópontjain való terjesztését és a tárolóba való írást. Ez a művelet CPU-, IO-és hálózati igényű.

Növekvő DWU:

- A rendszer lineárisan módosítja a vizsgálatok, összesítések és CTAS utasításokhoz tartozó rendszerek teljesítményét
- Növeli az olvasók és az írók számát a kiinduló betöltési műveletekhez
- Növeli az egyidejű lekérdezések és a párhuzamossági bővítőhelyek maximális számát.

## <a name="service-level-objective"></a>Szolgáltatási szint célkitűzése

A szolgáltatási szint célkitűzése (SLO) a méretezhetőségi beállítás, amely meghatározza az adattárház költségeit és teljesítményét. A Gen2 SQL-készlet szolgáltatási szintjei az adatraktár-egységekben (DWU) mérhetőek, például DW2000c.

A T-SQL-ben a SERVICE_OBJECTIVE beállítás határozza meg az SQL-készlet szolgáltatási szintjét.

```sql
CREATE DATABASE mySQLDW
( EDITION = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="capacity-limits"></a>Kapacitási korlátok

Minden SQL-kiszolgáló (például myserver.database.windows.net) rendelkezik egy [adatbázis-tranzakciós egység (DTU)](../sql-database/sql-database-what-is-a-dtu.md) kvótával, amely egy adott számú adattárház-egységet engedélyez. További információ: [munkaterhelés-kezelési kapacitás korlátai](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Hány adatraktár-egységre van szükségem

Az adatraktár-egységek ideális száma nagy mértékben függ a munkaterheléstől és a rendszerbe betöltött adatok mennyiségétől.

A számítási feladatok legjobb DWU megkeresésének lépései:

1. Először válasszon kisebb DWU.
2. Az alkalmazások teljesítményének figyelése az adatterhelések a rendszeren való tesztelésekor, a kiválasztott DWU számának megjelölésével.
3. Azonosítsa a maximális tevékenység időszakos időszakára vonatkozó további követelményeket. Előfordulhat, hogy a jelentős csúcsokat és a tevékenységekben lévő vályúkat bemutató munkaterheléseket gyakran kell méretezni.

Az SQL Analytics egy kibővíthető rendszer, amely nagy mennyiségű számítási és lekérdezési mennyiségű adatmennyiséget tud kiépíteni. Ha szeretné megtekinteni a skálázás valódi képességeit, különösen nagyobb DWU esetében, javasoljuk, hogy az adatkészletet méretezéssel méretezheti, hogy elegendő mennyiségű adattal rendelkezzen a processzorok megadásához. A méretezési teszteléshez legalább 1 TB-ot ajánlott használni.

> [!NOTE]
>
> A lekérdezési teljesítmény csak akkor nő további párhuzamos, ha a munka felosztható a számítási csomópontok között. Ha azt tapasztalja, hogy a méretezés nem változik a teljesítményen, előfordulhat, hogy a tábla kialakítását és/vagy a lekérdezéseit is meg kell adnia. A lekérdezések hangolásával kapcsolatos útmutatásért lásd: [felhasználói lekérdezések kezelése](sql-data-warehouse-overview-manage-user-queries.md).

## <a name="permissions"></a>Engedélyek

Az adatraktár-egységek módosításához az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql)utasításban ismertetett engedélyek szükségesek.

Az Azure-erőforrások, például az SQL-adatbázis közreműködői és SQL Server közreműködők beépített szerepkörei módosíthatják a DWU beállításait.

## <a name="view-current-dwu-settings"></a>Aktuális DWU-beállítások megtekintése

Az aktuális DWU-beállítás megtekintése:

1. Nyissa meg SQL Server Object Explorer a Visual Studióban.
2. Kapcsolódjon a logikai SQL Database kiszolgálóhoz társított Master adatbázishoz.
3. Válasszon a sys. database_service_objectives dinamikus felügyeleti nézetből. Például:

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Adatraktár-egységek módosítása

### <a name="azure-portal"></a>Azure Portal

DWU módosítása:

1. Nyissa meg a [Azure Portal](https://portal.azure.com), nyissa meg az adatbázist, és kattintson a **skálázás**elemre.

2. A **skála**alatt mozgassa a csúszkát balra vagy jobbra a DWU beállítás módosításához.

3. Kattintson a **Save** (Mentés) gombra. Ekkor megjelenik egy megerősítő üzenet. Kattintson az **igen** gombra a megerősítéshez vagy a **nem** gombra az elvetéshez.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A DWU módosításához használja a [set-AzSqlDatabase PowerShell-](/powershell/module/az.sql/set-azsqldatabase) parancsmagot. A következő példa beállítja a szolgáltatási szint célkitűzését, hogy DW1000c a Server MyServer üzemeltetett adatbázis-MySQLDW.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

További információ: PowerShell- [parancsmagok a SQL Data Warehousehoz](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

A T-SQL használatával megtekintheti a jelenlegi DWU-beállításokat, módosíthatja a beállításokat, és megtekintheti a folyamatot.

A DWU módosítása:

1. Kapcsolódjon a logikai SQL Database kiszolgálóhoz társított Master adatbázishoz.
2. Használja az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql) TSQL utasítást. A következő példa a szolgáltatási szint célkitűzését állítja be a DW1000c adatbázis-MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>REST API-k

A DWU módosításához használja az [adatbázis létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate) REST API. A következő példa a szolgáltatási szint célkitűzését állítja be a DW1000c adatbázis-MySQLDW, amely a kiszolgáló MyServer található. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoport.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000c
    }
}
```

További REST API példákat a [SQL Data Warehouse REST API](sql-data-warehouse-manage-compute-rest-api.md)-k című témakörben talál.

## <a name="check-status-of-dwu-changes"></a>DWU-változások állapotának ellenõrzése

A DWU módosításai több percet is igénybe vehetnek. Ha automatikusan méretezést végez, érdemes megfontolnia a logikát, hogy a művelet végrehajtása előtt bizonyos műveleteket végre lehessen hajtani.

Az adatbázis állapotának ellenőrzése különböző végpontokon keresztül lehetővé teszi az automatizálás megfelelő megvalósítását. A portál értesítést küld egy művelet és az adatbázisok aktuális állapotának befejezéséről, de nem teszi lehetővé az állapot programozott ellenőrzését.

A kibővíthető műveletekhez tartozó adatbázis-állapot nem ellenőrizhető a Azure Portal.

A DWU változásainak állapotának ellenõrzése:

1. Kapcsolódjon a logikai SQL Database kiszolgálóhoz társított Master adatbázishoz.

1. Az adatbázis állapotának vizsgálatához küldje el a következő lekérdezést.

    ```sql
    SELECT    *
    FROM      sys.databases
    ;
    ```
    
1. A művelet állapotának vizsgálatához küldje el a következő lekérdezést

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```
    
Ez a DMV az SQL-készlet különböző felügyeleti műveleteivel, például a művelettel és a művelet állapotával kapcsolatos információkat ad vissza, amely vagy IN_PROGRESS vagy kész.

## <a name="the-scaling-workflow"></a>A skálázási munkafolyamat

Amikor elindít egy méretezési műveletet, a rendszer először az összes nyitott munkamenetet megöli, és minden nyitott tranzakciót visszagörget, hogy konzisztens állapotot biztosítson. A skálázási műveletek esetében a méretezés csak a tranzakciós visszaállítás befejeződése után történik meg.  

- A skálázási műveletek esetében a rendszer leválasztja az összes számítási csomópontot, kiépíti a további számítási csomópontokat, majd újracsatlakoztatja a tárolási réteghez.
- A leskálázási művelethez a rendszer leválasztja az összes számítási csomópontot, majd csak a szükséges csomópontokat csatlakoztatja a tárolási réteghez.

## <a name="next-steps"></a>Következő lépések

A teljesítmény kezelésével kapcsolatos további információkért lásd: [erőforrás-osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md) , valamint a [memória és a Egyidejűség korlátai](memory-concurrency-limits.md).
