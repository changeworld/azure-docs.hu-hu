---
title: Tűzfalszabályok – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ez a cikk a Azure Database for PostgreSQL-nagy kapacitású (Citus) vonatkozó tűzfalszabályok leírását ismerteti.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b843cd1528630a21255053f623356a0379daacf6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975567"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Tűzfalszabályok a Azure Database for PostgreSQL-nagy kapacitású (Citus)
Azure Database for PostgreSQL kiszolgáló tűzfala megakadályozza a nagy kapacitású (Citus) koordinátor-csomóponthoz való hozzáférést, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal az egyes kérések származó IP-címe alapján engedélyezi a hozzáférést a kiszolgálóhoz.
A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. A tűzfalszabályok a kiszolgáló szintjén hozhatók létre.

**Tűzfalszabályok:** Ezek a szabályok lehetővé teszik, hogy az ügyfelek hozzáférhessenek a nagy kapacitású (Citus) koordinátori csomóponthoz, azaz az azonos logikai kiszolgálón belüli összes adatbázishoz. A kiszolgálói szintű tűzfalszabályok a Azure Portal használatával konfigurálhatók. A kiszolgálói szintű tűzfalszabályok létrehozásához az előfizetés tulajdonosának vagy az előfizetés közreműködőinek kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
A tűzfal alapértelmezés szerint letiltja a koordinátori csomóponthoz tartozó összes adatbázis-hozzáférést. A kiszolgáló másik számítógépről való használatának megkezdéséhez meg kell adnia egy vagy több kiszolgálóoldali tűzfalszabályok használatát a kiszolgálóhoz való hozzáférés engedélyezéséhez. A tűzfalszabályok használatával adhatja meg, hogy mely IP-címtartományok legyenek engedélyezve az internetről. A tűzfalszabályok nem érintik a Azure Portal webhelyhez való hozzáférést.
Az internetről és az Azure-ról érkező csatlakozási kísérleteknek előbb át kell haladnia a tűzfalon, mielőtt elérnék a PostgreSQL-adatbázist, ahogy az a következő ábrán is látható:

![Példa a tűzfal működésének folyamatára](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>Csatlakozás az internetről és az Azure-ból

A nagy kapacitású (Citus) kiszolgáló csoportjának tűzfala szabályozza, hogy ki csatlakozhat a csoport koordinátori csomópontjára. A tűzfal úgy határozza meg a hozzáférést, hogy a szabályok konfigurálható listáját tárgyalja. Minden szabály egy IP-cím vagy címtartomány, amely engedélyezve van a-ben.

Ha a tűzfal blokkolja a kapcsolatokat, az alkalmazás hibáihoz vezethet. A PostgreSQL JDBC-illesztőprogram használata például a következőhöz hasonló hibát jelez:

> Java. util. egyidejű. ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: végzetes: nincs PG\_HBA. conf bejegyzés a (z) "123.45.67.890" gazdagéphez, "citus", "citus", adatbázis: "", SSL

A szabályok definiálásának megismeréséhez tekintse meg a [Tűzfalszabályok létrehozása és kezelése](howto-hyperscale-manage-firewall-using-portal.md) című témakört.

## <a name="troubleshooting-the-database-server-firewall"></a>Az adatbázis-kiszolgáló tűzfalának hibaelhárítása
Ha a PostgreSQL-nagy kapacitású (Citus) szolgáltatáshoz való hozzáférés nem a várt módon működik, vegye figyelembe Microsoft Azure a következő pontokat:

* **Az engedélyezési lista módosításai még nem léptek érvénybe:** A nagy kapacitású (Citus) tűzfal konfigurációjának módosításaira vonatkozóan akár öt perc is eltelhet.

* **A felhasználó nincs hitelesítve, vagy helytelen jelszót használt:** Ha a felhasználó nem rendelkezik jogosultsággal a kiszolgálón, vagy helytelen a használt jelszó, a rendszer megtagadja a kapcsolódást a kiszolgálóval. A tűzfalbeállítások létrehozása csak olyan ügyfelek számára biztosít lehetőséget, akik megpróbálnak csatlakozni a kiszolgálóhoz; minden ügyfélnek továbbra is meg kell adnia a szükséges biztonsági hitelesítő adatokat.

Ha például egy JDBC-ügyfelet használ, a következő hibaüzenet jelenhet meg.
> Java. util. egyidejű. ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: végzetes: a jelszó-hitelesítés nem sikerült a (z) "Felhasználónév" felhasználó számára

* **Dinamikus IP-cím**: Ha az internetkapcsolata dinamikus IP-címkezeléssel rendelkezik, és problémákat okoz a tűzfalon való átjutás, próbálja ki a következő megoldások valamelyikét:

* Kérje meg az internetszolgáltatót (ISP) a nagy kapacitású (Citus) koordinátor csomóponthoz tartozó ügyfélszámítógépekhez rendelt IP-címtartomány számára, majd adja hozzá az IP-címtartományt tűzfalszabályként.

* Szerezze be a statikus IP-címzést az ügyfélszámítógépek helyett, majd adja hozzá a statikus IP-címet tűzfalszabályként.

## <a name="next-steps"></a>Következő lépések
A kiszolgálói szintű és az adatbázis-szintű Tűzfalszabályok létrehozásával kapcsolatos cikkekért lásd:
* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](howto-hyperscale-manage-firewall-using-portal.md)
