---
title: Erőforrások üzembe helyezése az előfizetésben
description: Leírja, hogyan lehet erőforráscsoportot létrehozni egy Azure Resource Manager sablonban. Azt is bemutatja, hogyan helyezhet üzembe erőforrásokat az Azure-előfizetési hatókörben.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 1a76e41b4b2264bc535752e8f765b3303080abbd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248410"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Erőforráscsoportok és erőforrások létrehozása az előfizetési szinten

Az Azure-előfizetésében lévő erőforrások kezelésének egyszerűbbé tétele érdekében [szabályzatokat](../../governance/policy/overview.md) vagy [szerepköralapú hozzáférés-vezérlést](../../role-based-access-control/overview.md) adhat meg és rendelhet hozzá az előfizetéshez. Az előfizetési szintű sablonokkal a szabályzatok deklaratív alkalmazása és szerepkörök társítása az előfizetéshez. Erőforráscsoportokat is létrehozhat, és erőforrásokat telepíthet.

A sablonok előfizetési szinten való üzembe helyezéséhez használja az Azure CLI-t, a PowerShellt vagy a REST API. A Azure Portal nem támogatja az előfizetés szintjén történő telepítést.

## <a name="supported-resources"></a>Támogatott erőforrások

A következő erőforrástípusok az előfizetés szintjén helyezhetők üzembe:

* [költségvetése](/azure/templates/microsoft.consumption/budgets)
* [központi telepítések](/azure/templates/microsoft.resources/deployments) – az erőforráscsoportok üzembe helyezett beágyazott sablonokhoz.
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Séma

Az előfizetési szintű központi telepítések sémája eltér az erőforráscsoport-telepítésekhez használt sémától.

Sablonok esetén használja a következőt:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

A paraméterérték sémája megegyezik az összes központi telepítési hatókörnél. A paraméter fájljaihoz használja a következőt:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Üzembe helyezési parancsok

Az előfizetési szintű központi telepítések parancsai eltérnek az erőforráscsoport-telepítések parancsaitól.

Az Azure CLI esetében használja az [az Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). A következő példa egy sablont helyez üzembe egy erőforráscsoport létrehozásához:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```


A PowerShell üzembe helyezési parancsához használja a [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) vagy a **New-AzSubscriptionDeployment**. A következő példa egy sablont helyez üzembe egy erőforráscsoport létrehozásához:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

REST API esetén használja a [központi telepítések – létrehozás az előfizetések hatókörében](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Központi telepítés helye és neve

Az előfizetési szintű központi telepítések esetében meg kell adnia egy helyet a központi telepítéshez. A központi telepítés helye nem azonos a telepített erőforrások helyétől. A központi telepítés helye határozza meg, hogy hol tárolja a telepítési adatforrásokat.

Megadhatja a központi telepítés nevét, vagy használhatja az alapértelmezett központi telepítési nevet is. Az alapértelmezett név a sablonfájl neve. Egy **azuredeploy. JSON** nevű sablon üzembe helyezése például létrehoz egy alapértelmezett központi telepítési nevet a **azuredeploy**.

Az egyes központi telepítési nevek esetében a hely nem módosítható. A központi telepítést nem lehet az egyik helyen létrehozni, ha egy másik helyen már van ilyen nevű üzemelő példány. Ha `InvalidDeploymentLocation`hibakódot kap, vagy más nevet vagy azonos helyet használ a korábbi üzembe helyezéshez a névben.

## <a name="use-template-functions"></a>A Template functions használata

Az előfizetési szintű központi telepítések esetében néhány fontos szempontot figyelembe kell venni a sablon funkcióinak használatakor:

* A [resourceGroup ()](template-functions-resource.md#resourcegroup) függvény **nem** támogatott.
* A [Reference ()](template-functions-resource.md#reference) és a [List ()](template-functions-resource.md#list) függvények támogatottak.
* Használja a [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) függvényt az előfizetési szinten üzembe helyezett erőforrások erőforrás-azonosítójának lekéréséhez.

  Ha például egy házirend-definíció erőforrás-AZONOSÍTÓját szeretné lekérni, használja a következőt:
  
  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  A visszaadott erőforrás-azonosító formátuma a következő:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

Ha egy Azure Resource Manager sablonban szeretne létrehozni egy erőforráscsoportot, Definiáljon egy [Microsoft. Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) -erőforrást az erőforráscsoport nevével és helyével. Létrehozhat egy erőforráscsoportot, és erőforrásokat helyezhet üzembe az adott erőforráscsoporthoz ugyanabban a sablonban.

A következő sablon egy üres erőforráscsoportot hoz létre.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Több erőforráscsoport létrehozásához használja a [Másolás elemet](copy-resources.md) az erőforráscsoportok használatával.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Az erőforrás-iterációval kapcsolatos további információkért lásd: az [erőforrás több példányának telepítése Azure Resource Manager-sablonokban](./copy-resources.md)és [oktatóanyag: több erőforrás-példány létrehozása Resource Manager-sablonokkal](./template-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Erőforráscsoport és erőforrások

Az erőforráscsoport létrehozásához és az erőforrások üzembe helyezéséhez használjon egy beágyazott sablont. A beágyazott sablon meghatározza az erőforráscsoporthoz telepítendő erőforrásokat. Állítsa be a beágyazott sablont az erőforráscsoport függőként, hogy az erőforrás-csoport az erőforrások telepítése előtt is elérhető legyen.

A következő példában létrehozunk egy erőforráscsoportot, és üzembe helyezünk egy Storage-fiókot az erőforráscsoporthoz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[parameters('rgName')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2017-10-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              }
              "kind": "StorageV2",
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="create-policies"></a>Szabályzatok létrehozása

### <a name="assign-policy"></a>Házirend kiosztása

Az alábbi példa egy meglévő szabályzat-definíciót rendel hozzá az előfizetéshez. Ha a házirend paramétereket fogad, adja meg őket objektumként. Ha a házirend nem fogad paramétereket, használja az alapértelmezett üres objektumot.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

A sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

A sablon PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Házirend meghatározása és hozzárendelése

[Megadhatja és hozzárendelhet](../../governance/policy/concepts/definition-structure.md) egy szabályzatot ugyanabban a sablonban.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

A házirend-definíció létrehozásához az előfizetésében, majd az előfizetésre való alkalmazásához használja az alábbi CLI-parancsot:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

A sablon PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="template-samples"></a>Sablonminták

* [Hozzon létre egy erőforráscsoportot, zárolja, és adjon hozzá engedélyeket](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).
* [Hozzon létre egy erőforráscsoportot, egy házirendet és egy házirend-hozzárendelést](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Következő lépések

* A szerepkörök hozzárendelésével kapcsolatos további tudnivalókért lásd: [Az Azure-erőforrásokhoz való hozzáférés kezelése RBAC és Azure Resource Manager sablonok használatával](../../role-based-access-control/role-assignments-template.md).
* A Azure Security Center munkaterület-beállításainak üzembe helyezésére példát a következő témakörben talál: [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* A sablonok a [githubon](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments)találhatók.
* A sablonokat [felügyeleti csoport szintjén](deploy-to-management-group.md) és [bérlői szinten](deploy-to-tenant.md)is üzembe helyezheti.
