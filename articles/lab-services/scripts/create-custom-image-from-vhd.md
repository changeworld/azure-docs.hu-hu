---
title: PowerShell – egyéni rendszerkép létrehozása a VHD-fájlból Azure Lab Services
description: Ez a PowerShell-szkript létrehoz egy egyéni rendszerképet egy VHD-fájlból Azure Lab Services.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 38383462a665ced1ccb6c6a2f062fab0492eee9a
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169981"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Egyéni rendszerkép létrehozása egy VHD-fájlból a PowerShell használatával Azure Lab Services

Ez a minta PowerShell-parancsfájl létrehoz egy egyéni rendszerképet egy VHD-fájlból Azure Lab Services

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor**. A parancsfájlhoz meglévő labor szükséges. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Erőforrások beolvasása. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Beszerzi egy Azure Storage-fiók hozzáférési kulcsait. |
| [Új – AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) | Azure-beli telepítést hoz létre egy erőforráscsoporthoz. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell-szkriptek is találhatók a [Azure Lab Services PowerShell-mintákban](../samples-powershell.md).
