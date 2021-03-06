---
title: Azure Service Bus névtér létrehozása sablon használatával
description: Service Bus üzenetküldési névtér létrehozása Azure Resource Manager sablon használatával
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 5febdd63ab6f854ca3244f8449f6f715a75e735f
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264475"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Service Bus névtér létrehozása Azure Resource Manager sablon használatával

Megtudhatja, hogyan helyezhet üzembe egy Azure Resource Manager sablont Service Bus névtér létrehozásához. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően. További információ a sablonok létrehozásáról: [Azure Resource Manager dokumentáció](/azure/azure-resource-manager/).

A következő sablonok a Service Bus névterek létrehozásához is elérhetők:

* [Service Bus névtér létrehozása a várólistával](./service-bus-resource-manager-namespace-queue.md)
* [Service Bus névtér létrehozása témakörrel és előfizetéssel](./service-bus-resource-manager-namespace-topic.md)
* [Service Bus névtér létrehozása a várólista-és engedélyezési szabállyal](./service-bus-resource-manager-namespace-auth-rule.md)
* [Service Bus névtér létrehozása témakörrel, előfizetéssel és szabállyal](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása

Ebben a rövid útmutatóban egy [meglévő Resource Manager-sablont](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) fog használni az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

További sablon-példákat az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)talál.

Service Bus-névtér létrehozása sablon üzembe helyezésével:

1. Válassza a **kipróbálás** a következő kódrészletből lehetőséget, majd kövesse az utasításokat az Azure Cloud shellbe való bejelentkezéshez.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Az erőforráscsoport neve a Service Bus-névtér neve, **RG** hozzáfűzéssel.

2. A PowerShell-szkript másolásához válassza a **Másolás** lehetőséget.
3. Kattintson a jobb gombbal a rendszerhéj-konzolra, majd válassza a **Beillesztés**lehetőséget.

Az Event hub létrehozása néhány percet vesz igénybe.

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

Az üzembe helyezett Service Bus-névtér megjelenítéséhez nyissa meg az erőforráscsoportot a Azure Portal, vagy használja az alábbi Azure PowerShell parancsfájlt. Ha a Cloud Shell továbbra is nyitva van, nem kell másolni/futtatnia a következő parancsfájl első és második sorát.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

A Azure PowerShell a sablon üzembe helyezésére szolgál ebben az oktatóanyagban. A sablon egyéb telepítési módszereivel kapcsolatban lásd:

* [A Azure Portal használatával](../azure-resource-manager/templates/deploy-portal.md).
* [Az Azure CLI használatával](../azure-resource-manager/templates/deploy-cli.md).
* [REST API használatával](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. Ha a Cloud Shell továbbra is nyitva van, nem kell másolni/futtatnia a következő parancsfájl első és második sorát.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy Service Bus névteret hozott létre. Tekintse meg a többi rövid útmutatót, amelyből megtudhatja, hogyan hozhat létre várólistákat, témaköröket és előfizetéseket, és hogyan használhatja azokat

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Ismerkedés a Service Bus témakörökkel](service-bus-dotnet-how-to-use-topics-subscriptions.md)
