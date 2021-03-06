---
title: Üzembe helyezés dedikált gazdagépen
description: Dedikált gazdagép használata a Azure Container Instances számítási feladatokhoz való valódi gazda szintű elkülönítés érdekében
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: adad0ddfc78530b3a3a7c139d9a95ec4790c8053
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934147"
---
# <a name="deploy-on-dedicated-hosts"></a>Üzembe helyezés dedikált gazdagépeken

A "dedikált" egy Azure Container Instances (ACI) SKU, amely elkülönített és dedikált számítási környezetet biztosít a biztonságosan futó tárolók számára. A dedikált SKU-eredmények használata minden olyan tároló csoportban, amely egy Azure-adatközpontban dedikált fizikai kiszolgálóval rendelkezik, így teljes munkaterhelés-elkülönítést biztosít a szervezet biztonsági és megfelelőségi követelményeinek kielégítése érdekében. 

A dedikált SKU megfelelő olyan tároló-munkaterhelésekhez, amelyek a fizikai kiszolgáló szemszögéből elkülönítik a számítási feladatokat.

## <a name="prerequisites"></a>Előfeltételek

* A dedikált SKU használatára vonatkozó előfizetések alapértelmezett korlátja 0. Ha ezt az SKU-t az üzemi tároló üzembe helyezéséhez szeretné használni, hozzon létre egy [Azure-support Request][azure-support] a korlát növeléséhez.

## <a name="use-the-dedicated-sku"></a>A dedikált SKU használata

> [!IMPORTANT]
> A dedikált SKU használata csak a legújabb API-verzióban (2019-12-01) érhető el, amely jelenleg folyamatban van. Adja meg ezt az API-verziót a telepítési sablonban.
>

Az API 2019-12-01-es verziójától kezdve a központi telepítési sablon tároló csoport tulajdonságai szakaszában található egy `sku` tulajdonság, amely egy ACI-telepítéshez szükséges. Jelenleg ezt a tulajdonságot használhatja egy Azure Resource Manager központi telepítési sablonhoz az ACI-hoz. További információ az ACI-erőforrások üzembe helyezéséről a sablonnal az [oktatóanyagban: többtárolós csoport üzembe helyezése Resource Manager-sablonnal](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

A `sku` tulajdonság a következő értékek egyikét veheti fel:
* `Standard` – a standard ACI üzembe helyezési lehetőség, amely továbbra is garantálja a hypervisor szintű biztonságot 
* `Dedicated` – a számítási feladatok szintjének elkülönítése dedikált fizikai gazdagépekkel a tároló csoport számára

## <a name="modify-your-json-deployment-template"></a>A JSON-telepítési sablon módosítása

A központi telepítési sablonban módosítsa vagy adja hozzá a következő tulajdonságokat:
* A `resources`alatt állítsa be a `apiVersion` a `2012-12-01`re.
* A tároló csoport tulajdonságai területen adjon hozzá egy `sku` tulajdonságot `Dedicated`értékkel.

Íme egy példa a tároló csoport központi telepítési sablonjának erőforrások szakaszára, amely a dedikált SKU-t használja:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

A következő egy teljes sablon, amely egyetlen tároló-példányt futtató minta tároló csoportot telepít:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>A tároló csoport üzembe helyezése

Ha létrehozta és szerkesztette a telepítési sablon fájlját az asztalon, feltöltheti azt a Cloud Shell könyvtárba a fájl húzásával. 

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Telepítse a sablont az az [Group Deployment Create][az-group-deployment-create] paranccsal.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát. A sikeres üzembe helyezés egy dedikált gazdagépen történik.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
