---
title: Biztonság – Azure Database for MySQL
description: A Azure Database for MySQL biztonsági funkcióinak áttekintése.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fb0a71a650a8c36d4da962adaf3f1f314c30d4c2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980007"
---
# <a name="security-in-azure-database-for-mysql"></a>Biztonság az Azure Database for MySQL-ben

Több biztonsági réteg is rendelkezésre áll, amelyek a Azure Database for MySQL-kiszolgálón lévő adatvédelmet biztosítanak. Ez a cikk ezeket a biztonsági beállításokat ismerteti.

## <a name="information-protection-and-encryption"></a>Adatvédelem és titkosítás

### <a name="in-transit"></a>Átvitel közben
A Azure Database for MySQL az adatátvitelt a Transport Layer Security segítségével titkosítja. A titkosítás (SSL/TLS) alapértelmezés szerint ki van kényszerítve.

### <a name="at-rest"></a>Nyugalmi állapotban
A Azure Database for MySQL szolgáltatás az FIPS 140-2 ellenőrzött titkosítási modult használja a REST-alapú adattárolási titkosításhoz. Az adatokat, beleértve a biztonsági másolatokat, a lemezeken titkosítva, a lekérdezések futtatásakor létrehozott ideiglenes fájlok kivételével. A szolgáltatás az Azure Storage-titkosításban található AES 256 bites titkosítást használja, és a kulcsokat a rendszer felügyeli. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le.


## <a name="network-security"></a>Hálózati biztonság
A Azure Database for MySQL-kiszolgálóval létesített kapcsolatok először egy regionális átjárón keresztül irányíthatók. Az átjáró nyilvánosan elérhető IP-címmel rendelkezik, a kiszolgáló IP-címei pedig védettek. Az átjáróval kapcsolatos további információkért tekintse meg a [kapcsolati architektúra című cikket](concepts-connectivity-architecture.md).  

Az újonnan létrehozott Azure Database for MySQL-kiszolgáló rendelkezik egy tűzfallal, amely blokkolja az összes külső kapcsolatot. Az átjáró elérése azonban nem engedélyezett a kiszolgálóhoz való kapcsolódáshoz. 

### <a name="ip-firewall-rules"></a>IP-tűzfalszabályok
Az IP-tűzfalszabályok az egyes kérelmek feladó IP-címe alapján biztosítanak hozzáférést a kiszolgálókhoz. További információért tekintse meg a [Tűzfalszabályok áttekintését](concepts-firewall-rules.md) .

### <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai
A Virtual Network szolgáltatás végpontja kiterjeszti a virtuális hálózati kapcsolatot az Azure-gerincen. A virtuális hálózati szabályok használatával engedélyezheti a Azure Database for MySQL-kiszolgáló számára a virtuális hálózatban lévő kijelölt alhálózatok kapcsolatainak engedélyezését. További információt a [Virtual Network szolgáltatás végpontjának áttekintése](concepts-data-access-and-security-vnet.md)című témakörben talál.

### <a name="private-ip"></a>Magánhálózati IP-cím
A privát hivatkozás lehetővé teszi, hogy privát végponton keresztül kapcsolódjon az Azure-Azure Database for MySQLhoz. Az Azure Private link lényegében az Azure-szolgáltatásokat a privát Virtual Networkon (VNet) belül hozza elérhetővé. A Péter-erőforrások a magánhálózati IP-cím használatával ugyanúgy érhetők el, mint a VNet lévő többi erőforráshoz. További információt a [privát hivatkozások áttekintése](concepts-data-access-security-private-link.md) című témakörben talál.

## <a name="access-management"></a>Hozzáférés-kezelés

A Azure Database for MySQL-kiszolgáló létrehozásakor meg kell adnia egy rendszergazdai felhasználó hitelesítő adatait. Ez a rendszergazda további MySQL-felhasználók létrehozására is használható.


## <a name="threat-protection"></a>Fenyegetések elleni védelem

A komplex [veszélyforrások elleni védelem](concepts-data-access-and-security-threat-protection.md) bekapcsolásával olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek a kiszolgálók eléréséhez vagy kiaknázásához.

A [naplózás elérhető](concepts-audit-logs.md) az adatbázisok tevékenységeinek nyomon követéséhez. 


## <a name="next-steps"></a>Következő lépések
- Tűzfalszabályok engedélyezése [IP](concepts-firewall-rules.md) -címekhez vagy [virtuális hálózatokhoz](concepts-data-access-and-security-vnet.md)