---
title: 'Gyors útmutató: Load Balancer létrehozása Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Load Balancer a Azure PowerShell használatával
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 0cd2bb54bb436beaa933195b88bc6f13a1b23e6f
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470428"
---
# <a name="quickstart-create-a-load-balancer-using-azure-powershell"></a>Rövid útmutató: Load Balancer létrehozása Azure PowerShell használatával

Ez a rövid útmutató bemutatja, hogyan hozhat létre Standard Load Balancert az Azure PowerShell használatával. A terheléselosztó teszteléséhez telepítsen három virtuális gépet (VM) a Windows Server rendszert futtató virtuális gépekre, és helyezzen terheléselosztást egy webalkalmazásba a virtuális gépek között. További információk a Standard Load Balancerről: [Mi a Standard Load Balancer?](load-balancer-standard-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

A terheléselosztó létrehozása előtt létre kell hoznia egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával. A következő példában létrehozunk egy *myresourcegroupslb erőforráscsoportban* nevű erőforráscsoportot a *EastUS* helyen:

```azurepowershell
$rgName='MyResourceGroupSLB'
$location='eastus'
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az alkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Hozzon létre egy nyilvános IP [-címet a New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). A következő példa létrehoz egy *myPublicIP* nevű zóna redundáns nyilvános IP-címet a *myresourcegroupslb erőforráscsoportban* erőforráscsoporthoz:

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard
```

Ha egy nyilvános IP-címet szeretne létrehozni az 1. zónában, használja a következőt:

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard
 -zone 1
```

Hozzon létre egy alapszintű nyilvános IP-címet a ```-SKU Basic``` használatával. Az alapszintű nyilvános IP-címek nem kompatibilisek a **standard** Load balancerrel. A Microsoft a **standard szintű** használatot javasolja a termelési munkaterhelésekhez.

> [!IMPORTANT]
> A rövid útmutató további része azt feltételezi, hogy a **standard** SKU a fenti SKU kiválasztási folyamat során lett kiválasztva.

## <a name="create-load-balancer"></a>Load Balancer létrehozása

Ebben a szakaszban az előtér-IP-címet és a háttérbeli címkészletet konfigurálja a terheléselosztó számára, majd létrehozza a standard Load Balancer.

### <a name="create-frontend-ip"></a>Előtérbeli IP-cím létrehozása

Hozzon létre egy előtér-IP-címet a [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). A következő példa létrehoz egy *myFrontEnd* nevű előtérbeli IP-konfigurációt, és csatolja a *myPublicIP* -címet:

```azurepowershell
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Háttérbeli címkészlet konfigurálása

Hozzon létre egy háttér-címkészletet a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). A virtuális gépek ehhez a háttér-készlethez csatlakoznak a hátralévő lépésekben. A következő példa egy *myBackEndPool*nevű háttér-címkészletet hoz létre:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

### <a name="create-a-health-probe"></a>Állapotminta létrehozása
Ahhoz, hogy a terheléselosztó monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Alapértelmezés szerint a virtuális gépeket a rendszer akkor távolítja el a terheléselosztó elosztásából, ha egy 15 másodperces intervallumon belül két, egymást követő hiba jelentkezik. Az állapotmintákat az alkalmazás egy protokollja vagy adott állapotellenőrzési oldala alapján lehet létrehozni.

Az alábbi példában egy TCP-mintavétel jön létre. Egyéni HTTP-mintavételt is létrehozhat részletesebb állapotellenőrzések elvégzéséhez. Egyéni HTTP-mintavétel használatakor létre kell hoznia az állapotellenőrzési oldalt, például: *healthcheck.aspx*. A mintavételnek a **HTTP 200 OK** választ kell visszaadnia ahhoz, hogy a terheléselosztó a gazdagépet a rotációban tartsa.

A TCP Health-mintavétel létrehozásához használja az [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). Az alábbi példában egy *myHealthProbe* nevű állapotmintát hozunk létre, amely a *80*-as *HTTP*-porton lévő virtuális gépeket monitorozza:

```azurepowershell
$probe = New-AzLoadBalancerProbeConfig `
 -Name 'myHealthProbe' `
 -Protocol Http -Port 80 `
 -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Ahhoz, hogy csak a megfelelő állapotú virtuális gépek fogadhassanak forgalmat, adja meg a használandó állapotmintát is.

Hozzon létre egy terheléselosztó-szabályt az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Az alábbi példa létrehoz egy *myLoadBalancerRule* nevű terheléselosztási szabályt, és elosztja a *80*-as *TCP*-port forgalmát:

```azurepowershell
$rule = New-AzLoadBalancerRuleConfig `
  -Name 'myLoadBalancerRuleWeb' -Protocol Tcp `
  -Probe $probe -FrontendPort 80 -BackendPort 80 `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bePool
```

### <a name="create-the-nat-rules"></a>A NAT-szabályok létrehozása

NAT-szabályok létrehozása a [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). Az alábbi példa a *myLoadBalancerRDP1* és a *myLoadBalancerRDP2* nevű NAT-szabályokat hozza létre, hogy engedélyezze az RDP-kapcsolatokat a 4221-es és 4222-es porttal rendelkező háttér-kiszolgálókkal:

```azurepowershell
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP1' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp -FrontendPort 4221 `
  -BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP2' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4222 `
  -BackendPort 3389

$natrule3 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP3' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4223 `
  -BackendPort 3389
```

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Hozza létre a standard Load Balancert a [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Az alábbi példa egy myLoadBalancer nevű nyilvános standard Load Balancer hoz létre az előtér-IP-konfigurációval, a háttér-készlettel, az állapot-mintavételsel, a terheléselosztási szabállyal és az előző lépésekben létrehozott NAT-szabályokkal:

```azurepowershell
$lb = New-AzLoadBalancer `
  -ResourceGroupName $rgName `
  -Name 'MyLoadBalancer' `
  -SKU Standard `
  -Location $location `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bepool `
  -Probe $probe `
  -LoadBalancingRule $rule `
  -InboundNatRule $natrule1,$natrule2,$natrule3
```

Alapszintű Load Balancer létrehozásához használja a ```-SKU Basic```. A Microsoft a standard szintű használatot javasolja a termelési munkaterhelésekhez.

> [!IMPORTANT]
> A rövid útmutató további része azt feltételezi, hogy a **standard** SKU a fenti SKU kiválasztási folyamat során lett kiválasztva.

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Mielőtt üzembe helyezne néhány virtuális gépet és tesztelhetné a terheléselosztót, hozza létre a támogató virtuális hálózati erőforrásokat: a virtuális hálózatot és a virtuális hálózati kártyákat. 

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy új virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Az alábbi példa létrehoz egy *mySubnet* hálózattal rendelkező *myVNet* nevű virtuális hálózatot:

```azurepowershell
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupSLB" `
  -Location $location `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-addresses-for-the-vms"></a>Nyilvános IP-címek létrehozása a virtuális gépekhez

Ha RDP-kapcsolaton keresztül szeretné elérni a virtuális gépeket, nyilvános IP-címmel kell rendelkeznie a virtuális gépek számára. Mivel ebben a forgatókönyvben egy standard Load Balancer van használatban, a virtuális gépekhez standard nyilvános IP-címeket kell létrehoznia a [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static
 

$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static


$RdpPublicIP_3 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_3" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static

```

Alapszintű nyilvános IP-címek létrehozásához használja a ```-SKU Basic```. A Microsoft a standard szintű használatot javasolja a termelési munkaterhelésekhez.

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Hálózati biztonságicsoport-szabály létrehozása a 3389-es porhoz
Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt, amely engedélyezi az RDP-kapcsolatokat a 3389-as porton a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell

$rule1 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleRDP' -Description 'Allow RDP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 1000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Hálózati biztonságicsoport-szabály létrehozása a 80-as porhoz
Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt, amely engedélyezi a bejövő kapcsolatokat a 80-as porton keresztül a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleHTTP' -Description 'Allow HTTP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 2000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RgName -Location $location `
-Name 'myNetworkSecurityGroup' -SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Hálózati adapterek létrehozása
Hozzon létre virtuális hálózati adaptereket, és társítsa azokat a korábbi lépésekben a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)paranccsal létrehozott nyilvános IP-címekkel és hálózati biztonsági csoportokkal. Az alábbi példa három virtuális NIC-t hoz létre. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során). Bármikor létrehozhat további virtuális NIC-ket és virtuális gépeket, és hozzáadhatja őket a terheléselosztóhoz:

```azurepowershell
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic1' -PublicIpAddress $RdpPublicIP_1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule1 -Subnet $vnet.Subnets[0]

$nicVM2 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic2' -PublicIpAddress $RdpPublicIP_2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule2 -Subnet $vnet.Subnets[0]

$nicVM3 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic3' -PublicIpAddress $RdpPublicIP_3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule3 -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell
$cred = Get-Credential
```

Most már létrehozhatja a virtuális gépeket a [New-AzVM](/powershell/module/az.compute/new-azvm)használatával. A következő példa két virtuális gépet hoz létre, és a szükséges virtuális hálózati összetevőket, ha azok még nem léteznek. Ebben a példában az előző lépésben létrehozott hálózati adapterek (*MyNic1*, *MyNic2*és *MyNic3*) hozzá vannak rendelve a Virtual Machines *myVM1*, a *myVM2*és a *VM3hoz*. Továbbá, mivel a hálózati adapterek a terheléselosztó háttér-készletéhez vannak társítva, a rendszer automatikusan hozzáadja a virtuális gépeket a háttér-készlethez.

```azurepowershell

# ############## VM1 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM1.Id

# Create a virtual machine
$vm1 = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig

# ############## VM2 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM2' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM2' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM2.Id

# Create a virtual machine
$vm2 = New-AzVM -ResourceGroupName $rgName -Zone 2 -Location $location -VM $vmConfig

# ############## VM3 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
| Add-AzVMNetworkInterface -Id $nicVM3.Id

# Create a virtual machine
$vm3 = New-AzVM -ResourceGroupName $rgName -Zone 3 -Location $location -VM $vmConfig
```

A három virtuális gép létrehozása és konfigurálása néhány percet vesz igénybe.

### <a name="install-iis-with-a-custom-web-page"></a>Az IIS telepítése egyéni weboldallal

Telepítse az IIS-t egy egyéni weboldallal mindkét háttérbeli virtuális gépen a következőképpen:

1. Szerezze be a három virtuális gép nyilvános IP-címét a `Get-AzPublicIPAddress`használatával.

   ```azurepowershell
     $vm1_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_1").IpAddress
     $vm2_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_2").IpAddress
     $vm3_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_3").IpAddress
    ```
2. Hozzon létre távoli asztali kapcsolatokat a *myVM1*, a *MyVM2*és a *myVM3* használatával a virtuális gépek nyilvános IP-címeivel a következőképpen: 

   ```azurepowershell    
     mstsc /v:$vm1_rdp_ip
     mstsc /v:$vm2_rdp_ip
     mstsc /v:$vm3_rdp_ip
   
    ```

3. Adja meg az egyes virtuális gépek hitelesítő adatait az RDP-munkamenet elindításához.
4. Indítsa el a Windows PowerShellt az egyes virtuális gépeken, és az alábbi parancsokkal telepítse az IIS-kiszolgálót, és frissítse az alapértelmezett htm-fájlt.

    ```azurepowershell
    # Install IIS
     Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host " + $env:computername)
    ```

5. Az RDP-kapcsolatok lezárása a *myVM1*, a *MyVM2*és a *myVM3*.


## <a name="test-load-balancer"></a>Terheléselosztó tesztelése
Szerezze be a terheléselosztó nyilvános IP-címét a [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). A következő példa a korábban létrehozott *myPublicIP* IP-címét kéri le:

```azurepowershell
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupSLB" `
  -Name "myPublicIP" | select IpAddress
```

A nyilvános IP-címet beírhatja egy böngészőbe. Ekkor megjelenik a webhely, amely tartalmazza annak a virtuális gépnek az eszköznevét, amelyhez a terheléselosztó forgalmat irányított, ahogy az alábbi példában is látható:

![Terheléselosztó tesztelése](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Annak megtekintéséhez, hogyan osztja el a terheléselosztó az alkalmazást futtató három virtuális gép között a forgalmat, kényszerítheti a webböngésző frissítését. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás eltávolításához.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroupSLB
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy standard Load Balancer, csatlakoztatott virtuális gépeket, konfigurálta az Load Balancer forgalmi szabályt, az állapot-mintavételt, majd tesztelte a Load Balancer. Ha többet szeretne megtudni a Azure Load Balancerről, folytassa a [Azure Load Balancer oktatóanyagokkal](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

További információ a [Load Balancer és a rendelkezésre állási zónákról](load-balancer-standard-availability-zones.md).