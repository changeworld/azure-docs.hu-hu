---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: jboeshart
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: jaboes
ms.custom: include file
ms.openlocfilehash: 126b488d2bb59e2904bee646301240efe6fe71a4
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76038145"
---
Ez a dokumentum a felügyelt és a nem felügyelt lemezek közötti különbségeket mutatja be, amikor Azure Resource Manager sablonokat használ a virtuális gépek kiépítéséhez. A példák segítségével frissítheti a nem felügyelt lemezeket használó meglévő sablonokat a felügyelt lemezekre. Hivatkozásként az [101-VM-Simple-Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) sablont használjuk útmutatóként. A sablont a [felügyelt lemezekkel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) és a korábbi verziókkal is megtekintheti a nem [felügyelt lemezekkel](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) , ha közvetlenül össze szeretné hasonlítani őket.

## <a name="unmanaged-disks-template-formatting"></a>Nem felügyelt lemezek sablonjának formázása

Először vessünk egy pillantást a nem felügyelt lemezek üzembe helyezésének módjára. Nem felügyelt lemezek létrehozásakor szükség van egy Storage-fiókra a VHD-fájlok tárolásához. Létrehozhat egy új Storage-fiókot, vagy használhat ilyet, amely már létezik. Ez a cikk bemutatja, hogyan hozhat létre új Storage-fiókot. Hozzon létre egy Storage-fiók erőforrást az erőforrások blokkban az alábbi ábrán látható módon.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

A virtuálisgép-objektumon belül adjon hozzá egy függőséget a Storage-fiókhoz, hogy az a virtuális gép előtt legyen létrehozva. A `storageProfile` szakaszban válassza ki a VHD-hely teljes URI-JÁT, amely a Storage-fiókra hivatkozik, és az operációsrendszer-lemez és az adatlemezek esetében szükséges.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Felügyelt lemezek sablonjának formázása

Az Azure Managed Disks használatával a lemez legfelső szintű erőforrás lesz, és már nem szükséges a felhasználó által létrehozandó Storage-fiók létrehozása. A felügyelt lemezek először a `2016-04-30-preview` API-verzióban voltak elérhetők, a következő API-verziókban érhetők el, és most már az alapértelmezett lemez típusa. A következő szakasz végigvezeti az alapértelmezett beállításokon, és részletesen ismerteti a lemezek további testreszabását.

> [!NOTE]
> Azt javasoljuk, hogy a `2016-04-30-preview`nál későbbi API-verziót használjon, mivel `2016-04-30-preview` és `2017-03-30`között megszakadt a változás.
>
>

### <a name="default-managed-disk-settings"></a>A felügyelt lemez alapértelmezett beállításai

A felügyelt lemezekkel rendelkező virtuális gépek létrehozásához többé nem kell létrehoznia a Storage-fiók erőforrását. Az alábbi sablonra hivatkozva a korábbi, nem részletezett lemezekre vonatkozó példákat érdemes megjegyezni:

- A `apiVersion` egy olyan verzió, amely támogatja a felügyelt lemezeket.
- a `osDisk` és a `dataDisks` többé nem hivatkozik a virtuális merevlemez adott URI azonosítóra.
- Ha további tulajdonságok megadása nélkül végzi a telepítést, a lemez a virtuális gép méretétől függően a tárolási típust fogja használni. Ha például olyan virtuálisgép-méretet használ, amely támogatja a Premium Storage-t (például Standard_D2s_v3), akkor a prémium szintű lemezek alapértelmezés szerint lesznek konfigurálva. Ezt a tárolási típus megadásához a lemez SKU-beállítása segítségével módosíthatja.
- Ha nincs megadva a lemez neve, az operációsrendszer-lemez `<VMName>_OsDisk_1_<randomstring>` formátuma, valamint az egyes adatlemezek `<VMName>_disk<#>_<randomstring>`.
  - Ha egy virtuális gépet egy egyéni rendszerképből hoz létre, akkor a rendszer az egyéni rendszerkép-erőforrásban definiált lemez tulajdonságaiból kéri le a Storage-fiók típusának és a lemez nevének alapértelmezett beállításait. Ezek felülbírálása a sablon értékeinek megadásával lehetséges.
- Alapértelmezés szerint az Azure Disk Encryption le van tiltva.
- Alapértelmezés szerint a lemezes gyorsítótárazás az operációsrendszer-lemezre írható/olvasható, és nincs az adatlemezek esetében.
- Az alábbi példában még mindig van egy Storage-fiók függőség, bár ez csak a diagnosztika tárolására szolgál, és nem szükséges a lemezes tároláshoz.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Legfelső szintű felügyelt lemez erőforrásának használata

A virtuálisgép-objektumban a lemez konfigurációjának megadására Alternatív megoldásként létrehozhat egy legfelső szintű lemez-erőforrást, és csatolhatja azt a virtuális gép létrehozása részeként. Létrehozhat például egy lemezes erőforrást az alábbiak szerint adatlemezként való használatra.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

A virtuálisgép-objektumon belül hivatkozzon a csatolni kívánt lemez objektumra. A `managedDisk` tulajdonságban létrehozott felügyelt lemez erőforrás-AZONOSÍTÓjának megadásával engedélyezheti a lemez mellékletét a virtuális gép létrehozásakor. A virtuális gép erőforrásának `apiVersion` beállítása `2017-03-30`. A rendszer hozzáadja a lemez erőforrásának függőségét annak érdekében, hogy a virtuális gép létrehozása előtt sikeresen létrejött legyen. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Felügyelt rendelkezésre állási készletek létrehozása felügyelt lemezeket használó virtuális gépekkel

A felügyelt lemezeket használó virtuális gépekkel felügyelt rendelkezésre állási készletek létrehozásához adja hozzá a `sku` objektumot a rendelkezésre állási csoport erőforrásaihoz, és állítsa a `name` tulajdonságot `Aligned`értékre. Ez a tulajdonság biztosítja, hogy az egyes virtuális gépek lemezei elég elszigeteltek legyenek egymástól az egyes meghibásodási pontok elkerülése érdekében. Azt is vegye figyelembe, hogy a rendelkezésre állási csoport erőforrásának `apiVersion` `2018-10-01`ra van beállítva.

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>standard SSD lemezek

Az alábbi paraméterek szükségesek a Resource Manager-sablonban standard SSD lemezek létrehozásához:

* a Microsoft *apiVersion* . a számítást `2018-04-01` (vagy újabb) értékre kell beállítani.
* *ManagedDisk. tárfióktípus* meghatározása `StandardSSD_LRS`ként

A következő példa egy standard SSD lemezeket használó virtuális gép *Properties. storageProfile. osDisk* szakaszát mutatja be:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Az standard SSD-lemezek sablonnal történő létrehozásával kapcsolatos példa: [virtuális gép létrehozása egy standard SSD Adatlemezekkel rendelkező Windows-lemezképből](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>További forgatókönyvek és testreszabások

A REST API-specifikációkkal kapcsolatos teljes információkért tekintse át a [felügyelt lemez létrehozása REST API dokumentációját](/rest/api/manageddisks/disks/disks-create-or-update). További forgatókönyveket, valamint az alapértelmezett és elfogadható értékeket is megtalálhatja, amelyeket az API-hoz a sablonok üzembe helyezésével lehet elküldeni. 

## <a name="next-steps"></a>Következő lépések

* A felügyelt lemezeket használó teljes sablonokhoz látogasson el az alábbi Azure-beli rövid útmutatókra mutató hivatkozásokra.
    * [Windowsos virtuális gép felügyelt lemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux rendszerű virtuális gép felügyelt lemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* A felügyelt lemezekkel kapcsolatos további információkért látogasson el az [Azure Managed Disks áttekintő](../articles/virtual-machines/windows/managed-disks-overview.md) dokumentumra.
* Tekintse át a virtuális gépek erőforrásainak dokumentációját a [Microsoft. számítás/virtualMachines sablon](/azure/templates/microsoft.compute/virtualmachines) dokumentációjában.
* Tekintse át a sablonra vonatkozó dokumentációt a lemez erőforrásaihoz a [Microsoft. számítási/lemezek sablon-hivatkozási](/azure/templates/microsoft.compute/disks) dokumentumának meglátogatásával.
* A felügyelt lemezek Azure-beli virtuálisgép-méretezési csoportokban való használatáról további információt az [adatlemezek használata méretezési csoportokkal](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) című dokumentumban találhat.
