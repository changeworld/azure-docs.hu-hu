---
title: Összetett veszélyforrások elleni védelem használata – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A fenyegetések elleni védelem rendellenes adatbázis-tevékenységeket észlel, ami az adatbázis potenciális biztonsági fenyegetéseket jelez.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 8b7f52ea318432e97a450a54526f6481b14139c9
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74776143"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql---single-server"></a>Komplex veszélyforrások elleni védelem Azure Database for PostgreSQL – egyetlen kiszolgáló

Az Azure Database for PostgreSQL-hez készült Komplex veszélyforrások elleni védelem észleli az adatbázisok hozzáférésére és az adatbázisok biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket.

A komplex veszélyforrások elleni védelem a fejlett adatbiztonsági ajánlat része, amely egy egységes csomag a speciális biztonsági funkciókhoz. A komplex veszélyforrások elleni védelem a [Azure Portalon](https://portal.azure.com) keresztül érhető el és felügyelhető, és jelenleg előzetes verzióban érhető el.

> [!NOTE]
> A komplex veszélyforrások elleni védelem funkció a következő Azure Government-és szuverén felhő-régiókban **nem** érhető el: US Gov Texas, US Gov Arizona, US gov Iowa, USA, gov Virginia, US DoD – keleti régió, US DoD – középső régió, Közép-németország, Észak-Németország, Kelet-Kína, Kelet-Kína 2. Tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/) az általános termékek rendelkezésre állása érdekében.
>

> [!NOTE]
> Ez a funkció az Azure minden régiójában elérhető, ahol a Azure Database for PostgreSQL általános célú és a memóriára optimalizált kiszolgálók esetében van telepítve.

## <a name="set-up-threat-detection"></a>Fenyegetés észlelésének beállítása
1. Indítsa el a Azure Portalt a [https://portal.azure.com](https://portal.azure.com).
2. Navigáljon a védelemmel ellátni kívánt Azure Database for PostgreSQL-kiszolgáló konfigurációs lapjára. A biztonsági beállítások területen válassza a **speciális veszélyforrások elleni védelem (előzetes verzió)** lehetőséget.
3. A **speciális veszélyforrások elleni védelem (előzetes verzió)** konfigurációs lapján:

   - Az összetett veszélyforrások elleni védelem engedélyezése a kiszolgálón.
   - Az **összetett veszélyforrások elleni védelem beállításaiban**a **riasztások küldése** a szövegmezőbe mezőbe írja be azoknak az e-maileknek a listáját, amelyek biztonsági riasztásokat kapnak a rendellenes adatbázis-tevékenységek észlelése után.
  
   ![Fenyegetés észlelésének beállítása](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>A rendellenes adatbázis-tevékenységek megismerése

A rendellenes adatbázis-tevékenységek észlelése után e-mailben értesítést kap. Az e-mail információt nyújt a gyanús biztonsági eseményekről, beleértve a rendellenes tevékenységek jellegét, az adatbázis nevét, a kiszolgáló nevét, az alkalmazás nevét és az esemény időpontját. Emellett az e-mail-cím a lehetséges okokkal és a javasolt műveletekkel kapcsolatos információkat nyújt az adatbázis lehetséges fenyegetésének kivizsgálásához és enyhítéséhez.
    
1. Kattintson a **legutóbbi riasztások megtekintése** hivatkozásra az e-mailben a Azure Portal elindításához és a Azure Security Center riasztások oldal megjelenítéséhez, amely áttekintést nyújt az SQL Database-ben észlelt aktív fenyegetésekről.
    
    ![Rendellenes tevékenység jelentés](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Aktív fenyegetések megtekintése:

    ![Aktív fenyegetések](./media/howto-database-threat-protection-portal/active-threats.png)

2. Egy adott riasztásra kattintva további részleteket és műveleteket kaphat a fenyegetés kivizsgálásához és a jövőbeli fenyegetések szervizelését.
    
    ![Adott riasztás](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Fenyegetések észlelésével kapcsolatos riasztások megismerése

A komplex veszélyforrások elleni védelem a [Azure Security Centerával](https://azure.microsoft.com/services/security-center/)integrálja a riasztásokat. 

Kattintson a **biztonsági riasztások** elemre a **veszélyforrások védelme** területen a Azure Security Center riasztások oldal elindításához, és tekintse át az adatbázisban észlelt aktív SQL-fenyegetéseket.

  ![Veszélyforrások elleni védelem ASC](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* A díjszabással kapcsolatos további információkért tekintse meg a [Azure Database for PostgreSQL díjszabási oldalát](https://azure.microsoft.com/pricing/details/postgresql/) .  
