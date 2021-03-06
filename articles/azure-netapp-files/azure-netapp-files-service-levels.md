---
title: Azure NetApp Files szolgáltatási szintjei | Microsoft Docs
description: A Azure NetApp Files szolgáltatási szintjeinek teljesítménybeli teljesítményét ismerteti.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832583"
---
# <a name="service-levels-for-azure-netapp-files"></a>Az Azure NetApp Files szolgáltatásszintjei
A szolgáltatási szint a kapacitási készlet egyik attribútuma. A szolgáltatási szinteket a rendszer a kötethez hozzárendelt kvóta alapján határozza meg, és megkülönbözteti a kapacitások megengedett maximális átviteli sebességét.

## <a name="supported-service-levels"></a>Támogatott szolgáltatási szintek

A Azure NetApp Files három szolgáltatási szintet támogat: *Ultra*, *Premium*és *standard*. 

* <a name="Ultra"></a>Ultra Storage

    Az ultra Storage-szinten legfeljebb 128 MiB/s átviteli sebesség adható meg a hozzárendelt mennyiségi kvóta alapján 1 TiB-onként. 

* <a name="Premium"></a>Prémium szintű Storage

    A Premium Storage-szint legfeljebb 64 MiB/s átviteli sebességet biztosít a hozzárendelt mennyiségi kvóta alapján 1 TiB-onként. 

* <a name="Standard"></a>Standard szintű Storage

    A standard szintű tárolási szint legfeljebb 16 MiB/s adatátviteli sebességet biztosít a hozzárendelt mennyiségi kvóta alapján 1 TiB-onként.

## <a name="throughput-limits"></a>Átviteli korlátok

A kötetek átviteli sebességének korlátját a következő tényezők kombinációja határozza meg:
* Azon kapacitási készlet szolgáltatási szintje, amelyhez a kötet tartozik
* A kötethez rendelt kvóta  

Ezt a koncepciót az alábbi ábra szemlélteti:

![Szolgáltatási szint illusztrációja](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

A fenti 1. példában egy, a prémium szintű Storage-szinttel rendelkező, 2 TiB-t hozzárendelő kapacitási készletből származó kötethez 128 MiB/s (2 TiB * 64 MiB/s) átviteli korlát lesz hozzárendelve. Ez a forgatókönyv a kapacitási készlet méretétől vagy a tényleges mennyiségi felhasználástól függetlenül érvényes.

A fenti 2. példában egy olyan kötetet, amely egy, a prémium szintű Storage-szinthez hozzárendelt, 100 GiB-as csomaggal rendelkező kapacitási készletből áll, 6,25 MiB/s (0,09765625 TiB * 64 MiB/s) átviteli korlátot kap. Ez a forgatókönyv a kapacitási készlet méretétől vagy a tényleges mennyiségi felhasználástól függetlenül érvényes.

## <a name="next-steps"></a>Következő lépések

- A különböző szolgáltatási szintek árának [Azure NetApp Files díjszabási oldalán](https://azure.microsoft.com/pricing/details/storage/netapp/) talál.
- A kapacitás felhasználásának számítási kapacitásának kiszámításához tekintse meg [Azure NetApp Files a Cost modelljét](azure-netapp-files-cost-model.md) 
- [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp Files szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/netapp/)