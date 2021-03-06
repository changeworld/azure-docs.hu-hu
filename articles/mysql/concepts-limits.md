---
title: Korlátozások – Azure Database for MySQL
description: Ez a cikk ismerteti a korlátozások az Azure Database for MySQL-hez, például kapcsolat és a tárolási motort lehetőségek számát.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 6954f306e0d0a346bd8f39776d987af99f7574dd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299090"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Azure Database for MySQL-hez korlátozásai
A következő szakaszok ismertetik a kapacitást, motor támogatása, jogosultság támogatja, adatok adatkezelési utasítást, és az adatbázis-szolgáltatás működési korlátai. Lásd még a MySQL-adatbázismotor esetében alkalmazandó [általános korlátozásokat](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) is.

## <a name="server-parameters"></a>Kiszolgálói paraméterek

Számos népszerű kiszolgálói paraméter minimális és maximális értékét a díjszabási szint és a virtuális mag határozza meg. A korlátokat az alábbi táblázatokban találja.

### <a name="max_connections"></a>max_connections

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Alapszintű|1|50|10|50|
|Alapszintű|2|100|10|100|
|Általános rendeltetés|2|300|10|600|
|Általános rendeltetés|4|625|10|1250|
|Általános rendeltetés|8|1250|10|2500|
|Általános rendeltetés|16|2500|10|5000|
|Általános rendeltetés|32|5000|10|10000|
|Általános rendeltetés|64|10000|10|20000|
|Memóriára optimalizált|2|600|10|800|
|Memóriára optimalizált|4|1250|10|2500|
|Memóriára optimalizált|8|2500|10|5000|
|Memóriára optimalizált|16|5000|10|10000|
|Memóriára optimalizált|32|10000|10|20000|

Amikor kapcsolatokat meghaladják a korlátot, a következő hiba jelenhet meg:
> 1040 (08004). hiba: Túl sok kapcsolat

> [!IMPORTANT]
> A legjobb megoldás érdekében javasoljuk, hogy használjon olyan kapcsolati Pooler, mint a ProxySQL, hogy hatékonyan kezelhesse a kapcsolatokat.

A MySQL-hez való új ügyfélkapcsolatok létrehozása időt vesz igénybe, a kapcsolatok pedig adatbázis-erőforrásokat foglalnak magukban, még akkor is, ha tétlenek. A legtöbb alkalmazás sok rövid életű kapcsolatot igényel, amely ezt a helyzetet összeképezi. Ennek eredményeképpen kevesebb erőforrás érhető el a tényleges munkaterheléshez, ami csökkenti a teljesítményt. Egy kapcsolati Pooler, amely csökkenti az üresjárati kapcsolatokat, és a meglévő kapcsolatokat újra felhasználva segít elkerülni ezt. A ProxySQL beállításával kapcsolatos további információkért látogasson el a [blogbejegyzésbe](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

## <a name="query_cache_size"></a>query_cache_size

A lekérdezés gyorsítótára alapértelmezés szerint ki van kapcsolva. A lekérdezési gyorsítótár engedélyezéséhez konfigurálja a `query_cache_type` paramétert. 

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) .

> [!NOTE]
> A lekérdezési gyorsítótár elavult a MySQL-5.7.20 telepítése, és el lett távolítva a MySQL 8,0-ben.

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Általános rendeltetés|2|0|0|16777216|
|Általános rendeltetés|4|0|0|33554432|
|Általános rendeltetés|8|0|0|67108864|
|Általános rendeltetés|16|0|0|134217728|
|Általános rendeltetés|32|0|0|134217728|
|Általános rendeltetés|64|0|0|134217728|
|Memóriára optimalizált|2|0|0|33554432|
|Memóriára optimalizált|4|0|0|67108864|
|Memóriára optimalizált|8|0|0|134217728|
|Memóriára optimalizált|16|0|0|134217728|
|Memóriára optimalizált|32|0|0|134217728|

## <a name="sort_buffer_size"></a>sort_buffer_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) .

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Általános rendeltetés|2|524288|32768|4194304|
|Általános rendeltetés|4|524288|32768|8388608|
|Általános rendeltetés|8|524288|32768|16777216|
|Általános rendeltetés|16|524288|32768|33554432|
|Általános rendeltetés|32|524288|32768|33554432|
|Általános rendeltetés|64|524288|32768|33554432|
|Memóriára optimalizált|2|524288|32768|8388608|
|Memóriára optimalizált|4|524288|32768|16777216|
|Memóriára optimalizált|8|524288|32768|33554432|
|Memóriára optimalizált|16|524288|32768|33554432|
|Memóriára optimalizált|32|524288|32768|33554432|

## <a name="join_buffer_size"></a>join_buffer_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) .

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Általános rendeltetés|2|262144|128|268435455|
|Általános rendeltetés|4|262144|128|536870912|
|Általános rendeltetés|8|262144|128|1073741824|
|Általános rendeltetés|16|262144|128|2147483648|
|Általános rendeltetés|32|262144|128|4294967295|
|Általános rendeltetés|64|262144|128|4294967295|
|Memóriára optimalizált|2|262144|128|536870912|
|Memóriára optimalizált|4|262144|128|1073741824|
|Memóriára optimalizált|8|262144|128|2147483648|
|Memóriára optimalizált|16|262144|128|4294967295|
|Memóriára optimalizált|32|262144|128|4294967295|

## <a name="max_heap_table_size"></a>max_heap_table_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) .

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Általános rendeltetés|2|16777216|16384|268435455|
|Általános rendeltetés|4|16777216|16384|536870912|
|Általános rendeltetés|8|16777216|16384|1073741824|
|Általános rendeltetés|16|16777216|16384|2147483648|
|Általános rendeltetés|32|16777216|16384|4294967295|
|Általános rendeltetés|64|16777216|16384|4294967295|
|Memóriára optimalizált|2|16777216|16384|536870912|
|Memóriára optimalizált|4|16777216|16384|1073741824|
|Memóriára optimalizált|8|16777216|16384|2147483648|
|Memóriára optimalizált|16|16777216|16384|4294967295|
|Memóriára optimalizált|32|16777216|16384|4294967295|

## <a name="tmp_table_size"></a>tmp_table_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) .

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Általános rendeltetés|2|16777216|1024|67108864|
|Általános rendeltetés|4|16777216|1024|134217728|
|Általános rendeltetés|8|16777216|1024|268435456|
|Általános rendeltetés|16|16777216|1024|536870912|
|Általános rendeltetés|32|16777216|1024|1073741824|
|Általános rendeltetés|64|16777216|1024|1073741824|
|Memóriára optimalizált|2|16777216|1024|134217728|
|Memóriára optimalizált|4|16777216|1024|268435456|
|Memóriára optimalizált|8|16777216|1024|536870912|
|Memóriára optimalizált|16|16777216|1024|1073741824|
|Memóriára optimalizált|32|16777216|1024|1073741824|

## <a name="storage-engine-support"></a>Storage engine-támogatás

### <a name="supported"></a>Támogatott
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMÓRIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nem támogatott
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [Archívum](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [ÖSSZEVONT](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Jogosultság-támogatás

### <a name="unsupported"></a>Nem támogatott
- Adatbázis-szerepkör: számos kiszolgálói paraméterek és beállítások véletlenül rontják a kiszolgáló teljesítményét vagy amelyet tagadni kell a Célrendszerben ACID tulajdonságait. Emiatt a szolgáltatás integritás és SLA-t, a termék szintjén fenntartására, ezt a szolgáltatást nem biztosít az adatbázis-szerepkör. Az alapértelmezett felhasználói fiók, amely jön létre egy új adatbázis-példány létrehozásakor, lehetővé teszi, hogy a felhasználó hajtani a legtöbb DDL és DML utasítást, a felügyelt adatbázis példányában. 
- SZUPER jogosultság: a hasonló [Super jogosultság](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) is korlátozott.
- Leszűkítés: a létrehozáshoz és a korlátozásához Super jogosultságok szükségesek. Ha biztonsági másolat használatával importálja az adatimportálást, távolítsa el manuálisan a `CREATE DEFINER` parancsokat, vagy a mysqldump végrehajtásakor a `--skip-definer` parancs használatával.

## <a name="data-manipulation-statement-support"></a>Adatok adatkezelési utasítás támogatása

### <a name="supported"></a>Támogatott
- a `LOAD DATA INFILE` támogatott, de a `[LOCAL]` paramétert meg kell adni, és át kell irányítani egy UNC elérési útra (Azure Storage csatlakoztatva az SMB-n keresztül).

### <a name="unsupported"></a>Nem támogatott
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Működési korlátai

### <a name="scale-operations"></a>A skálázási műveletek
- Dinamikus méretezés, és onnan az alapszintű tarifacsomagban is kapható jelenleg nem támogatott.
- Kiszolgáló-tároló méretének csökkentése nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítések
- Fő database engine verziói között az automatikus migrálási jelenleg nem támogatott. Ha a következő főverzióra szeretne frissíteni, hozzon létre egy [memóriaképet, és állítsa vissza](./concepts-migrate-dump-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- A PITR a funkció használatakor az ugyanezzel a konfigurációval, mint a kiszolgáló-alapú, az új kiszolgáló jön létre.
- Törölt kiszolgáló visszaállítása nem támogatott.

### <a name="vnet-service-endpoints"></a>Virtuális hálózati Szolgáltatásvégpontok
- Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

### <a name="storage-size"></a>Tárterület mérete
- Tekintse meg [a díjszabási](concepts-pricing-tiers.md) szinten a tárterületre vonatkozó korlátozásokat.

## <a name="current-known-issues"></a>Jelenlegi ismert problémák
- MySQL server-példány a megfelelő verziója a kapcsolat létrejötte után jeleníti meg. A megfelelő kiszolgálópéldány-motor verziójának beszerzéséhez használja a `select version();` parancsot.

## <a name="next-steps"></a>További lépések
- [Az egyes szolgáltatási szinteknél elérhető szolgáltatások](concepts-pricing-tiers.md)
- [Támogatott MySQL-adatbázis-verziók](concepts-supported-versions.md)
