---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299455"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Támogatott régiók
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Növekményes pillanatkép létrehozásához használhatja a Azure PowerShellt is. A Azure PowerShell legújabb verziójára lesz szüksége, a következő parancs telepíti vagy frissíti a meglévő telepítését a legújabb verzióra:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

A telepítését követően jelentkezzen be a PowerShell-munkamenetbe `az login`használatával.

Ha Azure PowerShell használatával szeretne növekményes pillanatképet létrehozni, állítsa a konfigurációt a [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) értékre a `-Incremental` paraméterrel, majd adja át ezt változóként a [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) a `-Snapshot` paraméterrel.

Cserélje le a `<yourDiskNameHere>`, `<yourResourceGroupNameHere>`és `<yourDesiredSnapShotNameHere>` értékeket az értékekkel, majd a következő parancsfájllal hozzon létre növekményes pillanatképet:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Az azonos lemezről származó növekményes pillanatképeket a `SourceResourceId` és a pillanatképek `SourceUniqueId` tulajdonságaival is azonosíthatja. `SourceResourceId` a szülő lemez Azure Resource Manager erőforrás-azonosítója. `SourceUniqueId` a lemez `UniqueId` tulajdonságában örökölt érték. Ha töröl egy lemezt, majd létrehoz egy azonos nevű új lemezt, akkor a `UniqueId` tulajdonság értéke megváltozik.

A `SourceResourceId` és a `SourceUniqueId` használatával létrehozhat egy adott lemezhez társított összes pillanatképet tartalmazó listát. Cserélje le a `<yourResourceGroupNameHere>` értéket az értékre, majd a következő példa használatával sorolja fel a meglévő növekményes pillanatképeket:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Resource Manager-sablon

Növekményes pillanatkép létrehozásához Azure Resource Manager sablonokat is használhat. Győződjön meg arról, hogy a apiVersion értéke **2019-03-01** , és hogy a növekményes tulajdonság értéke TRUE (igaz) is. A következő kódrészlet egy példa arra, hogyan hozhat létre növekményes pillanatképet Resource Manager-sablonokkal:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>További lépések

Ha szeretné megtekinteni a növekményes Pillanatképek különbözeti képességét bemutató mintakód használatát, tekintse meg az [Azure-Managed Disks biztonsági mentések másolása másik régióba a növekményes Pillanatképek különbözeti képességével](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)című témakört.
