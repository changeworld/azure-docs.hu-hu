---
title: Előre fizetés a szoftvercsomagok számára – Azure Reservations
description: Megtudhatja, hogyan fizethet elő előre a szoftveres csomagokra az utólagos elszámolású költségek megtakarítása érdekében.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3bb7a62433993f1af26b1ce8bcb4ed258c34623c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75973131"
---
# <a name="prepay-for-azure-software-plans"></a>Előre fizetés Azure-szoftvercsomagokért

Ha az Azure-ban előre fizetett SUSE-és RedHat-szoftveres használatot, az utólagos elszámolású költségekkel megtakaríthat pénzt. A kedvezmények csak a SUSE és a RedHat mérőszámokra vonatkoznak, és nem a virtuális gépek használatára. A további megtakarítások érdekében a virtuális gépek foglalása külön vásárolható meg.

A Azure Portalban a SUSE és a RedHat szoftvercsomag is megvásárolható. Csomag megvásárlása:

- Az utólagos elszámolású díjszabáshoz legalább egy Enterprise vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie.
- Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha a beállítás le van tiltva, az előfizetés nagyvállalati rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program esetében a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg a szoftvercsomagot.

## <a name="buy-a-software-plan"></a>Szoftvercsomag vásárlása

1. Jelentkezzen be a Azure Portalba, és lépjen a [foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)pontra.
2. Kattintson a **Hozzáadás** elemre, majd válassza ki a megvásárolni kívánt szoftvercsomagot.
Töltse ki a kötelező mezőket. Minden olyan SUSE Linux rendszerű virtuális gép vagy RedHat virtuális gép, amely megfelel a megvásárolni kívánt mennyiségnek, a kedvezményt kapja. A kedvezményt megkapó központi telepítések tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.
3. Válasszon előfizetést. A csomagért kell fizetni.
Az előfizetés fizetési módja a foglalás előzetes díjait terheli. Az előfizetés típusának Nagyvállalati Szerződésnak kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy külön szerződés az utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P).
    - Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva.
    - Az utólagos elszámolású előfizetések díját az előfizetés bankkártyája vagy a számla fizetési módja alapján számítjuk fel.
4. Válassza ki a hatókört. A hatókör egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat.
    - Egyszeri előfizetés – a csomagra vonatkozó kedvezményt a rendszer az előfizetésben szereplő használatra alkalmazza.
    - Megosztott – a csomagra vonatkozó kedvezményt a rendszer a számlázási környezetben található bármely előfizetésben szereplő példányokra alkalmazza. A nagyvállalati ügyfelek esetében a számlázási környezet a regisztráció, és tartalmazza a beléptetés összes előfizetését. Az utólagos elszámolású díjszabást használó egyéni csomagok esetében a számlázási környezet minden egyes, a fiók rendszergazdája által létrehozott, utólagos elszámolású díjszabási előfizetéssel rendelkező csomag.
5. Válasszon ki egy terméket a virtuális gép méretének és a rendszerkép típusának kiválasztásához. A kedvezmény csak a kiválasztott VM-méretre vonatkozik.
6. Válasszon egy vagy három éves időszakot.
7. Válasszon ki egy mennyiséget, amely az előre fizetett virtuálisgép-példányok száma, amelyek beszerezhetik a számlázási kedvezményt.
8. Vegye fel a terméket a kosárba, tekintse át és vásárolja meg.

A foglalási kedvezményt a rendszer automatikusan alkalmazza a szoftveres fogyasztásmérőre, amelyre előre fizet. A csomag nem fedi le a virtuális gépek számítási díjait. A virtuális gép foglalásait külön is megvásárolhatja.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>A kedvezmény a különböző SUSE VM-méretekre vonatkozik

A fenntartott VM-példányokhoz hasonlóan a SUSE Linux-csomagok is a példányok méretének rugalmasságát kínálnak. A kedvezmény akkor is érvényes, ha olyan virtuális gépet telepít, amely a vásárolt SUSE-csomagtól eltérő méretű. További információ: [a szoftveres csomagra vonatkozó kedvezmény alkalmazásának megismerése](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>RedHat csomag kedvezménye

A csomagok csak Red Hat Enterprise Linux virtuális gépek esetében érhetők el. A kedvezmény nem vonatkozik a RedHat Enterprise Linux SAP HANA virtuális gépekre vagy a RedHat Enterprise Linux SAP Business apps virtuális gépekre.

A RedHat csomagra vonatkozó kedvezmények csak a vásárláskor kiválasztott virtuálisgép-méretre vonatkoznak. A RHEL terveket nem lehet a vásárlás után visszatéríteni vagy kicserélni.


## <a name="cancellation-and-exchanges-not-allowed"></a>A törlés és az adatcsere nem engedélyezett

A vásárolt SUSE vagy RedHat csomagot nem lehet megszakítani vagy kicserélni. A használati szokásainak megfelelő csomagot vásárolja meg. A megvásárolni kívánt elemek azonosításával kapcsolatban lásd: [a szoftveres csomagra vonatkozó kedvezmény alkalmazásának megismerése](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Következő lépések

A foglalások kezelésével kapcsolatos információkért lásd: [Azure-foglalások kezelése](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

További információt a következő cikkekben talál:

- [Mi az az Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [A Reservations kezelése az Azure-ban](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [A SUSE foglalási kedvezmény alkalmazásának ismertetése](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
