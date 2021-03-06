---
title: Az Azure Virtual Machine Agent áttekintése
description: Az Azure Virtual Machine Agent áttekintése
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: 3d9c178201ab0c22ed4eab9cf65f7d48e59e1359
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246123"
---
# <a name="azure-virtual-machine-agent-overview"></a>Az Azure Virtual Machine Agent áttekintése
A Microsoft Azure virtuálisgép-ügynök (VM-ügynök) egy biztonságos, egyszerű folyamat, amely a virtuális gép (VM) interakcióját kezeli az Azure Fabric-vezérlővel. A virtuálisgép-ügynök elsődleges szerepköre az Azure-beli virtuális gépek bővítményeinek engedélyezése és végrehajtása. A virtuálisgép-bővítmények lehetővé teszik a virtuális gép telepítés utáni konfigurálását, például a szoftverek telepítését és konfigurálását. A virtuálisgép-bővítmények olyan helyreállítási funkciókat is lehetővé tesznek, mint például egy virtuális gép rendszergazdai jelszavának alaphelyzetbe állítása. Az Azure VM-ügynök nélkül nem futtathatók a virtuálisgép-bővítmények.

Ez a cikk az Azure-beli virtuális gépek ügynökének telepítését és észlelését ismerteti.

## <a name="install-the-vm-agent"></a>A virtuálisgép-ügynök telepítése

### <a name="azure-marketplace-image"></a>Azure Marketplace-rendszerkép

Az Azure-beli virtuálisgép-ügynök alapértelmezés szerint az Azure Marketplace-rendszerképből üzembe helyezett összes Windows rendszerű virtuális gépen telepítve van. Ha a portálról, a PowerShellből, a parancssori felületből vagy egy Azure Resource Manager sablonból telepít Azure Marketplace-rendszerképet, az Azure-beli virtuálisgép-ügynök is telepítve lesz.

A Windows vendég ügynök csomag két részből áll:

- Kiépítési ügynök (PA)
- Windows vendég ügynök (WinGa)

Egy virtuális gép elindításához a PA-nak telepítve kell lennie a virtuális gépen, de a WinGa nem szükséges a telepítéshez. A virtuális gép üzembe helyezésének ideje lehetőség kiválasztásával a WinGa telepítése nem végezhető el. Az alábbi példa bemutatja, hogyan választhatja ki a *provisionVmAgent* beállítást egy Azure Resource Manager sablonnal:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Ha nincsenek telepítve az ügynökök, nem használhat bizonyos Azure-szolgáltatásokat, például a Azure Backup vagy az Azure Security szolgáltatást. Ezeknek a szolgáltatásoknak telepíteniük kell egy bővítményt. Ha a WinGa nélküli virtuális gépet helyezett üzembe, akkor később is telepítheti az ügynök legújabb verzióját.

### <a name="manual-installation"></a>Manuális telepítés
A Windows rendszerű virtuális gép ügynöke manuálisan is telepíthető Windows Installer-csomaggal. Az Azure-ban üzembe helyezett egyéni virtuálisgép-lemezkép létrehozásakor szükség lehet manuális telepítésre. A Windows rendszerű virtuális gép ügynökének manuális telepítéséhez [töltse le a virtuálisgép-ügynök telepítőjét](https://go.microsoft.com/fwlink/?LinkID=394789). A virtuálisgép-ügynök a Windows Server 2008 R2 és újabb rendszereken támogatott.

> [!NOTE]
> Fontos, hogy frissítse a AllowExtensionOperations beállítást, miután manuálisan telepítette a VMAgent egy olyan virtuális gépre, amely a ProvisionVMAgent engedélyezése nélkül lett üzembe helyezve a lemezképből.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Előfeltételek
- A Windows rendszerű virtuális gép ügynökének legalább a Windows Server 2008 R2 (64-BITS) futtatásához szükségesnek kell lennie a .NET-keretrendszer 4,0-as verziójával. Lásd: [Az Azure-beli virtuálisgép-ügynökök minimális verziójának támogatása](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- Győződjön meg arról, hogy a virtuális gép rendelkezik hozzáféréssel az IP-168.63.129.16. További információ: [Mi az IP-168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

## <a name="detect-the-vm-agent"></a>A virtuálisgép-ügynök észlelése

### <a name="powershell"></a>PowerShell

Az Azure-beli virtuális gépekkel kapcsolatos információk lekéréséhez a Azure Resource Manager PowerShell-modul használható. Ha szeretné megtekinteni a virtuális gépekre vonatkozó információkat, például az Azure virtuálisgép-ügynök kiépítési állapotát, használja a [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

A következő összesűrített példa kimenet a *OSProfile*-ben beágyazott *ProvisionVMAgent* tulajdonságot mutatja be. Ezzel a tulajdonsággal határozható meg, hogy a virtuálisgép-ügynök telepítve van-e a virtuális gépen:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

A következő szkript használható a virtuális gépek neveinek és a virtuálisgép-ügynök állapotának tömör megjelenítéséhez:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Manuális észlelés

Windows rendszerű virtuális gépre való bejelentkezéskor a Feladatkezelő segítségével megvizsgálhatja a futó folyamatokat. Az Azure-beli virtuálisgép-ügynök kereséséhez nyissa meg a Feladatkezelő eszközt, kattintson a *részletek* fülre, és keresse meg a **WindowsAzureGuestAgent. exe**nevű folyamatot. A folyamat jelenléte azt jelzi, hogy a virtuálisgép-ügynök telepítve van.


## <a name="upgrade-the-vm-agent"></a>A virtuálisgép-ügynök frissítése
A Windows rendszerhez készült Azure VM-ügynök automatikusan frissül. Mivel az új virtuális gépek üzembe helyezése az Azure-ban történik, a virtuálisgép-kiépítési idő szerint kapják meg a legújabb virtuálisgép-ügynököt. Az egyéni virtuálisgép-rendszerképeket manuálisan kell frissíteni, hogy az új virtuálisgép-ügynököt a lemezkép létrehozási idején is tartalmazza.

## <a name="windows-guest-agent-automatic-logs-collection"></a>A Windows vendég ügynökének automatikus naplófájljainak gyűjteménye
A Windows vendég ügynökének van egy funkciója, amellyel automatikusan gyűjthet néhány naplót. Ezt a funkciót a CollectGuestLogs. exe folyamat végzi. Mind a Péter Cloud Services, mind a IaaS Virtual Machines, és a célja, hogy gyorsan & automatikusan gyűjtsön néhány diagnosztikai naplót egy virtuális gépről, így offline elemzéshez is használhatók. Az összegyűjtött naplók az eseménynaplók, az operációs rendszer naplói, az Azure-naplók és egyes beállításkulcsok. Létrehoz egy ZIP-fájlt, amely a virtuális gép Gazdagépére kerül át. Ebben a ZIP-fájlban a mérnöki csapatok és a támogatási szakemberek a virtuális gép tulajdonosának kérelmére vonatkozó problémák kivizsgálására is rámutatnak.

## <a name="next-steps"></a>Következő lépések
További információ a virtuálisgép-bővítményekről: [Azure-beli virtuális gépek bővítményei és funkcióinak áttekintése](overview.md).
