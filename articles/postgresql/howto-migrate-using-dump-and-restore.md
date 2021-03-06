---
title: Memóriakép és visszaállítás – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerteti, hogyan lehet kibontani egy PostgreSQL-adatbázist egy memóriakép-fájlba, és hogyan lehet visszaállítani egy pg_dump által létrehozott fájlból Azure Database for PostgreSQL-egyetlen kiszolgálón.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 4365338efa56593e80edcc19cba5944b213d2b72
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770237"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>A PostgreSQL-adatbázis migrálása a dump és a Restore használatával
A [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) használatával kinyerheti a PostgreSQL-adatbázist egy memóriaképfájl-fájlba, és [Pg_restoreheti](https://www.postgresql.org/docs/current/static/app-pgrestore.html) a PostgreSQL-adatbázis visszaállítását egy pg_dump által létrehozott archív fájlból.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md) , amely tűzfalszabályok használatával engedélyezi a hozzáférést és az adatbázist.
- telepített [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) parancssori segédprogramok

A PostgreSQL-adatbázis kiírásához és visszaállításához kövesse az alábbi lépéseket:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Memóriakép létrehozása pg_dump használatával, amely tartalmazza a betölteni kívánt adatfájlokat
Ha egy meglévő PostgreSQL-adatbázist szeretne biztonsági másolatot készíteni a helyszínen vagy egy virtuális gépen, futtassa a következő parancsot:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Ha például van egy helyi kiszolgálója és egy **testdb** nevű adatbázis
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>A cél Azure Database for PostgreSQLra való visszaállítás az pg_restore használatával
A céladatbázis létrehozása után a pg_restore és a-d,--dbname paraméterrel visszaállíthatja az adatait a célként megadott adatbázisba a memóriakép fájlból.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
A--No-Owner paraméterrel együtt a visszaállítás során létrehozott összes objektum tulajdonosa lesz a--username értékkel megadott felhasználó tulajdonában. További információkért tekintse meg a következőt: [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Ha a PostgreSQL-kiszolgáló SSL-kapcsolatokat igényel (alapértelmezés szerint Azure Database for PostgreSQL-kiszolgálókon), állítson be egy környezeti változót `PGSSLMODE=require`, hogy az pg_restore eszköz SSL-sel csatlakozzon. SSL nélkül előfordulhat, hogy a hiba beolvasható `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> A Windows-parancssorban futtassa a parancsot `SET PGSSLMODE=require` a pg_restore parancs futtatása előtt. Linux vagy bash esetén futtassa a parancsot `export PGSSLMODE=require` a pg_restore parancs futtatása előtt.
>

Ebben a példában a **testdb. dump** fájlból származó adatok visszaállítását a célkiszolgáló **mydemoserver.postgres.database.Azure.com**adatbázis- **mypgsqldb** . 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Az áttelepítési folyamat optimalizálása

A meglévő PostgreSQL-adatbázis Azure Database for PostgreSQL szolgáltatásba való áttelepítése az egyik módszer, ha biztonsági másolatot készít az adatbázisról a forrásról, és visszaállítja azt az Azure-ban. Az áttelepítés befejezéséhez szükséges idő minimalizálásához érdemes a következő paramétereket használni a biztonsági mentési és visszaállítási parancsokkal.

> [!NOTE]
> A szintaxissal kapcsolatos részletes információkat a [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)cikkeiben talál.
>

### <a name="for-the-backup"></a>A biztonsági mentéshez
- A biztonsági mentést a-FC kapcsolóval hajtsa végre, így a visszaállítás párhuzamosan is elvégezhető. Példa:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName -f Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>A visszaállításhoz
- Javasoljuk, hogy helyezze át a biztonságimásolat-fájlt egy olyan Azure-beli virtuális gépre, amelyben az a Azure Database for PostgreSQL-kiszolgáló, amelyre az áttelepítést végzi, és végezze el a virtuális gép pg_restore a hálózati késés csökkentése érdekében. Javasoljuk továbbá, hogy a virtuális gép a [gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-powershell.md) engedélyezésével legyen létrehozva.

- Alapértelmezés szerint már el kell végeznie, de meg kell nyitnia a memóriaképet annak ellenőrzéséhez, hogy a Create index utasítások az adatokat szúrják-e be. Ha nem ez a helyzet, helyezze át a Create index utasítást az adatbeszúrás után.

- Állítsa vissza a kapcsolókat – FC és-j *#* a visszaállítás integrálással. *#* a célkiszolgálón lévő magok száma. Azt is megteheti, hogy *#* beállítja a célkiszolgáló magok számának kétszeri megadását a hatás megjelenítéséhez. Példa:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- A memóriaképet úgy is szerkesztheti, ha hozzáadja a következő parancsot: *synchronous_commit = off;* , az elején, a parancs pedig *synchronous_commit = on;* végén. Ha nem kapcsolja be a végére, az alkalmazások megváltoznak az adatvesztés következtében.

- A cél Azure Database for PostgreSQL kiszolgálón a visszaállítás előtt vegye figyelembe a következőket:
    - A lekérdezési teljesítmény nyomon követésének kikapcsolása, mivel ezek a statisztikák nem szükségesek az áttelepítés során. Ezt úgy teheti meg, hogy pg_stat_statements. Track, pg_qs. query_capture_mode és pgms_wait_sampling. query_capture_mode értéket állít be a NONE értékre.

    - A Migrálás felgyorsításához használjon nagy számítási és magas memória-SKU-t, például 32 virtuális mag memóriát. A visszaállítás befejezése után egyszerűen méretezheti vissza az előnyben részesített SKU-ra. Minél nagyobb a SKU, annál több párhuzamosságot érhet el a pg_restore parancs megfelelő `-j` paraméterének növelésével. 

    - A célkiszolgáló további IOPS javíthatják a visszaállítási teljesítményt. A kiszolgáló tárolási méretének növelésével több IOPS is kiépítheti. Ez a beállítás nem vonható vissza, azonban érdemes megfontolni, hogy egy magasabb IOPS a jövőben is hasznát veheti-e a tényleges munkaterhelésnek.

Az éles környezetben való használat előtt ne felejtse el tesztelni és érvényesíteni ezeket a parancsokat tesztkörnyezetben.

## <a name="next-steps"></a>Következő lépések
- A PostgreSQL-adatbázis exportálással és importálással történő áttelepítésével kapcsolatban lásd: [a PostgreSQL-adatbázis migrálása az Exportálás és az importálás használatával](howto-migrate-using-export-and-import.md).
- Az adatbázisok Azure Database for PostgreSQLre való áttelepítésével kapcsolatos további információkért tekintse meg az [adatbázis-áttelepítési útmutatót](https://aka.ms/datamigration).
