---
title: Windows 7 rendszerű virtuális gép üzembe helyezése – Azure
description: Windows 7 rendszerű virtuális gépek konfigurálása és központi telepítése Windows rendszerű virtuális asztalon.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158dd9d6a38cc1aaf7667c0b16518e23b3f7cecf
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366683"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Windows 7 rendszerű virtuális gép üzembe helyezése a Windows Virtual Desktopban

Windows 7 rendszerű virtuális gép (VM) Windows rendszerű virtuális asztalon való üzembe helyezésének folyamata némileg eltér a Windows újabb verzióit futtató virtuális gépeknél. Ebből az útmutatóból megtudhatja, hogyan helyezheti üzembe a Windows 7 rendszert.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, kövesse a [gazdagép létrehozása a PowerShell](create-host-pools-powershell.md) használatával című témakör utasításait a gazdagépek létrehozásához. Ezután kövesse a [gazdagépek létrehozása az Azure Marketplace-ben](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) című témakör utasításait, hogy egy vagy több felhasználót rendeljen az asztali alkalmazás csoportjához.

## <a name="configure-a-windows-7-virtual-machine"></a>Windows 7 rendszerű virtuális gép konfigurálása

Ha elvégezte az előfeltételeket, készen áll a Windows 7 rendszerű virtuális gép konfigurálására a Windows Virtual Desktopban.

Windows 7 rendszerű virtuális gép beállítása Windows rendszerű virtuális asztalon:

1. Jelentkezzen be a Azure Portalba, és keresse meg a Windows 7 Enterprise rendszerképet, vagy töltse fel a saját testreszabott Windows 7 Enterprise-(x64-) rendszerképét.  
2. Helyezzen üzembe egy vagy több virtuális gépet a Windows 7 Enterprise rendszerű gazdagép operációs rendszereként. Győződjön meg arról, hogy a virtuális gépek engedélyezik RDP protokoll (RDP) (a TCP/3389 portot).
3. Kapcsolódjon a Windows 7 Enterprise-gazdagéphez az RDP használatával, és hitelesítse magát a telepítés konfigurálása során megadott hitelesítő adatokkal. 
4. Adja hozzá azt a fiókot, amelyet a gazdagéphez a "Távoli asztal user" csoport RDP-vel való csatlakozásakor használt. Ha ezt nem teszi meg, előfordulhat, hogy nem tud csatlakozni a virtuális géphez a Active Directory tartományhoz való csatlakoztatása után.
5. Nyissa meg a Windows Updatet a virtuális gépen.
6. Telepítse az összes Windows-frissítést a fontos kategóriába.
7. Telepítse az összes Windows-frissítést a választható kategóriába (kivéve a nyelvi csomagokat). Ezzel telepíti a RDP protokoll 8,0 frissítést ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)), amelyre el kell végeznie ezeket az utasításokat.
8. Nyissa meg a Helyicsoportházirend-szerkesztőt, és navigáljon a **Számítógép konfigurációja** > **Felügyeleti sablonok** > **Windows-összetevők** > **Távoli asztali szolgáltatások** > távoli asztal **munkamenet-gazdagép** > **távoli munkamenet-környezet**.
9. Engedélyezze a RDP protokoll 8,0 szabályzatot.
10. Csatlakoztassa a virtuális gépet a Active Directory tartományhoz.
11. Indítsa újra a virtuális gépet a következő parancs futtatásával:
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. A regisztrációs jogkivonat beszerzéséhez kövesse az [itt](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) található utasításokat.
13. [Töltse le a Windows 7 rendszerhez készült Windows Virtual Desktop Agent ügynököt](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Töltse le a Windows virtuális asztali ügynök kezelőjét a Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)rendszerhez.
15. Nyissa meg a Windows rendszerű virtuális asztali ügynök telepítőjét, és kövesse az utasításokat. Ha a rendszer kéri, adja meg a 12. lépésben létrehozott regisztrációs kulcsot.
16. Nyissa meg a Windows rendszerű virtuális asztali telepítőt, és kövesse az utasításokat.
17. Ha szeretné, tiltsa le a TCP/3389 portot a virtuális géphez való közvetlen RDP protokoll-hozzáférés eltávolításához.

## <a name="next-steps"></a>Következő lépések

A Windows rendszerű virtuális asztal üzembe helyezése most már készen áll a használatra. A kezdéshez [töltse le a Windows rendszerű virtuális asztali ügyfél legújabb verzióját](https://aka.ms/wvd/clients/windows) .

Az ismert problémák és a Windows 7 rendszerhez készült hibaelhárítási utasítások listáját a Windows rendszerű virtuális asztalon tekintse meg a hibaelhárítási cikket a Windows 7 rendszerű virtuális [gépek hibaelhárítása a Windows rendszerű virtuális asztalon](troubleshoot-windows-7-vm.md)című cikkben.
