---
title: Privát Azure Kubernetes Service-fürt létrehozása
description: Ismerje meg, hogyan hozhat létre egy privát Azure Kubernetes Service-(ak-) fürtöt
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: b8b4f8062d9f60648e22ab4eb0be78eb47159834
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79205172"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Privát Azure Kubernetes Service-fürt létrehozása

Egy privát fürtben a vezérlő síkja vagy az API-kiszolgáló belső IP-címekkel rendelkezik, amelyek a [magánhálózati internetes dokumentumok RFC1918](https://tools.ietf.org/html/rfc1918) vannak meghatározva. Privát fürt használatával biztosíthatja, hogy az API-kiszolgáló és a csomópont-készletek közötti hálózati forgalom csak a magánhálózaton maradjon.

A vezérlő síkja vagy az API-kiszolgáló egy Azure Kubernetes szolgáltatásban (ak) felügyelt Azure-előfizetésben található. Az ügyfél fürtje vagy csomópont-készlete az ügyfél előfizetésében található. A kiszolgáló és a fürt vagy a csomópont-készlet képes kommunikálni egymással az API-kiszolgáló virtuális hálózatának [Azure Private link szolgáltatásával][private-link-service] , valamint egy olyan privát végponttal, amely az ügyfél AK-fürt alhálózatán van kitéve.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure CLI 2.2.0 vagy újabb verziója

## <a name="create-a-private-aks-cluster"></a>Privát AK-fürt létrehozása

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot, vagy használjon egy meglévő erőforráscsoportot az AK-fürthöz.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Alapértelmezett alapszintű hálózatkezelés 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Where *--enable-Private-cluster* kötelező jelző egy privát fürthöz. 

### <a name="advanced-networking"></a>Speciális hálózatkezelés  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Where *--enable-Private-cluster* kötelező jelző egy privát fürthöz. 

> [!NOTE]
> Ha a Docker-híd CIDR (172.17.0.1/16) ütközne az alhálózati CIDR, módosítsa a Docker-híd megfelelőjét.

## <a name="options-for-connecting-to-the-private-cluster"></a>A privát fürthöz való csatlakozás lehetőségei

Az API-kiszolgáló végpontjának nincs nyilvános IP-címe. Az API-kiszolgáló kezeléséhez olyan virtuális gépet kell használnia, amely hozzáféréssel rendelkezik az AK-fürt Azure-Virtual Networkához (VNet). Több lehetőség is van a magánhálózati kapcsolat létrehozására a privát fürthöz.

* Hozzon létre egy virtuális gépet ugyanabba az Azure-Virtual Networkba (VNet), mint az AK-fürtöt.
* Használjon különálló hálózatban található virtuális GÉPET, és állítsa be a [virtuális hálózatok][virtual-network-peering]közötti társítást.  Erről a lehetőségről az alábbi szakaszban talál további információt.
* [Express Route-vagy VPN-][express-route-or-VPN] kapcsolat használata.

A legegyszerűbb lehetőség a virtuális gép létrehozása ugyanabban a VNET, mint az AK-fürt.  Az expressz útvonal és a VPN-EK növelik a költségeket és további hálózati bonyolultságot igényelnek.  A virtuális hálózat társításához meg kell terveznie a hálózati CIDR-tartományokat, hogy ne legyenek átfedésben lévő tartományok.

## <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

Ahogy említettük, a VNet-társítás az egyik módja a privát fürt elérésének. A VNet-társítás használatához létre kell hoznia egy kapcsolatot a virtuális hálózat és a magánhálózati DNS-zóna között.
    
1. Lépjen a Azure Portal MC_ * erőforráscsoporthoz.  
2. Válassza ki a magánhálózati DNS-zónát.   
3. A bal oldali ablaktáblán válassza ki a **virtuális hálózati** kapcsolatot.  
4. Hozzon létre egy új hivatkozást, amely hozzáadja a virtuális gép virtuális hálózatát a magánhálózati DNS-zónához. Néhány percet vesz igénybe, amíg a DNS-zóna hivatkozása elérhetővé válik.  
5. Térjen vissza a Azure Portal MC_ * erőforráscsoporthoz.  
6. A jobb oldali ablaktáblában válassza ki a virtuális hálózatot. A virtuális hálózat neve: *AK-vnet-\** .  
7. A bal oldali ablaktáblán válassza **a**társítások lehetőséget.  
8. Válassza a **Hozzáadás**lehetőséget, adja hozzá a virtuális gép virtuális hálózatát, majd hozza létre a társítást.  
9. Nyissa meg a virtuális hálózatot, ahol a virtuális gép rendelkezik **, válassza a társítások lehetőséget,** válassza ki az AK-beli virtuális hálózatot, majd hozza létre a társítást. Ha a címtartomány az AK-beli virtuális hálózaton és a virtuális gép virtuális hálózatának összevonásán alapul, a társítás sikertelen lesz. További információ: [Virtual Network peering][virtual-network-peering].

## <a name="dependencies"></a>Függőségek  

* A Private link Service csak a standard Azure Load Balancer esetén támogatott. Az alapszintű Azure Load Balancer nem támogatott.  
* Ha egyéni DNS-kiszolgálót szeretne használni, telepítsen egy DNS-t használó AD-kiszolgálót az IP-168.63.129.16 való továbbításhoz

## <a name="limitations"></a>Korlátozások 
* Az IP-címekre jogosult tartományok nem alkalmazhatók a privát API-kiszolgálói végpontra, csak a nyilvános API-kiszolgálóra érvényesek.
* A Availability Zones jelenleg bizonyos régiókban támogatott, tekintse meg a dokumentum elejét 
* Az [Azure Private link Service korlátozásai][private-link-service] a privát fürtökre, az Azure privát végpontokra és a virtuális hálózati szolgáltatás-végpontokra vonatkoznak, amelyek jelenleg nem támogatottak ugyanabban a virtuális hálózatban.
* A privát fürtben lévő virtuális csomópontok nem támogatják a privát Azure-beli virtuális hálózatokon lévő Azure Container Instances (ACI) használatát
* Nem támogatott az Azure DevOps integrációja a privát fürtökkel rendelkező dobozból
* Azon ügyfelek számára, akik számára engedélyezni kell a Azure Container Registryt a privát AK-val való munkavégzéshez, az Container Registry virtuális hálózatot az ügynök-fürt virtuális hálózatának kell megadnia.
* Nincs aktuális támogatás az Azure dev Spaces szolgáltatáshoz
* Meglévő AK-fürtök privát fürtökre való konvertálása nem támogatott
* Ha törli vagy módosítja a magánhálózati végpontot az ügyfél alhálózatán, a fürt működése leáll. 
* A tárolók élő adatAzure Monitor jelenleg nem támogatottak.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

