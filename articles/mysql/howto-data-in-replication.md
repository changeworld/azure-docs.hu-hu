---
title: Az adatreplikálás konfigurálása – Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL felhőbe irányuló replikálás beállítását ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: eaebcf50084223e1c1f4df30294bece96cffda6d
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774296"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Azure Database for MySQL konfigurálása felhőbe irányuló replikálás

Ebből a cikkből megtudhatja, hogyan állíthatja be a felhőbe irányuló replikálást a Azure Database for MySQL szolgáltatásban a fő-és a replika-kiszolgálók konfigurálásával. Felhőbe irányuló replikálás lehetővé teszi, hogy szinkronizálja az adatokat egy helyszíni, virtuális gépeken vagy más felhőalapú szolgáltató által üzemeltetett, a Azure Database for MySQL szolgáltatásban egy replikán futó fő MySQL-kiszolgálóról. 

Ez a cikk azt feltételezi, hogy a MySQL-kiszolgálókkal és-adatbázisokkal kapcsolatban legalább néhány korábbi élmény van.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Replikaként használandó MySQL-kiszolgáló létrehozása

1. Új Azure Database for MySQL-kiszolgáló létrehozása

   Hozzon létre egy új MySQL-kiszolgálót (pl. "replica.mysql.database.azure.com"). A kiszolgáló létrehozásához [a Azure Portal használatával Azure Database for MySQL kiszolgáló létrehozása](quickstart-create-mysql-server-database-using-azure-portal.md) című témakörben talál további információt. Ez a kiszolgáló a felhőbe irányuló replikálás replika-kiszolgálója.

   > [!IMPORTANT]
   > A Azure Database for MySQL-kiszolgálót a általános célú vagy a memória optimalizált díjszabási szintjein kell létrehozni.
   > 

2. Azonos felhasználói fiókok és megfelelő jogosultságok létrehozása

   A felhasználói fiókok nem replikálódnak a főkiszolgálóról a másodpéldány-kiszolgálóra. Ha azt tervezi, hogy hozzáférést biztosít a felhasználóknak a másodpéldány-kiszolgálóhoz, manuálisan kell létrehoznia az összes fiókot és a hozzá tartozó jogosultságokat az újonnan létrehozott Azure Database for MySQL-kiszolgálón.

## <a name="configure-the-master-server"></a>A főkiszolgáló konfigurálása
Az alábbi lépéseket követve elkészítheti és konfigurálhatja a helyszínen üzemeltetett MySQL-kiszolgálót egy virtuális gépen vagy más felhőalapú szolgáltató által a felhőbe irányuló replikálás számára üzemeltetett adatbázis-szolgáltatásban. Ez a kiszolgáló a "Master" az adatok replikálásához. 

1. Bináris naplózás bekapcsolása

   Ellenőrizze, hogy a bináris naplózás engedélyezve van-e a főkiszolgálón a következő parancs futtatásával: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Ha a [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) változót a "on" értékkel adja vissza, a bináris naplózás engedélyezve van a kiszolgálón. 

   Ha a `log_bin` a "OFF" értékkel tér vissza, kapcsolja be a bináris naplózást a saját. cnf fájl szerkesztésével, hogy a módosítás érvénybe léptetéséhez `log_bin=ON` és indítsa újra a kiszolgálót.

2. Fő kiszolgáló beállításai

   Felhőbe irányuló replikálás megköveteli, hogy a (z) `lower_case_table_names` paraméterek konzisztensek legyenek a fő-és a replika-kiszolgálók között. Ez a paraméter a Azure Database for MySQL alapértelmezés szerint 1. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Új replikációs szerepkör létrehozása és az engedélyek beállítása

   Hozzon létre egy felhasználói fiókot a fő kiszolgálón, amely replikációs jogosultságokkal van konfigurálva. Ez az SQL-parancsokkal vagy egy olyan eszközzel végezhető el, mint például a MySQL Workbench. Gondolja át, hogy az SSL-sel való replikálást tervezi-e, mivel ezt a felhasználó létrehozásakor meg kell adni. Tekintse át a MySQL dokumentációját, hogy megtudja, hogyan [adhat hozzá felhasználói fiókokat](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) a főkiszolgálón. 

   Az alábbi parancsokban az új, létrehozott replikációs szerepkör minden gépről képes hozzáférni a főkiszolgálóhoz, nem csak a főkiszolgálót üzemeltető gépről. Ezt a "syncuser@"% "beállítás megadásával teheti meg a felhasználó létrehozása parancsban. A [fiókok nevének megadásával](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)kapcsolatos további információkért tekintse meg a MySQL dokumentációját.

   **SQL-parancs**

   *Replikáció SSL használatával*

   Az SSL minden felhasználói kapcsolathoz való megköveteléséhez használja a következő parancsot egy felhasználó létrehozásához: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikáció SSL nélkül*

   Ha az SSL nem szükséges minden kapcsolathoz, használja a következő parancsot egy felhasználó létrehozásához:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   A MySQL Workbench replikációs szerepkörének létrehozásához nyissa meg a **felhasználók és jogosultságok** panelt a **felügyeleti** panelen. Ezután kattintson a **fiók hozzáadása**lehetőségre. 
 
   ![Felhasználók és jogosultságok](./media/howto-data-in-replication/users_privileges.png)

   Írja be a felhasználónevet a **bejelentkezési név** mezőbe. 

   ![Felhasználó szinkronizálása](./media/howto-data-in-replication/syncuser.png)
 
   Kattintson a **felügyeleti szerepkörök** panelre, majd válassza ki a **replikálás Slave** elemet a **globális jogosultságok**listájáról. Ezután kattintson az **Apply (alkalmaz** ) gombra a replikációs szerepkör létrehozásához.

   ![Replikálási Slave](./media/howto-data-in-replication/replicationslave.png)


4. A főkiszolgáló beállítása írásvédett módra

   Az adatbázis kiírásának megkezdése előtt a kiszolgálót csak olvasható módban kell elhelyezni. A csak olvasható módban a főkiszolgáló nem tudja feldolgozni az írási tranzakciókat. Értékelje ki a vállalatra gyakorolt hatást, és szükség esetén ütemezze a csak olvasási időszakot.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Bináris naplófájl nevének és eltolásának beolvasása

   Futtassa a [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) parancsot az aktuális bináris naplófájl nevének és eltolásának meghatározásához.
    
   ```sql
   show master status;
   ```
   Az eredménynek a következőhöz hasonlónak kell lennie. Ügyeljen arra, hogy a bináris fájl nevét jegyezze fel, mivel a későbbi lépésekben lesz használva.

   ![Fő állapot eredményei](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Főkiszolgáló kiírása és visszaállítása

1. Az összes adatbázis kiírása a főkiszolgálóról

   A mysqldump segítségével kitörölheti az adatbázisokat a főkiszolgálóról. Részletekért tekintse meg a következőt: [Dump & Restore](concepts-migrate-dump-restore.md). A MySQL-függvénytár és a tesztelési könyvtár kiírása nem szükséges.

2. Főkiszolgáló beállítása olvasási/írási módra

   Az adatbázis kiírása után állítsa vissza a fő MySQL-kiszolgálót olvasási/írási módba.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Memóriakép-fájl visszaállítása új kiszolgálóra

   Állítsa vissza a memóriakép-fájlt a Azure Database for MySQL szolgáltatásban létrehozott kiszolgálóra. A dump-fájlok MySQL-kiszolgálóra való visszaállításával kapcsolatban tekintse meg a [dump & Restore](concepts-migrate-dump-restore.md) című témakört. Ha a Memóriakép fájlja nagyméretű, töltse fel az Azure-beli virtuális gépre a replika-kiszolgálóval megegyező régión belül. Állítsa vissza a Azure Database for MySQL-kiszolgálóra a virtuális gépről.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>A fő-és a replika-kiszolgálók összekapcsolása a felhőbe irányuló replikálás indításához

1. Főkiszolgáló beállítása

   Az összes felhőbe irányuló replikálás függvényt tárolt eljárások hajtják végre. Az összes eljárást [felhőbe irányuló replikálás tárolt eljárásokban](reference-data-in-stored-procedures.md)találja. A tárolt eljárások a MySQL-rendszerhéjban vagy a MySQL Workbenchben is futtathatók. 

   Két kiszolgáló összekapcsolásához és a replikáció megkezdéséhez jelentkezzen be a cél replikát futtató kiszolgálóra az Azure DB for MySQL szolgáltatásban, és állítsa be a külső példányt főkiszolgálóként. Ezt a `mysql.az_replication_change_master` tárolt eljárással végezheti el a MySQL-hez készült Azure-ADATBÁZISon.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: a fő kiszolgáló állomásneve
   - master_user: a fő kiszolgáló felhasználóneve
   - master_password: a főkiszolgáló jelszava
   - master_log_file: bináris naplófájl neve a futtatási `show master status`
   - master_log_pos: bináris naplózási pozíció futtatása `show master status`
   - master_ssl_ca: HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány környezete. Ha nem használ SSL-t, adja át az üres karakterláncot.
       - Azt javasoljuk, hogy ezt a paramétert változóként adja át. További információért tekintse meg az alábbi példákat.

> [!NOTE]
> Ha a főkiszolgáló egy Azure-beli virtuális gépen fut, állítsa be az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" lehetőséget a "be" értékre, hogy a fő-és a replika-kiszolgálók kommunikálhassanak egymással. Ez a beállítás a **kapcsolatok biztonsági** beállításaiból módosítható. További információt a [Tűzfalszabályok kezelése a portál használatával](howto-manage-firewall-using-portal.md) című témakörben talál.

   **Példák**

   *Replikáció SSL használatával*

   A `@cert` változó a következő MySQL-parancsok futtatásával hozható létre: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Az SSL-sel történő replikáció beállítása a "companya.com" tartományban üzemeltetett főkiszolgáló és a Azure Database for MySQLban üzemeltetett replika-kiszolgáló között történik. Ez a tárolt eljárás fut a replikán. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replikáció SSL nélkül*

   Az SSL nélküli replikáció beállítása a "companya.com" tartományban üzemeltetett főkiszolgáló és a Azure Database for MySQLban üzemeltetett replika-kiszolgáló között történik. Ez a tárolt eljárás fut a replikán.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Replikáció indítása

   Az `mysql.az_replication_start` tárolt eljárás meghívásával kezdeményezheti a replikálást.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Replikáció állapotának megtekintése

   A replikálási állapot megtekintéséhez hívja meg a [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) parancsot a replika kiszolgálóján.
    
   ```sql
   show slave status;
   ```

   Ha `Slave_IO_Running` és `Slave_SQL_Running` állapota "igen", és a `Seconds_Behind_Master` értéke "0", a replikálás jól működik. `Seconds_Behind_Master` jelzi, hogy a replika milyen későn van. Ha az érték nem "0", az azt jelenti, hogy a replika frissítéseket dolgoz fel. 

## <a name="other-stored-procedures"></a>Egyéb tárolt eljárások

### <a name="stop-replication"></a>Replikáció leállítása

A fő-és a replika-kiszolgáló közötti replikáció leállításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Replikációs kapcsolat eltávolítása

A fő-és a replika-kiszolgáló közötti kapcsolat eltávolításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Replikálási hiba kihagyása

A replikálási hibák kihagyásához és a replikálás folytatásához használja a következő tárolt eljárást:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Következő lépések
- További információ a Azure Database for MySQL [felhőbe irányuló replikálásról](concepts-data-in-replication.md) . 
