---
title: Az Azure Database biztonsági ellenőrzőlistája | Microsoft Docs
description: Ez a cikk az Azure Database biztonságára vonatkozó ellenőrzőlista-készletet tartalmaz.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: d9283a36d5f7ccb82b2cc211485487d5a3dcce7b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79201025"
---
# <a name="azure-database-security-checklist"></a>Az Azure Database biztonsági ellenőrzőlistája

A biztonság növelése érdekében az Azure Database számos beépített biztonsági vezérlőt tartalmaz, amelyek segítségével korlátozható és szabályozható a hozzáférés.

Ezek a következők:

-    Tűzfal, amely lehetővé teszi, hogy az IP-cím alapján korlátozza a kapcsolatra [vonatkozó tűzfalszabályok](../../sql-database/sql-database-firewall-configure.md) létrehozását,
-    A Azure Portal elérhető kiszolgáló szintű tűzfal
-    SSMS elérhető adatbázis-szintű tűzfalszabályok
-    Biztonságos kapcsolódás az adatbázishoz biztonságos kapcsolati karakterláncok használatával
-    Hozzáférés-kezelés használata
-    Adattitkosítás
-    Az SQL Database naplózási funkciója
-    SQL Database fenyegetések észlelése

## <a name="introduction"></a>Introduction (Bevezetés)
A felhő-számítástechnika olyan új biztonsági paradigmokat igényel, amelyek számos alkalmazás-felhasználó, adatbázis-rendszergazda és programozó számára ismeretlenek. Ennek eredményeképpen néhány szervezet vonakodik az adatkezeléshez szükséges felhőalapú infrastruktúra megvalósításában, az észlelt biztonsági kockázatok miatt. A probléma nagy része azonban a Microsoft Azureba beépített biztonsági funkciók jobb megismerésével és Microsoft Azure SQL Databaseával is enyhíthető.

## <a name="checklist"></a>Ellenőrzőlista
Javasoljuk, hogy az ellenőrzőlista áttekintése előtt olvassa el az [Azure-adatbázis biztonsági eljárásairól](database-best-practices.md) szóló cikket. Az ajánlott eljárások megismerése után a lehető legtöbbet hozhatja ki ebből az ellenőrzőlistából. Ezt a feladatlistát követve meggyőződhet arról, hogy az Azure Database biztonságával kapcsolatos fontos problémák merültek fel.


|Ellenőrzőlista kategóriája| Leírás|
| ------------ | -------- |
|**Az adatvédelem**||
| <br> Titkosítás a mozgásban/átvitelben| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol)az adattitkosításhoz, amikor az adatátvitelt a hálózatokra helyezi.</li><li>Az adatbázishoz biztonságos kommunikáció szükséges az ügyfelektől a (z) [TDS (táblázatos adatfolyam)](https://msdn.microsoft.com/library/dd357628.aspx) protokollon keresztül a TLS protokoll (Transport Layer Security) alapján.</li></ul> |
|<br>Titkosítás inaktív állapotban| <ul><li>[Transzparens adattitkosítás](https://go.microsoft.com/fwlink/?LinkId=526242), ha az inaktív adattárolást fizikailag bármilyen digitális formában tárolják.</li></ul>|
|**Hozzáférés vezérlése**||  
|<br> Adatbázis-hozzáférés | <ul><li>A [hitelesítési](../../sql-database/sql-database-manage-logins.md) (Azure Active Directory hitelesítés) ad-hitelesítés Azure Active Directory által felügyelt identitásokat használ.</li><li>Az [engedélyezési](../../sql-database/sql-database-manage-logins.md) jogosultsággal rendelkező felhasználóknak a legkevésbé szükséges jogosultságokkal kell rendelkezniük.</li></ul> |
|<br>Alkalmazás-hozzáférés| <ul><li>A [sor szintjének biztonsága](https://msdn.microsoft.com/library/dn765131) (a biztonsági házirend használatával egyidejűleg a felhasználó identitása, szerepköre vagy végrehajtási környezete alapján korlátozza a sor szintű hozzáférést).</li><li>[Dinamikus adatmaszkolás](../../sql-database/sql-database-dynamic-data-masking-get-started.md) (engedélyezési & házirend használata, a bizalmas adatok expozíciójának korlátozása a nem privilegizált felhasználóknak való maszkolással)</li></ul>|
|**Proaktív figyelés**||  
| <br>& Észlelésének nyomon követése| <ul><li>A [naplózás](../../sql-database/sql-database-auditing.md) nyomon követi az adatbázis eseményeit, és az [Azure Storage-fiókban](../../storage/common/storage-create-storage-account.md)lévő naplóba/tevékenység naplóba írja azokat.</li><li>Az Azure-adatbázis állapotának nyomon követése [Azure monitor tevékenységi naplók](../../azure-monitor/platform/platform-logs-overview.md)használatával.</li><li>A [veszélyforrások észlelése](../../sql-database/sql-database-threat-detection.md) rendellenes adatbázis-tevékenységeket észlel, ami az adatbázis potenciális biztonsági fenyegetéseket jelez. </li></ul> |
|<br>Azure Security Center| <ul><li>[Adatfigyelés](../../security-center/security-center-enable-auditing-on-sql-databases.md) A Azure Security Center központi biztonsági monitorozási megoldásként használható az SQL és más Azure-szolgáltatások számára.</li></ul>|        

## <a name="conclusion"></a>Összegzés
Az Azure Database egy robusztus adatbázis-platform, amely a számos szervezeti és szabályozási megfelelőségi követelménynek megfelelő biztonsági funkciók széles skáláját tartalmazza. Az adatvédelmet az adataihoz való fizikai hozzáférés szabályozásával, valamint a fájl-, oszlop-vagy sorcsoport szintjén a transzparens adattitkosítás, a sejtek szintjének titkosításával vagy a sorok szintjének biztonságával könnyedén védetté teheti. A Always Encrypted a titkosított adatok elleni műveleteket is lehetővé teszi, leegyszerűsítve az alkalmazások frissítéseinek folyamatát. A SQL Database tevékenység naplózási naplóihoz való hozzáférés pedig a szükséges információkat nyújtja, így megtudhatja, hogyan és mikor férhet hozzá az adatokhoz.

## <a name="next-steps"></a>Következő lépések
Mindössze néhány lépés végrehajtásával fokozhatja az adatbázis védelmét a rosszindulatú felhasználókkal és a jogosulatlan hozzáféréssel szemben. Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

- [Tűzfalszabályok](../../sql-database/sql-database-firewall-configure.md) beállítása a kiszolgálóhoz és az adatbázishoz.
- Az adatainak védelme [titkosítással](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- [SQL Database naplózás](../../sql-database/sql-database-auditing.md)engedélyezése.

