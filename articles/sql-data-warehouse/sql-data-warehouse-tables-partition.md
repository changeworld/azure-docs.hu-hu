---
title: Táblázatok particionálása
description: Javaslatok és példák a Table Partitions használatára az SQL Analyticsben
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 25485502ff1ae6858ee7d0f840c22940dc3ab9b5
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192149"
---
# <a name="partitioning-tables-in-sql-analytics"></a>Táblázatok particionálása az SQL Analyticsben
Javaslatok és példák a Table Partitions használatára az SQL Analyticsben.

## <a name="what-are-table-partitions"></a>Mik azok a táblázatos partíciók?
A Table Partitions lehetővé teszi az adatai kisebb adatcsoportokra osztását. A legtöbb esetben a tábla partíciói egy Date oszlopon jönnek létre. A particionálás az összes SQL Analytics-táblázat típusában támogatott. többek között fürtözött oszlopcentrikus, fürtözött index és halom. A particionálás az összes terjesztési típuson is támogatott, beleértve a kivonatot vagy a ciklikus multiplexelés eloszlását is.  

A particionálás az adatkarbantartás és a lekérdezési teljesítmény előnyeit is kihasználhatja. Függetlenül attól, hogy mind a kettő, akár csak egy, az betöltéstől függ, és hogy ugyanazt az oszlopot mindkét célra használhatja-e, mivel a particionálás csak egy oszlopon végezhető el.

### <a name="benefits-to-loads"></a>Betöltési előnyök
Az SQL Analyticsben a particionálás elsődleges előnye, hogy a partíció törlésével, a váltással és az egyesítéssel javítja az adatterhelés hatékonyságát és teljesítményét. A legtöbb esetben az adatok egy dátum oszlopra vannak particionálva, amely szorosan kötődik ahhoz a sorrendhez, amelyben az adatok betöltődik az adatbázisba. A partíciók használatának egyik legnagyobb előnye az, hogy a tranzakciók naplózásának elkerüléséhez az adatmennyiséget. Noha egyszerűen beillesztheti, frissítheti vagy törölheti az adatvesztést, némi gondolkodással és erőfeszítéssel, ami jelentősen növelheti a teljesítményt a betöltési folyamat során.

A partíciós váltással gyorsan eltávolíthatja vagy lecserélheti a táblák egy szakaszát.  Előfordulhat például, hogy egy értékesítési tény táblázata csak az elmúlt 36 hónapra vonatkozó adatforgalmat tartalmaz. Minden hónap végén az értékesítési adatok legrégebbi hónapja törlődik a táblából.  Ezt az adattörlési utasítás segítségével törölheti a legrégebbi hónapra vonatkozó adatok törléséhez. Ha azonban egy Delete utasítással nagy mennyiségű adatsort töröl, akkor túl sok időt vehet igénybe, és megteremtheti a nagy tranzakciók kockázatát, amely hosszú időt vesz igénybe a visszaállítás, ha valami hiba történik. Az optimális megoldás a legrégebbi adatpartíció eldobása. Ha az egyes sorok törlése órákat is igénybe vehet, a teljes partíció törlése akár másodperceket is igénybe vehet.

### <a name="benefits-to-queries"></a>A lekérdezések előnyei
A particionálás a lekérdezési teljesítmény javítása érdekében is felhasználható. Olyan lekérdezés, amely egy szűrőt alkalmaz a particionált adatértékekre, a vizsgálat csak a megfelelő partícióra korlátozható. Ez a szűrési módszer elkerülheti a teljes táblázatos vizsgálatot, és csak kisebb adathalmazt vizsgálhat meg. A fürtözött oszlopcentrikus indexek bevezetésével a predikátumok kiiktatásának teljesítménybeli előnyei kevésbé hasznosak, de bizonyos esetekben a lekérdezések is hasznosak lehetnek. Ha például a Sales Fact tábla 36 hónapig van particionálva az értékesítési dátum mezővel, akkor az eladási dátum alapján szűrt lekérdezések kihagyhatják a keresést a szűrővel nem egyező partíciókban.

## <a name="sizing-partitions"></a>Méretezési partíciók
Míg a particionálás felhasználható bizonyos forgatókönyvek teljesítményének javítására, a **túl sok** partícióval rendelkező tábla létrehozása bizonyos körülmények között megsértheti a teljesítményt.  Ezek az információk különösen a fürtözött oszlopcentrikus táblák esetében érvényesek. Ahhoz, hogy a particionálás hasznos legyen, fontos megérteni, hogy mikor kell használni a particionálást és a létrehozandó partíciók számát. Nincs olyan nehéz szabály, amely szerint a partíciók száma túl sok, az adataitól függ, és hány partíciót tölt egyszerre. A sikeres particionálási séma általában több tízezer partíciót tartalmaz, és nem ezer.

**Fürtözött oszlopcentrikus** táblákon a partíciók létrehozásakor fontos megfontolni, hogy hány sor tartozik az egyes partíciók közé. A fürtözött oszlopcentrikus táblák optimális tömörítéséhez és teljesítményéhez legalább 1 000 000 sort kell kiszolgálni, és partícióra van szükség. A partíciók létrehozása előtt az SQL Analytics már az egyes táblákat 60 elosztott adatbázisokra osztja. A táblákhoz hozzáadott particionálások a háttérben létrehozott eloszlások mellett is megtalálhatók. Ha ezt a példát használja, ha az értékesítési tény tábla 36 havi partíciót tartalmazott, és az SQL Analytics-adatbázis 60-es eloszlással rendelkezik, akkor az értékesítési tény táblázatának tartalmaznia kell az 60 000 000-as sorokat havonta, vagy 2 100 000 000-sort, ha az összes hónap fel van töltve. Ha egy tábla kevesebb, mint az ajánlott minimális számú sor a partíción, érdemes lehet kevesebb partíciót használni a sorok partíciók számának növeléséhez. További információ: [indexelési](sql-data-warehouse-tables-index.md) cikk, amely a fürt oszlopcentrikus indexek minőségét értékelő lekérdezéseket tartalmaz.

## <a name="syntax-differences-from-sql-server"></a>Szintaxisbeli különbségek SQL Server
Az SQL Analytics bevezeti a SQL Servernál egyszerűbb partíciók definiálásának módját. A particionálási függvények és sémák nem használhatók az SQL Analyticsben, mivel azok SQL Server vannak. Ehelyett mindössze annyit kell tennie, hogy azonosítja a particionált oszlopot és a határ pontokat. Míg a particionálás szintaxisa kis mértékben eltérhet a SQL Servertól, az alapvető fogalmak ugyanazok. A SQL Server és az SQL Analytics a tábla egy partíciós oszlopát támogatja, amely tartományos partíció lehet. További információ a particionálásról: [particionált táblák és indexek](/sql/relational-databases/partitions/partitioned-tables-and-indexes).

A következő példa a [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) utasítást használja a FactInternetSales tábla particionálásához a OrderDateKey oszlopon:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Particionálás áttelepítése SQL Serverról
SQL Server partíciós definíciók áttelepíthetők az SQL Analyticsre egyszerűen:

- Távolítsa el a SQL Server [partíciós sémát](/sql/t-sql/statements/create-partition-scheme-transact-sql).
- Adja hozzá a [partíciós függvény](/sql/t-sql/statements/create-partition-function-transact-sql) definícióját a Create TABLEhoz.

Ha SQL Server-példányról telepít át particionált táblát, a következő SQL segítségével megtalálhatja az egyes partíciókban található sorok számát. Ne feledje, hogy ha ugyanazt a particionálási részletességet használja az SQL Analytics szolgáltatásban, a partíciók száma a 60 tényezővel csökken.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="partition-switching"></a>Partíciós váltás
Az SQL Analytics támogatja a partíciók felosztását, egyesítését és váltását. Ezeket a függvényeket az [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) utasítás használatával hajtja végre.

Két táblázat közötti partíciók váltásához gondoskodnia kell arról, hogy a partíciók illeszkedjenek a megfelelő szegélyekhez, és hogy a tábla definíciói megegyezzenek. Mivel az ellenőrzési megkötések nem érhetők el egy tábla értékei tartományának betartatásához, a forrás táblának ugyanazokat a partíciós határokat kell tartalmaznia, mint a célként megadott tábla. Ha a partíció határai nem azonosak, akkor a partíciós kapcsoló meghiúsul, mert a partíciós metaadatok nem lesznek szinkronizálva.

### <a name="how-to-split-a-partition-that-contains-data"></a>Adattároló partíció felosztása
Egy olyan partíció felosztásának leghatékonyabb módja, amely már tartalmaz olyan partíciót, amely `CTAS` utasítást használ. Ha a particionált tábla fürtözött oszlopcentrikus, akkor a tábla partíciójának üresnek kell lennie a felosztás előtt.

A következő példa egy particionált oszlopcentrikus táblát hoz létre. Egy sort szúr be minden partícióba:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);
```

A következő lekérdezés megkeresi a sorok darabszámát a `sys.partitions` Catalog nézet használatával:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

A következő felosztási parancs hibaüzenetet kap:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, 15. szint, 1. állapot, az ALTER PARTITION utasítás line 44 SPLIT záradéka nem sikerült, mert a partíció nem üres. Csak üres partíciók oszthatók meg, ha a táblán létezik egy oszlopcentrikus index. Az ALTER PARTITION utasítás kiadása előtt érdemes lehet letiltani a oszlopcentrikus indexet, majd a oszlopcentrikus indexet újra felépíteni a MÓDOSÍTÁSi partíció befejeződése után.

A `CTAS` segítségével azonban új táblát hozhat létre az adattároláshoz.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

A partíciós határok igazítása esetén a kapcsoló engedélyezett. Ekkor a forrástábla üres partícióval fog elhagyni, amelyet később fel lehet osztani.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Minden, ami a bal oldalon van, az új partíciós határokba való igazítás az `CTAS`használatával történik, majd visszaváltja az adathalmazt a fő táblába.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Ha elvégezte az adatok áthelyezését, érdemes frissíteni a megcélzott táblázat statisztikáit. A statisztikák frissítése biztosítja, hogy a statisztikák pontosan tükrözzék a saját partíciójuk adatainak új eloszlását.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Új adatbázis betöltése olyan partícióba, amely egyetlen lépésben tartalmaz egy adott adathalmazt.
Az adatpartíciók partíciók közötti betöltése kényelmes módszer egy olyan tábla új adattípusának megadásához, amely nem látható a felhasználók számára az új adatváltásban.  Nagy kihívást jelenthet a forgalmas rendszereken a partíciós váltáshoz kapcsolódó zárolási tartalom kezeléséhez.  Egy partíció meglévő adattartalmának törléséhez egy `ALTER TABLE` szükséges az adatváltáshoz.  Ezután egy másik `ALTER TABLE`ra volt szükség az új adatváltáshoz.  Az SQL Analyticsben az `TRUNCATE_TARGET` lehetőség a `ALTER TABLE` parancsban támogatott.  A `TRUNCATE_TARGET` a `ALTER TABLE` parancs felülírja az új adattal rendelkező partíció meglévő értékeit.  Az alábbi példa egy olyan példát használ, amely a `CTAS` használatával új táblát hoz létre a meglévő adattal, beszúrja az új adatkészleteket, majd visszaváltja az összes adathalmazt a célként megadott táblába, felülírja a meglévőket.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Tábla particionálási forrásának vezérlője
Ha el szeretné kerülni a tábla definícióját a forrás-ellenőrzési rendszeren a **rozsdásodás** alól, érdemes figyelembe vennie a következő megközelítést:

1. A tábla létrehozása particionált táblaként, de partíciós értékek nélkül

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT` a táblázatot a telepítési folyamat részeként:

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

Ezzel a megközelítéssel a forrás vezérlőelemben lévő kód statikus marad, és a particionálási határértékek dinamikusak. az adatbázis időbeli alakulása.

## <a name="next-steps"></a>További lépések
A táblázatok létrehozásával kapcsolatos további információkért tekintse meg a táblák [áttekintését](sql-data-warehouse-tables-overview.md)ismertető cikket.

