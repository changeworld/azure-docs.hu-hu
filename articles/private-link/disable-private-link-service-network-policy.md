---
title: 'Az Azure Private link Service forrás IP-címéhez tartozó hálózati házirendek letiltása '
description: Ismerje meg, hogyan tilthatja le a hálózati házirendeket az Azure Private linkhez
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452994"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Hálózati házirendek letiltása a Private link Service forrás IP-címéhez

A privát kapcsolati szolgáltatás forrás IP-címének kiválasztásához az alhálózaton explicit letiltási beállítást `privateLinkServiceNetworkPolicies` szükséges. Ez a beállítás csak arra a privát IP-címére érvényes, amelyet a privát kapcsolati szolgáltatás forrás IP-címeként választott. Az alhálózatban található egyéb erőforrásokhoz a hozzáférés a hálózati biztonsági csoportok (NSG) biztonsági szabályok definíciója alapján van szabályozva. 
 
Ha bármely Azure-ügyfelet (PowerShell, CLI vagy sablonok) használ, további lépésekre van szükség a tulajdonság módosításához. A szabályzatot letilthatja a Cloud shellrel Azure Portal vagy Azure PowerShell helyi telepítése, az Azure CLI vagy a Azure Resource Manager sablonok használatával.  
 
Az alábbi lépések végrehajtásával tiltsa le a *myVirtualNetwork* nevű virtuális hálózat magánhálózati kapcsolati szolgáltatásának hálózati házirendjeit egy *myResourceGroup*nevű erőforráscsoport *alapértelmezett* alhálózatával. 

## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Ez a szakasz azt ismerteti, hogyan lehet letiltani az alhálózat magánhálózati végpont-házirendjeit a Azure PowerShell használatával.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Ez a szakasz azt ismerteti, hogyan lehet letiltani az alhálózati végpont-házirendeket az Azure CLI-vel.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Sablon használata
Ez a szakasz azt ismerteti, hogyan lehet letiltani az alhálózati végpont-házirendeket Azure Resource Manager sablon használatával.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Következő lépések
- További információ az [Azure Private-végpontról](private-endpoint-overview.md)
 
