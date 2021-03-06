---
title: Azure Service Fabric-fürt konfigurációjának frissítése
description: Ismerje meg, hogyan frissítheti az Azure Service Fabric-fürtöt egy Resource Manager-sablon használatával futtató konfigurációt.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463289"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Fürt konfigurációjának frissítése az Azure-ban 

Ez a cikk azt ismerteti, hogyan szabhatja testre a Service Fabric-fürthöz tartozó különböző hálók beállításait. Az Azure-ban üzemeltetett fürtök esetében a beállításokat a [Azure Portal](https://portal.azure.com) vagy egy Azure Resource Manager sablon segítségével szabhatja testre.

> [!NOTE]
> Nem minden beállítás érhető el a portálon, és az [ajánlott eljárás egy Azure Resource Manager sablon használatával történő Testreszabás](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). A portál kizárólag Service Fabric Dev\Test forgatókönyvek esetében érhető el.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Fürtkonfiguráció testreszabása Resource Manager-sablonok használatával
Az Azure-fürtöket a JSON Resource Manager-sablonnal lehet konfigurálni. További információ a különböző beállításokról: [fürtök konfigurációs beállításai](service-fabric-cluster-fabric-settings.md). Az alábbi lépések bemutatják, hogyan adhat hozzá új beállításokat a *diagnosztika* szakaszhoz a Azure erőforrás-kezelő használatával. *MaxDiskQuotaInMB*

1. Nyissa meg a következőt: https://resources.azure.com
2. Navigáljon az előfizetéshez **, és bontsa** ki az előfizetések ->  **\<az** előfizetését > -> **resourceGroups** -> \<**az erőforráscsoport** > -> **szolgáltatók** -> **Microsoft. ServiceFabric** -> - **fürtök** ->  **\<a fürt nevét >**
3. A jobb felső sarokban válassza az **írás/írás lehetőséget.**
4. Válassza a **Szerkesztés** lehetőséget, és frissítse a `fabricSettings` JSON-elemet, és adjon hozzá egy új elemet:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

A fürt beállításait a következő módokon is testreszabhatja a Azure Resource Manager használatával:

- Az erőforrás-kezelő sablon exportálásához és frissítéséhez használja a [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) .
- A [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) használatával exportálhatja és frissítheti a Resource Manager-sablont.
- A Resource Manager-sablon exportálásához és frissítéséhez használja az [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) -t.
- A beállítás közvetlen módosításához használja a Azure PowerShell [set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) és a [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) parancsokat.
- Az Azure CLI az [SF cluster Setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting) paranccsal közvetlenül módosíthatja a beállításokat.

## <a name="next-steps"></a>Következő lépések
* Tudnivalók a [Service Fabric-fürt beállításairól](service-fabric-cluster-fabric-settings.md).
* Ismerje meg, hogyan [méretezheti a fürtöt a és a](service-fabric-cluster-scale-up-down.md)szolgáltatásban.
