---
title: Azure PowerShell-példaszkript – Virtuális gép hálózati forgalmának szűrése | Microsoft Docs
description: Azure PowerShell-példaszkript – Virtuális gép kimenő és bejövő hálózati forgalmának szűrése.
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
ms.openlocfilehash: 46b368220a320679e93024f592d980f2b756c3a2
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890200"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>A bejövő és kimenő virtuális gép hálózati forgalmának szűrése

Ez a példaszkript előtérbeli és háttérbeli alhálózattal rendelkező virtuális hálózatot hoz létre. Az előtér-alhálózatra irányuló bejövő hálózati forgalom a HTTP és a HTTPS értékre van korlátozva, míg a háttérbeli alhálózatról az internetre irányuló kimenő forgalom nem engedélyezett. A szkript futtatása után egyetlen, két NIC-vel rendelkező virtuális gép fog a rendelkezésére állni. Mindegyik NIC eltérő alhálózathoz csatlakozik.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-network/filter-network-traffic/filter-network-traffic.ps1  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript az alábbi parancsokkal létrehoz egy erőforráscsoportot, egy virtuális hálózatot és hálózati biztonsági csoportokat. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Alhálózati konfigurációs objektumot hoz létre. |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehoz egy Azure-beli virtuális hálózatot és előtérbeli alhálózatot. |
| [Új – AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Hálózati biztonsági csoporthoz rendelendő biztonsági szabályokat hoz létre. |
| [Új – AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |NSG-szabályokat hoz létre, amelyek engedélyeznek vagy blokkolnak adott alhálózatokra mutató bizonyos portokat. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Az NSG-ket alhálózatokhoz társítja. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Létrehoz egy nyilvános IP-címet a virtuális gép internetről való eléréséhez. |
| [Új – AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Virtuális hálózati adaptereket hoz létre, és a virtuális hálózat előtérbeli és háttérbeli alhálózataihoz csatolja őket. |
| [Új – AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Virtuális gépet hoz létre. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/azure/overview).

További hálózatkezelési PowerShell-példaszkripteket az [Azure-hálózatkezelés áttekintő dokumentációjában](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) találhat.
