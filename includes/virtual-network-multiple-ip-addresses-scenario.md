---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179041"
---
## <a name="scenario"></a>Forgatókönyv
Egy virtuális Gépet egyetlen hálózati adapterrel létrehozott, és a egy virtuális hálózathoz csatlakozik. A virtuális gép van szükség, három különböző *privát* IP-címek és a két *nyilvános* IP-címeket. Az IP-címek vannak rendelve az alábbi IP-konfigurációk:

* **IPConfig-1:** Hozzárendel egy *statikus* magánhálózati IP-cím és a egy *statikus* nyilvános IP-címet.
* **IPConfig-2:** Hozzárendel egy *statikus* magánhálózati IP-cím és a egy *statikus* nyilvános IP-címet.
* **IPConfig-3:** Hozzárendel egy *statikus* magánhálózati IP-cím és a nyilvános IP-cím.
  
    ![Több IP-cím](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

Az IP-konfiguráció társítva, a hálózati adapterhez, ha a hálózati adapter létrehozása és a hálózati Adaptert a virtuális Géphez van csatolva a virtuális gép létrehozásakor. A forgatókönyvhöz használt IP-címek vannak az ábrán látható. Bármilyen IP cím és a hozzárendelés-típusok szükséges rendelhet hozzá.

> [!NOTE]
> A jelen cikkben ismertetett lépések rendeli IP-konfigurációk mindegyike egyetlen hálózati adapterre, bár minden hálózati adapternek a virtuális gép több hálózati Adapterrel is hozzárendelheti a több IP-konfiguráció. Több hálózati adapterrel rendelkező virtuális gép létrehozása, olvassa el a [több hálózati adapterrel rendelkező virtuális gép létrehozása](../articles/virtual-machines/windows/multiple-nics.md) cikk.