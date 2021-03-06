---
title: BLOB Storage-módosíthatatlansági szabályzatok beállítása és kezelése – Azure Storage
description: Megtudhatja, hogyan tárolhatók a blob (Object) tárolók féreg (egyszer írható és olvasható) támogatása, hogy a megadott intervallumban nem törölhető, nem módosítható állapotban tárolja az információkat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 05a155584f0cb69191883cb82b3db0af435ccc12
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970102"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>BLOB Storage-módosíthatatlansági szabályzatok beállítása és kezelése

Az Azure Blob Storage nem módosítható tárolója lehetővé teszi, hogy a felhasználók üzleti szempontból kritikus fontosságú adatobjektumokat tároljanak egy FÉREGben (egyszer írható, olvasható) állapot. Ez az állapot a felhasználó által megadott intervallumban nem törölhető és nem módosítható adatvesztést tesz lehetővé. A megőrzési időtartam időtartama alatt a Blobok létrehozhatók és olvashatók, de nem módosíthatók és nem törölhetők. A nem módosítható tárterület az összes Azure-régióban az általános célú v2 és a blob Storage-fiókok esetében érhető el.

Ez a cikk bemutatja, hogyan állíthatja be és kezelheti a blob Storage-ban lévő módosíthatatlansági-házirendeket és jogi megtartási jogokat a Azure Portal, a PowerShell vagy az Azure CLI használatával. A nem módosítható tárolóval kapcsolatos további információkért lásd: [üzleti szempontból kritikus blob-adatok tárolása a](storage-blob-immutable-storage.md)nem módosítható tárolóval.

## <a name="set-retention-policies-and-legal-holds"></a>Adatmegőrzési szabályzatok és jogcímek beállítása

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Hozzon létre egy új tárolót, vagy válasszon ki egy már meglévőt a nem módosítható állapotban tartandó blobok tárolására. A tárolónak egy általános célú v2-vagy blob Storage-fiókban kell lennie.

2. A tároló beállításainál válassza a **hozzáférési szabályzat** lehetőséget. Ezután válassza a **házirend hozzáadása** a nem módosítható **blob Storage**-ban lehetőséget.

    ![Tároló beállításai a portálon](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Az időalapú adatmegőrzés engedélyezéséhez válassza az **időalapú megőrzés** lehetőséget a legördülő menüből.

    !["Időalapú megőrzés" kiválasztva a "házirend típusa" alatt](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Adja meg a megőrzési időtartamot napokban (az elfogadható értékek 1 – 146000 nap).

    !["Megőrzési időszak frissítése a következőre" mező](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    A házirend kezdeti állapota zárolva van, így a zárolás előtt tesztelheti a szolgáltatást, és módosíthatja a szabályzatot. A szabályzat zárolása elengedhetetlen ahhoz, hogy megfeleljen a (z) SEC 17a-4 előírásoknak.

5. Zárolja a szabályzatot. Kattintson a jobb gombbal a három pontra ( **..** .), és a következő menü jelenik meg további műveletekkel:

    ![A menü zárolási szabályzata](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Válassza a **zárolási házirend** elemet, és erősítse meg a zárolást. A szabályzat zárolva van, és nem törölhető, csak a megőrzési időtartam kiterjesztései lesznek engedélyezve. A blob-törlések és felülbírálások nem engedélyezettek. 

    ![A menü zárolási szabályzatának megerősítése](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. A jogcímek engedélyezéséhez válassza a **házirend hozzáadása**lehetőséget. Válassza a **jogi megtartás** lehetőséget a legördülő menüből.

    !["Jogi megtartás" a "szabályzat típusa" alatt található menüben](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Hozzon létre egy jogi megtartást egy vagy több címkével.

    !["Címke neve" mező a házirend típusa alatt](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. A jogi megtartási szabály törléséhez távolítsa el az alkalmazott jogi megtartási azonosító címkét.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A szolgáltatás a következő parancs-csoportokba tartozik: `az storage container immutability-policy` és `az storage container legal-hold`. `-h` futtatásával tekintheti meg a parancsokat.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az az. Storage modul támogatja a nem módosítható tárolókat.  A szolgáltatás engedélyezéséhez kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a telepített PowerShellGet legújabb verziója van telepítve: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Távolítsa el a Azure PowerShell korábbi telepítését.
3. Azure PowerShell telepítése: `Install-Module Az –Repository PSGallery –AllowClobber`.

A következő PowerShell-parancsfájl a hivatkozás. Ez a szkript létrehoz egy új Storage-fiókot és-tárolót. Ezután bemutatjuk, hogyan lehet beállítani és törölni az időalapú adatmegőrzési szabályzatokat (más néven módosíthatatlansági-szabályzatokat), és ki kell zárnia a megőrzési időt.

Először hozzon létre egy Azure Storage-fiókot:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Jogi tartalékok beállítása és törlése:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Időalapú módosíthatatlansági házirendek létrehozása vagy frissítése:

```powershell
# Create a time-based immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Módosíthatatlansági szabályzatok beolvasása:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Módosíthatatlansági házirendek zárolása (`-Force` hozzáadása a kérés elvetéséhez):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Módosíthatatlansági házirendek kiterjesztése:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Zárolt módosíthatatlansági szabályzat eltávolítása (`-Force` hozzáadása a kérés elvetéséhez):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>A védett hozzáfűzési Blobok írásának engedélyezése

### <a name="portal"></a>[Portal](#tab/azure-portal)

![További hozzáfűzési írások engedélyezése](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A szolgáltatás a következő parancs-csoportokba tartozik: `az storage container immutability-policy` és `az storage container legal-hold`. `-h` futtatásával tekintheti meg a parancsokat.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Create an immutablity policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Következő lépések

[Üzleti szempontból kritikus fontosságú blob-alapú adattárolás tárolása a nem módosítható tárolóval](storage-blob-immutable-storage.md)
