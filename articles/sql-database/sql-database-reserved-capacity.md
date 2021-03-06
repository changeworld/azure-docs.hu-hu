---
title: Költségek megtakarítása
description: Megtudhatja, hogyan vásárolhat Azure SQL Database fenntartott kapacitást a számítási költségek mentéséhez.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 0713e1ed98cc87b95cad1f84672148cd062e0b58
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979994"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>SQL Database számítási erőforrások költségeinek megtakarítása Azure SQL Database fenntartott kapacitással

Az utólagos elszámolású díjszabáshoz képest a számítási erőforrások foglalásával megtakaríthatja Azure SQL Databaseeit. A Azure SQL Database fenntartott kapacitással egy vagy három éves időszakra kiterjedően vállaljuk a SQL Database használatát, hogy a számítási költségek jelentős kedvezményt kapjanak. SQL Database fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a központi telepítési típust, a teljesítménnyel kapcsolatos szintet és a kifejezést.


A foglalást nem kell hozzárendelni meghatározott SQL Database példányokhoz (különálló adatbázisok, rugalmas készletek vagy felügyelt példányok). Az olyan SQL Database példányok, amelyek már futnak vagy újonnan telepítettek, automatikusan megkapják a kedvezményt. A foglalás megvásárlásával véglegesíti a számítási költségek felhasználását egy vagy három év alatt. A foglalás megvásárlása után a foglalási attribútumoknak megfelelő SQL Database számítási díjakra már nem számítunk fel díjat. A foglalások nem fedik le a SQL Database-példányhoz társított szoftver-, hálózatkezelési és tárolási díjakat. A foglalási időszak végén a számlázási juttatás lejár, és az SQL-adatbázisok díját az utólagos elszámolású díjszabás szerint számoljuk el. A foglalások nem automatikus megújítást végeznek. A díjszabással kapcsolatos információkért tekintse meg a [SQL Database fenntartott kapacitás ajánlatát](https://azure.microsoft.com/pricing/details/sql-database/managed/).

A [Azure Portal](https://portal.azure.com)Azure SQL Database fenntartott kapacitást is vásárolhat. A foglalásért fizethet [előre vagy havi részletekben](../cost-management-billing/reservations/monthly-payments-reservations.md). SQL Database fenntartott kapacitás megvásárlásához:

- Legalább egy Nagyvállalati vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
- Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg SQL Database fenntartott kapacitást.

Az ügyfelek és az utólagos elszámolású ügyfelek díjszabása a foglalások beszerzése után: az [Azure foglalási szolgáltatás használatának megismerése a nagyvállalati beléptetéshez](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) , valamint az [Azure-foglalás használatának](../cost-management-billing/reservations/understand-reserved-instance-usage.md)ismertetése az utólagos elszámolású előfizetéshez.

## <a name="determine-the-right-sql-size-before-purchase"></a>A megfelelő SQL-méret meghatározása a vásárlás előtt

A foglalás méretének a meglévő vagy hamarosan üzembe helyezett önálló adatbázisok, rugalmas készletek vagy felügyelt példányok által az adott régión belüli, valamint az azonos teljesítményszint és hardveres generáció használatával felhasznált teljes számítási mennyiség alapján kell alapulnia.

Tegyük fel például, hogy egy általános célt futtat, Gen5 – 16 virtuális mag rugalmas készletet, és két üzleti szempontból kritikus, Gen5 – 4 virtuális mag önálló adatbázist használ. Továbbá Tételezzük fel, hogy a következő hónapban szeretne üzembe helyezni egy további általános célú, Gen5 – 16 virtuális mag rugalmas készletet és egy üzleti szempontból kritikus Gen5 – 32 virtuális mag rugalmas készletet. Azt is tegyük fel, hogy tudnia kell, hogy legalább 1 évig szüksége lesz ezekre az erőforrásokra. Ebben az esetben vásárolnia kell egy 32 (2x16) virtuális mag, 1 éves foglalást az önálló adatbázisok/rugalmas készlet általános célú Gen5 és a 40 (2x4 + 32) virtuális mag 1 éves foglalás a különálló adatbázis/rugalmas készlet üzleti szempontból kritikus fontosságú – Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>SQL Database fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd a foglalások ablaktáblán válassza a **SQL Database** lehetőséget a SQL Database új foglalásának megvásárlásához.
4. Töltse ki a kötelező mezőket. Meglévő vagy új önálló adatbázisok, rugalmas készletek vagy felügyelt példányok, amelyek megfelelnek a kiválasztott attribútumoknak, a fenntartott kapacitás kedvezményét kapják meg. A kedvezményt megkapó SQL Database példányok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.
    ![képernyőkép a SQL Database fenntartott kapacitás vásárlásának elküldése előtt](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

A következő táblázat a kötelező mezőket ismerteti.

| Mező      | Leírás|
|------------|--------------|
|Előfizetés|A SQL Database fenntartott kapacitás foglalásának megfizetéséhez használt előfizetés. Az előfizetéshez tartozó fizetési módot a SQL Database fenntartott kapacitás foglalásának előzetes költségei alapján számítjuk fel. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P), vagy az utólagos elszámolású díjszabással rendelkező egyéni szerződés (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. Az utólagos elszámolású előfizetések esetében az előfizetés díjait a bankkártya vagy a számla fizetési módja alapján számítjuk fel.|
|Hatókör       |A virtuális mag foglalási hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja: <br/><br/>**Megosztott**, a virtuális mag foglalási kedvezményt a rendszer a számlázási környezetben lévő előfizetésekben futó SQL Database példányokra alkalmazza. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.<br/><br/>**Egyszeri előfizetés**esetén a virtuális mag foglalási kedvezményt az előfizetésben SQL Database példányokra alkalmazza a rendszer. <br/><br/>**Egy erőforráscsoport**, a foglalási kedvezményt a rendszer a kijelölt előfizetésben található SQL Database példányokra, valamint az adott előfizetéshez tartozó kiválasztott erőforráscsoporthoz alkalmazza.|
|Region (Régió)      |Az SQL Database fenntartott kapacitás foglalása által érintett Azure-régió.|
|Központi telepítés típusa|Az az SQL-erőforrástípus, amelyre a foglalást megvásárolni szeretné.|
|Teljesítményszint|A SQL Database példányok szolgáltatási szintje.
|Időtartam        |Egy vagy három év.|
|Mennyiség    |A SQL Database fenntartott kapacitás foglalása keretében megvásárolt számítási erőforrások mennyisége. A mennyiség a kiválasztott Azure-régióban és a lefoglalt teljesítményszint virtuális mag, és a számlázási kedvezményt kapja. Ha például futtatja vagy tervezi SQL Database példányok futtatását az USA keleti régiójában lévő Gen5 16 virtuális mag teljes számítási kapacitásával, akkor az összes példány előnyének maximalizálásához a 16-as mennyiséget kell megadnia. |

1. Tekintse át a SQL Database fenntartott kapacitás foglalásának költségeit a costs ( **költségek** ) szakaszban.
1. Válassza a **Beszerzés** lehetőséget.
1. Válassza a **foglalás megtekintése** lehetőséget a vásárlás állapotának megtekintéséhez.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Virtuális mag méretének rugalmassága

a virtuális mag méretének rugalmassága segít a teljesítmény-és a régión belüli vertikális felskálázásban, a fenntartott kapacitási juttatás elvesztése nélkül. SQL Database fenntartott kapacitással rugalmasan helyezheti át a gyors adatbázisokat a készletek és az önálló adatbázisok között a normál működés (ugyanabban a régióban és teljesítményszint) részeként, a fenntartott kapacitás elvesztése nélkül. Benefit. Ha nem alkalmazott puffert tart fenn a foglalásban, a teljesítménybeli tüskéket hatékonyan kezelheti a költségkeret túllépése nélkül.

## <a name="limitation"></a>Korlátozás

Nem foglalhat le DTU-alapú (alapszintű, standard vagy prémium) SQL-adatbázisokat.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Következő lépések

Az virtuális mag foglalási kedvezményt a rendszer automatikusan alkalmazza azon SQL Database példányok számára, amelyek megfelelnek az SQL Database fenntartott kapacitás foglalási hatókörének és attribútumainak. A SQL Database fenntartott kapacitás foglalásának hatókörét a [Azure Portal](https://portal.azure.com), a PowerShell, a CLI vagy az API segítségével frissítheti.

A SQL Database fenntartott kapacitás foglalásának kezelésével kapcsolatos információkért lásd: [SQL Database fenntartott kapacitás kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mi az az Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Az Azure Reservations kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Az Azure Reservations-kedvezmény ismertetése](../cost-management-billing/reservations/understand-reservation-charges.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)
