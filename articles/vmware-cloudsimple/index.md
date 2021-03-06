---
title: Azure VMware Solutions (AVS)
description: Az Azure VMware Solutions (AVS) dokumentációs portálja.
author: sharaths-cs
ms.author: b-mashar
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d81ea6778f3ba31d72c34334b1439994b076647c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025214"
---
# <a name="azure-vmware-solution-by-avs"></a>Az AVS Azure VMware-megoldása

Üdvözli az AVS Azure VMware-megoldásának teljes körű segítséget nyújtó portálja.
A dokumentációs webhelyen megismerheti az alábbi témaköröket:

## <a name="overview"></a>Áttekintés

További információ az Azure VMware Solutionsről (AVS)

* A funkciókkal, előnyökkel és használati forgatókönyvekkel kapcsolatos további információért lásd: [Mi az AVS Azure VMware-megoldása?](cloudsimple-vmware-solutions-overview.md)
* [A legfontosabb felügyeleti alapelvek](key-concepts.md) áttekintése

## <a name="quickstart"></a>Első lépések

Bevezetés a megoldás használatába

* [A szolgáltatás inicializálásának és a kapacitás megvásárlásának](quickstart-create-cloudsimple-service.md) megismerése
* Új VMware-környezet létrehozásához lásd: [AVS-magánfelhős környezet konfigurálása](quickstart-create-private-cloud.md)
* A [VMware rendszerű virtuális gépek felhasználása az Azure-ban](quickstart-create-vmware-virtual-machine.md) című cikkből megtudhatja, hogyan egységesítheti a felügyeletet a VMware-ben és az Azure-ban

## <a name="concepts"></a>Alapelvek

Tudnivalók az alábbi fogalmakról

* [AVS-szolgáltatás](cloudsimple-service.md) (más néven „Azure VMware Solutions (AVS) – Szolgáltatás”). Ezt az erőforrást régiónként egyszer kell létrehozni.
* Kapacitás vásárlása a környezet számára egy vagy több [AVS-csomópont](cloudsimple-node.md) erőforrás létrehozásával. Ez az erőforrás „Az AVS Azure VMware-megoldása – Csomópont” néven is ismert.
* VMware-környezet inicializálása és konfigurálása az [AVS-magánfelhők](cloudsimple-private-cloud.md) használatával.
* Felügyelet egységesítése az [AVS-virtuálisgépek](cloudsimple-virtual-machines.md) (más néven „Azure VMware Solutions (AVS) – Virtuális gép”) használatával.
* Az alapul szolgáló hálózat megtervezése [virtuális helyi hálózatok vagy alhálózatok](cloudsimple-vlans-subnets.md) használatával.
* Az alapul szolgáló hálózat szegmentálása és védelme a [tűzfaltábla](cloudsimple-firewall-tables.md) erőforrás használatával.
* Biztonságos hozzáférés a VMware-környezetekhez a WAN-hálózaton keresztül [VPN-átjárók](cloudsimple-vpn-gateways.md) használatával.
* A számítási feladatokhoz történő nyilvános hozzáférés engedélyezése [nyilvános IP-cím](cloudsimple-public-ip-address.md) használatával.
* Kapcsolatok létrehozása az Azure-beli virtuális hálózatokkal és helyszíni hálózatokkal [Azure-hálózati kapcsolat](cloudsimple-azure-network-connection.md) használatával.
* Riasztási e-mail-célok konfigurálása a [Fiókkezelés](cloudsimple-account.md) használatával.
* Felhasználói és rendszertevékenység naplóinak megtekintése a [tevékenységkezelési](cloudsimple-activity.md) képernyők használatával.
* A különböző [Vmware-összetevők](vmware-components.md) megismerése.

## <a name="tutorials"></a>Oktatóanyagok

Megtudhatja, hogy hajthatja végre az alábbi gyakori feladatokat:

* [AVS-szolgáltatás](create-cloudsimple-service.md) létrehozása azon régiónként egyszer, amelyekben üzembe szeretné helyezni a VMware-környezeteket.
* Alapvető szolgáltatásfunkciók kezelése az [AVS-portálon](access-cloudsimple-portal.md).
* Kapacitás engedélyezése és az infrastruktúra számlázásának optimalizálása [AVS-csomópontok vásárlásával](create-nodes.md).
* VMware-környezetek konfigurációinak kezelése AVS-magánfelhők használatával. [Létrehozhat](create-private-cloud.md), [kezelhet](manage-private-cloud.md), [bővíthet](expand-private-cloud.md) vagy [zsugoríthat](shrink-private-cloud.md) AVS-magánfelhőket.
* Egységesített felügyelet engedélyezése [Azure-előfizetések leképezésével](azure-subscription-mapping.md).
* Felhasználói és rendszertevékenység monitorozása a [Tevékenységoldalakkal](monitor-activity.md).
* A környezetek hálózatkezelésének konfigurálása [alhálózatok létrehozásával és kezelésével](create-vlan-subnet.md).
* Környezet szegmentálása és védelme [tűzfaltáblákkal és -szabályokkal](firewall.md).
* Számítási feladatok bejövő internet-hozzáférésének engedélyezése [nyilvános IP-címek kiosztásával](public-ips.md).
* Belső hálózatokról vagy ügyfél-munkaállomásokról való csatlakozás engedélyezése [VPN beállításával](vpn-gateway.md).
* Kommunikáció engedélyezése a [helyszíni környezetekből](on-premises-connection.md) és az [Azure-beli virtuális hálózatokra](virtual-network-connection.md).
* Riasztási célok konfigurálása és az összes megvásárolt kapacitás megtekintése a [fiók összegzésében](account.md)
* Azon [felhasználók](users.md) megtekintése, akik hozzáfértek az AVS-portálhoz.
* VMware virtuális gépek kezelése az Azure Portalon:
    * [Virtuális gép létrehozása](azure-create-vm.md) az Azure Portalon.
    * Saját létrehozású [virtuális gépek kezelése](azure-manage-vm.md).

## <a name="how-to-guides"></a>Útmutatók

Ezek az útmutatók az alábbi célok elérésére szolgáló megoldásokat ismertetik:

* [A környezet védelme](private-cloud-secure.md)
* Harmadik féltől származó eszközök telepítése, valamint további felhasználói és külső hitelesítési források engedélyezése a vSphere-ben [jogosultságemeléssel](escalate-privileges.md).
* A különböző VMware-szolgáltatásokhoz való hozzáférés szabályozása a [helyszíni DNS konfigurálásával](on-premises-dns-setup.md).
* Név és cím lefoglalásának engedélyezése a számítási feladatok számára a [számítási feladatokhoz tartozó DNS és DHCP konfigurálásával](dns-dhcp-setup.md).
* Annak megismerése, hogy a szolgáltatás hogyan biztosítja a platform biztonságát és funkcióit a szolgáltatás [frissítésével és újabb verzióra váltásával](vmware-components.md#updates-and-upgrades).
* A biztonsági mentés teljes bekerülési költségének (TCO) megtakarítása egy biztonsági mentési mintaarchitektúra [harmadik féltől származó biztonsági mentési szoftverrel (például a Veeammal)](backup-workloads-veeam.md) történő létrehozásával.
* Biztonságos környezet létrehozása az inaktív állapotú titkosítás [harmadik féltől származó KMS titkosítási szoftverrel](vsan-encryption.md) történő engedélyezésével.
* Az Azure Active Directory (Azure AD) felügyeletének VMware-be történő kiterjesztése az [Azure AD-identitásforrás](azure-ad.md) konfigurálásával.
