---
title: Azure PowerShell-példaszkript – Forgalom irányítása hálózati virtuális készüléken keresztül | Microsoft Docs
description: Azure PowerShell-példaszkript – Forgalom irányítása hálózati virtuális készüléken keresztül.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: kumud
ms.openlocfilehash: b31714e62ba0e7a0e4882d9b9fa830b3a14fe855
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888512"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Forgalom irányítása hálózati virtuális készüléken keresztül

Ez a példaszkript előtérbeli és háttérbeli alhálózattal rendelkező virtuális hálózatot hoz létre. Ezen kívül létrehoz egy virtuális gépet, amelyen az IP-továbbítás két alhálózat közötti útválasztása engedélyezve van. A szkript futtatása után hálózati szoftvereket, például tűzfal-alkalmazást telepíthet a virtuális gépre.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```
## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript az alábbi parancsokkal létrehoz egy erőforráscsoportot, egy virtuális hálózatot és hálózati biztonsági csoportokat. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehoz egy Azure-beli virtuális hálózatot és előtérbeli alhálózatot. |
| [Új – AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Háttérbeli és DMZ-alhálózatokat hoz létre. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Létrehoz egy nyilvános IP-címet a virtuális gép internetről való eléréséhez. |
| [Új – AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Virtuális hálózati adatpert hoz létre, és engedélyezi hozzá az IP-továbbítást. |
| [Új – AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Egy hálózati biztonsági csoportot (NSG) hoz létre. |
| [Új – AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | NSG-szabályokat hoz létre, amelyek engedélyezik a virtuális gép bejövő HTTP- és HTTPS-portjait. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)| Az alhálózatokhoz rendeli az NSG-ket és az útválasztási táblázatokat. |
| [Új – AzRouteTable](/powershell/module/az.network/new-azroutetable)| Útválasztási táblázatot hoz létre minden útvonalhoz. |
| [Új – AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)| Útvonalakat hoz létre az alhálózatok és az Internet közötti forgalom útválasztásához a virtuális gépen keresztül. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Létrehoz egy virtuális gépet, és csatolja hozzá a NIC-t. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai hitelesítő adatokat. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)  | Töröl egy erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/azure/overview).

További hálózatkezelési PowerShell-példaszkripteket az [Azure-hálózatkezelés áttekintő dokumentációjában](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) találhat.
