---
title: A Virtual Network API Management használata a Application Gateway
titleSuffix: Azure API Management
description: Megtudhatja, hogyan állíthatja be és konfigurálhatja az Azure API Management belső Virtual Networkekben a Application Gateway (WAF) felülettel
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: 2b8cf66afa1d8aa592d5755ebab70cd6ad2e75fd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298053"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>API Management integrálása egy belső VNET Application Gateway

## <a name="overview"> </a> Áttekintés

A API Management szolgáltatás belső módban is konfigurálható Virtual Network, amely csak a Virtual Networkon keresztül érhető el. Az Azure Application Gateway egy olyan Pásti szolgáltatás, amely egy 7. rétegbeli Load balancert biztosít. Fordított proxy szolgáltatásként működik, és biztosítja a webalkalmazási tűzfal (WAF) ajánlatát.

A belső VNET API Management kiépített, a Application Gateway előtérrel való egyesítése a következő helyzetekben teszi lehetővé:

* Ugyanazt a API Management erőforrást használja a belső fogyasztók és a külső fogyasztók általi felhasználáshoz.
* Használjon egyetlen API Management erőforrást, és a API Management számára elérhető API-k egy részhalmaza van megadva a külső felhasználók számára.
* A nyilvános internetről be-és kikapcsolt állapotban lévő API Managementhoz való hozzáférés váltásának lépésenkénti módja.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:

* Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Tanúsítványok – pfx és CER az API állomásneve és a pfx a fejlesztői portál állomásneve számára.

## <a name="scenario"> </a> Forgatókönyv

Ez a cikk azt ismerteti, hogyan használható egyetlen API Management szolgáltatás a belső és a külső felhasználók számára, és hogyan működhet egyetlen előtérben a helyszíni és a felhőalapú API-k esetében is. Azt is megtudhatja, hogyan teheti elérhetővé az API-k csak egy részhalmazát (a példában zöld színnel kiemelve) a külső felhasználásra a Application Gateway-ben rendelkezésre álló útválasztási funkciók használatával.

Az első telepítési példában az összes API-t csak a Virtual Networkon belül felügyeli a rendszer. A belső fogyasztók (narancssárga színnel kiemelve) a belső és külső API-k elérésére is jogosultak. A forgalom soha nem megy ki az internetre. A nagy teljesítményű kapcsolatok expressz Route-áramkörökön keresztül érkeznek.

![URL-útvonal](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Kezdés előtt

* Ügyeljen arra, hogy az Azure PowerShell legújabb verzióját használja. Tekintse meg a telepítési utasításokat a következő helyen: [Install Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Mi szükséges a API Management és Application Gateway közötti integráció létrehozásához?

* **Háttér-kiszolgáló készlete:** Ez a API Management szolgáltatás belső virtuális IP-címe.
* **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezeket a beállításokat a rendszer a készletben lévő összes kiszolgálóra alkalmazza.
* **Előtér-port:** Ez az Application gatewayen megnyitott nyilvános port. A forgalom a háttér-kiszolgálók egyikére lesz átirányítva.
* **Figyelő:** A figyelő egy előtérbeli porttal, egy protokollal (Http vagy Https, a kis- és a nagybetűk megkülönböztetésével) és SSL tanúsítványnévvel rendelkezik (SSL-kiszervezés konfigurálásakor).
* **Szabály:** A szabály egy figyelőt hoz létre egy háttér-kiszolgáló készlethez.
* **Egyéni állapot** mintavétele: Application Gateway alapértelmezés szerint az IP-címeken alapuló mintavételeket használja annak megállapítására, hogy a BackendAddressPool mely kiszolgálók aktívak. A API Management szolgáltatás csak a megfelelő állomásfejléc-fejlécre vonatkozó kérelmekre válaszol, ezért az alapértelmezett mintavételek sikertelenek lesznek. Meg kell határozni egy egyéni állapot-mintavételt, hogy az Application Gateway megtudja, hogy a szolgáltatás életben van, és továbbítania kell a kérelmeket.
* **Egyéni tartományi tanúsítványok:** Az internetről API Management eléréséhez létre kell hoznia az állomásnév CNAME hozzárendelését az Application Gateway előtér-DNS-névre. Ezzel biztosíthatja, hogy a API Management Application Gateway küldött állomásnév fejléce és tanúsítványa egy APIM legyen felismerhető. Ebben a példában két tanúsítványt fogunk használni – a háttérrendszer és a fejlesztői portál számára.  

## <a name="overview-steps"> </a> API Management és Application Gateway integrálásához szükséges lépések

1. Egy erőforráscsoport létrehozása a Resource Manager számára.
2. Hozzon létre egy Virtual Network, alhálózat és nyilvános IP-címet a Application Gateway számára. Hozzon létre egy másik alhálózatot a API Managementhoz.
3. Hozzon létre egy API Management szolgáltatást a fent létrehozott VNET-alhálózaton belül, és ellenőrizze, hogy a belső módot használja-e.
4. Állítson be egy egyéni tartománynevet a API Management szolgáltatásban.
5. Hozzon létre egy Application Gateway konfigurációs objektumot.
6. Hozzon létre egy Application Gateway erőforrást.
7. Hozzon létre egy CNAME-t a Application Gateway nyilvános DNS-nevéből a API Management proxy állomásnévre.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>A fejlesztői portál külsőleg való kimutatása Application Gateway

Ebben az útmutatóban a **fejlesztői portált** külső célközönségeknek is elérhetővé tesszük a Application Gatewayon keresztül. További lépéseket igényel a fejlesztői portál figyelő, a mintavétel, a beállítások és a szabályok létrehozásához. A részleteket a megfelelő lépésekben találhatja meg.

> [!WARNING]
> Ha az Azure AD-t vagy harmadik féltől származó hitelesítést használ, engedélyezze Application Gateway a [cookie-alapú munkamenet-affinitás](../application-gateway/features.md#session-affinity) funkciót.

> [!WARNING]
> Ha meg szeretné akadályozni, hogy Application Gateway WAF a OpenAPI-specifikáció letöltését a fejlesztői portálon, le kell tiltania a tűzfalszabály `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`.

## <a name="create-a-resource-group-for-resource-manager"></a>Erőforráscsoport létrehozása a Resource Managerhez

### <a name="step-1"></a>1\. lépés

Jelentkezzen be az Azure-ba

```powershell
Connect-AzAccount
```

Hitelesítés a hitelesítő adataival.

### <a name="step-2"></a>2\. lépés

Válassza ki a kívánt előfizetést.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>3\. lépés

Hozzon létre egy erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez lesz az erőforráscsoport erőforrásainak alapértelmezett helye. Győződjön meg arról, hogy az Application Gateway létrehozására irányuló összes parancs ugyanazt az erőforráscsoportot használja.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Hozzon létre egy Virtual Network és egy alhálózatot az Application Gateway számára

Az alábbi példa bemutatja, hogyan hozhat létre Virtual Network a Resource Manager használatával.

### <a name="step-1"></a>1\. lépés

Rendelje hozzá a 10.0.0.0/24 címtartományt az alhálózati változóhoz, amelyet a rendszer a Virtual Network létrehozásakor Application Gateway használni.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>2\. lépés

Rendelje hozzá a 10.0.1.0/24 címtartományt az alhálózati változóhoz, amelyet a rendszer a Virtual Network létrehozásakor API Management használni.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>3\. lépés

Hozzon létre egy **appgwvnet** nevű Virtual Network az **APIM-appGw-RG** erőforráscsoporthoz az USA nyugati régiójában. Használja a 10.0.0.0/16 előtagot a 10.0.0.0/24 és a 10.0.1.0/24 alhálózattal.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>4\. lépés

Alhálózati változó társítása a következő lépésekhez

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>API Management szolgáltatás létrehozása belső módban konfigurált VNET belül

Az alábbi példa bemutatja, hogyan hozhat létre egy API Management szolgáltatást egy VNET, amely csak belső hozzáférésre van konfigurálva.

### <a name="step-1"></a>1\. lépés

Hozzon létre egy API Management Virtual Network objektumot a fent létrehozott alhálózati $apimsubnetdata használatával.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>2\. lépés

Hozzon létre egy API Management szolgáltatást a Virtual Networkon belül.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

A fenti parancs sikeres végrehajtásához tekintse meg a [belső VNET eléréséhez szükséges DNS-konfigurációt API Management szolgáltatás](api-management-using-with-internal-vnet.md#apim-dns-configuration) eléréséhez. Ez a lépés több mint fél óráig is eltarthat.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Egyéni tartománynév beállítása API Management

> [!IMPORTANT]
> Az [új fejlesztői portálhoz](api-management-howto-developer-portal.md) az alábbi lépéseken felül a API Management felügyeleti végpontjának való kapcsolódás engedélyezése is szükséges.

### <a name="step-1"></a>1\. lépés

Inicializálja a következő változókat a tartományokhoz tartozó titkos kulcsokkal rendelkező tanúsítványok részleteivel. Ebben a példában a `api.contoso.net` és a `portal.contoso.net`t fogjuk használni.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>2\. lépés

Hozza létre és állítsa be a proxyhoz tartozó állomásnév-konfigurációs objektumokat és a portált.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Az örökölt fejlesztői portál kapcsolatának konfigurálásához a `-HostnameType DeveloperPortal` és a `-HostnameType Portal`cseréjére van szükség.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy nyilvános IP- **publicIP01** az erőforráscsoporthoz.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Amikor a szolgáltatás elindul, egy IP-cím lesz kiosztva az Application Gatewaynek.

## <a name="create-application-gateway-configuration"></a>Application Gateway-konfiguráció létrehozása

Az Application Gateway létrehozása előtt minden konfigurációs elemet be kell állítani. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

### <a name="step-1"></a>1\. lépés

Hozzon létre egy **gatewayIP01** nevű Application Gateway IP-konfigurációt. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány elfoglal egy IP-címet.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>2\. lépés

Konfigurálja az előtér-IP-portot a nyilvános IP-végponthoz. Ez a port az a port, amelyhez a végfelhasználók csatlakoznak.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>3\. lépés

Konfigurálja az előtérbeli IP-portot egy nyilvános IP-címvégponttal.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>4\. lépés

Konfigurálja a Application Gateway tanúsítványait, amelyeket a rendszer az áthaladó forgalom visszafejtésére és újratitkosítására használ.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>5\. lépés

Hozza létre a HTTP-figyelőket a Application Gatewayhoz. Rendelje hozzájuk az előtér-IP-konfigurációt, a portot és az SSL-tanúsítványokat.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>6\. lépés

Hozzon létre egyéni mintavételeket a API Management Service `ContosoApi` proxy tartomány végpontján. Az elérési út `/status-0123456789abcdef` az összes API Management szolgáltatásban üzemeltetett alapértelmezett állapot-végpont. Állítsa be `api.contoso.net` egyéni mintavételi állomásnévként az SSL-tanúsítvánnyal biztonságossá tételéhez.

> [!NOTE]
> A hostname `contosoapi.azure-api.net` az alapértelmezett proxy állomásnév, amelyet a rendszer a `contosoapi` nevű szolgáltatás nyilvános Azure-ban való létrehozásakor konfigurált.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>7\. lépés

Töltse fel az SSL-kompatibilis háttér-készlet erőforrásaihoz használni kívánt tanúsítványt. Ez ugyanaz a tanúsítvány, amelyet a fenti 4. lépésben adott meg.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>8\. lépés

A Application Gateway HTTP-háttér beállításainak konfigurálása. Ez magában foglalja a háttérbeli kérelmek időtúllépési korlátjának beállítását, amely után a rendszer megszakította azokat. Ez az érték eltér a mintavétel időkorlátján.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>9\. lépés

Konfigurálja a **apimbackend** nevű HÁTTÉRbeli IP-címkészletet a fent létrehozott API Management szolgáltatás belső virtuális IP-címével.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>10. lépés

Hozzon létre szabályokat a Application Gateway számára az alapszintű útválasztás használatához.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Módosítsa a-Szabálytípus és az útválasztást a fejlesztői portál bizonyos oldalaihoz való hozzáférés korlátozásához.

### <a name="step-11"></a>11. lépés

Adja meg a példányok számát és a méretet a Application Gateway számára. Ebben a példában a [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) -t használjuk a API Management erőforrás fokozott biztonsága érdekében.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>12. lépés

A WAF beállítása "megelőzés" módban.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Application Gateway létrehozása

Hozzon létre egy Application Gateway az előző lépésekből származó összes konfigurációs objektummal.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME a API Management proxy hostname a Application Gateway erőforrás nyilvános DNS-nevéhez

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Nyilvános IP-cím használata esetén a Application Gateway dinamikusan hozzárendelt DNS-nevet igényel, amely nem könnyen használható.

A Application Gateway DNS-nevét olyan CNAME rekord létrehozásához kell használni, amely a APIM-proxy állomásnevét (például a fenti példákban `api.contoso.net`) erre a DNS-névre mutat. Az előtérbeli IP CNAME rekordjának konfigurálásához kérje le a Application Gateway és a hozzá tartozó IP/DNS-név részleteit a PublicIPAddress elem használatával. A-Records használata nem ajánlott, mert a virtuális IP-cím az átjáró újraindításakor változhat.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"> </a> Összefoglalás
A VNET konfigurált Azure API Management egyetlen átjáró felületet biztosít az összes konfigurált API-hoz, függetlenül attól, hogy azok a helyszínen vagy a felhőben vannak tárolva. A Application Gateway és az API Management integrációja biztosítja a rugalmasságot, hogy az egyes API-k szelektíven elérhetők legyenek az interneten, valamint a webalkalmazási tűzfalat is biztosítson az API Management-példányhoz.

## <a name="next-steps"> </a> További lépések
* További információ az Azure Application Gateway
  * [Application Gateway áttekintése](../application-gateway/application-gateway-introduction.md)
  * [Webalkalmazási tűzfal Application Gateway](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Application Gateway elérésiút-alapú útválasztás használatával](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* További információ a API Management és a virtuális hálózatok
  * [A API Management csak a VNET-en keresztül érhető el](api-management-using-with-internal-vnet.md)
  * [API Management használata a VNET-ben](api-management-using-with-vnet.md)
