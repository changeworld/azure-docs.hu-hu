---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 8607fd6f01ea02841017d09dac7236812f2330b9
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133691"
---
| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Virtuális gépek [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> régiónként. |
| Virtuálisgép-magok összesen, [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) |régiónként 20<sup>1</sup> . A korlát növeléséhez forduljon az ügyfélszolgálathoz. |
| Azure spot VM-alapú teljes mag/ [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md) |régiónként 20<sup>1</sup> . A korlát növeléséhez forduljon az ügyfélszolgálathoz. |
| VM/sorozat, például Dv2 és F, mag/ [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md) |régiónként 20<sup>1</sup> . A korlát növeléséhez forduljon az ügyfélszolgálathoz. |
| [Rendelkezésre állási](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) csoportok/előfizetés |2 000 régiónként. |
| Virtuális gépek rendelkezésre állási készlete | 200 |
| Tanúsítványok/előfizetés |Korlátlan<sup>2</sup> |

<sup>1</sup> Az alapértelmezett korlátok az ajánlati kategória típusa szerint változnak, például az ingyenes próbaverziós és az utólagos elszámolású, valamint az adatsorozatok, például a Dv2, az F és a G. A Nagyvállalati Szerződés-előfizetések alapértelmezett értéke például 350.

<sup>2</sup> A Azure Resource Manager a tanúsítványokat a Azure Key Vault tárolja. Az előfizetéshez tartozó tanúsítványok száma korlátlan. Üzemelő példányok 1 MB-os korlátja, amely egyetlen virtuális gépről vagy rendelkezésre állási csoportból áll.

> [!NOTE]
> A virtuális gépek magjai a regionális teljes korláttal rendelkeznek. Emellett a régión belüli méretre (például Dv2 és F) is korlátozva van. Ezeket a korlátokat külön kényszeríti a rendszer. Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés 30 a1-es virtuális gépet vagy 30 D1 virtuális gépet telepíthet, vagy a kettő kombinációja nem haladhatja meg az összes 30 magot. Egy kombináció például 10 a1 virtuális gép és 20 D1 virtuális gép.  
> <!-- -->
> 
