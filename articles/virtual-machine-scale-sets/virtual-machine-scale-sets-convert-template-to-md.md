---
title: Méretezési csoport sablonjának konvertálása felügyelt lemez használatához
description: Azure Resource Manager virtuálisgép-méretezési csoport sablonjának átalakítása felügyelt lemezes méretezési csoport sablonba.
keywords: virtuálisgép-méretezési csoportok
author: mayanknayar
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: 4ab5c48c6673a2353c70fe808d09aa15675e0424
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278128"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Méretezési csoport sablonjának átalakítása felügyelt lemezes méretezési csoport sablonba

A felügyelt lemezeket nem használó méretezési csoport létrehozásához Resource Manager-sablonnal rendelkező ügyfelek módosíthatják azt a felügyelt lemez használatára. Ez a cikk bemutatja, hogyan használhatók a felügyelt lemezek, példaként egy lekéréses kérelem az Azure-beli [Gyorsindítás sablonokból](https://github.com/Azure/azure-quickstart-templates), egy közösségi adattár a Resource Manager-sablonokhoz. A teljes pull-kérést itt tekintheti meg: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998), és a diff megfelelő részei alább találhatók, valamint magyarázatokkal is rendelkeznek:

## <a name="making-the-os-disks-managed"></a>Az operációsrendszer-lemezek felügyelt állapotba állítása

A következő különbségekben a Storage-fiókkal és a lemez tulajdonságaival kapcsolatos számos változó törlődik. A Storage-fiók típusa már nem szükséges (Standard_LRS az alapértelmezett), de igény szerint megadható. A felügyelt lemezeken csak Standard_LRS és Premium_LRS támogatott. A rendszer a régi sablonban új Storage-fiók utótagját, egyedi karakterlánc-tömböt és SA Count értéket használta a Storage-fiókok nevének létrehozásához. Ezek a változók már nem szükségesek az új sablonban, mert a felügyelt lemez automatikusan létrehozza a Storage-fiókokat az ügyfél nevében. Hasonlóképpen, a VHD-tároló neve és az operációsrendszer-lemez neve már nem szükséges, mert a felügyelt lemez automatikusan a mögöttes tároló blob-tárolókat és lemezeket nevezi el.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


A következő eltérések esetén a számítási API-t a 2016-04-30-es verzióra frissíti a rendszer, amely a legkorábbi szükséges verzió a méretezési csoportokkal felügyelt lemezek támogatásához. Ha szükséges, a nem felügyelt lemezeket az új API-verzióban használhatja a régi szintaxissal. Ha csak a számítási API-verziót frissíti, és nem változtat semmi mást, a sablonnak továbbra is ugyanúgy kell működnie, mint korábban.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

A következő különbségekben a Storage-fiók erőforrása teljesen el lesz távolítva az erőforrások tömbből. Az erőforrásra már nincs szükség, mert a felügyelt lemez automatikusan létrehozza azokat.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

A következő eltérések esetében láthatjuk, hogy a méretezési csoporttól a Storage-fiókokat létrehozó hurokra hivatkozó függő záradékot töröljük. A régi sablonban ez azt biztosítja, hogy a Storage-fiókok a méretezési csoport létrehozása előtt jöttek létre, de ez a záradék már nem szükséges a felügyelt lemezzel. A VHD-tárolók tulajdonságot is eltávolítja, az operációsrendszer-lemez neve tulajdonsággal együtt, mivel ezek a tulajdonságok a felügyelt lemez alapján automatikusan kezelhetők a motorháztető alatt. Ha prémium operációsrendszer-lemezeket szeretne, hozzáadhat `"managedDisk": { "storageAccountType": "Premium_LRS" }` a "osDisk" konfigurációhoz. A virtuális gép SKU-jának csak nagybetűs vagy kisbetűvel rendelkező virtuális gépek használhatják a prémium szintű lemezeket.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Nincs explicit tulajdonság a méretezési csoport konfigurációjában, hogy a felügyelt vagy nem felügyelt lemezt kívánja-e használni. A méretezési csoport tudja, hogy melyiket használja a tárolási profilban lévő tulajdonságok alapján. Ezért fontos, hogy a sablon módosításakor ügyeljen arra, hogy a megfelelő tulajdonságok a méretezési csoport tárolási profiljában legyenek.


## <a name="data-disks"></a>Adatlemezek

A fenti módosításokkal a méretezési csoport felügyelt lemezeket használ az operációsrendszer-lemezhez, de mi az adatlemezek? Adatlemezek hozzáadásához adja hozzá a "storageProfile" alatt található "dataDisks" tulajdonságot a "osDisk" értékkel megegyező szinten. A tulajdonság értéke az objektumok JSON-listája, amelyek mindegyike a "LUN" tulajdonságokkal rendelkezik (amelynek egyedinek kell lennie adatlemezként egy virtuális gépen), a "createOption" ("Empty" jelenleg az egyetlen támogatott lehetőség) és a "diskSizeGB" (a lemez mérete gigabájtban; nagyobbnak kell lennie, mint 0 és kevesebb, mint 1024) az alábbi példában látható módon:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Ha megadja `n` lemezeket ebben a tömbben, a méretezési csoport minden virtuális gépe `n` adatlemezeket kap. Ne feledje azonban, hogy ezek az adatlemezek nyers eszközök. Nincsenek formázva. Az ügyfél feladata a lemezek csatlakoztatása, particionálása és formázása a használatuk előtt. Megadhatja az egyes adatlemez-objektumok `"managedDisk": { "storageAccountType": "Premium_LRS" }` is, így meghatározhatja, hogy prémium szintű adatlemeznek kell lennie. A virtuális gép SKU-jának csak nagybetűs vagy kisbetűvel rendelkező virtuális gépek használhatják a prémium szintű lemezeket.

Ha többet szeretne megtudni a méretezési csoportokkal rendelkező adatlemezek használatáról, tekintse meg [ezt a cikket](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Következő lépések
A méretezési csoportokkal rendelkező Resource Manager-sablonok esetében például keressen rá a "vmss" kifejezésre az [Azure Gyorsindítás sablonok GitHub](https://github.com/Azure/azure-quickstart-templates)-tárházában.

Általános információkért tekintse meg a [méretezési csoportok fő oldalát](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

