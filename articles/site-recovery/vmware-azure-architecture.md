---
title: VMware virtuális gép vész-helyreállítási architektúrája Azure Site Recovery
description: Ez a cikk áttekintést nyújt azokról az összetevőkről és architektúráról, amelyeket a helyszíni VMware virtuális gépek vész-helyreállításának beállításakor használ az Azure-ba Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: ccf258594aa68fc9b5d0189c9ada640078e0ba6f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514867"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware – Azure vész-helyreállítási architektúra

Ez a cikk azokat az architektúrákat és folyamatokat ismerteti, amelyeket a rendszer a helyszíni VMware-hely és az Azure között a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával végez a helyreállítási replikáció, a feladatátvétel és a VMWare virtuális gépek (VM-EK) helyreállítási folyamatainak telepítésekor.


## <a name="architectural-components"></a>Az architektúra összetevői

Az alábbi táblázat és ábra áttekintést nyújt az Azure-ba irányuló VMware vész-helyreállításhoz használt összetevőkről.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Azure-előfizetés, Azure Storage-fiók a gyorsítótárhoz, a felügyelt lemezekhez és az Azure-hálózathoz. | A helyszíni virtuális gépekről replikált adatok tárolása az Azure Storage-ban történik. Az Azure-beli virtuális gépek a replikált adatokkal jönnek létre, amikor feladatátvételt hajt végre a helyszínen az Azure-ba. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló számítógépe** | Egyetlen helyszíni gép. Azt javasoljuk, hogy egy letöltött OVF-sablonból üzembe helyezhető VMware virtuális gépként futtassa.<br/><br/> A gép az összes helyszíni Site Recovery-összetevőt futtatja, amely tartalmazza a konfigurációs kiszolgálót, a Process Servert és a fő célkiszolgáló kiszolgálót. | **Konfigurációs kiszolgáló**: koordinálja a helyszíni és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.<br/><br/> **Process Server**: alapértelmezés szerint telepítve van a konfigurációs kiszolgálón. Replikációs adatkérést kap; a gyorsítótárazással, tömörítéssel és titkosítással optimalizálja azt. és elküldi az Azure Storage-nak. A folyamatkiszolgáló ezenfelül telepíti az Azure Site Recovery mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni gépek automatikus felderítését. Az üzembe helyezés során további, különálló folyamat-kiszolgálókat adhat hozzá a replikációs forgalom nagyobb mennyiségének kezeléséhez.<br/><br/> **Fő célkiszolgáló**: alapértelmezés szerint telepítve van a konfigurációs kiszolgálón. Az Azure-beli feladat-visszavétel során kezeli a replikációs adatok kezelését. Nagyméretű központi telepítések esetén további, különálló fő célkiszolgáló adható hozzá a feladat-visszavételhez.
**VMware-kiszolgálók** | A VMware virtuális gépek helyszíni vSphere ESXi-kiszolgálókon futnak. A gazdagépek felügyeletéhez vCenter-kiszolgálót ajánlunk. | Site Recovery telepítése során VMware-kiszolgálókat ad hozzá a Recovery Services-tárolóhoz.
**Replikált gépek** | A mobilitási szolgáltatás minden replikált VMware virtuális gépen telepítve van. | Javasoljuk, hogy engedélyezze az automatikus telepítést a Process Serverről. Azt is megteheti, hogy manuálisan telepíti a szolgáltatást, vagy automatikus telepítési módszert használ, például Configuration Manager.

**VMware-ről Azure-ra architektúra**

![Összetevők](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Replikációs folyamat

1. Amikor engedélyezi a replikációt egy virtuális géphez, a kezdeti replikáció az Azure Storage-ba megkezdődik a megadott replikációs házirend használatával. Vegye figyelembe a következőket:
    - A VMware virtuális gépek esetében a replikáció a virtuális gépen futó mobilitási szolgáltatás ügynökének szinte folyamatos, közel folyamatos, a replikáláshoz.
    - A rendszer alkalmazza a replikációs házirend beállításait:
        - **RPO küszöbértéke**. Ez a beállítás nincs hatással a replikálásra. Segít a figyelésben. Egy esemény jön létre, és opcionálisan egy e-mailt küld, ha az aktuális RPO meghaladja a megadott küszöbértéket.
        - **Helyreállítási pont megőrzése**. Ezzel a beállítással megadható, hogy mennyi idő elteltével kell megszakítani a megszakítást. A Premium Storage maximális megőrzése 24 óra. Standard szintű tárolás esetén 72 óra. 
        - **Alkalmazás-konzisztens Pillanatképek**. Az alkalmazással konzisztens pillanatkép az alkalmazás igényeitől függően 1 – 12 óra lehet. A pillanatképek standard Azure Blob-Pillanatképek. A virtuális gépen futó mobilitási ügynök egy VSS-pillanatképet kér ennek a beállításnak megfelelően, és a könyvjelzőket, amelyek a replikálási adatfolyamban lévő alkalmazás-konzisztens pontként jelennek meg.

2. A forgalom az Azure Storage nyilvános végpontjait az interneten keresztül replikálja. Másik lehetőségként használhatja az Azure ExpressRoute-t a [Microsoft-partnerekkel](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). A helyek közötti virtuális magánhálózati (VPN) kapcsolaton keresztüli replikálása nem támogatott a helyszíni helyről az Azure-ba.
3. A kezdeti replikálás befejeződése után a változásokat az Azure-ba replikálva megkezdődik. A rendszer a gépek nyomon követését a folyamat kiszolgálójára továbbítja.
4. A kommunikáció a következőképpen történik:

    - A virtuális gépek a replikációs felügyelet érdekében a HTTPS 443 bejövő porton kommunikálnak a helyszíni konfigurációs kiszolgálóval.
    - A konfigurációs kiszolgáló az Azure-ba irányuló replikációt a HTTPS 443 kimenő porton keresztül hangolja össze.
    - A virtuális gépek replikációs adatküldést küldenek a folyamat-kiszolgálónak (amely a konfigurációs kiszolgáló gépen fut) a HTTPS 9443 bejövő porton. Ez a port módosítható.
    - A Process Server replikációs adatokat fogad, optimalizálja és titkosítja, majd az Azure Storage-ba küldi az 443-as porton keresztül.
5. A replikációs adatnaplók először egy gyorsítótárbeli Storage-fiókba helyezik az Azure-ban. A rendszer feldolgozza ezeket a naplókat, és az adattárolást egy Azure-beli felügyelt lemez (ún. ASR-lemez) tárolja. A helyreállítási pontok ezen a lemezen jönnek létre.




**VMware – Azure replikálási folyamat**

![Replikációs folyamat](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

Miután beállította a replikálást, és elvégezte a vész-helyreállítási részletezést (feladatátvételi teszt) annak ellenőrzéséhez, hogy minden a vártnak megfelelően működik-e, futtathatja a feladatátvételt és a feladat-visszavételt.

1. A művelet végrehajtása egyetlen gépen meghiúsul, vagy létrehozhat egy helyreállítási tervet, amely egyszerre több virtuális gép feladatátvételét hajtja végre. Egy helyreállítási terv előnye, hogy az egyetlen gép feladatátvétele helyett a következőket használja:
    - Az alkalmazás-függőségek modellezéséhez az összes virtuális gépet egyetlen helyreállítási tervbe kell belefoglalni az alkalmazáson belül.
    - A manuális műveletekhez parancsfájlokat, Azure-runbookok és szüneteltetést is hozzáadhat.
2. A kezdeti feladatátvétel elindítása után véglegesíti azt az Azure-beli virtuális gép munkaterhelésének megkezdéséhez.
3. Ha az elsődleges helyszíni hely ismét elérhetővé válik, előkészítheti a feladat-visszavétel feladatait. A feladat-visszavétel érdekében be kell állítania egy feladat-visszavételi infrastruktúrát, beleértve a következőket:

    * **Ideiglenes Process Server az Azure-ban**: az Azure-ból való feladat-visszavételhez állítson be egy Azure-beli virtuális gépet, amely az Azure-ból történő replikáció kezelésére szolgáló folyamat-kiszolgálóként működik. Ez a virtuális gép a feladatok visszaadását követően törölhető.
    * **VPN-kapcsolat**: a feladat-visszavétel érdekében VPN-kapcsolatra (vagy ExpressRoute) van szükség az Azure-hálózatról a helyszíni helyre.
    * **Különálló fő célkiszolgáló**: alapértelmezés szerint a helyszíni VMWare virtuális gépen a konfigurációs kiszolgálóval telepített fő célkiszolgáló kezeli a feladat-visszavételt. Ha nagy mennyiségű forgalmat kell visszaadnia, hozzon létre egy külön helyszíni fő célkiszolgáló erre a célra.
    * **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. A rendszer automatikusan létrehozza ezt a házirendet, amikor létrehoz egy replikációs házirendet a helyszínről az Azure-ba.
4. Az összetevők érvénybe lépése után a feladat-visszavétel három műveletben fordul elő:

    - 1\. lépés: az Azure-beli virtuális gépek ismételt védetté tételével, hogy az Azure-ból vissza lehessen térni a helyszíni VMware virtuális gépekre.
    -  2\. fázis: feladatátvétel futtatása a helyszíni helyre.
    - 3\. fázis: a munkaterhelések visszaállítása után engedélyezze újra a helyszíni virtuális gépek replikálását.
    
 
**VMware-feladat-visszavétel az Azure-ból**

![Feladat-visszavétel](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Következő lépések

[Ezt az oktatóanyagot](vmware-azure-tutorial.md) követve engedélyezheti a VMware – Azure replikálást.
