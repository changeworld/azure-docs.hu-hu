---
title: Változó több példányának meghatározása
description: A másolási művelettel Azure Resource Manager sablonban több alkalommal is megismételheti a változókat.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ea4caf3553b3cd14eec194b8cef0db59499a4f4c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622884"
---
# <a name="variable-iteration-in-azure-resource-manager-templates"></a>Változó iteráció Azure Resource Manager-sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egynél több értéket egy változóhoz a Azure Resource Manager-sablonban. Ha hozzáadja a **Másolás** elemet a sablon változók szakaszához, az üzembe helyezés során dinamikusan beállíthatja egy változó elemeinek számát. Emellett ne kelljen megismételni a sablon szintaxisát.

A másolást [erőforrásokkal](copy-resources.md), [erőforrásokkal](copy-properties.md)és [kimenetekkel](copy-outputs.md)is elvégezheti.

## <a name="variable-iteration"></a>Változó iteráció

A másolási elem a következő általános formátumú:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

A **Name** tulajdonság bármely olyan érték, amely a hurok azonosítására szolgál. A **Count** tulajdonság határozza meg a változóhoz használni kívánt iterációk számát.

A **bemeneti** tulajdonság határozza meg a megismételni kívánt tulajdonságokat. A **bemeneti** tulajdonság értékével létrehozott elemek tömbjét hozza létre. Ez lehet egy tulajdonság (például egy karakterlánc) vagy egy olyan objektum, amely több tulajdonsággal rendelkezik.

Az alábbi példa azt szemlélteti, hogyan hozható létre karakterlánc-értékek tömbje:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

Az előző sablon egy tömböt ad vissza a következő értékekkel:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

A következő példa bemutatja, hogyan hozhat létre olyan objektumok tömbjét, amelyeknek három tulajdonsága van: Name, diskSizeGB és diskIndex.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

Az előző példa egy tömböt ad vissza a következő értékekkel:

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> A változó iteráció egy eltolási argumentumot támogat. Az eltolásnak az iteráció neve után kell érkeznie, például copyIndex (' diskNames ', 1). Ha nem ad meg eltolási értéket, az alapértelmezett érték 0 az első példánynál.
>

A másolás elemet egy változón belül is használhatja. A következő példa egy olyan objektumot hoz létre, amely egy tömböt tartalmaz egy értékként.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

Az előző példa egy objektumot ad vissza a következő értékekkel:

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

A következő példa bemutatja a másolás változókkal való használatának különböző módszereit.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>Másolási korlátok

A szám nem lehet nagyobb, mint 800.

A darabszám nem lehet negatív szám. Ha Azure PowerShell 2,6-as vagy újabb, Azure CLI-2.0.74 vagy újabb verzióval, REST API vagy a **2019-05-10** -es vagy újabb verziójával rendelkező sablont telepít, akkor a Count értéket nullára állíthatja. A PowerShell, a CLI és a REST API korábbi verziói nem támogatják a nulla értéket a darabszámhoz.

## <a name="example-templates"></a>Példa sablonok

Az alábbi példák egy változó több értékének létrehozására vonatkozó gyakori forgatókönyveket mutatnak be.

|Sablon  |Leírás  |
|---------|---------|
|[Változók másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Bemutatja a változók megismétlésének különböző módszereit. |
|[Több biztonsági szabály](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Több biztonsági szabályt telepít egy hálózati biztonsági csoportra. A biztonsági szabályokat egy paraméter alapján hozza létre. A paraméternél tekintse meg a [több NSG-paramétert tartalmazó fájlt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Következő lépések

* Az oktatóanyag lépéseinek megismeréséhez lásd [: oktatóanyag: több erőforrás-példány létrehozása Resource Manager-sablonok használatával](template-tutorial-create-multiple-instances.md).
* A másolási elem egyéb felhasználási módjaiért lásd:
  * [Erőforrás-iteráció Azure Resource Manager-sablonokban](copy-resources.md)
  * [Tulajdonság-iteráció Azure Resource Manager-sablonokban](copy-properties.md)
  * [Kimeneti iteráció Azure Resource Manager-sablonokban](copy-outputs.md)
* Ha szeretné megismerni a sablonok egyes részeit, olvassa el a [Azure Resource Manager sablonok készítése](template-syntax.md)című szakaszt.
* A sablon üzembe helyezésével kapcsolatos további információkért lásd: [alkalmazások központi telepítése Azure Resource Manager sablonnal](deploy-powershell.md).

