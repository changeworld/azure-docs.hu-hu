---
title: Azure-irányítópultok programozott létrehozása
description: Az Azure-irányítópultok programozott létrehozásához használja a Azure Portalban található irányítópultot sablonként. JSON-hivatkozást tartalmaz.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: 414427c722b3531c994bb99dbd5d1332c5253dfd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900930"
---
# <a name="programmatically-create-azure-dashboards"></a>Azure-irányítópultok programozott létrehozása

Ez a dokumentum végigvezeti az Azure-irányítópultok programozott létrehozásának és közzétételének folyamatán. Az alábbi irányítópult az egész dokumentumra hivatkozik.

![minta-irányítópult](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Áttekintés

A [Azure Portal](https://portal.azure.com) megosztott irányítópultok olyan [erőforrások](../azure-resource-manager/management/overview.md) , mint a Virtual Machines és a Storage-fiókok. Az erőforrásokat programozott módon kezelheti a [Azure Resource Manager REST API](/rest/api/)-k, az [Azure CLI](/cli/azure)és a [Azure PowerShell parancsok](/powershell/azure/get-started-azureps)használatával.  

Ezen API-kra számos funkció támaszkodik az erőforrások kezelésének megkönnyítésére. Ezen API-k és eszközök mindegyike lehetőséget kínál az erőforrások létrehozására, listázására, lekérésére, módosítására és törlésére. Mivel az irányítópultok erőforrások, kiválaszthatja kedvenc API-ját vagy eszközét.

Bármelyik használt eszközön létre kell hoznia egy JSON-ábrázolást az irányítópult-objektumhoz. Ez az objektum az irányítópulton lévő csempék adatait tartalmazza. Ez magában foglalja a méreteket, a pozíciókat, a hozzájuk kötött erőforrásokat, valamint a felhasználói testreszabásokat.

A JSON-dokumentum felépítésének leghatékonyabb módja a Azure Portal használata. A csempéket interaktív módon is hozzáadhatja és elhelyezheti. Ezután exportálja a JSON-t, és hozzon létre egy sablont az eredményből a parancsfájlok, programok és központi telepítési eszközök későbbi használatához.

## <a name="create-a-dashboard"></a>Irányítópult létrehozása

Irányítópult létrehozásához válassza az **irányítópult** lehetőséget a [Azure Portal](https://portal.azure.com) menüben, majd válassza az **új irányítópult**lehetőséget.

![új irányítópult-parancs](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Csempék megkereséséhez és hozzáadásához használja a csempe gyűjteményét. A csempék a húzással lesznek hozzáadva. Néhány csempe támogatja az átméretezést húzással.

![a fogópont húzásával módosíthatja a méretet](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Mások rögzített méretűek, hogy a helyi menüben legyenek kiválasztva.

![méretek a méretezési menüben a méret módosításához](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Az irányítópult megosztása

Miután konfigurálta az irányítópultot, a következő lépések végrehajtásával tegye közzé az irányítópultot a **Share** paranccsal, majd használja a erőforrás-kezelő a JSON beolvasásához.

![irányítópult megosztása](./media/azure-portal-dashboards-create-programmatically/share-command.png)

A **megosztás** elem kiválasztásával kiválaszthatja, hogy melyik előfizetést és erőforráscsoportot szeretné közzétenni. Írási hozzáféréssel kell rendelkeznie a kiválasztott előfizetéshez és erőforráscsoporthoz. További információ: szerepkör- [hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).

![megosztás és hozzáférés módosítása](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Az irányítópult JSON-ábrázolásának beolvasása

A közzététel csak néhány másodpercet vesz igénybe.  Ha elkészült, a következő lépés az [erőforrás-kezelő](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) megnyitása a JSON beolvasásához.

![Tallózás erőforrás-kezelő](./media/azure-portal-dashboards-create-programmatically/search-resource-explorer.png)

A erőforrás-kezelő navigáljon a kiválasztott előfizetéshez és erőforráscsoporthoz. Ezután válassza ki az újonnan közzétett irányítópult-erőforrást a JSON megjelenítéséhez.

![JSON megtekintése erőforrás-kezelő](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json-detail.png)

## <a name="create-a-template-from-the-json"></a>Sablon létrehozása a JSON-ből

A következő lépés egy sablon létrehozása ebből a JSON-ból. A sablon programozott módon használható a megfelelő erőforrás-kezelési API-kkal, parancssori eszközökkel vagy a portálon.

Nem kell teljes mértékben megértenie az irányítópult JSON-struktúráját sablon létrehozásához. A legtöbb esetben szeretné megőrizni az egyes csempék szerkezetét és konfigurációját. Ezután parametrizálja azon Azure-erőforrások készletét, amelyeket a csempék mutatnak. Tekintse meg az exportált JSON-irányítópultot, és keresse meg az Azure-erőforrás-azonosítók összes előfordulását. A példában szereplő irányítópult több csempét tartalmaz, amelyek mindegyike egyetlen Azure-beli virtuális gépen található. Ennek az az oka, hogy az irányítópult csak ezt az egyetlen erőforrást vizsgálja. Ha a dokumentum végén található JSON-minta alapján keres, a "/Subscriptions" kifejezésnél az azonosító számos előfordulása látható.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Ha a későbbiekben szeretné közzétenni az irányítópultot bármelyik virtuális gépen, parametrizálja a karakterlánc minden előfordulását a JSON-n belül.

Az Azure-ban erőforrásokat létrehozó API-k esetében két megközelítés létezik:

* A kötelező API-k egyszerre egy erőforrást hoznak létre. További információ: [erőforrások](/rest/api/resources/resources).
* Sablon alapú központi telepítési rendszer, amely több, egymástól függő erőforrást hoz létre egyetlen API-hívással. További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és-Azure PowerShellokkal](../azure-resource-manager/resource-group-template-deploy.md).

A sablon alapú üzembe helyezés támogatja a paraméterezés és a Templates használatát. Ezt a megközelítést használjuk ebben a cikkben.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Irányítópult létrehozása sablon alapján a sablon központi telepítése alapján

Az Azure számos erőforrás üzembe helyezését teszi lehetővé. Létre kell hoznia egy központi telepítési sablont, amely a telepítendő erőforrások készletét és a közöttük fennálló kapcsolatokat fejezi ki.  Az egyes erőforrások JSON-formátuma ugyanaz, mintha egyenként létrehozta őket. A különbség az, hogy a sablon nyelve néhány olyan fogalmat is hozzáad, mint például a változók, paraméterek, alapszintű függvények és egyebek. Ez a kiterjesztett szintaxis csak egy sablon központi telepítésének kontextusában támogatott. Nem működik, ha a korábban tárgyalt, kötelező API-kkal van használatban. További információ: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](../azure-resource-manager/resource-group-authoring-templates.md).

A paraméterezés a sablon paraméterének szintaxisa alapján kell elvégezni.  Az erőforrás-azonosító összes példányát lecseréli az itt látható módon.

Példa JSON-tulajdonságra a nehezen kódolt erőforrás-AZONOSÍTÓval:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Példa a JSON-tulajdonság paraméteres verzióra konvertálása sablon paraméterei alapján

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Deklarálja a szükséges sablon-metaadatokat és a JSON-sablon tetején található paramétereket:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

A dokumentum végén a teljes, munkasablon látható.

A sablon konfigurálása után a következő módszerek bármelyikével telepítheti azt:

* [REST API-k](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](/cli/azure/group/deployment#az-group-deployment-create)
* [A Azure Portal sablon üzembe helyezésének lapja](https://portal.azure.com/#create/Microsoft.Template)

Íme a példában szereplő irányítópult JSON két verziója. Az első az a verzió, amelyet az erőforráshoz már kötött portálról exportáltunk. A második a sablon verziója, amely programozott módon köthető bármely virtuális géphez, és üzembe helyezhető a Azure Resource Manager használatával.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Példa-irányítópult JSON-ábrázolása a sablon előtt

Ebből a példából megtudhatja, Mit várhat, ha követte ezt a cikket. Az utasítások a már üzembe helyezett irányítópult JSON-ábrázolását exportálták. A rögzített erőforrás-azonosítók, amelyek azt mutatják, hogy ez az irányítópult egy adott Azure-beli virtuális gépen mutat.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>A példában szereplő irányítópult sablon-ábrázolása

Az irányítópult sablonjának verziója három, `virtualMachineName`, `virtualMachineResourceGroup`és `dashboardName`nevű paramétert adott meg.  A paraméterek lehetővé teszik, hogy az irányítópultot egy másik Azure-beli virtuális gépen mutassa be minden egyes telepítésekor. Ez az irányítópult programozott módon konfigurálható és üzembe helyezhető úgy, hogy bármely Azure-beli virtuális gépre mutasson. A szolgáltatás teszteléséhez másolja a következő sablont, és illessze be a [Azure Portal sablon üzembe helyezése lapra](https://portal.azure.com/#create/Microsoft.Template).

Ez a példa egy irányítópultot helyez üzembe önmagával, de a sablon nyelve lehetővé teszi több erőforrás üzembe helyezését, valamint egy vagy több irányítópult egymás melletti elhelyezését.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```
