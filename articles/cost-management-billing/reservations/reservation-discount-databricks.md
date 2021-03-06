---
title: Az előzetes Azure Databricks-vásárlásért járó kedvezmény alkalmazásának módja
description: Megtudhatja, hogyan vonatkozik az előzetes Azure Databricks-vásárlásért járó kedvezmény az Ön használatára.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 69f83fc1d390fcbea77468045b89ee6024e7fe9a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200316"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Az előzetes Azure Databricks-vásárlásért járó kedvezmény alkalmazásának módja

Az előre megvásárolt kötött Azure Databricks-egységeket (DBCU) a vásárlási időtartam során bármikor felhasználhatja. Az előre megvásárolt DBCU-kból való levonás automatikusan megtörténik az Azure Databricks használata esetén.

A virtuális gépektől eltérően az előre megvásárolt egységek nem járnak le óránként. Az egységek a vásárlási időtartam során bármikor felhasználhatók. Az előzetes vásárlásért járó kedvezmények igénybevételéhez nem szükséges újból üzembe helyeznie vagy hozzárendelnie egy előzetesen vásárolt csomagot az Azure Databricks-munkaterületeihez a használathoz.

Az előzetes vásárlásért járó kedvezmény csak az Azure Databricks-egységek (DBU) használatára vonatkozik. Az egyéb – például számítási, tárolási és hálózati – díjakat külön számítjuk fel.

## <a name="pre-purchase-discount-application"></a>Az előzetes vásárlásért járó kedvezmény alkalmazása

Az előzetesen vásárolt Databricks-egységek minden Databricks-számításifeladathoz és -szinthez felhasználhatók. Az előzetes vásárlást előre kifizetett kötött Databricks-egységek készletének lehet tekinteni. A rendszer a készletből vonja le a használatot, a számítási feladattól és a szinttől függetlenül. A használat levonása a következő arányban történik:

| **Számítási feladat** | **DBU-alkalmazási arány – Standard szint** | **DBU-alkalmazási arány – Prémium szint** |
| --- | --- | --- |
| Adatelemzés | 0,4 | 0,55 |
| Adatfeldolgozás | 0,15 | 0,30 |
| Adatfeldolgozás (egyszerű) | 0,07 | 0,22 |

Ha például bizonyos mennyiségű Standard szintű Adatelemzést használ fel, az előzetesen vásárolt kötött Databricks-egységekből 0,4 egység lesz levonva. Ha bizonyos mennyiségű Standard szintű Egyszerű adatelemzést használ fel, az előzetesen vásárolt kötött Databricks-egységekből 0,07 egység lesz levonva.

## <a name="determine-plan-use"></a>A csomaghasználat meghatározása

A DBCU-csomaghasználat meghatározásához lépjen az Azure Portal > **Foglalások** elemre, majd kattintson a megvásárolt Databricks-csomagra. Itt jelenik meg az eddigi kihasználtság, a fennmaradó egységekkel együtt. A foglalás használatának meghatározásával kapcsolatos további információkért tekintse meg a [foglalás használatát](reservation-apis.md#see-reservation-usage) ismertető cikket.

## <a name="how-discount-application-shows-in-usage-data"></a>A kedvezmény alkalmazásának megjelenése a használati adatokban

Ha előzetes vásárlási kedvezmény vonatkozik a Databricks-használatra, a használati adatokban az igénybevétel szerinti költségek nullaként jelennek meg. A foglalási költségekkel és használati adatokkal kapcsolatos további információkért tekintse meg a [Nagyvállalati szerződés foglalási költségeinek és kihasználtságának lekérése](understand-reserved-instance-usage-ea.md) szakaszt.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- A foglalások kezelésének megismerése érdekében tekintse meg [Az Azure Reservations kezelése](manage-reserved-vm-instance.md) szakaszt.
- Az Azure Databricks-egységek előzetes vásárlásával történő megtakarításra vonatkozó információkért tekintse meg az [Azure Databricks-költségek előzetes vásárlással történő optimalizálását](prepay-databricks-reserved-capacity.md) ismertető szakaszt.
- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
  - [A Reservations kezelése az Azure-ban](manage-reserved-vm-instance.md)
  - [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](understand-reserved-instance-usage.md)
  - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
