---
title: Tűzfalszabályok – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan használhatók a tűzfalszabályok a Azure Database for PostgreSQL-egyetlen kiszolgálóhoz való kapcsolódáshoz.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5d462be1caa3787cb7ff9a455be595ec5784eefe
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157270"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Tűzfalszabályok Azure Database for PostgreSQL – egyetlen kiszolgáló
Azure Database for PostgreSQL kiszolgáló tűzfala megakadályozza az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal az egyes kérések származó IP-címe alapján engedélyezi a hozzáférést a kiszolgálóhoz.
A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. A tűzfalszabályok a kiszolgáló szintjén hozhatók létre.

**Tűzfalszabályok:** Ezek a szabályok lehetővé teszik az ügyfelek számára, hogy elérjék a teljes Azure Database for PostgreSQL-kiszolgálót, azaz az azonos logikai kiszolgálón belüli összes adatbázist. A kiszolgálói szintű tűzfalszabályok a Azure Portal vagy az Azure CLI-parancsok használatával konfigurálhatók. A kiszolgálói szintű tűzfalszabályok létrehozásához az előfizetés tulajdonosának vagy az előfizetés közreműködőinek kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
A tűzfal alapértelmezés szerint letiltja a Azure Database for PostgreSQL-kiszolgálóhoz tartozó összes adatbázis-hozzáférést. A kiszolgáló másik számítógépről való használatának megkezdéséhez meg kell adnia egy vagy több kiszolgálóoldali tűzfalszabályok használatát a kiszolgálóhoz való hozzáférés engedélyezéséhez. A tűzfalszabályok használatával adhatja meg, hogy mely IP-címtartományok legyenek engedélyezve az internetről. A tűzfalszabályok nem érintik a Azure Portal webhelyhez való hozzáférést.
Az internetről és az Azure-ról érkező csatlakozási kísérleteknek előbb át kell haladnia a tűzfalon, mielőtt elérnék a PostgreSQL-adatbázist, ahogy az a következő ábrán is látható:

![Példa a tűzfal működésének folyamatára](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
A kiszolgálói szintű tűzfalszabályok a Azure Database for PostgreSQL kiszolgálón található összes adatbázisra érvényesek. Ha a kérés IP-címe a kiszolgálószintű tűzfalszabályokban megadott tartományok egyikében található, a tűzfal engedélyezi a csatlakozást.
Ha a kérés IP-címe nem a kiszolgálói szintű tűzfalszabályok egyikében megadott tartományon belül van, a kapcsolati kérelem sikertelen lesz.
Ha például az alkalmazás a PostgreSQL-hez készült JDBC-illesztőprogrammal csatlakozik, akkor ez a hiba akkor jelentkezhet, amikor a tűzfal blokkolja a kapcsolatot.
> Java. util. egyidejű. ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: végzetes: nincs PG\_HBA. conf bejegyzés a (z) "123.45.67.890" gazdagéphez, "adminuser", "PostgreSQL" adatbázis ", SSL

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Javasoljuk, hogy keresse meg bármely alkalmazás vagy szolgáltatás kimenő IP-címét, és explicit módon engedélyezze az adott IP-címekhez vagy tartományokhoz való hozzáférést. Megkeresheti például egy Azure App Service kimenő IP-címét, vagy használhat egy virtuális géphez vagy más erőforráshoz kötött nyilvános IP-címet (lásd alább a virtuális gép magánhálózati IP-címével való csatlakozáshoz a szolgáltatási végpontokon). 

Ha az Azure-szolgáltatáshoz nem érhető el rögzített kimenő IP-cím, az összes Azure-adatközpont IP-címéről is engedélyezheti a kapcsolódást. Ezt a beállítást engedélyezheti a Azure Portal az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** lehetőségre a **kapcsolat biztonsági** paneljén **, és** a **Mentés**gombra kattintva. Az Azure CLI-ből egy tűzfalszabály-beállítás, amely a 0.0.0.0 értékkel egyenlő, és ezzel egyenértékű. Ha a kapcsolódási kísérlet nem engedélyezett, a kérelem nem éri el a Azure Database for PostgreSQL-kiszolgálót.

> [!IMPORTANT]
> Az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítással konfigurálhatja a tűzfalat úgy, hogy az az Azure összes kapcsolatát engedélyezze, beleértve a más ügyfelek előfizetéseit is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

![Az Azure-szolgáltatásokhoz való hozzáférés engedélyezése a portálon](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Csatlakozás VNet
Ha biztonságosan szeretne csatlakozni a Azure Database for PostgreSQL-kiszolgálóhoz egy VNet, vegye fontolóra a [VNet szolgáltatás-végpontok](./concepts-data-access-and-security-vnet.md)használatát. 

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
A Azure Portalon kívül a tűzfalszabályok programozott módon is kezelhetők az Azure CLI használatával.
Lásd még: [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése az Azure CLI-](howto-manage-firewall-using-cli.md) vel

## <a name="troubleshooting-firewall-issues"></a>Tűzfalakkal kapcsolatos problémák elhárítása
Vegye figyelembe a következő szempontokat, amikor a PostgreSQL-kiszolgáló Microsoft Azure adatbázisához való hozzáférés nem a várt módon viselkedik:

* **Az engedélyezési lista módosításai még nem léptek érvénybe:** A Azure Database for PostgreSQL-kiszolgáló tűzfal-konfigurációjának változásaira vonatkozóan akár öt perc is eltelhet.

* **A bejelentkezési azonosító nincs engedélyezve, vagy helytelen jelszót használt:** Ha a bejelentkezési azonosító nem rendelkezik engedéllyel a Azure Database for PostgreSQL-kiszolgálón, vagy helytelen a használt jelszó, a rendszer megtagadja a kapcsolódást a Azure Database for PostgreSQL-kiszolgálóhoz. A tűzfalbeállítások létrehozása csak olyan ügyfelek számára biztosít lehetőséget, akik megpróbálnak csatlakozni a kiszolgálóhoz; minden ügyfélnek továbbra is meg kell adnia a szükséges biztonsági hitelesítő adatokat.

   Ha például egy JDBC-ügyfelet használ, a következő hibaüzenet jelenhet meg.
   > Java. util. egyidejű. ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: végzetes: a jelszó-hitelesítés nem sikerült a (z) "Felhasználónév" felhasználó számára

* **Dinamikus IP-cím**: Ha az internetkapcsolata dinamikus IP-címkezeléssel rendelkezik, és problémákat okoz a tűzfalon való átjutás, próbálja ki a következő megoldások valamelyikét:

   * Kérje meg az internetszolgáltatót (ISP) a Azure Database for PostgreSQL-kiszolgálót elérő ügyfélszámítógépekhez hozzárendelt IP-címtartomány számára, majd adja hozzá az IP-címtartományt tűzfalszabályként.

   * Szerezze be a statikus IP-címzést az ügyfélszámítógépek helyett, majd adja hozzá a statikus IP-címet tűzfalszabályként.

* **Úgy tűnik, hogy a kiszolgáló IP-címe nyilvános:** A Azure Database for PostgreSQL-kiszolgálóval létesített kapcsolatok egy nyilvánosan elérhető Azure-átjárón keresztül irányíthatók. A kiszolgáló tényleges IP-címét azonban tűzfal védi. További információért tekintse meg a [kapcsolati architektúráról szóló cikket](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Következő lépések
A kiszolgálói szintű és az adatbázis-szintű Tűzfalszabályok létrehozásával kapcsolatos cikkekért lásd:
* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](howto-manage-firewall-using-portal.md)
* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése az Azure CLI-vel](howto-manage-firewall-using-cli.md)
- [VNet szolgáltatásbeli végpontok a Azure Database for PostgreSQL](./concepts-data-access-and-security-vnet.md)
