---
title: PowerShell-parancsfájl Azure Cosmos-fiók frissítéséhez
description: Azure PowerShell parancsfájl-minta – Azure Cosmos-fiók frissítése vagy régiók módosítása
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: 075c33b0818aa3ec8b16158f538ae302446ff5f7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445023"
---
# <a name="update-an-azure-cosmos-account-or-modify-regions-using-powershell"></a>Azure Cosmos-fiók frissítése vagy régiók módosítása a PowerShell használatával

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

> [!NOTE]
> A régiók nem módosíthatók, és más Cosmos-fiókok tulajdonságai nem módosíthatók ugyanabban a műveletben. Ezeket két külön műveletnek kell elvégeznie.
> [!NOTE]
> Ez a példa egy SQL-(Core-) API-fiók használatát mutatja be. Ha más API-khoz szeretné használni ezt a mintát, másolja a kapcsolódó tulajdonságokat, és alkalmazza az API-specifikus parancsfájlra.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update.ps1 "Add a region to an Azure Cosmos account")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
|**Azure-erőforrások**| |
| [Új – AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Hozzon létre egy erőforrást. |
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | Erőforrás frissítése. |
|**Azure-erőforráscsoportok**| |
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.
