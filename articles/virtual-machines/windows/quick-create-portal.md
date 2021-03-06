---
title: Rövid útmutató – Windows rendszerű virtuális gép létrehozása a Azure Portal
description: Ez a rövid útmutató a Windows rendszerű virtuális gépek az Azure Portallal történő létrehozását ismerteti.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 002d374f5be606688121ef4a3952383567c43e85
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240043"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Rövid útmutató: Windows rendszerű virtuális gép létrehozása az Azure Portalon

Az Azure-beli virtuális gépek (VM-ek) létrehozhatók az Azure Portal segítségével. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a virtuális gépek és a társított erőforrások létrehozásához. Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Portal egy virtuális gép (VM) üzembe helyezéséhez az Azure-ban, amely a Windows Server 2019-et futtatja. A virtuális gép működésének ellenőrzéséhez ezután RDP-kapcsolaton keresztül csatlakozzon a géphez, és telepítse az IIS-webkiszolgálót.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Írja be a **virtuális gépeket** a keresésbe.
1. A **szolgáltatások**területen válassza a **virtuális gépek**lehetőséget.
1. A **virtuális gépek** lapon válassza a **Hozzáadás**lehetőséget. 
1. Az **Alapok** fül **Projektadatok** részén győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva, és válassza az **Új létrehozása** lehetőséget az Erőforráscsoport részen. A név mezőbe írja be a *myResourceGroup* nevet. 

    ![Új erőforráscsoport létrehozása virtuális géphez](./media/quick-create-portal/project-details.png)

1. A **példány részletei**területen írja be a myVM **nevet a virtuális GÉPNEK** , majd válassza az *USA keleti* **régiója**lehetőséget, majd a **rendszerképhez**válassza a *Windows Server 2019 Datacenter* lehetőséget. Hagyja változatlanul a többi alapértelmezett értéket.

    ![Példány részletei szakasz](./media/quick-create-portal/instance-details.png)

1. A **Rendszergazdai fiók**, területen adjon meg egy felhasználónevet (pl. *azureuser*) és egy jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Felhasználónév és jelszó megadása](./media/quick-create-portal/administrator-account.png)

1. A **bejövő portszabályok**területen válassza a **kijelölt portok engedélyezése** lehetőséget, majd válassza az **RDP (3389)** és a **http (80)** elemet a legördülő menüből.

    ![RDP- és a HTTP-portok megnyitása](./media/quick-create-portal/inbound-port-rules.png)

1. Hagyja változatlanul a többi alapértelmezett beállítást, és kattintson a **Áttekintés + létrehozás** gombra a lap alján.

    ![Áttekintés és létrehozás](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Hozzon létre egy távoli asztali kapcsolatot a virtuális géppel. Ezek az utasítások ismertetik, hogyan csatlakozhat a virtuális gépéhez egy Windows rendszerű gépről. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12).

1. Kattintson a virtuális gép áttekintés lapján található **kapcsolat** gombra. 

    ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/quick-create-portal/portal-quick-start-9.png)
    
2. A **Csatlakozás virtuális géphez** lapon tartsa meg az alapértelmezett beállításokat az IP-cím, az 3389-as porton keresztül történő csatlakozáshoz, majd kattintson az **RDP-fájl letöltése**elemre.

2. Nyissa meg a letöltött RDP-fájlt, és kattintson a **Csatlakozás** gombra, amikor a rendszer erre kéri. 

3. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet **localhost**\\*felhasználónév* formátumban, és adja meg a virtuális géphez létrehozott jelszót, majd kattintson az **OK** gombra.

4. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A kapcsolat létrehozásához kattintson az **Igen** vagy **Folytatás** gombra.

## <a name="install-web-server"></a>Webkiszolgáló telepítése

A virtuális gép működésének ellenőrzéséhez telepítse az IIS-webkiszolgálót. Nyisson meg egy PowerShell-parancssort a virtuális gépen, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ha elkészült, zárja be a virtuális géphez nyitott RDP-kapcsolatot.


## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

A portálon válassza ki a virtuális gépet, és a virtuális gép áttekintésében kattintson az IP-cím jobb oldalán található **Másolás** gombra a másoláshoz, és illessze be egy böngésző lapra. Ekkor megnyílik az alapértelmezett IIS-Kezdőlap, és a következőhöz hasonlóan kell kinéznie:

![Alapértelmezett IIS-webhely](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. 

Válassza ki a virtuális géphez tartozó erőforráscsoportot, majd válassza a **Törlés**lehetőséget. Erősítse meg az erőforráscsoport nevét az erőforrások törlésének befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, megnyitott egy hálózati portot a webes forgalom számára, valamint telepített egy alapszintű webkiszolgálót. Ha bővebb információra van szüksége az Azure-alapú virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)