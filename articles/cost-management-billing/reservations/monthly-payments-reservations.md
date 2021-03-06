---
title: Azure-foglalások vásárlása előre fizetéssel vagy havi kifizetésekkel
description: Ismerje meg, hogyan vásárolhat Azure-foglalásokat előre fizetéssel vagy havi kifizetésekkel.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: banders
ms.openlocfilehash: 77d663fa01e24acf63acd68d0b8d7cf4cc741055
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587090"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Foglalások vásárlása havi kifizetésekkel

Eddig csak előre fizetéssel lehetett Azure-foglalásokat vásárolni. Mostantól havi kifizetésekkel is fizethet a foglalásokért. Az előre fizetéses vásárlástól eltérően, amikor kifizeti a teljes összeget, a havi fizetéses lehetőség egyenlően osztja el a foglalás teljes költségét az időszak minden hónapjára. A foglalások előre vagy havonta fizetett összege megegyezik, és ha a havi fizetést választja, nem kell extra díjakat fizetnie.

A havonta fizetendő összeg a helyi pénznem aktuális hónapban érvényes átváltási árfolyamától függően változhat.

A havi kifizetések az alábbiak esetében érhetőek el:

- Virtual machines (Virtuális gépek)
- Azure Storage
- SQL Database-adatbázisok
- SQL Data Warehouse
- Cosmos DB
- App Service-bélyeg díja
- Felügyelt lemez
- Azure Data Explorer
- Azure Database for MariaDB, MySQL és PostgreSQL
- Azure VMware Solution by CloudSimple


Foglalások vásárlása az [Azure Portalon](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Példa a foglalásvásárlásra](./media/monthly-payments-reservations/purchase-reservation.png)

A foglalás megvásárlásakor megtekintheti a fizetési ütemezést. Kattintson a **Teljes kifizetési ütemezés megtekintése** lehetőségre.

![Példa a foglalás fizetési ütemezésére](./media/monthly-payments-reservations/prepurchase-schedule.png)

A fizetési ütemezés vásárlás utáni megtekintéséhez válasszon ki egy foglalást, kattintson a **Foglalás rendelési azonosítója** lehetőségre, majd a **Kifizetések** lapra.

## <a name="view-payments-made"></a>A megtörtént kifizetések megtekintése

A megtörtént kifizetéseket az API-k és a használati adatok használatával, valamint a költségelemzésben tekintheti meg. A havonta kifizetett foglalások esetében a gyakoriság értéke **ismétlődőként** jelenik meg a használati adatokban és a Reservation Charges API-ban. Az előre kifizetett foglalások esetében az érték **egyszeriként** jelenik meg.

A költségelemzés az alapértelmezett nézetben jeleníti meg a havi vásárlásokat. A vásárlások megtekintéséhez alkalmazza a **vásárlás** szűrőt a **Költségtípus** elemre és az **ismétlődő** szűrőt a **Gyakoriság** elemre. Ha csak a foglalásokat szeretné megtekinteni, a **Foglalások** esetében alkalmazzon szűrőt.

![Példa a foglalásvásárlási költségekre a költségelemzésben](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Váltás havi kifizetésekre megújításkor

A foglalás megújításakor lehetősége van a számlázás gyakoriságát havira módosítani.

## <a name="exchange-and-refunds"></a>Csere és visszatérítés

A többi foglaláshoz hasonlóan a havi számlázással vásárolt foglalások esetében is lehetőség van visszatérítésre és cserére. Jelenleg egy támogatási kérés küldésével indíthatja el a havi számlázással vásárolt foglalás cseréjét vagy visszatérítését.

Ha egy havi kifizetéses foglalást cserél le, akkor az új vásárlás teljes élettartamra vetített költségének nagyobbnak kell lennie, mint a lecserélni kívánt foglalás lemondott, fennmaradó kifizetései. Csere esetén nincsenek más korlátozások vagy díjak. Cserére akkor van lehetősége, ha egy előre kifizetett foglalás helyett vásárol egy havi számlázású, új foglalást. Az új foglalás élettartamra vetített értékének azonban nagyobbnak kell lennie, mint a lecserélni kívánt foglalás időarányos értéke.

Ha egy havi kifizetéses foglalást mond le, a Microsoft jogosult 12% lemondási díjat alkalmazni a lemondott kifizetésekért, amelyekért kötelezettséget vállalt. A Microsoft azonban jelenleg nem vonja le ezt a díjat. A lemondott kifizetések, amelyekért kötelezettséget vállalt, le lesznek vonva az 50 000 USD értékű visszatérítési korlátból. Amennyiben a lemondási díjat levonták, a visszatérítési korlát nem módosul.

A cserével és a visszatérítéssel kapcsolatos további információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>További lépések

- A foglalásokkal kapcsolatos további információkért tekintse meg a [Mi az az Azure Reservations?](save-compute-costs-reservations.md) című cikket.
- A foglalásvásárlás előtt elvégzendő feladatok megismeréséhez tekintse meg [a megfelelő virtuálisgép-méret vásárlási előtti megállapítását](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy) ismertető cikket
