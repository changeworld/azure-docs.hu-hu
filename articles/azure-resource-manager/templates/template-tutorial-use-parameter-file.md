---
title: Oktatóanyag – sablon üzembe helyezése a paraméter használatával
description: Használjon olyan paramétereket, amelyek tartalmazzák a Azure Resource Manager-sablon üzembe helyezéséhez használandó értékeket.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4e3f4f1c829436415880e66f0cf0170107732bda
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368842"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>Oktatóanyag: paraméterek használata a Resource Manager-sablon üzembe helyezéséhez

Ebből az oktatóanyagból megtudhatja, hogyan használhatók a [paraméter-fájlok](parameter-files.md) az üzembe helyezés során beadott értékek tárolására. Az előző oktatóanyagokban beágyazott paramétereket használt a telepítési paranccsal. Ez a megközelítés a sablon tesztelésére szolgál, de a központi telepítések automatizálásakor könnyebb lehet átadni a környezete értékeit. A paraméter-fájlok megkönnyítik egy adott környezet paramétereinek értékének becsomagolását. Ebben az oktatóanyagban paramétereket hozhat létre fejlesztési és éles környezetekhez. A művelet végrehajtása körülbelül **12 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [címkékre vonatkozó oktatóanyagot](template-tutorial-add-tags.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sablon áttekintése

A sablon számos, az üzembe helyezés során megadható paramétert tartalmaz. Az előző oktatóanyag végén a sablon a következőképpen néz ki:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Ez a sablon jól működik, de most egyszerűen kezelheti a sablonhoz megadott paramétereket.

## <a name="add-parameter-files"></a>Paraméter-fájlok hozzáadása

A paraméter fájljai a sablonhoz hasonló struktúrával rendelkező JSON-fájlok. A fájlban adja meg az üzembe helyezés során átadni kívánt paramétereket.

A VS Code-ban hozzon létre egy új fájlt a következő tartalommal. Mentse a fájlt a **azuredeploy. Parameters. dev. JSON**néven.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Ez a fájl a fejlesztési környezethez tartozó paraméter-fájl. Figyelje meg, hogy Standard_LRSt használ a Storage-fiókhoz, az erőforrásokat pedig **fejlesztői** előtaggal látja el, és beállítja a **környezeti** címkét a **dev**értékre.

Ismét hozzon létre egy új fájlt a következő tartalommal. Mentse a fájlt a **azuredeploy. Parameters. prod. JSON**néven.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Ez a fájl az éles környezethez tartozó paraméter-fájl. Figyelje meg, hogy Standard_GRSt használ a Storage-fiókhoz, és megnevezi az erőforrásokat a **contoso** előtaggal, és beállítja a környezeti címkét az **éles** **környezetben** . Valós éles környezetben érdemes lehet olyan app Service-t használni, amely nem ingyenes, de továbbra is ezt az SKU-t fogjuk használni ehhez az oktatóanyaghoz.

## <a name="deploy-template"></a>Sablon üzembe helyezése

A sablon üzembe helyezéséhez használja az Azure CLI-t vagy a Azure PowerShell.

A sablon utolsó tesztje hozzon létre két új erőforráscsoportot. Egyet a fejlesztői környezethez, egyet pedig az éles környezethez.

Először üzembe kell helyezni a fejlesztői környezetet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.dev.json
```

---

Most üzembe helyezzük az éles környezetben.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.prod.json
```

---

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az Azure Portal lévő erőforráscsoportokat.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok**lehetőséget.
1. Ekkor megjelenik az oktatóanyagban üzembe helyezett két új erőforráscsoport.
1. Válassza ki az erőforráscsoportot, és tekintse meg a telepített erőforrásokat. Figyelje meg, hogy az adott környezethez tartozó paraméterben megadott értékeknek felelnek meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét. Ha elvégezte ezt a sorozatot, három erőforráscsoport törölhető – myResourceGroup, myResourceGroupDev és myResourceGroupProd.
3. Válassza ki az erőforráscsoport nevét.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Gratulálunk, készen áll a sablonok Azure-ba történő üzembe helyezésének bevezetésére. Tudassa velünk, ha megjegyzésekkel és javaslatokkal rendelkezik a visszajelzések szakaszban. Köszönjük!

Készen áll a sablonokkal kapcsolatos speciális fogalmak beugrására. A következő oktatóanyag részletesen ismerteti a sablon-referenciák dokumentációjának használatát, amely segítséget nyújt a telepítendő erőforrások definiálásához.

> [!div class="nextstepaction"]
> [A sablonreferencia felhasználása](template-tutorial-create-encrypted-storage-accounts.md)
