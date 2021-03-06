---
title: Azure-fájlmegosztás biztonsági mentése az Azure CLI-vel
description: Ismerje meg, hogyan használhatja az Azure-fájlmegosztás biztonsági mentését az Azure CLI-vel az Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844041"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Azure-fájlmegosztás biztonsági mentése a CLI-vel

Az Azure parancssori felülete (CLI) parancssori felületet biztosít az Azure-erőforrások kezeléséhez. Ez nagyszerű eszköz az Azure-erőforrások használatára szolgáló egyéni automatizálás kialakításához. Ez a cikk az Azure-fájlmegosztás Azure CLI-vel történő biztonsági mentésének részleteit ismerteti. Az [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) vagy az [Azure Portal](backup-afs.md) használatával is elvégezheti ezeket a lépéseket.

Az oktatóanyag végén megtudhatja, hogyan hajthatja végre az alábbi műveleteket az Azure CLI használatával:

* Recovery Services-tároló létrehozása
* Azure-fájlmegosztás biztonsági mentésének engedélyezése
* Igény szerinti biztonsági mentés indítása fájlmegosztás esetén

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A parancssori felület helyi telepítéséhez és használatához az Azure CLI 2.0.18-as vagy újabb verzióját kell futtatnia. A CLI verziójának megkereséséhez `run az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Service-tároló egy olyan entitás, amely összevont nézetet és felügyeleti képességet biztosít az összes biztonsági mentési elemhez. Amikor egy védett erőforrás biztonsági mentésének feladata fut, a rendszer egy helyreállítási pontot hoz létre a Recovery Services-tárolóban. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

A Recovery Services-tároló létrehozásához kövesse az alábbi lépéseket:

1. Egy tároló egy erőforráscsoporthoz kerül. Ha nem rendelkezik meglévő erőforráscsoporthoz, hozzon létre egy újat az [az Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal. Ebben az oktatóanyagban az új erőforráscsoport- *azurefiles* hozunk létre az USA keleti régiójában.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. A tároló létrehozásához használja az az [Backup Vault Create](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) parancsmagot. Azonos helyet kell megadnia a tárolóhoz az erőforráscsoport esetében.

    A következő példa egy *azurefilesvault* nevű Recovery Services-tárolót hoz létre az USA keleti régiójában.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Adja meg a tároló tárolásához használni kívánt redundancia típusát. [Helyileg redundáns tárolást](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) vagy [földrajzilag redundáns tárolást](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)használhat.

    A következő példa a tárolási redundancia beállítást állítja be a **Georedundant** *azurefilesvault* az az [Backup Vault Backup-Properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) parancsmag használatával.

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Annak vizsgálatához, hogy a tároló sikeresen létrejött-e, az az [Backup Vault show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) parancsmaggal kérheti le a tár részleteit. A következő példa a fenti lépésekben létrehozott *azurefilesvault* részleteit jeleníti meg.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    A kimenet a következő válaszhoz hasonló lesz:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Azure-fájlmegosztás biztonsági mentésének engedélyezése

Ez a szakasz azt feltételezi, hogy már rendelkezik egy Azure-fájlmegosztás, amelyhez biztonsági mentést kíván beállítani. Ha még nem rendelkezik ilyennel, hozzon létre egy Azure-fájlmegosztást az az [Storage Share Create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) paranccsal.

A fájlmegosztás biztonsági mentésének engedélyezéséhez létre kell hoznia egy védelmi szabályzatot, amely meghatározza, hogy mikor fusson a biztonsági mentési feladatok, és hogy mennyi ideig tárolja a rendszer a helyreállítási pontokat. Biztonsági mentési szabályzatot az az [Backup Policy Create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) parancsmag használatával hozhat létre.

A következő példa az az [Backup Protection Enable-for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) parancsmagot használja a *azurefiles* -fájlmegosztás biztonsági mentésének engedélyezéséhez a *afsaccount* Storage-fiókban az *1. ütemezett* biztonsági mentési szabályzat használatával:

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

A kimenetben található **Name** attribútum a Backup szolgáltatás által a **biztonsági mentés engedélyezése** művelethez létrehozott feladatokhoz tartozó névvel egyezik. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmagot.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Igény szerinti biztonsági mentés indítása a fájlmegosztás számára

Ha egy igény szerinti biztonsági mentést szeretne elindítani a fájlmegosztás számára ahelyett, hogy a biztonsági mentési szabályzatot a ütemezett időpontban futtatni szeretné, használja az az [Backup Protection Backup-Now](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) parancsmagot.

Az igény szerinti biztonsági mentés elindításához a következő paramétereket kell megadnia:

* **--Container-Name** a fájlmegosztást üzemeltető Storage-fiók neve. A tároló **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Container List](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.
* **--Item-Name** annak a fájlmegosztásnak a neve, amelynek el szeretné indítani az igény szerinti biztonsági mentést. A biztonsági másolatban szereplő elem **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) parancsot.
* **--megtartás – addig** , amíg meg nem adja a helyreállítási pont megőrzésének dátumát. Az értéket UTC formátumban kell megadni (nn-hh-éééé).

Az alábbi példa egy igény szerinti biztonsági mentést indít el a *afsaccount* -fájlmegosztás lévő *azuresfiles* -tárolóban, *20-01-2020*-ig megőrzéssel.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

A kimenet **Name** attribútuma a Backup szolgáltatás által a "igény szerinti biztonsági mentés" művelethez létrehozott feladatokhoz tartozó névvel egyezik. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmagot.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [állíthatja vissza az Azure-FÁJLMEGOSZTÁS parancssori](restore-afs-cli.md) felületét
* Ismerje meg, hogyan [kezelheti az Azure file share ackups a CLI](manage-afs-backup-cli.md) használatával
