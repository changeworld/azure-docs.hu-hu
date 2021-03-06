---
title: Az előfizetésben lévő összes virtuális gép adatainak összegyűjtése a PowerShell-lel
description: Az előfizetésben lévő összes virtuális gép adatainak összegyűjtése a PowerShell-lel
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: dcscontentpm
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: 237081380445f2b2e4168ee3afe9a3ed7544fc89
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900203"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>Az előfizetésben lévő összes virtuális gép adatainak összegyűjtése a PowerShell-lel

Ez a szkript létrehoz egy CSV-t, amely tartalmazza a virtuális gép nevét, az erőforráscsoport nevét, a régiót, a Virtual Networkt, az alhálózatot, a magánhálózati IP-címet, az operációsrendszer-típust és a virtuális gépek nyilvános IP-címét a megadott

Ha nem rendelkezik Azure- [előfizetéssel](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free) .

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához csak kattintson a kódblokk jobb felső sarkában található **Kipróbálás** elemre. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="sample-script"></a>Példaszkript

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

#Provide the name of the csv file to be exported
$reportName = "myReport.csv"

Select-AzSubscription $subscriptionId
$report = @()
$vms = Get-AzVM
$publicIps = Get-AzPublicIpAddress 
$nics = Get-AzNetworkInterface | ?{ $_.VirtualMachine -NE $null} 
foreach ($nic in $nics) { 
    $info = "" | Select VmName, ResourceGroupName, Region, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
    $vm = $vms | ? -Property Id -eq $nic.VirtualMachine.id 
    foreach($publicIp in $publicIps) { 
        if($nic.IpConfigurations.id -eq $publicIp.ipconfiguration.Id) {
            $info.PublicIPAddress = $publicIp.ipaddress
            } 
        } 
        $info.OsType = $vm.StorageProfile.OsDisk.OsType 
        $info.VMName = $vm.Name 
        $info.ResourceGroupName = $vm.ResourceGroupName 
        $info.Region = $vm.Location 
        $info.VirturalNetwork = $nic.IpConfigurations.subnet.Id.Split("/")[-3] 
        $info.Subnet = $nic.IpConfigurations.subnet.Id.Split("/")[-1] 
        $info.PrivateIpAddress = $nic.IpConfigurations.PrivateIpAddress 
        $report+=$info 
    } 
$report | ft VmName, ResourceGroupName, Region, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
$report | Export-CSV "$home/$reportName"
```

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő parancsokat használja az előfizetésben található virtuális gépek adatainak CSV-exportálásának létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

|Parancs|Megjegyzések|
|-|-|
|[Select-AzSubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Set-AzContext)|Beállítja a bérlőt, az előfizetést és a környezetet az aktuális munkamenetben használni kívánt parancsmagokhoz.|
|[Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM)|A virtuális gép tulajdonságainak beolvasása.|
|[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzPublicIpAddress)|Egy nyilvános IP-címet kap.|
|[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzNetworkInterface)|Hálózati adapter beolvasása.|

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](https://docs.microsoft.com/azure/virtual-machines/windows/powershell-samples?toc=/azure/virtual-machines/windows/toc.json) találhat.


