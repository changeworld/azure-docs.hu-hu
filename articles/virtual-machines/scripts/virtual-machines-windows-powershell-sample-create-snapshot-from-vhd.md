---
title: Pillanatkép létrehozása virtuális merevlemezről több azonos felügyelt lemez létrehozásához – PowerShell-minta
description: Azure PowerShell-példaszkript – Pillanatkép létrehozása virtuális merevlemezről több azonos felügyelt lemez gyors létrehozásához
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: de89ff77d09124449b64f664c60c72f870319b93
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613805"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Pillanatkép létrehozása virtuális merevlemezről több, egymással azonos felügyelt lemez gyors létrehozásához a PowerShell segítségével

Ez a szkript létrehoz egy pillanatképet egy VHD-fájlról egy azonos vagy eltérő előfizetéshez tartozó tárfiókban. A szkript segítségével importálhat egy speciális (nem általánosított vagy syspreppel előkészített) virtuális merevlemezt egy pillanatképbe, majd a pillanatképpel létrehozhat több, egymással azonos felügyelt lemezt rövid idő alatt. Emellett használhatja arra is, hogy importál egy adatokat tartalmazó virtuális merevlemezt egy pillanatképbe, majd a pillanatképpel létrehoz több felügyelt lemez rövid idő alatt. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>Következő lépések

[Felügyelt lemez létrehozása pillanatképből](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Virtuális gép létrehozása felügyelt lemez rendszerlemezként való csatlakoztatásával](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
