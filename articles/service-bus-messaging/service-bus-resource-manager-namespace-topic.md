---
title: Azure Service Bus névtérbeli témakör létrehozása sablon használatával
description: 'Rövid útmutató: Service Bus névtér létrehozása témakörrel és előfizetéssel Azure Resource Manager sablon használatával'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d3d55200-5c60-4b5f-822d-59974cafff0e
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 98ca0f95f5b2aab9651b97442f1f38651767f1df
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264492"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Gyors útmutató: Service Bus névtér létrehozása témakörrel és előfizetéssel egy Azure Resource Manager sablon használatával

Ez a cikk bemutatja, hogyan használható egy Azure Resource Manager sablon, amely létrehoz egy Service Bus névteret, valamint egy témakört és egy előfizetést az adott névtéren belül. A cikk azt ismerteti, hogyan határozható meg, hogy mely erőforrások legyenek telepítve, és Hogyan határozható meg a központi telepítés végrehajtásakor megadott paraméterek. Ezt a sablont saját üzembe helyezési műveleteihez is használhatja, vagy akár igényeinek megfelelően testre is szabhatja

További információ a sablonok létrehozásáról: [Azure Resource Manager-sablonok][Authoring Azure Resource Manager templates]készítése.

A teljes sablonhoz tekintse meg a [Service Bus névteret témakör és előfizetés][Service Bus namespace with topic and subscription] sablonnal.

> [!NOTE]
> A következő Azure Resource Manager sablonok tölthetők le és üzemelő példányban.
> 
> * [Service Bus névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Service Bus névtér létrehozása a várólistával](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus névtér létrehozása a várólista-és engedélyezési szabállyal](service-bus-resource-manager-namespace-auth-rule.md)
> * [Service Bus névtér létrehozása témakörrel, előfizetéssel és szabállyal](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> A legújabb sablonok kereséséhez látogasson el az [Azure Gyorsindítás sablonok][Azure Quickstart Templates] galériába, és keressen rá **Service Bus**.
> 
> 

## <a name="what-do-you-deploy"></a>Mit telepít?

Ezzel a sablonnal Service Bus névteret helyezhet üzembe a témakörrel és az előfizetéssel.

[Service Bus témakörök és előfizetések](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) egy-a-többhöz típusú kommunikációt biztosítanak egy *közzétételi/* előfizetési minta formájában.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz egy `Parameters` nevű szakaszt, amely az összes paraméter értékét tartalmazza. Definiáljon egy paramétert azokhoz az értékekhez, amelyek az üzembe helyezett projekttől függően változnak, vagy azon a környezeten alapulnak, amelyet üzembe szeretne helyezni. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

A sablon a következő paramétereket definiálja:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
A létrehozandó Service Bus névtér neve.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
A Service Bus névtérben létrehozott témakör neve.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
A Service Bus névtérben létrehozott előfizetés neve.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
A sablon Service Bus API-verziója.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```
## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások
Egy szabványos Service Bus **üzenetküldési**névteret hoz létre, amely tartalmazza a témakört és az előfizetést.

```json
"resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

A JSON-szintaxis és-tulajdonságok esetében lásd: [névterek](/azure/templates/microsoft.servicebus/namespaces), [témakörök](/azure/templates/microsoft.servicebus/namespaces/topics)és [előfizetések](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions).

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure parancssori felület (CLI)
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Következő lépések
Most, hogy Azure Resource Manager használatával hozta létre és telepítette az erőforrásokat, megtudhatja, hogyan kezelheti ezeket az erőforrásokat a következő cikkek megtekintésével:

* [A Service Bus kezelése a PowerShell használatával](service-bus-manage-with-ps.md)
* [Service Bus erőforrások kezelése a Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
