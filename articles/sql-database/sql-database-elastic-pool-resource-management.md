---
title: Erőforrás-kezelés sűrű rugalmas készletekben | Microsoft Docs
description: Számítási erőforrások kezelése az Azure SQL rugalmas készletekben számos adatbázissal.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 11/18/2019
ms.openlocfilehash: 4ce00743f6b77e6ac3e672e0ebce1e5eafc8235d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74187188"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Erőforrás-kezelés sűrű rugalmas készletekben

Azure SQL Database [rugalmas készletek](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) költséghatékony megoldás számos különböző erőforrás-használattal rendelkező adatbázis kezelésére. A rugalmas készletben lévő összes adatbázis ugyanazokat az erőforrásokat (például CPU-t, memóriát, munkaszálakat, tárolóhelyet, tempdb) osztja meg, amelyek feltételezik, hogy a készletben lévő adatbázisok csak egy részhalmaza fogja használni a számítási erőforrásokat. Ez a feltételezés lehetővé teszi, hogy a rugalmas készletek költséghatékonyak legyenek. Ahelyett, hogy minden erőforrásért fizetnie kellene, az ügyfeleknek sokkal kevesebb erőforrásra van szükségük, és a készlet összes adatbázisa között meg kell osztani.

## <a name="resource-governance"></a>Erőforrások szabályozása

Az erőforrás-megosztás megköveteli, hogy a rendszer alaposan vezérelje az erőforrás-használatot a "zajos szomszéd" hatás minimalizálásához, ahol egy magas erőforrás-felhasználású adatbázis más adatbázisokra is hatással van ugyanabban a rugalmas készletben. Ugyanakkor a rendszernek elegendő erőforrást kell biztosítania az olyan funkciókhoz, mint a magas rendelkezésre állás és a vész-helyreállítás (HADR), a biztonsági mentés és a visszaállítás, a figyelés, a lekérdezési tároló és az automatikus Finomhangolás a megbízható működés érdekében.

A Azure SQL Database több erőforrás-irányítási mechanizmus használatával éri el ezeket a célokat, beleértve a Windows- [feladatok objektumainak](https://docs.microsoft.com/windows/win32/procthread/job-objects) feldolgozási szintű erőforrás-szabályozását, a Windows [Fájlkiszolgálói erőforrás-kezelőt (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) a tárolási kvóta kezelésére, valamint a SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) módosított és bővített verzióját, hogy az erőforrás-szabályozást a Azure SQL Database-ban futó egyes SQL Server példányokon implementálja.

A rugalmas készletek átfogó tervezési célja költséghatékony. Emiatt a rendszer szándékosan lehetővé teszi, hogy az ügyfelek _sűrű_ készleteket hozzanak létre, azaz a készleteket az adatbázisok számával vagy a maximálisan engedélyezett értékekkel, de a számítási erőforrások mérsékelt kiosztásával. Ugyanebből az okból a rendszer nem foglalja le az összes potenciálisan szükséges erőforrást a belső folyamataihoz, de lehetővé teszi az erőforrások megosztását a belső folyamatok és a felhasználói munkaterhelések között.

Ez a megközelítés lehetővé teszi, hogy az ügyfelek sűrű rugalmas készleteket használjanak a megfelelő teljesítmény és jelentős költségmegtakarítás érdekében. Ha azonban egy sűrű készlet adatbázisain lévő számítási feladatok elég intenzívek, az erőforrás-tartalom jelentős lesz. Az erőforrás-tartalom csökkenti a felhasználói munkaterhelés teljesítményét, és negatív hatással lehet a belső folyamatokra.

Ha az erőforrás-tartalom sűrűn csomagolt készletben történik, az ügyfelek a következő műveletek közül választhatnak:
- A lekérdezés számítási feladatának finomhangolása az erőforrások felhasználásának csökkentése érdekében.
- Csökkentheti a készlet sűrűségét azáltal, hogy egyes adatbázisokat egy másik készletbe helyez át, vagy önálló adatbázisokba helyezi őket.
- A készlet vertikális felskálázásával további erőforrásokat érhet el.

Az utolsó két művelet megvalósításával kapcsolatos javaslatokért lásd a jelen cikk későbbi, [operatív javaslatai](#operational-recommendations) című témakörét. Az erőforrás-tartalom csökkentése a felhasználói számítási feladatok és a belső folyamatok esetében is előnyös, és lehetővé teszi, hogy a rendszer megbízhatóan fenntartsa a várt szolgáltatási szintet.

## <a name="monitoring-resource-consumption"></a>Erőforrás-felhasználás figyelése

Ahhoz, hogy a teljesítmény romlása elkerülhető legyen az erőforrás-tartalom miatt, a sűrű rugalmas készleteket használó ügyfeleknek proaktívan figyelniük kell az erőforrás-használatot, és időben kell végrehajtaniuk, ha az erőforrás-tartalom egyre nagyobb mértékben befolyásolja a munkaterheléseket. A folyamatos monitorozás azért fontos, mert a készletben lévő erőforrás-használat idővel megváltozik, a felhasználói munkaterhelés változásai, az adatmennyiségek és a terjesztés változásai, a készlet sűrűsége és a SQL Server adatbázis-kezelő változásai. 

Azure SQL Database több mérőszámot biztosít, amelyek az ilyen típusú figyelés szempontjából relevánsak. Az egyes mérőszámok ajánlott átlagos értéke meghaladja az erőforrás-tartalmat a készletben, és a korábban említett műveletek egyikével kell foglalkoznia.

|Metrika neve|Leírás|Ajánlott átlagos érték| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|A rugalmas készlethez társított SQL Server folyamat CPU-kihasználtsága, amelyet a mögöttes operációs rendszer mérnek. Minden adatbázis [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) nézetében, valamint a `master`-adatbázis [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetében érhető el. Ezt a mérőszámot a Azure Monitor is kibocsátja, ahol a [neve](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent`, és a Azure Portal megtekinthető. Ez az érték azonos a rugalmas készletben lévő összes adatbázis esetében.|70% alá. Előfordulhat, hogy az alkalmi rövid tüskék akár 90%-ot is elfogadhatónak tartanak.|
|`max_worker_percent`|[Munkavégző szál]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) kihasználtsága. A készlet minden adatbázisához, valamint magához a készlethez van megadva. A munkaszálak száma az adatbázis szintjén és a készlet szintjén eltérő korlátozásokkal jár, ezért a mérőszám mindkét szinten történő figyelése ajánlott. Minden adatbázis [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) nézetében, valamint a `master`-adatbázis [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetében érhető el. Ezt a mérőszámot a Azure Monitor is kibocsátja, ahol a [neve](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent`, és a Azure Portal megtekinthető.|80% alá. Az akár 100%-os tüskék miatt a kapcsolódási kísérletek és a lekérdezések sikertelenek lesznek.|
|`avg_data_io_percent`|IOPS kihasználtsága olvasási és írási fizikai IO-hoz. A készlet minden adatbázisához, valamint magához a készlethez van megadva. A IOPS számának és a készlet szintjének különböző korlátai vannak, ezért a mérőszám mindkét szinten történő figyelése ajánlott. Minden adatbázis [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) nézetében, valamint a `master`-adatbázis [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetében érhető el. Ezt a mérőszámot a Azure Monitor is kibocsátja, ahol a [neve](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent`, és a Azure Portal megtekinthető.|80% alá. Előfordulhat, hogy az alkalmi rövid tüskék akár 100%-ot is elfogadhatónak tartanak.|
|`avg_log_write_percent`|Adatforgalom kihasználtsága a tranzakciónapló írási IO-írásához. A készlet minden adatbázisához, valamint magához a készlethez van megadva. Az adatbázis szintjén különböző korlátozások vonatkoznak a naplózási teljesítményre, és a készlet szintjén, ezért a mérőszám mindkét szinten történő figyelése ajánlott. Minden adatbázis [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) nézetében, valamint a `master`-adatbázis [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetében érhető el. Ezt a mérőszámot a Azure Monitor is kibocsátja, ahol a [neve](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent`, és a Azure Portal megtekinthető. Ha ez a metrika eléri a 100%-ot, az adatbázis összes módosítása (INSERT, UPDATE, DELETE, MERGE utasítások, SELECT... INTO, BULK INSERT stb.) lassabb lesz.|90% alá. Előfordulhat, hogy az alkalmi rövid tüskék akár 100%-ot is elfogadhatónak tartanak.|
|`oom_per_second`|A memórián kívüli (bácsi) hibák aránya egy rugalmas készletben, amely a memória nyomásának jelzője. Elérhető a [sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current) nézetben. A mérőszám kiszámításához tekintse meg a minta lekérdezés [példáit](#examples) .|0|
|`avg_storage_percent`|Tárterület kihasználtsága a rugalmas készlet szintjén. A `master` adatbázis [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetében érhető el. Ezt a mérőszámot a Azure Monitor is kibocsátja, ahol a [neve](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent`, és a Azure Portal megtekinthető.|80% alá. Az Adatnövekedés nélküli készletek esetében 100%-ot tud megközelíteni.|
|`tempdb_log_used_percent`|A tranzakciós napló területének kihasználtsága a `tempdb` adatbázisban. Annak ellenére, hogy az egyik adatbázisban létrehozott ideiglenes objektumok nem láthatók ugyanabban a rugalmas készletben lévő más adatbázisokban, `tempdb` egy megosztott erőforrás az ugyanabban a készletben lévő összes adatbázishoz. A `tempdb`ban egy adatbázisból indított, hosszú ideig futó vagy üresjáratban lévő tranzakció a tranzakciónapló nagy részét felhasználhatja, és az ugyanabban a készletben lévő más adatbázisokban lévő lekérdezések hibáit okozhatja. Elérhető a [sys. dm_db_log_space_usage](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) nézetben. Ez a metrika Azure Monitor is kibocsátható, és Azure Portal megtekinthető. Tekintse át a minta lekérdezés [példáit](#examples) a metrika aktuális értékének visszaküldéséhez.|50% alá. Az alkalmi tüskék akár 80%-ot is elfogadhatók.|
|||

Ezen mérőszámok mellett Azure SQL Database olyan nézetet biztosít, amely a tényleges erőforrás-irányítási korlátokat adja vissza, valamint olyan nézeteket, amelyek erőforrás-kihasználtsági statisztikát adnak vissza az erőforráskészlet szintjén, valamint a munkaterhelés csoport szintjén.

|Nézet neve|Leírás|  
|-----------------|--------------------------------|  
|[sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Az erőforrás-irányítási mechanizmusok által az aktuális adatbázisban vagy rugalmas készletben használt aktuális konfigurációs és kapacitási beállítások visszaadása.|
|[sys. dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Az aktuális erőforráskészlet-állapottal, az erőforráskészlet aktuális konfigurációjával és a halmozott erőforráskészlet statisztikájának adatait adja vissza.|
|[sys. dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|A számítási feladatok csoportjának statisztikáit és a munkaterhelés-csoport aktuális konfigurációját adja vissza. Ez a nézet összekapcsolható a sys. dm_resource_governor_resource_pools használatával a `pool_id` oszlopban az erőforráskészlet adatainak lekéréséhez.|
|[sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Az elmúlt 32 percben az erőforráskészlet kihasználtsági statisztikáit adja vissza. Minden sor egy 20 másodperces intervallumot képvisel. Az `delta_` oszlopok az egyes statisztikai adategységek változását adják vissza az intervallumban.|
|[sys. dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|A munkaterhelési csoport kihasználtsági statisztikáit adja vissza az elmúlt 32 percben. Minden sor egy 20 másodperces intervallumot képvisel. Az `delta_` oszlopok az egyes statisztikai adategységek változását adják vissza az intervallumban.|
|||

Ezek a nézetek az erőforrások kihasználtságának figyelésére és az erőforrás-tartalmak közel valós idejű megoldására használhatók. Az elsődleges és olvasható másodlagos replikák (beleértve a földrajzi replikákat is) felhasználói terhelése a `SloSharedPool1` erőforráskészlet és `UserPrimaryGroup.DBId[N]` munkaterhelés-csoportba tartozik, ahol a `N` az adatbázis-azonosító értékének.

Az aktuális erőforrás-használat figyelése mellett a sűrű készleteket használó ügyfelek egy külön adattárban is kezelhetik az erőforrás-kihasználtsági adatok előzményeit. Ezeket az értékeket a prediktív elemzések segítségével proaktív módon kezelheti az erőforrások kihasználtsága a múltbeli és szezonális trendek alapján.

## <a name="operational-recommendations"></a>Működési javaslatok

**Hagyjon elegendő erőforrás-belmagasságot**. Ha az erőforrás-tartalom és a teljesítmény romlása megtörténik, a megoldás a korábban feljegyzett bizonyos adatbázisok áthelyezését is magában foglalhatja az érintett rugalmas készletből, vagy a készlet felskálázását. Ezeknek a műveleteknek a végrehajtásához azonban további számítási erőforrások szükségesek. A prémium és üzletileg kritikus készletek esetében ezeknek a műveleteknek az összes adatot át kell helyeznie az áthelyezett adatbázisokhoz vagy a rugalmas készletben lévő összes adatbázishoz, ha a készlet fel van méretezve. Az adatátvitel hosszú távú és erőforrás-igényes művelet. Ha a készlet már magas erőforrás-terhelés alatt van, a megoldási művelet önmagában is csökkenti a teljesítményt. Szélsőséges esetekben előfordulhat, hogy az adatbázis áthelyezése vagy a készlet felskálázása során nem lehetséges az erőforrás-tartalom megoldása, mert a szükséges erőforrások nem érhetők el. Ebben az esetben az érintett rugalmas készlet lekérdezési feladatainak ideiglenes csökkentése az egyetlen megoldás lehet.

A sűrű készleteket használó ügyfeleknek szorosan figyelniük kell az erőforrás-kihasználtsági trendeket a korábban leírtak szerint, és el kell végezni a kockázatcsökkentő műveletet, miközben a metrikák a javasolt tartományokban maradnak, és a rugalmas készletben még mindig van elegendő erőforrás.

Az erőforrás-használat több tényezőtől függ, amelyek az egyes adatbázisok és az egyes rugalmas készletek esetében idővel változnak. A sűrű készletekben az optimális ár/teljesítmény arány folyamatos monitorozást és kiegyensúlyozást igényel, amely az adatbázisokat több kihasználható készletből a kevésbé használt készletekbe helyezi át, és szükség szerint új készleteket hoz létre a megnövekedett számítási feladatok elvégzéséhez.

Ne **Helyezze át a "forró" adatbázisokat**. Ha a készlet szintjén lévő erőforrás-tartalmat elsősorban kis mennyiségű, magas kihasználtságú adatbázis okozta, akkor előfordulhat, hogy a kísértés, hogy ezeket az adatbázisokat egy kevésbé használt készletbe helyezi át, vagy önálló adatbázisokat kíván készíteni. Ha azonban egy adatbázis továbbra is magas kihasználtsággal rendelkezik, nem ajánlott, mert az áthelyezési művelet tovább csökkenti a teljesítményt, mind az áthelyezett adatbázis, mind a teljes készlet esetében. Ehelyett várjon, amíg a magas kihasználtságot el nem éri, vagy helyezze át a kevésbé használt adatbázisokat ahelyett, hogy feloldja az erőforrás-nyomást a készlet szintjén. A nagyon alacsony kihasználtságú adatbázisok azonban nem biztosítanak előnyt ebben az esetben, mert nem csökkenti az erőforrás-használatot a készlet szintjén.

**Hozzon létre új adatbázisokat a "karantén" készletben**. Azokban az esetekben, amikor új adatbázisok jönnek létre gyakran, például a bérlői adatbázis-modellt használó alkalmazások, fennáll a kockázata annak, hogy egy meglévő rugalmas készletbe helyezett új adatbázis váratlanul jelentős erőforrásokat fog használni, és más adatbázisokra is hatással lehet. és a készlet belső folyamatai. A kockázat enyhítése érdekében hozzon létre egy különálló "karanténba helyezett" készletet, amely bőséges erőforrás-elosztással rendelkezik. Ezt a készletet használja az új adatbázisokhoz, amelyek még nem ismertek az erőforrás-felhasználási mintákat. Ha egy adatbázis egy üzleti ciklushoz (például egy hétre vagy egy hónapra) maradt ebben a készletben, és az erőforrás-felhasználás ismert, akkor áthelyezhető egy olyan készletbe, amely elegendő kapacitással rendelkezik a további erőforrás-használat kielégítéséhez.

**Kerülje a túlságosan sűrű logikai kiszolgálókat**. A Azure SQL Database logikai kiszolgálókon legfeljebb 5000 adatbázist [támogat](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) . Több ezer adatbázissal rendelkező rugalmas készleteket használó ügyfelek több rugalmas készletet is megtekinthetnek egyetlen kiszolgálón, a támogatott korláttal együtt. A több ezer adatbázist tartalmazó logikai kiszolgálók azonban működési kihívásokat hoznak létre. A kiszolgálók összes adatbázisának számbavételét igénylő műveletek, például az adatbázisok megtekintése a portálon, lassabbak lesznek. A működési hibák, például a kiszolgálói szintű bejelentkezések vagy tűzfalszabályok helytelen módosítása hatással van a nagyobb számú adatbázisra. A logikai kiszolgáló véletlen törléséhez segítségre van szükség a Microsoft ügyfélszolgálatatól az adatbázisok helyreállításához a törölt kiszolgálón, és a rendszer az összes érintett adatbázisra vonatkozóan tartós kimaradást okoz.

Javasoljuk, hogy az adatbázisok számát a maximális támogatottnál alacsonyabb értékre korlátozza a logikai kiszolgálókon. Számos esetben a kiszolgálónként akár 1000-2000 adatbázis használata is optimális. A véletlen kiszolgáló törlésének valószínűségének csökkentése érdekében javasoljuk, hogy a logikai kiszolgálón vagy az erőforráscsoporthoz [törölje a törlési zárolást](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) .

A múltban bizonyos forgatókönyvek olyan helyzetekben, amikor az adatbázisokat a-ben, a-ben, illetve az azonos logikai kiszolgálón található rugalmas készletek között helyezik át, az adatbázisok a logikai kiszolgálók közötti áthelyezésekor gyorsabbak voltak. Jelenleg minden adatbázis-áthelyezés a forrás-és cél logikai kiszolgálótól függetlenül ugyanazon a sebességgel fut.

## <a name="examples"></a>Példák

### <a name="monitoring-memory-utilization"></a>Memóriahasználat figyelése

Ez a lekérdezés kiszámítja az egyes erőforráskészlet `oom_per_second` metrikáját az elmúlt 32 percben. Ez a lekérdezés egy rugalmas készlet bármely adatbázisában végrehajtható.

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>`tempdb` naplózási terület kihasználtságának figyelése

Ez a lekérdezés a `tempdb_log_used_percent` metrika aktuális értékét adja vissza. Ez a lekérdezés egy rugalmas készlet bármely adatbázisában végrehajtható.

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>Következő lépések

* A rugalmas készletek bemutatása: a [rugalmas készletek segítségével több Azure SQL Database-adatbázist kezelhet és méretezheti](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool).
* A lekérdezési számítási feladatok az erőforrás-kihasználtság csökkentése érdekében történő hangolásával kapcsolatos információkért lásd: [figyelés és hangolás]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index), [figyelés és teljesítmény-Finomhangolás](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview).
