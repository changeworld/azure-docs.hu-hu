---
title: Lekérdezési tár – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL-Single Server lekérdezés-tárolási szolgáltatását ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: ccc503e6718ee8f516920cfbea3ad86e7ed81d84
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768265"
---
# <a name="monitor-performance-with-the-query-store"></a>Teljesítmény figyelése a lekérdezési tárolóval

**A következőkre vonatkozik:** Azure Database for PostgreSQL – egykiszolgálós verzió: 9,6, 10, 11

A Azure Database for PostgreSQL lekérdezés-tárolási funkciója lehetővé teszi a lekérdezési teljesítmény időbeli nyomon követését. A Query Store leegyszerűsíti a teljesítménnyel kapcsolatos hibaelhárítást, így gyorsan megtalálhatja a leghosszabb ideig futó és a legtöbb erőforrás-igényes lekérdezést. A Query Store automatikusan rögzíti a lekérdezések és a futásidejű statisztikák előzményeit, és megőrzi azokat az áttekintéshez. Elkülöníti az adatokat az időablakok alapján, hogy az adatbázis használati mintái láthatók legyenek. A rendszer az összes felhasználóra, adatbázisra és lekérdezésre vonatkozó, **azure_sys** nevű adatbázisban tárolja a Azure Database for PostgreSQL-példányban.

> [!IMPORTANT]
> Ne módosítsa a **azure_sys** adatbázist vagy annak sémáit. Ez megakadályozza a lekérdezési tár és a kapcsolódó teljesítmény-funkciók megfelelő működését.

## <a name="enabling-query-store"></a>A lekérdezési tároló engedélyezése
A lekérdezési tároló egy opt-in funkció, így alapértelmezés szerint nem aktív a kiszolgálón. Az áruház globálisan engedélyezett vagy le van tiltva az adott kiszolgálón lévő összes adatbázis esetében, és az adatbázison nem kapcsolható be és ki.

### <a name="enable-query-store-using-the-azure-portal"></a>A lekérdezési tároló engedélyezése a Azure Portal használatával
1. Jelentkezzen be a Azure Portalba, és válassza ki a Azure Database for PostgreSQL-kiszolgálót.
2. Válassza ki a **kiszolgáló paramétereit** a menü **Beállítások** szakaszában.
3. Keresse meg a `pg_qs.query_capture_mode` paramétert.
4. Állítsa be `TOP` és **mentse**az értéket.

A várakozási statisztika engedélyezése a lekérdezési tárolóban: 
1. Keresse meg a `pgms_wait_sampling.query_capture_mode` paramétert.
1. Állítsa be `ALL` és **mentse**az értéket.


A paramétereket az Azure CLI használatával is megadhatja.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Akár 20 percet is igénybe vehet, hogy az első köteg az azure_sys adatbázisban maradjon.

## <a name="information-in-query-store"></a>Információk a Query Store-ban
A lekérdezési tároló két tárolót tartalmaz:
- Egy futtatókörnyezeti statisztika tárolója a lekérdezés-végrehajtási statisztikai adatok megőrzéséhez.
- Várakozási statisztika tároló a várakozó statisztikai adatok megőrzéséhez.

A Query Store használatának gyakori forgatókönyvei a következők:
- A lekérdezések adott időintervallumban történő végrehajtása számának meghatározása
- Lekérdezés átlagos végrehajtási idejének összevetése az időablakok között a nagy eltérések megjelenítéséhez
- A leghosszabb ideig futó lekérdezések azonosítása az elmúlt X órában
- Az erőforrásokra váró legfontosabb N lekérdezések azonosítása
- Egy adott lekérdezés várakozási természetének megismerése

A lemezterület-használat minimalizálásához a futásidejű statisztika tárolójában lévő futtatókörnyezet-végrehajtási statisztika egy rögzített, konfigurálható időablakban van összesítve. Az ezekben a tárolókban található információk láthatók a lekérdezési tár nézeteinek lekérdezésével.

## <a name="access-query-store-information"></a>Hozzáférési lekérdezési tár adatai

A lekérdezési tár adatait a rendszer a postgres-kiszolgálón lévő azure_sys adatbázisban tárolja. 

A következő lekérdezés adatokat ad vissza a lekérdezési tárolóban található lekérdezésekről:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Vagy a várakozási statisztikák lekérdezése:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

A lekérdezési tárolóban lévő adattárakat is kibocsáthatja [Azure monitor naplókba](../azure-monitor/log-query/log-query-overview.md) az elemzéshez és a riasztásokhoz, Event Hubs a folyamatos átvitelhez és az Azure Storage-hoz az archiváláshoz. A konfigurálandó naplózási kategóriák a következők: **QueryStoreRuntimeStatistics** és **QueryStoreWaitStatistics**. A telepítéssel kapcsolatos további tudnivalókért tekintse meg a [Azure monitor diagnosztikai beállításokról](../azure-monitor/platform/diagnostic-settings.md) szóló cikket.


## <a name="finding-wait-queries"></a>Várakozási lekérdezések keresése
A várakozási eseménytípus hasonló módon kombinálja a különböző várakozási eseményeket a gyűjtők között. A lekérdezési tároló a várakozási esemény típusát, az adott várakozási esemény nevét és a kérdéses lekérdezést biztosítja. A várakozási idő és a lekérdezési futtatókörnyezet statisztikájának összekapcsolása azt jelenti, hogy mélyebben meg kell ismernie, hogy mi járul hozzá a teljesítmény jellemzőinek lekérdezéséhez.

Íme néhány példa arra, hogyan szerezhet be több betekintést a számítási feladatokba a lekérdezési tároló várakozási statisztikájának használatával:

| **Általi képernyőfigyelés** | **Művelet** |
|---|---|
|Magas zárolási várakozások | Jelölje be az érintett lekérdezésekhez tartozó lekérdezési szövegeket, és azonosítsa a célként megadott entitásokat. A lekérdezési tárolóban megtekintheti azokat a lekérdezéseket, amelyek ugyanazt az entitást módosítják, amely gyakran és/vagy magas időtartammal van végrehajtva. A lekérdezések azonosítása után érdemes lehet módosítani az alkalmazás logikáját, hogy javítsa a párhuzamosságot, vagy használjon kevésbé korlátozó elkülönítési szintet.|
| Magas puffer IO-várakozások | A lekérdezési tárolóban megkeresheti a nagy számú fizikai olvasással rendelkező lekérdezéseket. Ha egyeznek a magas i/o-várakozásokkal rendelkező lekérdezésekkel, érdemes lehet egy indexet bevezetni az alapul szolgáló entitáson, hogy a vizsgálat helyett a kereséseket végezze. Ez csökkentheti a lekérdezések i/o-terhelését. Tekintse át a kiszolgáló **teljesítményére vonatkozó javaslatokat** a portálon, és ellenőrizze, hogy vannak-e olyan indexelési javaslatok ehhez a kiszolgálóhoz, amely optimalizálja a lekérdezéseket.|
| Nagy memória-várakozások | A lekérdezési tárolóban megkeresheti a leggyakoribb memóriát használó lekérdezéseket. Ezek a lekérdezések valószínűleg késleltetik az érintett lekérdezések további előrehaladását. Tekintse át a kiszolgáló **teljesítményére vonatkozó javaslatokat** a portálon, és ellenőrizze, hogy vannak-e olyan indexelési javaslatok, amelyek optimalizálják ezeket a lekérdezéseket.|

## <a name="configuration-options"></a>Beállítási lehetőségek
Ha a lekérdezési tároló engedélyezve van, a rendszer 15 perces összesítési ablakokban tárolja az adatvesztést, és ablakban akár 500 különböző lekérdezést is beállíthat. 

A lekérdezési tároló paramétereinek konfigurálásához a következő beállítások érhetők el.

| **Paraméter** | **Leírás** | **Alapértelmezett** | **Tartomány**|
|---|---|---|---|
| pg_qs. query_capture_mode | Meghatározza, hogy mely utasítások legyenek követve. | Nincs | nincs, felül, mind |
| pg_qs. max_query_text_length | Beállítja a maximálisan menthető lekérdezési hosszt. A rendszer csonkolja a hosszú lekérdezéseket. | 6000 | 100 – 10K |
| pg_qs. retention_period_in_days | Beállítja a megőrzési időtartamot. | 7 | 1 - 30 |
| pg_qs. track_utility | Beállítja, hogy nyomon követhető-e a segédprogram parancsai | a | be, ki |

A következő lehetőségek kifejezetten a várakozási statisztikára vonatkoznak.

| **Paraméter** | **Leírás** | **Alapértelmezett** | **Tartomány**|
|---|---|---|---|
| pgms_wait_sampling. query_capture_mode | Meghatározza, hogy mely utasítások követik nyomon a várakozási statisztikát. | Nincs | nincs, az összes|
| Pgms_wait_sampling. history_period | Adja meg a gyakoriságot (ezredmásodpercben), amikor a várakozási események mintavételezése történik. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs. query_capture_mode** felülírja **pgms_wait_sampling. query_capture_mode**. Ha pg_qs. query_capture_mode nincs, a pgms_wait_sampling. query_capture_mode beállításnak nincs hatása.


A [Azure Portal](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md) használatával beolvashatja vagy beállíthatja a paraméter egy másik értékét.

## <a name="views-and-functions"></a>Nézetek és függvények
A lekérdezési tárolót a következő nézetekkel és függvényekkel tekintheti meg és kezelheti. A PostgreSQL nyilvános szerepkörben bárki használhatja ezeket a nézeteket a lekérdezési tárolóban lévő információk megjelenítéséhez. Ezek a nézetek csak a **azure_sys** adatbázisban érhetők el.

A lekérdezések normalizálása úgy történik, hogy a konstansok és konstansok eltávolítása után megvizsgálják a szerkezetét. Ha két lekérdezés megegyezik a literális értékektől, akkor ugyanazzal a kivonattal fog rendelkezni.

### <a name="query_storeqs_view"></a>query_store. qs_view
Ez a nézet a lekérdezési tárolóban lévő összes adathalmazt adja vissza. Minden különböző adatbázis-AZONOSÍTÓhoz, felhasználói AZONOSÍTÓhoz és lekérdezési AZONOSÍTÓhoz egy sor van. 

|**Name (Név)**   |**Típus** | **Hivatkozik**  | **Leírás**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | AZONOSÍTÓ az runtime_stats_entries táblából|
|user_id    |OID    |pg_authid. OID  |Az utasítást végrehajtó felhasználó OID azonosítója|
|db_id  |OID    |pg_database. OID    |Az utasítást elvégező adatbázis OID azonosítója|
|query_id   |bigint  || Belső kivonatoló kód, amely az utasítás elemzési fájából lett kiszámítva|
|query_sql_text |Varchar (10000)  || Egy reprezentatív utasítás szövege. Az azonos struktúrával rendelkező különböző lekérdezések együtt vannak csoportosítva; Ez a szöveg a fürtben lévő lekérdezések első példányának szövege.|
|plan_id    |bigint |   |A lekérdezésnek megfelelő csomag azonosítója, még nem érhető el|
|start_time |időbélyeg  ||  A lekérdezések időgyűjtők szerint vannak összesítve – a gyűjtő időkerete alapértelmezés szerint 15 percet vesz igénybe. Ez a bejegyzéshez tartozó időgyűjtőnek megfelelő kezdési idő.|
|end_time   |időbélyeg  ||  A bejegyzéshez tartozó Time gyűjtőnek megfelelő befejezési idő.|
|hívások  |bigint  || A lekérdezés végrehajtásainak száma|
|total_time |dupla pontosság   ||  Lekérdezés végrehajtásának teljes ideje (ezredmásodperc)|
|min_time   |dupla pontosság   ||  Lekérdezés minimális végrehajtási ideje (ezredmásodperc)|
|max_time   |dupla pontosság   ||  Lekérdezés végrehajtásának maximális ideje (ezredmásodperc)|
|mean_time  |dupla pontosság   ||  A lekérdezés végrehajtásának átlagos ideje ezredmásodpercben|
|stddev_time|   dupla pontosság    ||  A lekérdezés végrehajtásához használt idő szórása ezredmásodpercben |
|sorok   |bigint ||  Az utasítás által lekért vagy érintett sorok száma összesen|
|shared_blks_hit|   bigint  ||  A megosztott blokk gyorsítótárának találatok száma az utasítás szerint|
|shared_blks_read|  bigint  ||  Az utasítás által olvasott megosztott blokkok teljes száma|
|shared_blks_dirtied|   bigint   || Az utasítás által dirtied megosztott blokkok teljes száma |
|shared_blks_written|   bigint  ||  Az utasítás által írt megosztott blokkok teljes száma|
|local_blks_hit|    bigint ||   A helyi blokk-gyorsítótár találatok száma az utasítás szerint|
|local_blks_read|   bigint   || Az utasítás által olvasott helyi blokkok teljes száma|
|local_blks_dirtied|    bigint  ||  Az utasítás által dirtied helyi blokkok teljes száma|
|local_blks_written|    bigint  ||  Az utasítás által írt helyi blokkok teljes száma|
|temp_blks_read |bigint  || Az utasítás által olvasott Temp blokkok teljes száma|
|temp_blks_written| bigint   || Az utasítás által írt Temp blokkok teljes száma|
|blk_read_time  |dupla pontosság    || Az utasítás összes olvasási blokkjának olvasása (ha track_io_timing engedélyezve van, ellenkező esetben nulla).|
|blk_write_time |dupla pontosság    || Az utasításban a blokkok írásának teljes ideje ezredmásodpercben (ha track_io_timing engedélyezve van, ellenkező esetben nulla)|
    
### <a name="query_storequery_texts_view"></a>query_store. query_texts_view
Ez a nézet a lekérdezési tárolóban lévő szöveges adatok visszaadása. Minden különböző query_text egy sor van.

|**Name (Név)**|  **Típus**|   **Leírás**|
|---|---|---|
|query_text_id  |bigint     |A query_texts tábla azonosítója|
|query_sql_text |Varchar (10000)     |Egy reprezentatív utasítás szövege. Az azonos struktúrával rendelkező különböző lekérdezések együtt vannak csoportosítva; Ez a szöveg a fürtben lévő lekérdezések első példányának szövege.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store. pgms_wait_sampling_view
Ez a nézet visszaadja az események várakozási idejének értékét a lekérdezési tárolóban. Minden különböző adatbázis-AZONOSÍTÓhoz, felhasználói AZONOSÍTÓhoz, lekérdezési AZONOSÍTÓhoz és eseményhez egy sor van.

|**Name (Név)**|  **Típus**|   **Hivatkozik**| **Leírás**|
|---|---|---|---|
|user_id    |OID    |pg_authid. OID  |Az utasítást végrehajtó felhasználó OID azonosítója|
|db_id  |OID    |pg_database. OID    |Az utasítást elvégező adatbázis OID azonosítója|
|query_id   |bigint     ||Belső kivonatoló kód, amely az utasítás elemzési fájából lett kiszámítva|
|event_type |szöveg       ||Az esemény típusa, amelynek a háttere várakozik|
|esemény  |szöveg       ||A várakozási esemény neve, ha a háttér jelenleg várakozik|
|hívások  |Egész szám        ||A rögzített esemény száma|


### <a name="functions"></a>Functions
Query_store. qs_reset () érvénytelen értéket ad vissza

`qs_reset` a lekérdezési tároló által eddig összegyűjtött összes statisztikát elveti. Ezt a függvényt csak a kiszolgáló-rendszergazdai szerepkörrel lehet végrehajtani.

Query_store. staging_data_reset () érvénytelen értéket ad vissza

`staging_data_reset` elveti a memóriában összegyűjtött összes statisztikát a lekérdezési tárolóban (vagyis a memóriában lévő olyan adatokat, amelyek még nem lettek kiürítve az adatbázisba). Ezt a függvényt csak a kiszolgáló-rendszergazdai szerepkörrel lehet végrehajtani.

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák
- Ha a PostgreSQL-kiszolgáló a default_transaction_read_only paraméterrel rendelkezik, a Query Store nem tudja rögzíteni az adatmennyiséget.
- A lekérdezés-tárolási funkció megszakítható, ha hosszú Unicode-lekérdezéseket (> = 6000 bájt) tapasztal.
- [Olvasási replikák](concepts-read-replicas.md) : a rendszer a főkiszolgálóról replikálja a lekérdezési tároló adatait. Ez azt jelenti, hogy egy olvasási replika lekérdezési tárolója nem biztosít statisztikát az olvasási replikán futó lekérdezésekről.


## <a name="next-steps"></a>Következő lépések
- További információ az [olyan forgatókönyvekről, ahol a lekérdezési tár különösen hasznos lehet](concepts-query-store-scenarios.md).
- További információ a [query Store használatának ajánlott eljárásairól](concepts-query-store-best-practices.md).
