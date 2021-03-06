---
title: Architektúra – Hyper-V vész-helyreállítás egy másodlagos helyre Azure Site Recovery
description: Ez a cikk áttekintést nyújt a helyszíni Hyper-V virtuális gépek vész-helyreállításának architektúráról egy másodlagos System Center VMM-helyre Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 3e81e353d2912f56a932ce118a0424e45e758df7
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133012"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Architektúra – Hyper-V replikáció másodlagos helyre

Ez a cikk azokat az összetevőket és folyamatokat ismerteti, amelyek részt vesznek a System Center Virtual Machine Manager- (VMM-) felhőkben lévő helyszíni Hyper-V virtuális gépeknek (VM-eknek) az Azure Portalon elérhető [Azure Site Recovery](site-recovery-overview.md) szolgáltatással egy másodlagos VMM-helyre történő replikációjában.
a

## <a name="architectural-components"></a>Az architektúra összetevői

A következő táblázat és ábra a Hyper-V-replikáció másodlagos helyre történő replikálásához használt összetevők magas szintű nézetét ismerteti.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Azure-előfizetés | Létre kell hoznia egy Recovery Services-tárolót az Azure-előfizetésében a VMM-helyek közti replikáció vezényléséhez és felügyeletéhez.
**VMM-kiszolgáló** | Szüksége lesz egy elsődleges és egy másodlagos VMM-helyre. | Javasoljuk, hogy legyen egy VMM-kiszolgáló az elsődleges helyen, és egy a másodlagos helyen.
**Hyper-V kiszolgáló** |  Legalább egy Hyper-V gazdakiszolgáló az elsődleges és a másodlagos VMM-felhőkben. | A rendszer LAN vagy VPN hálózaton keresztül replikálja az adatokat az elsődleges és másodlagos Hyper-V gazdakiszolgálók között Kerberos vagy tanúsítványalapú hitelesítés használatával.  
**Hyper-V virtuális gépek** | A Hyper-V gazdakiszolgálón. | A forrás gazdakiszolgálókon legalább egy replikálni kívánt virtuális gépnek kell futnia.

**Helyszíni – helyszíni architektúra**

![Két helyszíni hely közötti replikálás](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Replikációs folyamat

1. A kezdeti replikáció indításakor a rendszer a [Hyper-V virtuális gép pillanatképének](https://technet.microsoft.com/library/dd560637.aspx) pillanatképét is elvégzi.
2. A virtuális GÉPEN lévő virtuális merevlemezeket egy-egy, a másodlagos helyre replikálja a rendszer.
3. Ha a lemez megváltozik, miközben a kezdeti replikálás folyamatban van, a Hyper-V replika replikációs nyomon követése a változásokat Hyper-V replikációs naplókként (. HRL) követi nyomon. Ezek a naplófájlok ugyanabban a mappában találhatók, mint a lemezek. Minden lemezhez tartozik egy. HRL fájl, amelyet a rendszer a másodlagos helyre továbbít. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. A kezdeti replikálás befejezésekor a rendszer törli a virtuális gép pillanatképét, és megkezdi a különbözeti replikációt.
5. A rendszer a naplózott lemezmódosításokat szinkronizálja, és egyesíti a szülőlemezzel.


## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

- A feladatátvételt több gép feladatátvételének megszervezéséhez hajthatja végre, vagy létrehozhat egy helyreállítási terveket.
- Futtathat tervezett vagy nem tervezett feladatátvételt a helyszíni helyek között. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
    - Ha nem tervezett feladatátvételt hajt végre egy másodlagos helyre, a másodlagos helyen található feladatátvételi gépek nem védettek.
    - Ha tervezett feladatátvételt futtatott, a feladatátvétel után a másodlagos hely gépei védettek lesznek.
- A kezdeti feladatátvétel futtatását követően véglegesíti azt, hogy megkezdje a munkaterhelések elérését a replika virtuális gépről.
- Ha az elsődleges hely ismét elérhetővé válik, visszatérhet.
    - A visszirányú replikálás kezdeményezésével megkezdheti a replikálást a másodlagos helyről az elsődlegesre. A fordított replikáció során a virtuális gépek védett állapotba kerülnek, de a másodlagos adatközpont marad továbbra is az aktív hely.
    - Ha azt szeretné, hogy újra az elsődleges hely legyen az aktív hely, kezdeményezzen egy tervezett feladatátvételt a másodlagos helyről az elsődleges helyre, majd hajtson végre ismét fordított replikálást.



## <a name="next-steps"></a>További lépések


[Ezt az oktatóanyagot](hyper-v-vmm-disaster-recovery.md) követve engedélyezheti a Hyper-V replikálását a VMM-felhők között.
