---
title: Felkészülés a Hyper-V virtuális gépek vész-helyreállítására az Azure-ba Azure Site Recovery
description: Megtudhatja, hogyan készítheti elő a helyszíni Hyper-V virtuális gépeket az Azure-ba való vész-helyreállításra Azure Site Recovery használatával.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b36e4688ebd6e929a56869a6bb191d98b2f1f432
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239839"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Helyszíni Hyper-V-kiszolgálók előkészítése az Azure-ba irányuló vészhelyreállításhoz

Ez a cikk azt ismerteti, hogyan készítse elő a helyszíni Hyper-V-infrastruktúrát, ha a [Azure site Recovery](site-recovery-overview.md)használatával szeretné beállítani a Hyper-VM-eszközök vész-helyreállítását az Azure-ba.


Ez egy sorozat második oktatóanyaga, amely bemutatja, hogyan állíthatja be a vész-helyreállítást az Azure-ba helyszíni Hyper-V virtuális gépekre. Az első oktatóanyagban [beállítjuk a](tutorial-prepare-azure.md) Hyper-V vész-helyreállításhoz szükséges Azure-összetevőket.

Ennek az oktatóanyagnak a segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Tekintse át a Hyper-V követelményeit és a VMM vonatkozó követelményeket, ha a Hyper-V-gazdagépeket a System Center VMM felügyeli.
> * Készítse elő a VMM, ha van ilyen.
> * Ellenőrizze az Azure-beli helyekhez való internet-hozzáférést.
> * Készítse elő a virtuális gépeket az Azure-ba való feladatátvétel után.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes utasításokért tekintse át a Site Recovery tartalomjegyzékének útmutató című cikkét.

## <a name="before-you-start"></a>Előkészületek

Győződjön meg arról, hogy előkészítette az Azure-t az [ebben a sorozatban szereplő első oktatóanyagban](tutorial-prepare-azure.md)leírtak szerint.

## <a name="review-requirements-and-prerequisites"></a>A követelmények és előfeltételek áttekintése

Győződjön meg arról, hogy a Hyper-V-gazdagépek és virtuális gépek megfelelnek a követelményeknek.

1. [Ellenőrizze](hyper-v-azure-support-matrix.md#on-premises-servers) a helyszíni kiszolgáló követelményeit.
2. [Tekintse át az](hyper-v-azure-support-matrix.md#replicated-vms) Azure-ba replikálni kívánt Hyper-V virtuális gépek követelményeit.
3. A Hyper-V gazdagép [hálózatkezelésének](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)keresése; a helyszíni Hyper-V-gazdagépek esetében pedig a gazdagép és a vendég [tárterület](hyper-v-azure-support-matrix.md#hyper-v-host-storage) támogatása.
4. Ellenőrizze az Azure támogatott [hálózati](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [tárolási](hyper-v-azure-support-matrix.md#azure-storage) és [számítási](hyper-v-azure-support-matrix.md#azure-compute-features) lehetőségeit a feladatátvételt követően.
5. Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük az [Azure virtuális gépekre vonatkozó feltételeinek](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>VMM előkészítése (nem kötelező)

Ha a Hyper-V-gazdagépeket a VMM felügyeli, elő kell készítenie a helyszíni VMM-kiszolgálót. 

- Győződjön meg arról, hogy a VMM-kiszolgáló legalább egy felhővel rendelkezik, és egy vagy több gazdagépet tartalmaz. A Hyper-V-gazdagépen, amelyen a virtuális gépek futnak, a felhőben kell elhelyezkedniük.
- Készítse elő a VMM-kiszolgálót a hálózati leképezéshez.

### <a name="prepare-vmm-for-network-mapping"></a>VMM előkészítése hálózati leképezéshez

Ha VMM használ, a [hálózati leképezési](site-recovery-network-mapping.md) térképek a helyszíni VMM virtuálisgép-hálózatok és az Azure-beli virtuális hálózatok között vannak. A leképezés biztosítja, hogy az Azure-beli virtuális gépek a megfelelő hálózathoz kapcsolódjanak, amikor a feladatátvétel után jönnek létre.

Készítse elő a VMM a hálózati megfeleltetéshez a következőképpen:

1. Győződjön meg arról, hogy rendelkezik olyan [VMM logikai hálózattal](https://docs.microsoft.com/system-center/vmm/network-logical) , amely ahhoz a felhőhöz van társítva, amelyben a Hyper-V-gazdagépek találhatók.
2. Győződjön meg arról, hogy rendelkezik a logikai hálózathoz társított virtuálisgép- [hálózattal](https://docs.microsoft.com/system-center/vmm/network-virtual) .
3. A VMM-ben kapcsolja össze a virtuális gépeket a virtuálisgép-hálózattal.

## <a name="verify-internet-access"></a>Internet-hozzáférés ellenőrzése

1. Az oktatóanyag szempontjából a legegyszerűbb konfiguráció a Hyper-V-gazdagépek és a VMM-kiszolgáló számára, hogy proxy használata nélkül közvetlen hozzáférést biztosítson az internethez. 
2. Győződjön meg arról, hogy a Hyper-V-gazdagépek és a VMM-kiszolgáló, ha szükséges, el tudja érni a szükséges URL-címeket.   
3. Ha IP-cím alapján vezérli a hozzáférést, győződjön meg a következőket:
    - Az IP-cím alapú tűzfalszabályok csatlakozni tudnak az [Azure Datacenter IP-tartományokhoz](https://www.microsoft.com/download/confirmation.aspx?id=41653), valamint a HTTPS (443) porthoz.
    - Engedélyezze az előfizetéshez tartozó Azure-régió IP-címtartományt.
    
### <a name="required-urls"></a>Szükséges URL-címek


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Feladatátvételi forgatókönyv esetén előfordulhat, hogy csatlakozni szeretne a replikált helyszíni hálózathoz.

Ha a feladatátvételt követően RDP használatával szeretne csatlakozni a Windows rendszerű virtuális gépekhez, engedélyezze a hozzáférést a következő módon:

1. Ha internetes hozzáférést kíván használni, engedélyezze az RDP-t a helyszíni virtuális gépen a feladatátvétel előtt. Ellenőrizze, hogy a **Nyilvános** résznél felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.
2. Ha helyek közötti VPN-kapcsolatot kíván használni, engedélyezze az RDP-t a helyszíni gépen. Engedélyezze az RDP-t a **Windows tűzfal** -> **Engedélyezett alkalmazások és szolgáltatások** területén a **Tartomány és Privát** hálózatok számára.
   Ellenőrizze, hogy az operációs rendszer tárolóhálózati szabályzata **OnlineAll** értékre van-e állítva. [További információk](https://support.microsoft.com/kb/3031135). A virtuális gépen nem lehetnek függőben lévő Windows-frissítések a feladatátvétel elindításakor. Ha vannak, akkor nem fog tudni bejelentkezni a virtuális gépre, amíg a frissítés be nem fejeződik.
3. A feladatátvételt követően ellenőrizze a **Rendszerindítási diagnosztika** részt a Windows Azure virtuális gépen a virtuális gép képernyőképének megtekintéséhez. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális gép, majd tekintse át a [hibaelhárítási tippeket](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

A feladatátvételt követően az Azure-beli virtuális gépeket ugyanazzal az IP-címmel érheti el, mint a replikált helyszíni virtuális gépet, vagy egy másik IP-címet. [További](concepts-on-premises-to-azure-networking.md) információ az IP-címzés feladatátvételhez való beállításáról.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> Állítsa be a vész- [helyreállítást az Azure-ba Hyper-v virtuális gépekre](tutorial-hyper-v-to-azure.md)
> [állítsa be a vész-helyreállítást az Azure-ba Hyper-v virtuális gépek VMM-felhőkben](tutorial-hyper-v-vmm-to-azure.md)
