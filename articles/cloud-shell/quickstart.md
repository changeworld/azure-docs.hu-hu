---
title: Azure Cloud Shell rövid útmutató – bash
description: Megtudhatja, hogyan használhatja a bash parancssort a böngészőben a Azure Cloud Shell használatával.
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 574841b3a89385a3b8bf048d5ed36f40fac99a83
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252128"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>A bash gyors üzembe helyezése Azure Cloud Shell

Ez a dokumentum részletesen ismerteti, hogyan használható a bash a [Azure Portal](https://ms.portal.azure.com/)Azure Cloud Shellban.

> [!NOTE]
> Azure Cloud Shell rövid útmutatóban is elérhető egy [PowerShell](quickstart-powershell.md) .

## <a name="start-cloud-shell"></a>Kezdés Cloud Shell
1. **Cloud Shell** elindítása a Azure Portal legfelső navigációs sávján. <br>
![](media/quickstart/shell-icon.png)

2. Válasszon egy előfizetést, és hozzon létre egy Storage-fiókot, és Microsoft Azure a fájlok megosztását.
3. Válassza a "tároló létrehozása" lehetőséget.

> [!TIP]
> A rendszer minden munkamenetben automatikusan hitelesíti az Azure CLI-t.

### <a name="select-the-bash-environment"></a>Bash-környezet kiválasztása
Győződjön meg arról, hogy a környezet legördülő lista a rendszerhéj ablak bal oldalán `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Előfizetés beállítása
1. Azon előfizetések listázása, amelyekhez hozzáférése van.
   ```azurecli-interactive
   az account list
   ```

2. Az előnyben részesített előfizetés beállítása: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Az előfizetést a későbbi, `/home/<user>/.azure/azureProfile.json`t használó munkamenetek esetében emlékezni fogjuk.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy új erőforráscsoportot a "MyRG" nevű WestUS.
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Linux rendszerű virtuális gép létrehozása
Hozzon létre egy Ubuntu virtuális gépet az új erőforráscsoporthoz. Az Azure CLI létrehozza az SSH-kulcsokat, és beállítja velük a virtuális gépet. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> A `--generate-ssh-keys` használata arra utasítja az Azure CLI-t, hogy nyilvános és titkos kulcsokat hozzon létre és állítson be a virtuális gépen, és `$Home` könyvtárat. Alapértelmezés szerint a kulcsok a `/home/<user>/.ssh/id_rsa` és a `/home/<user>/.ssh/id_rsa.pub`Cloud Shell kerülnek. A `.ssh` mappát a csatolt fájlmegosztás 5 GB-os rendszerképe őrzi meg, amely a `$Home`megőrzésére szolgál.

A virtuális gépen lévő felhasználóneve a Cloud Shell ($User@Azure:) által használt Felhasználónév.

### <a name="ssh-into-your-linux-vm"></a>SSH-t a linuxos virtuális gépre
1. Keresse meg a virtuális gép nevét a Azure Portal keresési sávjában.
2. Kattintson a "kapcsolódás" gombra a virtuális gép nevének és nyilvános IP-címének lekéréséhez. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH-t a virtuális gépre a `ssh` cmd fájllal.
   ```
   ssh username@ipaddress
   ```

Az SSH-kapcsolatok létrehozásakor az Ubuntu Welcome promptot kell látnia. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Takarítás 
1. Lépjen ki az SSH-munkamenetből.
   ```azurecli-interactive
   exit
   ```

2. Törölje az erőforráscsoportot és a benne található összes erőforrást.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Következő lépések
[Tudnivalók a bash-fájlok megőrzéséről Cloud Shell](persisting-shell-storage.md) <br>
[További tudnivalók az Azure CLI-ről](https://docs.microsoft.com/cli/azure/) <br>
[Tudnivalók a Azure Files Storage szolgáltatásról](../storage/files/storage-files-introduction.md) <br>
