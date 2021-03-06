---
title: Karbantartási értesítések beszerzése a parancssori felület használatával
description: Megtekintheti az Azure-ban futó virtuális gépek karbantartási értesítéseit, és megkezdheti az önkiszolgáló karbantartást az Azure CLI használatával.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4ad57c1c71a51f948bd405a5487a1e27e36bfff7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920892"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Tervezett karbantartási értesítések kezeléséhez az Azure CLI használatával

**Ez a cikk a Linux és Windows rendszerű virtuális gépekre is vonatkozik.**

A CLI használatával megtekintheti, hogy mikor ütemezik a virtuális gépek [karbantartását](maintenance-notifications.md). A tervezett karbantartási információk az [az VM Get-instance-View](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view)címről érhetők el.
 
A karbantartási adatokat csak akkor adja vissza a rendszer, ha karbantartási terv van. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Karbantartás indítása

A következő hívás elindítja a karbantartást egy virtuális gépen, ha `IsCustomerInitiatedMaintenanceAllowed` True (igaz) értékre van állítva.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klasszikus üzembe helyezések

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Ha továbbra is a klasszikus üzembe helyezési modellel telepített örökölt virtuális gépekkel rendelkezik, a klasszikus Azure CLI használatával lekérdezheti a virtuális gépeket, és megkezdheti a karbantartást.

Győződjön meg arról, hogy a megfelelő módban van, hogy a klasszikus virtuális gép használatával működjön a következő beírásával:

```
azure config mode asm
```

Egy *myVM*nevű virtuális gép karbantartási állapotának lekéréséhez írja be a következőt:

```
azure vm show myVM 
``` 

A *myVM* nevű klasszikus virtuális gép karbantartásának megkezdéséhez a *myService* szolgáltatásban és a *myDeployment* -telepítésben írja be a következőt:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Következő lépések

A tervezett karbantartást a [Azure PowerShell](maintenance-notifications-powershell.md) vagy a [portál](maintenance-notifications-portal.md)használatával is kezelheti.
