---
title: Linux Virtual Machines újbóli üzembe helyezése az Azure-ban | Microsoft Docs
description: Linux rendszerű virtuális gépek újratelepítése az Azure-ban az SSH-kapcsolódási problémák enyhítése érdekében.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2c4152ec5e67761ce9be9a81bfbf39e1591787da
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913514"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Linuxos virtuális gép újratelepítése új Azure-csomópontra
Ha problémákba ütközik az SSH-val vagy az Azure-beli linuxos virtuális géppel való alkalmazással kapcsolatos problémák elhárítása során, akkor a virtuális gép újbóli üzembe helyezése segíthet. Egy virtuális gép újratelepítésekor a virtuális gépet az Azure-infrastruktúra egy új csomópontjára helyezi át, majd visszakapcsolja azt. A rendszer megőrzi a konfigurációs beállításokat és a kapcsolódó erőforrásokat. Ez a cikk bemutatja, hogyan telepíthet újra egy virtuális gépet az Azure CLI vagy a Azure Portal használatával.

> [!NOTE]
> A virtuális gép újbóli üzembe helyezése után az ideiglenes lemez elvész, és a rendszer frissíti a virtuális hálózati adapterhez társított dinamikus IP-címeket. 


## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata
Telepítse a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t, és jelentkezzen be az Azure-fiókjába az [az login](/cli/azure/reference-index)használatával.

Telepítse újra a virtuális gépet az [az VM redeploy](/cli/azure/vm)paranccsal. A következő példa újratelepíti a *myVM* nevű virtuális gépet a *myResourceGroup*nevű erőforráscsoport-csoportba:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>A klasszikus Azure parancssori felület használata

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Telepítse a [legújabb Azure-beli klasszikus CLI](../../cli-install-nodejs.md) -t, és jelentkezzen be az Azure-fiókjába. Győződjön meg arról, hogy Resource Manager módban van (`azure config mode arm`).

A következő példa újratelepíti a *myVM* nevű virtuális gépet a *myResourceGroup*nevű erőforráscsoport-csoportba:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Következő lépések
Ha problémába ütközik a virtuális géphez való csatlakozással kapcsolatban, az [SSH-kapcsolatok hibaelhárításával](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) és az [SSH-hibaelhárítás részletes lépéseivel](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)kapcsolatban talál konkrét segítséget. Ha nem fér hozzá a virtuális gépen futó alkalmazáshoz, az [alkalmazások hibaelhárításával kapcsolatos problémákat](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)is elolvashatja.


