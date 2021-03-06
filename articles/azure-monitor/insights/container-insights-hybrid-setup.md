---
title: Hibrid Kubernetes-fürtök konfigurálása a Azure Monitor for containers szolgáltatásban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható Azure Monitor a tárolók számára Azure Stack vagy más környezetben üzemeltetett Kubernetes-fürtök figyelésére.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6d03716b988b1139e01d41120f48ea9a9bf34be1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198054"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Hibrid Kubernetes-fürtök konfigurálása Azure Monitor tárolók számára

A tárolók Azure Monitor széles körű monitorozási élményt biztosítanak az Azure-ban üzemeltetett Azure Kubernetes szolgáltatás (ak) és az AK-os [motor](https://github.com/Azure/aks-engine)számára, amely az Azure-ban üzemeltetett, önállóan felügyelt Kubernetes-fürt. Ez a cikk bemutatja, hogyan engedélyezheti az Azure-on kívül üzemeltetett Kubernetes-fürtök figyelését, és így hasonló figyelési élményt érhet el.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik az alábbiakkal:

* Egy Log Analytics-munkaterület.

    A tárolók Azure Monitor Log Analytics munkaterületet támogatnak az Azure [Products By Region régióban](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)felsorolt régiókban. Saját munkaterület létrehozásához [Azure Resource Manager](../platform/template-workspace-configuration.md), a [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy a [Azure Portal](../learn/quick-create-workspace.md)használatával hozható létre.

    >[!NOTE]
    >Az azonos nevű fürttel rendelkező több fürt figyelésének engedélyezése ugyanahhoz a Log Analytics-munkaterülethez nem támogatott. A fürt nevének egyedinek kell lennie.
    >

* Tagja a **log Analytics közreműködő szerepkörnek** a tárolók figyelésének engedélyezéséhez. A Log Analytics munkaterület elérésének szabályozásával kapcsolatos további információkért lásd: a [munkaterület hozzáférésének kezelése és a naplózási adatok](../platform/manage-access.md)

* A [Helm-ügyfél](https://helm.sh/docs/using_helm/) a megadott Kubernetes-fürthöz tartozó tárolók diagramjának Azure monitor bevezetéséhez.

* A következő proxy-és tűzfal-konfigurációs információk szükségesek a Linux rendszerhez készült Log Analytics-ügynöknek a Azure Monitorval való kommunikációhoz:

    |Ügynök erőforrása|Portok |
    |------|---------|   
    |*.ods.opinsights.azure.com |443-as port |  
    |*.oms.opinsights.azure.com |443-as port |  
    |*.blob.core.windows.net |443-as port |  
    |*. dc.services.visualstudio.com |443-as port |

* A tároló ügynök a Kubelet `cAdvisor secure port: 10250` vagy `unsecure port :10255` megnyitását igényli a fürt összes csomópontján a teljesítmény-metrikák gyűjtéséhez. Javasoljuk, hogy konfigurálja `secure port: 10250` a Kubelet cAdvisor, ha még nincs konfigurálva.

* A tároló ügynöknek a következő környezeti változókat kell megadnia a tárolón ahhoz, hogy kommunikálni tudjon a fürtön belüli Kubernetes API szolgáltatással a leltári adatok összegyűjtéséhez – `KUBERNETES_SERVICE_HOST` és `KUBERNETES_PORT_443_TCP_PORT`.

>[!IMPORTANT]
>A hibrid Kubernetes-fürtök figyeléséhez támogatott minimális ügynök verziója ciprod10182019 vagy újabb.

## <a name="supported-configurations"></a>Támogatott konfigurációk

A következőt hivatalosan támogatja a tárolók Azure Monitor.

- Környezetek: helyszíni Kubernetes, AK-motor az Azure-ban és Azure Stack. További információ: az [AK-motor Azure stackon](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- A Kubernetes és a támogatási szabályzat verziói ugyanazok, mint a [támogatott AK](../../aks/supported-kubernetes-versions.md)-verziók.
- Tároló futtatókörnyezete: Docker és Moby
- Linux RENDSZERű kiadás a Master és a Worker csomópontjaihoz: Ubuntu (18,04 LTS és 16,04 LTS)
- Támogatott hozzáférés-vezérlés: Kubernetes RBAC és nem RBAC

## <a name="enable-monitoring"></a>Monitorozás engedélyezése

A hibrid Kubernetes-fürthöz tartozó tárolók Azure Monitorának engedélyezése a következő lépések végrehajtásával történik.

1. Konfigurálja a Log Analytics munkaterületet a Container bepillantást biztosító megoldással.

2. Engedélyezze a Azure Monitor for containers HELM diagramot Log Analytics munkaterülettel.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>A Azure Monitor containers megoldás hozzáadása

A megoldás a megadott Azure Resource Manager sablonnal is üzembe helyezhető a Azure PowerShell parancsmaggal `New-AzResourceGroupDeployment` vagy az Azure CLI használatával.

Ha ismeri a sablon segítségével üzembe helyezni erőforrásokat fogalmát, lásd:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Az Azure CLI 2.0.59 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa `az --version`. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört.

Ez a módszer két JSON-sablont tartalmaz. Egy sablon határozza meg, a konfigurációt a figyelés, és a másik paraméterértékeket, hogy adja meg az alábbiakat tartalmazza:

- **workspaceResourceId** – a log Analytics munkaterület teljes erőforrás-azonosítója.
- **workspaceRegion** – az a régió, amely a munkaterületet hozza létre, és a munkaterület **tulajdonságai között a** Azure Portal.

A **containerSolutionParams. JSON** fájlban lévő `workspaceResourceId` paraméter értékének megadásához a log Analytics munkaterület teljes erőforrás-azonosítójának megadásához hajtsa végre a következő lépéseket, majd futtassa a PowerShell-parancsmagot vagy az Azure CLI-parancsot a megoldás hozzáadásához.

1. Sorolja fel az összes olyan előfizetést, amelyhez hozzáféréssel rendelkezik a következő parancs használatával:

    ```azurecli
    az account list --all -o table
    ```

    A kimenet az alábbihoz hasonló lesz:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Másolja a **SubscriptionId**értékét.

2. Váltson a Log Analytics munkaterületet üzemeltető előfizetésre a következő paranccsal:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Az alábbi példa az előfizetésekben lévő munkaterületek listáját jeleníti meg az alapértelmezett JSON-formátumban.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    A kimenetben keresse meg a munkaterület nevét, majd másolja az adott Log Analytics munkaterület teljes erőforrás-AZONOSÍTÓját a mező **azonosítója**alá.

4. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Mentse ezt a fájlt containerSolution. JSON néven egy helyi mappába.

6. A következő JSON-szintaxist illessze be a fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Szerkessze a **workspaceResourceId** értékeit a 3. lépésben átmásolt értékkel, és a **WorkspaceRegion** másolja a **régió** értékét az Azure CLI parancs futtatása után az [monitor log-Analytics Workspace show](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list)paranccsal.

8. Mentse ezt a fájlt containerSolutionParams. JSON néven egy helyi mappába.

9. Készen áll a sablon üzembe helyezésére.

   * A Azure PowerShell használatával történő üzembe helyezéshez használja az alábbi parancsokat a sablont tartalmazó mappában:

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Az Azure CLI-vel való üzembe helyezéshez futtassa a következő parancsokat:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Miután engedélyezte a figyelés, a fürt mérőszámok megtekintéséhez nagyjából 15 percet igénybe vehet.

## <a name="install-the-chart"></a>A diagram telepítése

A HELM diagram engedélyezéséhez tegye a következőket:

1. Adja hozzá az Azure-diagramok tárházát a helyi listához a következő parancs futtatásával:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. A következő parancs futtatásával telepítse a diagramot:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Ha a Log Analytics munkaterület az Azure China-ban van, futtassa a következő parancsot:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Ha az Log Analytics munkaterület az Azure US Governmentben van, futtassa a következő parancsot:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Az ügynök adatgyűjtésének konfigurálása

Az ügynök adatgyűjtési beállításai a ConfigMap-ből vezérelhetők, és a diagramon a 1.0.0. Tekintse meg az ügynök adatgyűjtési beállításaival kapcsolatos dokumentációt [itt](container-insights-agent-config.md).

A diagram sikeres üzembe helyezését követően áttekintheti a hibrid Kubernetes-fürt adatait a Azure Portal tárolók Azure Monitorban.  

>[!NOTE]
>A betöltési késés körülbelül öt – tíz perc az ügynöktől az Azure Log Analytics-munkaterületen véglegesítve. A fürt állapota megjelenítheti a **nem adat** vagy **ismeretlen** értéket, amíg a Azure monitor összes szükséges figyelési adata nem érhető el.

## <a name="troubleshooting"></a>Hibakeresés

Ha a hibrid Kubernetes-fürt figyelésének engedélyezésére tett kísérlet során hiba lép fel, másolja a [TroubleshootError_nonAzureK8s. Ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) PowerShell-parancsfájlt, és mentse a számítógép egyik mappájába. Ez a parancsfájl az észlelt problémák észleléséhez és megoldásához nyújt segítséget. A probléma megoldásához a következő problémák észlelhetők:

* A megadott Log Analytics munkaterület érvényes
* A Log Analytics munkaterület a Azure Monitor for containers megoldáshoz van konfigurálva. Ha nem, konfigurálja a munkaterületet.
* OmsAgent REPLICASET hüvely fut
* OmsAgent daemonset elemet hüvely fut
* A OmsAgent állapotfigyelő szolgáltatás fut
* A tároló ügynökön konfigurált Log Analytics munkaterület azonosítója és kulcsa megegyezik azzal a munkaterülettel, amelyhez az Insight konfigurálva van.
* Ellenőrizze, hogy az összes linuxos feldolgozó csomópont rendelkezik-e `kubernetes.io/role=agent` címkével az RS Pod-hoz. Ha nem létezik, adja hozzá.
* Ellenőrizze, `cAdvisor secure port:10250` vagy `unsecure port: 10255` meg van-e nyitva a fürt összes csomópontján.

Azure PowerShell végrehajtásához használja a következő parancsokat a parancsfájlt tartalmazó mappában:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Következő lépések

Ha a figyelés engedélyezve van a hibrid Kubernetes-fürt és a rajtuk futó számítási feladatok állapotának és erőforrás-felhasználásának gyűjtéséhez, Ismerje meg, [hogyan használhatja](container-insights-analyze.md) a Azure monitor for containers szolgáltatást.
