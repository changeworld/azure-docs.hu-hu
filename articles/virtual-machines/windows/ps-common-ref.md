---
title: Az Azure Virtual Machines általános PowerShell-parancsai
description: Általános PowerShell-parancsok az Azure-beli Windows rendszerű virtuális gépek létrehozásának és kezelésének megkezdéséhez.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 1d66908d956f60ec894af50c45fd64387639addf
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981279"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Általános PowerShell-parancsok az Azure-Virtual Machines létrehozásához és kezeléséhez

Ez a cikk az Azure-előfizetésben található virtuális gépek létrehozásához és kezeléséhez használható Azure PowerShell-parancsokat ismerteti.  Az adott parancssori kapcsolókkal és lehetőségekkel kapcsolatos részletesebb segítségért használhatja a **Get-Help** *parancsot*.

 

Ezek a változók akkor lehetnek hasznosak, ha a cikkben szereplő parancsok közül többet futtat:

- $location – a virtuális gép helye. A [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) használatával megkeresheti az Ön számára megfelelő [földrajzi régiót](https://azure.microsoft.com/regions/) .
- $myResourceGroup – a virtuális gépet tartalmazó erőforráscsoport neve.
- $myVM – a virtuális gép neve.

## <a name="create-a-vm---simplified"></a>Virtuális gép létrehozása – egyszerűsített

| Tevékenység | Parancs |
| ---- | ------- |
| Egyszerű virtuális gép létrehozása | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> A New-AzVM *egyszerűsített* paramétereket tartalmaz, ahol minden szükséges egyetlen név. A-name értéket fogja használni az új virtuális gép létrehozásához szükséges összes erőforrás neveként. Több is megadható, de ez minden szükséges.|
| Virtuális gép létrehozása egyéni rendszerképből | New-AzVm-ResourceGroupName $myResourceGroup-Name $myVM ImageName "myImage" – hely $location  <BR></BR><BR></BR>Már létre kell hoznia egy saját [felügyelt képet](capture-image-resource.md). A rendszerkép használatával több, azonos virtuális gép is létrehozható. |



## <a name="create-a-vm-configuration"></a>Virtuális gép konfigurációjának létrehozása

| Tevékenység | Parancs |
| ---- | ------- |
| Virtuális gép konfigurációjának létrehozása |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $MyVM-VMSize "Standard_D1_v1"<BR></BR><BR></BR>A virtuálisgép-konfiguráció a virtuális gép beállításainak definiálására vagy frissítésére szolgál. A konfiguráció inicializálva van a virtuális gép nevével és [méretével](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Konfigurációs beállítások hozzáadása |$vm = [set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $VM-Windows-számítógépnév $MyVM-hitelesítőadat $cred-ProvisionVMAgent-EnableAutoUpdate<BR></BR><BR></BR>Az operációs rendszer beállításai, beleértve a [hitelesítő adatokat](https://technet.microsoft.com/library/hh849815.aspx) , hozzá lettek adva a New-AzVMConfig használatával korábban létrehozott konfigurációs objektumhoz. |
| Hálózati adapter hozzáadása |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $VM-ID $nic. ID<BR></BR><BR></BR>A virtuális gépnek [hálózati adapterrel](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kell rendelkeznie a virtuális hálózatban való kommunikációhoz. A [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) használatával lekérheti a meglévő hálózati adapterek objektumait is. |
| Platform rendszerképének meghatározása |$vm = [set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $VM-közzétevő neve "publisher_name"-ajánlat "publisher_offer"-sku "product_sku"-version "Latest"<BR></BR><BR></BR>A rendszer a korábban a New-AzVMConfig használatával létrehozott konfigurációs objektumhoz adja hozzá a [rendszerképekkel kapcsolatos adatokat](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . A parancs által visszaadott objektum csak akkor használatos, ha az operációsrendszer-lemezt platform-lemezkép használatára állítja be. |
| Virtuális gép létrehozása |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $MyResourceGroup-Location $Location-VM $VM<BR></BR><BR></BR>Az összes erőforrás egy [erőforráscsoporthoz](../../azure-resource-manager/management/manage-resource-groups-powershell.md)lett létrehozva. A parancs futtatása előtt futtassa a New-AzVMConfig, a set-AzVMOperatingSystem, a set-AzVMSourceImage, a Add-AzVMNetworkInterface és a set-AzVMOSDisk parancsot. |
| Virtuális gép frissítése |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $MYRESOURCEGROUP – VM $VM<BR></BR><BR></BR>Szerezze be a virtuális gép aktuális konfigurációját a Get-AzVM használatával, módosítsa a virtuálisgép-objektum konfigurációs beállításait, majd futtassa ezt a parancsot. |

## <a name="get-information-about-vms"></a>Virtuális gépek adatainak beolvasása

| Tevékenység | Parancs |
| ---- | ------- |
| Egy előfizetésben lévő virtuális gépek listázása |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Erőforráscsoporthoz tartozó virtuális gépek listázása |Get-AzVM-ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Az előfizetéshez tartozó erőforráscsoportok listájának lekéréséhez használja a [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Virtuális gép adatainak lekérése |Get-AzVM-ResourceGroupName $myResourceGroup – név $myVM |

## <a name="manage-vms"></a>Virtuális gépek kezelése
| Tevékenység | Parancs |
| --- | --- |
| Virtuális gép elindítása |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $MyResourceGroup – név $myVM |
| Virtuális gép leállítása |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Futó virtuális gép újraindítása |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Virtuális gép törlése |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |


## <a name="next-steps"></a>Következő lépések
* A virtuális gépek létrehozásának alapvető lépései a [Windows virtuális gép létrehozása a Resource Manager és a PowerShell használatával](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című témakörben találhatók.

