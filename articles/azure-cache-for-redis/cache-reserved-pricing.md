---
title: Előre fizetés a számításhoz fenntartott kapacitással – Azure cache a Redis
description: Előre fizetés az Azure cache számára a fenntartott kapacitású számítási erőforrások Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77530301"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Előre fizetés az Azure cache számára a fenntartott kapacitású számítási erőforrások Redis

Az Azure cache for Redis mostantól lehetővé teszi a pénz megtakarítását a számítási erőforrások előre fizetett költségeivel az utólagos elszámolású árakhoz képest. Az Azure cache for Redis fenntartott kapacitás esetén egy vagy három éves időszakra vonatkozó előzetes kötelezettségvállalás a gyorsítótárban, hogy a számítási költségek jelentős kedvezményt kapjanak. Az Azure cache Redis fenntartott kapacitásának megvásárlásához meg kell adnia az Azure-régiót, a szolgáltatási szintet és a kifejezést.

A foglalást nem kell hozzárendelni adott Azure cache-hez a Redis-példányokhoz. A Redis vagy újonnan üzembe helyezett Azure cache már futtatva automatikusan kihasználja a fenntartott díjszabás előnyeit, a lefoglalt gyorsítótár méretétől függően. A foglalás megvásárlásával előre fizet a számítási költségekre egy vagy három év alatt. A foglalás megvásárlása után a foglalási attribútumoknak megfelelő Redis számítási díjakra vonatkozó Azure cache-t már nem számoljuk el az utólagos elszámolású díjszabás alapján. A foglalás nem vonatkozik a gyorsítótárhoz társított hálózati vagy tárolási díjakra. A foglalási időszak végén a számlázási juttatás lejár, és a Redis tartozó Azure cache számlázása az utólagos elszámolású díjszabás szerint történik. A foglalások nem automatikus megújítást végeznek. A díjszabással kapcsolatos információkért tekintse meg az [Azure cache Redis fenntartott kapacitás ajánlatát](https://azure.microsoft.com/pricing/details/cache).

Az Azure cache Redis fenntartott kapacitást is vásárolhat a [Azure Portalban](https://portal.azure.com/). A fenntartott kapacitás megvásárlása:

* Legalább egy Nagyvállalati vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
* A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg az Azure cache-t a Redis fenntartott kapacitáshoz.

Annak részleteiről, hogy a nagyvállalati ügyfelek és az utólagos elszámolású ügyfelek hogyan számítanak fel díjat a foglalások beszerzéséhez, tekintse meg az [Azure foglalás használatának ismertetése a nagyvállalati beléptetéssel](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) és [Az Azure-foglalás használatának megismerése](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)az utólagos elszámolású előfizetések esetében.


## <a name="determine-the-right-cache-size-before-purchase"></a>A gyorsítótár megfelelő méretének meghatározása a vásárlás előtt

A foglalás méretének a meglévő vagy hamarosan üzembe helyezett gyorsítótár egy adott régión belüli és ugyanazon szolgáltatási réteg használatával felhasznált teljes számítási mennyiségén kell alapulnia.

Tegyük fel például, hogy egy általános célt futtat, Gen5 – 32 virtuális mag cache-t és két memóriát optimalizált, Gen5 – 16 virtuális mag gyorsítótárat. Továbbá Tételezzük fel, hogy a következő hónapban szeretne üzembe helyezni egy további általános célt, a Gen5 – 32 virtuális mag adatbázis-kiszolgálót és egy, a Gen5 – 16 virtuális mag adatbázis-kiszolgálót. Tegyük fel, hogy tudnia kell, hogy legalább 1 évig szüksége lesz ezekre az erőforrásokra. Ebben az esetben vásárolnia kell egy 64 (2x32) virtuális mag, 1 éves foglalást az Egyadatbázisos általános célú Gen5 és a 48 (2x16 + 16) virtuális mag 1 év foglalása egyetlen adatbázishoz optimalizált memória esetén – Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Azure cache vásárlása a Redis fenntartott kapacitáshoz

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd a foglalások ablaktáblán válassza a **Redis Azure cache** lehetőséget, ha új foglalást szeretne vásárolni a gyorsítótárak számára.
4. Töltse ki a kötelező mezőket. Meglévő vagy új adatbázisok, amelyek megfelelnek a kiválasztott attribútumoknak, a fenntartott kapacitás kedvezményét kapják meg. A kedvezményt megkapó Redis-példányok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.


![A fenntartott díjszabás áttekintése](media/cache-reserved-pricing/cache-reserved-price.png)


A következő táblázat a kötelező mezőket ismerteti.

| Mező | Leírás |
| :------------ | :------- |
| Előfizetést   | A Redis számára fenntartott kapacitás foglalásához az Azure cache-re való fizetéshez használt előfizetés. Az előfizetéshez tartozó fizetési módot az Azure cache Redis fenntartott kapacitás foglalásának előzetes költségei alapján számítjuk fel. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P), vagy az utólagos elszámolású díjszabással rendelkező egyéni szerződés (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. Az utólagos elszámolású előfizetések esetében az előfizetés díjait a bankkártya vagy a számla fizetési módja alapján számítjuk fel.
| Hatókör | A foglalás hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja: </br></br> **Megosztva**a foglalási kedvezményt a rendszer az Azure cache-re alkalmazza a számlázási környezetben lévő előfizetésekben futó Redis-példányok esetében. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.</br></br> **Egy előfizetés**esetén a foglalási kedvezmény az Azure cache-re érvényes az előfizetéshez tartozó Redis-példányok esetében. </br></br> **Egy erőforráscsoport**, a foglalási kedvezményt a rendszer az Azure cache-re alkalmazza a kijelölt előfizetésben található Redis-példányok esetében, valamint az adott előfizetéshez tartozó kiválasztott erőforráscsoportot.
| Régió | A Redis fenntartott kapacitás foglalásához az Azure cache által érintett Azure-régió.
| Tarifacsomag | A Redis-kiszolgálók Azure gyorsítótárának szolgáltatási szintje.
| Időszak | Egy vagy három év
| Mennyiség | Az Azure cache-ben megvásárolni kívánt számítási erőforrások mennyisége a Redis fenntartott kapacitás foglalásához. A mennyiség a kijelölt Azure-régióban és szolgáltatási szinten foglalt gyorsítótárak száma, amelyek foglalása folyamatban van, és a számlázási kedvezményt kapja. Ha például olyan Redis-kiszolgálókon futtatja az Azure cache-t, amelyeken az USA keleti régiójában a teljes gyorsítótár kapacitása összesen 26 GB, akkor a mennyiséget 26-ra kell megadnia, hogy maximalizálja az összes gyorsítótár előnyét.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="cache-size-flexibility"></a>Gyorsítótár méretének rugalmassága

A gyorsítótár méretének rugalmassága segít a szolgáltatási szinten vagy régióban történő vertikális felskálázásban, a fenntartott kapacitási juttatás elvesztése nélkül.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Következő lépések

A foglalási kedvezményt a rendszer automatikusan alkalmazza az Azure gyorsítótárba olyan Redis-példányok esetében, amelyek megfelelnek a foglalási hatókörnek és attribútumoknak. A foglalás hatókörét a Azure Portal, a PowerShell, az Azure CLI vagy az API segítségével frissítheti.

*  Ha szeretné megtudni, hogyan alkalmazza a fenntartott kapacitást a Redis Azure cache-re, tekintse meg [Az Azure foglalási kedvezmény ismertetése](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md) című témakört.

* Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

    * [Mi az az Azure Reservations?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Az Azure Reservations kezelése](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Az Azure Reservations-kedvezmény ismertetése](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [A foglalási kihasználtság ismertetése vállalati regisztrációnál](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)

