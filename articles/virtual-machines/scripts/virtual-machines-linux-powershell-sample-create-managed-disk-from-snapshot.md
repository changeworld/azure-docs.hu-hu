---
title: Felügyelt lemez létrehozása pillanatkép-PowerShell-minta alapján
description: Azure PowerShell-példaszkript – Felügyelt lemez létrehozása pillanatképből
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 20a03c981aa3e7d7b3afc3e4a493c0285ebc79d1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463692"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>Felügyelt lemez létrehozása pillanatképből a PowerShell használatával

Ez a szkript egy felügyelt lemezt hoz létre egy pillanatképből. A használatával virtuális gépeket állíthat vissza operációsrendszer- és adatlemezek pillanatképeiből. A megfelelő pillanatképekből létrehozhatja operációs rendszerek és adatok felügyelt lemezeit, majd a felügyelt lemezek csatolásával létrehozhat egy új virtuális gépet. Meglévő virtuális gépek adatlemezeit is visszaállíthatja pillanatképekből létrehozott adatlemezek csatolásával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja arra, hogy létrehozzon egy felügyelt lemezt egy pillanatképből. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/Get-AzSnapshot) | Beolvassa a pillanatkép tulajdonságait.  |
| [Új – AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Létrehoz egy lemezkonfigurációt lemezlétrehozáshoz. Tartalmazza a szülőpillanatkép erőforrás-azonosítóját, a helyet (mely azonos a szülőpillanatkép helyével) és a tárolás típusát.  |
| [Új – AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Létrehoz egy lemezt a paraméterként megadott lemezkonfiguráció, lemeznév és erőforráscsoport-név alapján. |

## <a name="next-steps"></a>Következő lépések


[Virtuális gép létrehozása felügyelt lemezből](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
