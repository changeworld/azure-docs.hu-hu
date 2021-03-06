---
title: Pillanatkép másolása egy másik régióban lévő Storage-fiókba – CLI-minta
description: Azure CLI-parancsfájl minta – Pillanatkép exportálása/másolása virtuális merevlemezként ugyanazon vagy eltérő régióban lévő Storage-fiókba.
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 05868682708ee2f064addf698508b5e178424963
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75375865"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>Pillanatkép exportálása/másolása egy másik régióban lévő Storage-fiókba a CLI használatával

Ez a szkript egy felügyelt pillanatképet exportál egy másik régió tárfiókjába. Először létrehozza a pillanatkép SAS URI-azonosítóját, majd annak használatával átmásolja a pillanatképet egy másik régióban lévő tárfiókba. A szkript használatával vészhelyreállítási célokból eltérő régiókban készíthet biztonsági másolatokat a felügyelt lemezeiről.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy felügyelt pillanatkép SAS URI-azonosítójának létrehozásához, majd a pillanatkép a SAS URI használatával egy tárfiókba való másolásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot) | Létrehoz egy írásvédett SAS-t, amelynek használatával a mögöttes VHD-fájl átmásolható egy tárfiókba, vagy letölthető a helyszíni rendszerre.  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Aszinkron módon átmásol egy blobot egy tárfiókból egy másikba. |

## <a name="next-steps"></a>Következő lépések

[Felügyelt lemez létrehozása virtuális merevlemezből](virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További virtuális gépek és felügyelt lemezek a CLI-parancsfájlok az [Azure Windows VM dokumentációjában](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)találhatók.
