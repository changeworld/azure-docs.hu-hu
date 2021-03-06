---
title: Azure VMware Solutions (AVS) rövid útmutató – VMware virtuális gépek létrehozása az Azure-ban
description: Ismerje meg, hogyan hozhat létre és konfigurálhat VMware virtuális gépeket Azure Portal Azure VMware Solutions (AVS) használatával
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 575d59d60ebfcfdbe4d234d608e8d988006773c2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019553"
---
# <a name="quickstart---create-vmware-vms-on-azure"></a>Rövid útmutató – VMware virtuális gépek létrehozása az Azure-ban

Ha virtuális gépet szeretne létrehozni a Azure Portalban, használjon olyan virtuálisgép-sablonokat, amelyekhez az AVS-rendszergazda engedélyezte az előfizetését. A virtuálisgép-sablonok a VMware-infrastruktúrában találhatók.

## <a name="avs-vm-creation-on-azure-requires-a-vm-template"></a>Az Azure-beli AVS VM-létrehozáshoz virtuálisgép-sablon szükséges

Hozzon létre egy virtuális gépet az AVS Private-felhőben a vCenter felhasználói felületén. Sablon létrehozásához kövesse a [virtuális gép klónozása sablonhoz a vSphere webes ügyfélprogramban](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)című témakör utasításait. Tárolja a virtuálisgép-sablont az AVS Private Cloud vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Virtuális gép létrehozása a Azure Portalban

1. Válassza az **Összes szolgáltatás** elemet.

2. Az **AVS Virtual Machines**keresése.

3. Kattintson a **Hozzáadás** parancsra.

    ![AVS-beli virtuális gép létrehozása](media/create-cloudsimple-virtual-machine.png)

4. Adja meg az alapszintű adatokat, és kattintson a **Tovább: méret**elemre.

    ![AVS-alapú virtuális gép létrehozása – alapismeretek](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Mező | Leírás |
    | ------------ | ------------- |
    | Előfizetést | Az AVS Private Cloud-hoz társított Azure-előfizetés. |
    | Erőforráscsoport | Az erőforráscsoport, amelyhez a virtuális gép hozzá lesz rendelve. Választhat egy meglévő csoportot, vagy létrehozhat egy újat. |
    | Name (Név) | A virtuális gép azonosítására szolgáló név.  |
    | Földrajzi egység | Az az Azure-régió, amelyben a virtuális gép üzemeltetve van.  |
    | AVS Private Cloud | Az AVS Private Cloud, ahol létre szeretné hozni a virtuális gépet. |
    | Erőforráskészlet | A virtuális géphez hozzárendelt erőforráskészlet. Válasszon a rendelkezésre álló erőforráskészlet közül. |
    | vSphere-sablon | a virtuális gép vSphere-sablonja.  |
    | Felhasználónév | A virtuális gép rendszergazdájának felhasználóneve (Windows-sablonok esetén).|
    | Jelszó |  A virtuális gép rendszergazdájának jelszava (Windows-sablonok esetén). |
    | Jelszó megerősítése | Erősítse meg a jelszót. |

5. Válassza ki a virtuális gép számára a magok és a memória kapacitásának számát, majd kattintson a Next (tovább) gombra **: konfigurációk**. Jelölje be a jelölőnégyzetet, ha a teljes CPU-virtualizációt el szeretné tenni a vendég operációs rendszernek. A hardveres virtualizációt igénylő alkalmazások a bináris fordítás vagy a paravirtualizációs nélküli virtuális gépeken is futtathatók. További információkért lásd a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">VMware hardveres virtualizációs szolgáltatás elérhetővé</a>tételével foglalkozó cikket.

    ![AVS virtuális gép létrehozása – méret](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurálja a hálózati adaptereket és a lemezeket az alábbi táblázatokban leírtak szerint, és kattintson a **felülvizsgálat + létrehozás**gombra.

    ![AVS-alapú virtuális gép létrehozása – konfigurációk](media/create-cloudsimple-virtual-machine-configurations.png)

    Hálózati adapterek esetében kattintson a **hálózati adapter hozzáadása** lehetőségre, és konfigurálja a következő beállításokat.

    | Vezérlés | Leírás |
    | ------------ | ------------- |
    | Name (Név) | Adja meg a felületet azonosító nevet.  |
    | Network (Hálózat) | Az AVS Private Cloud vSphere válassza ki a konfigurált elosztott porttartomány listáját. |
    | Adapter | Válasszon ki egy vSphere-adaptert a virtuális géphez konfigurált elérhető típusok listájából. További információkért lásd a VMware tudásbázist a <a href="https://kb.vmware.com/s/article/1001805" target="_blank">virtuális gép hálózati adapterének kiválasztásával</a>foglalkozó cikkben. |
    | Bekapcsolás rendszerindításkor | Adja meg, hogy engedélyezi-e a hálózati adapter hardverét a virtuális gép indításakor. Az alapértelmezett érték az **Engedélyezés**. |

    Lemezek esetében kattintson a **lemez hozzáadása** elemre, és konfigurálja a következő beállításokat.

    | Tétel | Leírás |
    | ------------ | ------------- |
    | Name (Név) | Adjon meg egy nevet a lemez azonosításához. |
    | Méret | Válasszon ki egy rendelkezésre álló méretet. |
    | SCSI-vezérlő | Válasszon ki egy SCSI-vezérlőt a lemezhez. |
    | Mód | Meghatározza, hogy a lemez hogyan vegyen részt a pillanatképekben. Válasszon egyet a következő lehetőségek közül: <br> – Független állandó: a lemezre írt összes adattal véglegesen írásra kerül.<br> – Független nem állandó: a lemezre írt módosítások a virtuális gép kikapcsolásakor vagy alaphelyzetbe állításakor törlődnek. A független, nem állandó mód lehetővé teszi, hogy a virtuális gépet mindig ugyanabban az állapotban indítsa újra. További információt a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware dokumentációjában</a>talál.

7. Az érvényesítés befejezésekor tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra. A módosítások elvégzéséhez kattintson a felül található lapfülekre, vagy kattintson a gombra.

    ![AVS-alapú virtuális gép létrehozása – áttekintés](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Következő lépések

* [AVS-beli virtuális gépek listájának megtekintése](azure-create-vm.md#view-list-of-avs-virtual-machines)
* [AVS-beli virtuális gép kezelése az Azure-ból](azure-manage-vm.md)
