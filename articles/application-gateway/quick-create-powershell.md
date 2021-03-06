---
title: 'Gyors útmutató: webes forgalom irányítása a PowerShell használatával'
titleSuffix: Azure Application Gateway
description: Ismerje meg, hogyan használható a Azure PowerShell egy olyan Azure-Application Gateway létrehozásához, amely egy háttér-készletben lévő virtuális gépekre irányítja a webes forgalmat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/05/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: abb38dfc342c8ff692ed1a3a05376b5dcefe8a3d
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399559"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Rövid útmutató: a webes forgalom közvetlen továbbítása az Azure Application Gateway használatával Azure PowerShell

Ebben a rövid útmutatóban a Azure PowerShell használatával hozhat létre Application Gateway-t. Ezt követően ellenőrizze, hogy megfelelően működik-e. 

Az Application Gateway az alkalmazás webes forgalmát egy háttér-készlet adott erőforrásaira irányítja. A figyelőket hozzárendelheti a portokhoz, szabályokat hozhat létre, és erőforrásokat adhat hozzá egy háttér-készlethez. Az egyszerűség kedvéért ez a cikk egy egyszerű telepítőt használ egy nyilvános előtér-IP-címmel, egy alapszintű figyelővel, amely egyetlen helyet üzemeltet az Application gatewayben, egy alapszintű kérelem-útválasztási szabályt és két virtuális gépet a háttér-készletben.

Ez a rövid útmutató az [Azure CLI](quick-create-cli.md) vagy a [Azure Portal](quick-create-portal.md)használatával is elvégezhető.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure PowerShell a 1.0.0 vagy újabb verzió](/powershell/azure/install-az-ps) (ha a Azure PowerShell helyileg futtatja).

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Az Azure-hoz való kapcsolódáshoz futtassa a `Connect-AzAccount`.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy újat.

Új erőforráscsoport létrehozásához használja a `New-AzResourceGroup` parancsmagot: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra.  Az Application Gateway-alhálózat csak Application Gateway átjárókat tartalmazhat. Más erőforrások nem engedélyezettek.  Létrehozhat egy új alhálózatot Application Gatewayhoz, vagy használhat egy meglévőt is. Ebben a példában két alhálózatot hoz létre ebben a példában: egyet az Application Gateway számára, és egy másikat a háttér-kiszolgálók számára. A Application Gateway előtérbeli IP-címét a használati esetnek megfelelően lehet nyilvános vagy privátként beállítani. Ebben a példában egy nyilvános előtérbeli IP-címet választ.

1. Hozza létre az alhálózati konfigurációkat a `New-AzVirtualNetworkSubnetConfig`használatával.
2. Hozza létre a virtuális hálózatot az alhálózati konfigurációk `New-AzVirtualNetwork`használatával. 
3. Hozza létre a nyilvános IP-címet a `New-AzPublicIpAddress`használatával. 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

### <a name="create-the-ip-configurations-and-frontend-port"></a>Az IP-konfigurációk és az előtérbeli port létrehozása

1. A `New-AzApplicationGatewayIPConfiguration` használatával hozza létre az Application Gateway-vel létrehozott alhálózatot társító konfigurációt. 
2. A `New-AzApplicationGatewayFrontendIPConfig` használatával hozza létre azt a konfigurációt, amely hozzárendeli a korábban az Application gatewayhez létrehozott nyilvános IP-címet. 
3. Az Application Gateway eléréséhez használja a `New-AzApplicationGatewayFrontendPort` az 80-es port hozzárendeléséhez.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>A háttérkészlet létrehozása

1. A `New-AzApplicationGatewayBackendAddressPool` használatával hozza létre a háttér-készletet az Application Gateway számára. A háttér-készlet most üres lesz, és a háttér-kiszolgálói hálózati adapterek a következő szakaszban való létrehozásakor a rendszer hozzáadja azokat a háttér-készlethez.
2. Konfigurálja a háttér-készlet beállításait a `New-AzApplicationGatewayBackendHttpSetting`.

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Figyelő létrehozása és szabály hozzáadása

Az Azure-ban egy figyelőnek kell engedélyeznie az Application Gateway számára a háttér-készletnek megfelelő útválasztási forgalmat. Az Azure-ban az is szükséges, hogy a figyelő melyik háttér-készletet használja a bejövő forgalomhoz. 

1. Hozzon létre egy figyelőt a `New-AzApplicationGatewayHttpListener` használatával a korábban létrehozott előtér-konfigurációval és a frontend-porttal. 
2. A `New-AzApplicationGatewayRequestRoutingRule` használatával hozzon létre egy *rule1*nevű szabályt. 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Most, hogy létrehozta a szükséges támogatási erőforrásokat, hozza létre az Application Gatewayt:

1. Az Application Gateway paramétereinek megadásához használja a `New-AzApplicationGatewaySku`.
2. Az Application Gateway létrehozásához használja a `New-AzApplicationGateway`.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="backend-servers"></a>Háttér-kiszolgálók

Most, hogy létrehozta a Application Gateway, hozza létre a háttérben futó virtuális gépeket, amelyek a webhelyeket üzemeltetik. A háttér a hálózati adapterek, a virtuálisgép-méretezési csoportok, a nyilvános IP-címek, a belső IP-címek, a teljes tartománynevek (FQDN) és a több-bérlős háttérrendszer, például a Azure App Service tagjai lehetnek. Ebben a példában két virtuális gépet hoz létre az Azure-hoz, hogy háttér-kiszolgálóként használja az Application Gateway-t. Az IIS-t a virtuális gépeken is telepítheti annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az Application Gateway-t.

#### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

1. Szerezze be a legutóbb létrehozott Application Gateway háttér-készlet konfigurációját a `Get-AzApplicationGatewayBackendAddressPool`.
2. Hozzon létre egy hálózati adaptert `New-AzNetworkInterface`.
3. Hozzon létre egy virtuálisgép-konfigurációt `New-AzVMConfig`.
4. Hozza létre a virtuális gépet a `New-AzVM`.

Ha a virtuális gépek létrehozásához az alábbi mintakód-mintát futtatja, az Azure kéri a hitelesítő adatok megadását. Adja meg a felhasználónévhez és a jelszóhoz az *azureuser* nevet:
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="test-the-application-gateway"></a>Az Application Gateway tesztelése

Bár az IIS nem szükséges az Application Gateway létrehozásához, ezt a rövid útmutatóban telepítette annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az Application Gatewayt. Az IIS használata az Application Gateway teszteléséhez:

1. Futtassa `Get-AzPublicIPAddress` az Application Gateway nyilvános IP-címének lekéréséhez. 
2. Másolja és illessze be a nyilvános IP-címet a böngésző címsorába. A böngésző frissítésekor látnia kell a virtuális gép nevét. Egy érvényes válasz ellenőrzi, hogy az Application Gateway sikeresen létrejött-e, és hogy sikeresen tud-e kapcsolatot létesíteni a háttérrel.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Az Application Gateway tesztelése](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Application Gateway használatával létrehozott erőforrásokra, törölje az erőforráscsoportot. Az erőforráscsoport törlésekor az Application Gateway és az összes kapcsolódó erőforrás is törlődik. 

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése alkalmazásátjáróval az Azure PowerShell használatával](./tutorial-manage-web-traffic-powershell.md)

