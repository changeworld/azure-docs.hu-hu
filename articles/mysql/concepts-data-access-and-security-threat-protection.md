---
title: Komplex veszélyforrások elleni védelem – Azure Database for MySQL
description: Ismerje meg a komplex veszélyforrások elleni védelem fogalmait, amely észleli a rendellenes adatbázis-tevékenységeket, amelyek az adatbázis potenciális biztonsági fenyegetéseit jelzik.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 747e2f088b545026775feed45535b1f9bc90209f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74771002"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure Database for MySQL komplex veszélyforrások elleni védelem

Az Azure Database for MySQL-hez készült Komplex veszélyforrások elleni védelem észleli az adatbázisok hozzáférésére és az adatbázisok biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket.

> [!NOTE]
> A komplex veszélyforrások elleni védelem nyilvános előzetes verzióban érhető el.

A komplex veszélyforrások elleni védelem a fejlett adatbiztonsági ajánlat része, amely egy egységes csomag a speciális biztonsági funkciókhoz. A komplex veszélyforrások elleni védelem a [Azure Portalon](https://portal.azure.com) keresztül vagy [REST API](/rest/api/mysql/serversecurityalertpolicies)használatával érhető el és kezelhető. A szolgáltatás általános célú és memóriára optimalizált kiszolgálókon érhető el.

> [!NOTE]
> A komplex veszélyforrások elleni védelem funkció a következő Azure Government-és szuverén felhő-régiókban **nem** érhető el: US Gov Texas, US Gov Arizona, US gov Iowa, USA, gov Virginia, US DoD – keleti régió, US DoD – középső régió, Közép-németország, Észak-Németország, Kelet-Kína, Kelet-Kína 2. Tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/) az általános termékek rendelkezésre állása érdekében.


## <a name="what-is-advanced-threat-protection"></a>Mi a komplex veszélyforrások elleni védelem?

A Azure Database for MySQL komplex veszélyforrások elleni védelme új biztonsági réteget biztosít, amely lehetővé teszi az ügyfelek számára, hogy a rendellenes tevékenységekre vonatkozó biztonsági riasztások révén észlelni és reagálni tudjanak a potenciális fenyegetésekre. A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről, valamint a lehetséges sebezhetőségekről, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. A Azure Database for MySQL komplex veszélyforrások elleni védelme a riasztásokat [Azure Security Centerokkal](https://azure.microsoft.com/services/security-center/)integrálja, amely a gyanús tevékenységek részleteit tartalmazza, és a fenyegetés kivizsgálásával és enyhítésével kapcsolatos lépéseket javasolja. A Azure Database for MySQL komplex veszélyforrások elleni védelme egyszerűvé teszi a potenciális fenyegetések kezelését az adatbázison anélkül, hogy biztonsági szakértőnek kellene lennie, vagy speciális biztonsági monitorozási rendszereket kellene kezelnie. 

![Komplex veszélyforrások elleni védelem fogalma](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Komplex veszélyforrások elleni védelem – riasztások 
A Azure Database for MySQL komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához, és a következő riasztásokat indíthatják el:
- **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha megváltozik a Azure Database for MySQL-kiszolgáló hozzáférési mintája, ahol valaki szokatlan földrajzi helyről jelentkezett be a Azure Database for MySQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan Azure-adatközpontból**: Ez a riasztás akkor aktiválódik, ha módosul a hozzáférési minta a Azure Database for MySQL-kiszolgálón, ahol valaki egy szokatlan Azure-adatközpontból jelentkezett be a kiszolgálóra, amely a legutóbbi időszakban volt látható. Bizonyos esetekben a riasztás legitim műveletet észlel (az új alkalmazás az Azure-ban, Power BI, Azure Database for MySQL a lekérdezés-szerkesztőben). Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés a nem ismert rendszerbiztonsági**csoporttól: Ez a riasztás akkor aktiválódik, ha módosul a hozzáférési minta a Azure Database for MySQL-kiszolgálón, ahol valaki egy szokatlan rendszerbiztonsági tag (Azure Database for MySQL felhasználó) használatával jelentkezett be a kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Találgatásos kényszerített Azure Database for MySQL hitelesítő adatok**: Ez a riasztás akkor aktiválódik, ha a különböző hitelesítő adatokkal rendelkező sikertelen bejelentkezések rendellenesen magas száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* A díjszabással kapcsolatos további információkért tekintse meg a [Azure Database for MySQL díjszabási oldalát](https://azure.microsoft.com/pricing/details/mysql/) . 
* [Azure Database for MySQL komplex veszélyforrások elleni védelem](howto-database-threat-protection-portal.md) konfigurálása a Azure Portal használatával  
