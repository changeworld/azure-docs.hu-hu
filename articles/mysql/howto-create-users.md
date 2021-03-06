---
title: Felhasználók létrehozása – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan hozhat létre új felhasználói fiókokat Azure Database for MySQL-kiszolgálóval való kommunikációhoz.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e76e63030cc8e10c857d361cca69e1d35ba8c2c1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269405"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Felhasználók létrehozása Azure Database for MySQL-kiszolgálón 
Ez a cikk azt ismerteti, hogyan hozhat létre felhasználókat egy Azure Database for MySQL-kiszolgálón.

Amikor először hozta létre a Azure Database for MySQL, a kiszolgáló-rendszergazdai bejelentkezési felhasználónevet és jelszót adott meg. További információt a rövid útmutatóban [talál.](quickstart-create-mysql-server-database-using-azure-portal.md) A kiszolgáló-rendszergazdai bejelentkezési felhasználónevet megkeresheti a Azure Portal.

A kiszolgáló-rendszergazda felhasználó bizonyos jogosultságokat kap a kiszolgáló számára a felsoroltak szerint: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCIÁk, INDEX, ALTER, adatbázisok megjelenítése, ideiglenes táblák létrehozása, táblák ZÁROLÁSa, végrehajtás, REPLIKÁLÁSi SLAVE, REPLIKÁCIÓ ÜGYFÉL, NÉZET LÉTREHOZÁSA, NÉZET MEGJELENÍTÉSE, RUTIN LÉTREHOZÁSA, ALTER RUTIN, FELHASZNÁLÓ LÉTREHOZÁSA, ESEMÉNY, ESEMÉNYINDÍTÓ

A Azure Database for MySQL-kiszolgáló létrehozása után az első kiszolgáló-rendszergazdai felhasználói fiókkal további felhasználókat hozhat létre, és rendszergazdai hozzáférést adhat hozzájuk. A kiszolgálói rendszergazdai fiókkal kevesebb jogosultsággal rendelkező felhasználó hozható létre, akik egyéni adatbázis-sémákkal rendelkeznek hozzáféréssel.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>További rendszergazda felhasználók létrehozása a Azure Database for MySQLban
1. Kérje le a kapcsolatfelvételi adatokat és a rendszergazda felhasználónevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló és a bejelentkezési adatok könnyen megtalálhatók a kiszolgáló **Áttekintés** lapján vagy a Azure Portal **Tulajdonságok** lapján. 

2. Az adatbázis-kiszolgálóhoz való kapcsolódáshoz használja a rendszergazdai fiókot és a jelszót. Használhatja az előnyben részesített ügyfélprogramot, például a MySQL Workbench, a MySQL. exe, a HeidiSQL vagy más eszközöket. 
   Ha nem tudja, hogyan csatlakozhat, tekintse meg a következőt: a [MySQL Workbench használata a kapcsolódáshoz és az adatlekérdezéshez](./connect-workbench.md)

3. Szerkessze és futtassa a következő SQL-kódot. Cserélje le az új felhasználónevet a helyőrző értékre `new_master_user`. Ez a szintaxis megadja a felsorolt jogosultságokat az összes adatbázis-sémán ( *.* ) a felhasználónévre (new_master_user ebben a példában). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. A támogatások ellenőrzése 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Adatbázis-felhasználók létrehozása a Azure Database for MySQLban

1. Kérje le a kapcsolatfelvételi adatokat és a rendszergazda felhasználónevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló és a bejelentkezési adatok könnyen megtalálhatók a kiszolgáló **Áttekintés** lapján vagy a Azure Portal **Tulajdonságok** lapján. 

2. Az adatbázis-kiszolgálóhoz való kapcsolódáshoz használja a rendszergazdai fiókot és a jelszót. Használhatja az előnyben részesített ügyfélprogramot, például a MySQL Workbench, a MySQL. exe, a HeidiSQL vagy más eszközöket. 
   Ha nem tudja, hogyan csatlakozhat, tekintse meg a következőt: a [MySQL Workbench használata a kapcsolódáshoz és az adatlekérdezéshez](./connect-workbench.md)

3. Szerkessze és futtassa a következő SQL-kódot. Cserélje le a helyőrző értékét `db_user` a kívánt új felhasználónévre, és a helyőrző értékét `testdb` a saját adatbázisának nevére.

   Ez az SQL Code-szintaxis egy új, testdb nevű adatbázist hoz létre példaként. Ezután létrehoz egy új felhasználót a MySQL szolgáltatásban, és minden jogosultságot biztosít az új adatbázis-sémához (testdb.\*) az adott felhasználó számára. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Ellenőrizze az adatbázison belüli támogatást.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Jelentkezzen be a kiszolgálóra, és adja meg a kijelölt adatbázist az új Felhasználónév és jelszó használatával. Ez a példa a MySQL parancssort jeleníti meg. Ezzel a paranccsal a rendszer a felhasználónévhez tartozó jelszót kéri. Cserélje le a saját kiszolgáló nevét, az adatbázis nevét és a felhasználónevet.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>További lépések
Nyissa meg a tűzfalat az új felhasználói gépek IP-címei számára a kapcsolódáshoz: [Azure Database for MySQL tűzfalszabályok létrehozása és kezelése a Azure Portal vagy az](howto-manage-firewall-using-portal.md) [Azure CLI](howto-manage-firewall-using-cli.md)használatával.

A felhasználói fiókok kezelésével kapcsolatos további információkért tekintse meg a következő témakört: MySQL termékdokumentáció a [felhasználói fiókok kezeléséhez](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), szintaxis és [jogosultságok](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html) [megadása](https://dev.mysql.com/doc/refman/5.7/en/grant.html).
