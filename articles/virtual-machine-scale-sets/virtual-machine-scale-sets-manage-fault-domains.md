---
title: Tartalék tartományok kezelése az Azure-beli virtuálisgép-méretezési csoportokban
description: Megtudhatja, hogyan választhatja ki a megfelelő számú tartalék a virtuálisgép-méretezési csoport létrehozásakor.
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275724"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoporthoz tartozó tartalék tartományok megfelelő számának kiválasztása
A virtuálisgép-méretezési csoportok alapértelmezés szerint öt tartalék tartománnyal jönnek létre a zónák nélküli Azure-régiókban. A virtuálisgép-méretezési csoportok zónákra épülő telepítését támogató régiók esetén a tartalék tartomány alapértelmezett értéke 1 az egyes zónák esetében. Ebben az esetben az FD = 1 érték azt jelenti, hogy a méretezési csoportba tartozó virtuálisgép-példányok számos rackben lesznek elosztva a legjobb erőfeszítés alapján.

Azt is megteheti, hogy összehangolja a méretezési csoport tartalék tartományának számát Managed Disks tartalék tartományok számával. Ez az igazítás segíthet megakadályozni a kvórum elvesztését, ha egy teljes Managed Disks tartalék tartomány leáll. Az FD-szám beállítható úgy, hogy az egyes régiókban elérhető Managed Disks tartalék tartományok száma kisebb vagy egyenlő legyen. Tekintse át ezt a [dokumentumot](../virtual-machines/windows/manage-availability.md) , és ismerkedjen meg az Managed Disks a tartalék tartományok régiónként való számával.

## <a name="rest-api"></a>REST API
Beállíthatja a tulajdonságot `properties.platformFaultDomainCount` 1, 2 vagy 3 értékre (ha nincs megadva az alapértelmezett 5). Tekintse [meg REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)dokumentációját.

## <a name="azure-cli"></a>Azure parancssori felület (CLI)
A (z) `--platform-fault-domain-count` paramétert beállíthatja 1, 2 vagy 3 értékre (ha nincs megadva az alapértelmezett 5). Tekintse meg az Azure CLI dokumentációját [itt](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.

## <a name="next-steps"></a>Következő lépések
- További információ az Azure-környezetek [rendelkezésre állásával és redundanciával kapcsolatos funkcióival](../virtual-machines/windows/availability.md) kapcsolatban.
