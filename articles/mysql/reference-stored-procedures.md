---
title: Felügyeleti tárolt eljárások – Azure Database for MySQL
description: Megtudhatja, hogy az Azure Database for MySQL tárolt eljárásai hasznosak-e az adatreplikáció konfigurálásához, az időzóna és a lekérdezési lekérdezések megadásához.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 7ab77f822ace61ccb023dffe6d79fb1d08278d11
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774940"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Azure Database for MySQL felügyelet tárolt eljárásai

A tárolt eljárások Azure Database for MySQL-kiszolgálókon érhetők el a MySQL-kiszolgáló kezeléséhez. Ide tartozik a kiszolgáló kapcsolatainak kezelése, a lekérdezések és a felhőbe irányuló replikálás beállítása.  

## <a name="data-in-replication-stored-procedures"></a>Tárolt eljárások felhőbe irányuló replikálás

A beérkező adatokra épülő replikáció lehetővé teszi, hogy szinkronizálja egy helyszínen, virtuális gépeken vagy más felhőszolgáltatók által üzemeltetett adatbázis-szolgáltatásokban futó MySQL-kiszolgáló adatait az Azure Database for MySQL szolgáltatásba.

A következő tárolt eljárások a főkiszolgálók és a replikák közötti felhőbe irányuló replikálás beállítására és eltávolítására szolgálnak.

|**Tárolt eljárás neve**|**Bemeneti paraméterek**|**Kimeneti paraméterek**|**Használati Megjegyzés**|
|-----|-----|-----|-----|
|*MySQL. az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|–|Az adatok SSL-móddal történő átviteléhez adja át a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány környezetét a master_ssl_ca paraméternek. </br><br>Az adatok SSL nélküli átviteléhez adjon meg egy üres karakterláncot a master_ssl_ca paraméternek.|
|*MySQL. az_replication _start*|–|–|Elindítja a replikálást.|
|*MySQL. az_replication _stop*|–|–|Leállítja a replikálást.|
|*MySQL. az_replication _remove_master*|–|–|Eltávolítja a replikálási kapcsolatot a fő és a replika között.|
|*MySQL. az_replication_skip_counter*|–|–|Egy replikációs hiba kihagyása.|

A Azure Database for MySQL a Master és a replika közötti felhőbe irányuló replikálás beállításához tekintse meg a [felhőbe irányuló replikálás konfigurálását ismertető témakört](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Egyéb tárolt eljárások

A következő tárolt eljárások érhetők el Azure Database for MySQL a kiszolgáló kezeléséhez.

|**Tárolt eljárás neve**|**Bemeneti paraméterek**|**Kimeneti paraméterek**|**Használati Megjegyzés**|
|-----|-----|-----|-----|
|*MySQL. az_kill*|processlist_id|–|[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) paranccsal egyenértékű. Leállítja a megadott processlist_idhoz társított kapcsolatokat, miután leállította a kapcsolatok végrehajtásának utasításait.|
|*MySQL. az_kill_query*|processlist_id|–|[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) paranccsal egyenértékű. Leállítja azt az utasítást, amely szerint a kapcsolatok jelenleg végrehajtás alatt állnak. Maga a kapcsolatok maradnak életben.|
|*MySQL. az_load_timezone*|–|–|Betölti az időzóna-táblákat, hogy az `time_zone` paraméter elnevezett értékre legyen beállítva (pl. "USA/csendes-óceáni térség").|

## <a name="next-steps"></a>Következő lépések
- További információ a [felhőbe irányuló replikálás](howto-data-in-replication.md) beállításáról
- Az [időzóna-táblázatok](howto-server-parameters.md#working-with-the-time-zone-parameter) használatának ismertetése
