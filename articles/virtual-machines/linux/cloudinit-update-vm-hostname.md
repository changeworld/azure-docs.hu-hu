---
title: A Cloud-init használatával állítsa be a Linux rendszerű virtuális gép állomásnevét
description: Linux rendszerű virtuális gép testreszabása a Cloud-init használatával az Azure CLI-vel
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 631b8ef83d5fbf10ec401df7432b23238f2ae2e6
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969172"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Az Azure-beli Linux rendszerű virtuális gép állomásnévének beállítása a Cloud-init használatával
Ez a cikk bemutatja, hogyan lehet a [Cloud-init](https://cloudinit.readthedocs.io) használatával konfigurálni egy adott állomásnevet egy virtuális GÉPEN (VM) vagy virtuálisgép-méretezési csoportokon (VMSS) az Azure üzembe helyezési ideje alatt. Ezek a felhő-init parancsfájlok az első rendszerindítás során futnak az Azure-beli erőforrások kiépítés után. További információ arról, hogyan működik a Cloud-init natív módon az Azure-ban és a támogatott Linux-disztribúciókban: a [Cloud-init áttekintése](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Az állomásnév beállítása a Cloud-init használatával
Alapértelmezés szerint az állomásnév ugyanaz, mint a virtuális gép neve, amikor új virtuális gépet hoz létre az Azure-ban.  Ha az [az VM Create](/cli/azure/vm)paranccsal hoz létre egy virtuális gépet az Azure-ban az az VM Create paranccsal, a Cloud-init parancsfájl futtatásával állítsa be a Cloud-init fájlt a `--custom-data` kapcsolóval.  

A frissítési folyamat működés közbeni megtekintéséhez hozzon létre egy fájlt a *cloud_init_hostname. txt* nevű aktuális rendszerhéjban, és illessze be a következő konfigurációt. Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_hostname.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. A **Nano** Editor használatához válassza a #1 lehetőséget. Győződjön meg arról, hogy a teljes Cloud-init fájl megfelelően van másolva, különösen az első sorban.  

```yaml
#cloud-config
hostname: myhostname
```

A rendszerkép telepítése előtt létre kell hoznia egy erőforráscsoportot az az [Group Create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális gépet az [az VM Create](/cli/azure/vm) paranccsal, és határozza meg a Cloud-init fájlt `--custom-data cloud_init_hostname.txt` a következőképpen:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

A létrehozást követően az Azure CLI a virtuális géppel kapcsolatos információkat jeleníti meg. Használja a `publicIpAddress` SSH-val a virtuális gépre. Adja meg a saját lakcímét a következő módon:

```bash
ssh <publicIpAddress>
```

A virtuális gép nevének megtekintéséhez használja a `hostname` parancsot az alábbiak szerint:

```bash
hostname
```

A virtuális gépnek a Cloud-init fájlban megadott értékként jelentenie kell a gazdagépet, ahogy az a következő példában látható:

```bash
myhostname
```

## <a name="next-steps"></a>Következő lépések
További felhő-inicializálási példákat a konfiguráció változásairól a következő témakörben talál:
 
- [További linuxos felhasználó hozzáadása egy virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása a meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [Virtuális gép helyi gazdagépének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok behelyezése](tutorial-automate-vm-deployment.md)
