---
title: Azure-beli tűzfal létrehozása a FreeBSD csomagszűrő használatával
description: Ismerje meg, hogyan helyezhet üzembe NAT-tűzfalat a FreeBSD PF használatával az Azure-ban.
author: KylieLiang
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 9b78c0d93b57a3e3f4963088d0b93f121f57483c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945110"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Biztonságos tűzfal létrehozása az Azure-ban a FreeBSD csomagszűrő használatával
Ez a cikk bemutatja, hogyan helyezhet üzembe egy NAT-tűzfalat a FreeBSD Packer-szűrő segítségével Azure Resource Manager sablon használatával a gyakori webkiszolgálói forgatókönyvhöz.

## <a name="what-is-pf"></a>Mi az a PF?
A PF (csomagszűrő, szintén írásos PF) egy BSD licenccel rendelkező állapot-nyilvántartó csomagszűrő, egy központi szoftver a tűzfalak számára. A PF azóta gyorsan fejlődött, és mostantól számos előnnyel jár a többi elérhető tűzfallal szemben. A hálózati címfordítás (NAT) a PF-ben van, mivel az első nap, majd a csomagütemező és az aktív üzenetsor-kezelés integrálva lett a PF-ba, a ALTQ integrálásával és a PF konfigurációján keresztül történő konfigurálásával. Az olyan funkciók, mint például a pfsync segítségével és a CARP a feladatátvételhez és a redundancia, a munkamenet-hitelesítés Authpf, valamint az FTP-proxy a nehéz FTP protokoll tűzfalának megkönnyítéséhez, a PF-t is kiterjesztette. Röviden, a PF egy hatékony és sokoldalú tűzfal. 

## <a name="get-started"></a>Bevezetés
Ha szeretne egy biztonságos tűzfalat beállítani a felhőben a webkiszolgálók számára, akkor kezdjük. A Azure Resource Manager sablonban használt parancsfájlokat alkalmazhatja a hálózati topológia beállításához is.
A Azure Resource Manager sablon egy olyan FreeBSD-alapú virtuális gépet állít be, amely a PF-t és két virtuális gépet a telepített és konfigurált Nginx-webkiszolgálón keresztül hajtja végre a NAT-/Redirection. A NAT-ot a két webkiszolgáló kimenő forgalmán kívül a NAT/átirányítási virtuális gép elfogja a HTTP-kéréseket, és átirányítja őket a két webkiszolgálóra ciklikus multiplexelés esetén. A VNet a privát, nem irányítható IP-címtartomány 10.0.0.2/24-et használja, és módosíthatja a sablon paramétereit. A Azure Resource Manager sablon egy útválasztási táblázatot is definiál a teljes VNet, amely az Azure-beli alapértelmezett útvonalaknak a cél IP-cím alapján való felülbírálásához használt egyes útvonalak gyűjteménye. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Üzembe helyezés az Azure CLI-n keresztül
Az az [login](/cli/azure/reference-index)használatával telepítenie kell a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t, és be kell jelentkeznie egy Azure-fiókba. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy erőforráscsoport-nevet `myResourceGroup` a `West US` helyen.

```azurecli
az group create --name myResourceGroup --location westus
```

Ezután telepítse a [PF-FreeBSD-Setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) sablont az [az Group Deployment Create](/cli/azure/group/deployment)paranccsal. Töltse le az [azuredeploy. Parameters. JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) fájlt ugyanazon az elérési úton, és adja meg a saját erőforrás-értékeit, például `adminPassword`, `networkPrefix`és `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Körülbelül öt perc elteltével a rendszer a `"provisioningState": "Succeeded"`adatait fogja kapni. Ezt követően SSH-t használhat a frontend VM (NAT) vagy az Nginx webkiszolgáló eléréséhez egy böngészőben a virtuális gép (NAT) nyilvános IP-címét vagy teljes tartománynevét használva. Az alábbi példa felsorolja a `myResourceGroup` erőforráscsoporthoz tartozó előtéri virtuális géphez (NAT) hozzárendelt teljes tartománynevet és nyilvános IP-címet. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>További lépések
Be szeretné állítani saját NAT-adatait az Azure-ban? Nyílt forráskódú, ingyenes, de hatékony? Ezt követően a PF jó választás. A " [PF-FreeBSD-Setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup)" sablon használatával mindössze öt percet kell beállítania a NAT-tűzfal ciklikus időszeleteléses terheléselosztással történő beállításához az Azure-beli FreeBSD PF használatával a gyakori webkiszolgálói forgatókönyvhöz. 

Ha szeretné megismerni a FreeBSD Azure-beli ajánlatát, tekintse meg az Azure-beli [FreeBSD bemutatása](freebsd-intro-on-azure.md)című témakört.

Ha többet szeretne megtudni a PF-ről, tekintse meg a [FreeBSD kézikönyvét](https://www.freebsd.org/doc/handbook/firewalls-pf.html) vagy a [PF-User útmutatóját](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
