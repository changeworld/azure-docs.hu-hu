---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66416064"
---
* Az átalakításhoz újra kell indítani a virtuális gépet, ezért ütemezze a virtuális gépek migrálását egy meglévő karbantartási időszakra. 

* Az átalakítás nem vonható vissza. 

* Vegye figyelembe, hogy bármely rendelkező felhasználók a [virtuális gépek Közreműködője](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör nem tudják módosítani a Virtuálisgép-méret (mivel azok sikerült előzetes átalakítás). Ennek az az oka a felügyelt lemezekkel rendelkező virtuális gépek a felhasználót, hogy az operációsrendszer-lemezek Microsoft.Compute/disks/write engedélye szükséges.

* Mindenképpen tesztelje az átalakítást. Migráljon egy tesztcélú virtuális gépet, mielőtt végrehajtja a migrálást éles környezetben.

* Az átalakítás közben felszabadítja a virtuális gépet. A virtuális gép új IP-címet kap, amikor elindul az átalakítás után. Szükség esetén [hozzárendelhet egy statikus IP-címet](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) a virtuális géphez.

* Tekintse át az Azure-beli Virtuálisgép-ügynök az átalakítási folyamat támogatásához szükséges minimális verzióját. Ellenőrizze és frissítse az ügynök verzióját vonatkozó információkért lásd: [minimális verzió támogatása az Azure-beli Virtuálisgép-ügynökök](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)