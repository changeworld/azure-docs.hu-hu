---
title: WinRM konfigurálása az Azure-beli virtuális gépek létrehozása után | Azure piactér
description: A cikk azt ismerteti, hogyan konfigurálható a Rendszerfelügyeleti webszolgáltatások (WinRM) az Azure-ban üzemeltetett virtuális gépek létrehozása után.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pabutler
ms.openlocfilehash: 7d050b32b212f66623a24bcf87d40111fc5973a5
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77481374"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>A WinRM konfigurálása a virtuális gépek létrehozása után

Ez a cikk bemutatja, hogyan konfigurálhat egy meglévő Azure-beli virtuális gépet (VM) a WinRM HTTPS protokollon keresztüli engedélyezéséhez.  Ez a konfiguráció csak a Windows-alapú virtuális gépekre vonatkozik, és a következő kétlépéses folyamatot igényli:

1. Engedélyezze a HTTP protokollon keresztüli hálózati forgalmat.  Ezt a beállítást a Azure Portalban fogja konfigurálni a virtuális géphez.
2. Konfigurálja úgy a virtuális gépet, hogy a megadott PowerShell-parancsfájlok futtatásával engedélyezze a WinRM szolgáltatást.


## <a name="enabling-port-traffic"></a>A portok forgalmának engedélyezése

A felügyeleti webszolgáltatások HTTPS protokollon keresztüli használata a 5986-es portot használja, amely alapértelmezés szerint nincs engedélyezve az Azure piactéren kínált előre konfigurált Windows-alapú virtuális gépeken. A protokoll engedélyezéséhez kövesse az alábbi lépéseket egy új szabály hozzáadásához a hálózati biztonsági csoport (NSG) számára a [Azure Portal](https://portal.azure.com)használatával.  További információ a NSG: [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Navigáljon a panel **virtuális gépei >**   <*virtuális gép neve*>   **> Beállítások/hálózatkezelés**elemre.
2.  Kattintson a NSG nevére (ebben a példában a **testvm11002**) a tulajdonságainak megjelenítéséhez:

    ![Hálózati biztonsági csoport tulajdonságai](./media/nsg-properties.png)
 
3. A **Beállítások**területen válassza a **bejövő biztonsági szabályok** lehetőséget a panel megjelenítéséhez.
4. Kattintson a **+ Hozzáadás** gombra egy `WinRM_HTTPS` nevű új szabály létrehozásához a 5986-as TCP-porthoz.

    ![Bejövő hálózati biztonsági szabály hozzáadása](./media/nsg-new-rule.png)

5. Ha befejezte az értékek megadását, kattintson **az OK gombra** .  A bejövő biztonsági szabályok listájának a következő új bejegyzéseket kell tartalmaznia.

    ![A bejövő hálózati biztonsági szabályok listázása](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>A virtuális gép konfigurálása a WinRM engedélyezéséhez 

A következő lépésekkel engedélyezheti és konfigurálhatja a Rendszerfelügyeleti webszolgáltatásokat a Windows rendszerű virtuális gépen.   

1. Hozzon létre egy Távoli asztal kapcsolatot az Azure által üzemeltetett virtuális géppel.  További információ: [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  A további lépések a virtuális gépen lesznek futtatva.
2. Töltse le a következő fájlokat, és mentse őket a virtuális gép egy mappájába:
    - [ConfigureWinRM. ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [MakeCert. exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf. cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Nyissa meg a **PowerShell-konzolt** emelt szintű jogosultságokkal (**Futtatás rendszergazdaként**). 
4. Futtassa a következő parancsot, és adja meg a szükséges paramétert: a virtuális gép teljes tartományneve (FQDN): <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![PowerShell-konfigurációs parancsfájl 1](./media/powershell-file1.png)

    Ez a parancsfájl attól függ, hogy a másik két fájl ugyanabban a mappában van-e.


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a WinRM szolgáltatást, készen áll a [virtuális gép üzembe helyezésére az összetevő virtuális merevlemezéről](./cpp-deploy-vm-vhd.md).
