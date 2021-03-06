---
title: Az Azure-beli dedikált gazdagépek Reserved Instances-kedvezményének ismertetése | Microsoft Docs
description: Megtudhatja, hogyan érvényesül az Azure Reserved VM Instances-kedvezmény Azure-beli dedikált gazdagépek esetében.
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 26b71952e3d24214331b314f723728b56e3c4254
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207772"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>Az Azure-foglalási kedvezmény alkalmazása Azure-beli dedikált gazdagépek esetében

Miután megvásárolt egy Azure-beli fenntartott, dedikált gazdagéppéldányt, a foglalási kedvezmény automatikusan érvényesül azokon a dedikált gazdagépeken, amelyek megfelelnek a foglalás attribútumainak és mennyiségének. A foglalás fedezi a dedikált gazdagépek számítási költségeit.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha *folyamatosan igénybe veszi*. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy dedikált gazdagép törlésekor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák  *elvesznek*.

## <a name="reservation-discount-for-dedicated-hosts"></a>Foglalási kedvezmény dedikált gazdagépek esetében

Az Azure-beli fenntartott, dedikált gazdagéppéldány kedvezményt biztosít a dedikált gazdagépekhez használt számítási infrastruktúrához kapcsolódó költségek esetében. A kedvezmény a dedikált gazdagépekre vonatkozik, függetlenül attól, hogy a virtuális gépek használják-e őket. A foglalás nem fedezi a dedikált gazdagépen üzembe helyezett virtuális géphez kapcsolódó további (például licencelési, hálózathasználati vagy tárolási) költségeket.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége,  [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik azok a foglalások az Azure-ban?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Azure-beli dedikált gazdagépek használata](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Dedikált gazdagépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Foglalások kezelése az Azure-ban](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [A foglalási kihasználtság ismertetése CSP-előfizetésnél](https://docs.microsoft.com/partner-center/azure-reservations)

