---
title: Az Azure Kubernetes Service (ak)-fürt üzembe helyezésének figyelése | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti az előfizetésében már üzembe helyezett tárolók Azure Monitor az Azure Kubernetes Service-(ak-) fürtök figyelését.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8589ea71b5c7affadc61d5e4543f734a660ab543
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275450"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>A már üzembe helyezett Azure Kubernetes Service-(ak-) fürt figyelésének engedélyezése

Ez a cikk azt ismerteti, hogyan állíthat be Azure Monitor a tárolók számára az [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) -ben üzemeltetett felügyelt Kubernetes-fürtök figyelésére, amelyek már telepítve vannak az előfizetésében.

Engedélyezheti a már üzembe helyezett AK-fürtök figyelését a támogatott módszerek egyikével:

* Azure CLI
* Terraform
* [Azure monitor](#enable-from-azure-monitor-in-the-portal) vagy közvetlenül a Azure Portal [AK-fürtjéből](#enable-directly-from-aks-cluster-in-the-portal)
* A [megadott Azure Resource Manager sablonnal](#enable-using-an-azure-resource-manager-template) a Azure PowerShell parancsmaggal `New-AzResourceGroupDeployment` vagy az Azure CLI használatával.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portal](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Engedélyezze az Azure CLI használatával

A következő lépés lehetővé teszi, hogy az AKS-fürt Azure CLI-vel figyelését. Ebben a példában, nem kell egy létrehozása, vagy adjon meg egy meglévő munkaterületet. Ez a parancs leegyszerűsíti a folyamatot, egy alapértelmezett munkaterületet hoz létre az AKS-fürt előfizetés az alapértelmezett erőforráscsoportot, ha egy még nem létezik a régióban.  A létrehozott alapértelmezett munkaterület a *alapértelmezettmunkaterület-\<GUID >-\<régió >* formátumához hasonlít.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

A kimenet az alábbihoz hasonló lesz:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integrálás meglévő munkaterülettel

Ha inkább egy meglévő munkaterületet szeretne integrálni, hajtsa végre a következő lépéseket a `--workspace-resource-id` paraméterhez szükséges Log Analytics munkaterület teljes erőforrás-AZONOSÍTÓjának megadásához, majd futtassa a parancsot, hogy engedélyezze a figyelési bővítményt a megadott munkaterületen.  

1. Sorolja fel az összes olyan előfizetést, amelyhez hozzáféréssel rendelkezik a következő parancs használatával:

    ```azurecli
    az account list --all -o table
    ```

    A kimenet az alábbihoz hasonló lesz:

    ```output
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

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    A kimenetben keresse meg a munkaterület nevét, majd másolja az adott Log Analytics munkaterület teljes erőforrás-AZONOSÍTÓját a mező **azonosítója**alá.

4. Futtassa a következő parancsot a figyelési bővítmény engedélyezéséhez, és cserélje le a `--workspace-resource-id` paraméter értékét. A karakterlánc értékének idézőjelek közé kell esnie:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    A kimenet az alábbihoz hasonló lesz:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Engedélyezze a Terraform használatával

1. Adja hozzá a **oms_agent** kiegészítő profilt a meglévő [azurerm_kubernetes_cluster erőforráshoz](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Adja hozzá a [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) a Terraform dokumentációjának lépéseit követve.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Engedélyezés Azure Monitor a portálon

Ahhoz, hogy az AKS-fürt az Azure Portalon az Azure Monitor figyelését, tegye a következőket:

1. A Azure Portal válassza a **figyelő**elemet.

2. Válassza ki a **tárolókat** a listából.

3. A **figyelő-tárolók** lapon válassza a **nem figyelt fürtök**lehetőséget.

4. A nem figyelt fürtök listájában keresse meg a tárolót a listában, és kattintson az **Engedélyezés**gombra.   

5. Ha egy meglévő Log Analytics munkaterülettel rendelkezik, amely a fürttel azonos előfizetésben található, **Azure monitor a tárolók** lapon, válassza ki a kívánt elemet a legördülő listából.  
    A lista preselects az alapértelmezett munkaterületre és a helyre, amely az AKS-tároló üzembe helyezése az előfizetésben.

    ![AKS-tárolók elemzés, monitorozás engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Ha új Log Analytics munkaterületet szeretne létrehozni a figyelési adatok fürtből való tárolásához, kövesse az [log Analytics munkaterület létrehozása](../../azure-monitor/learn/quick-create-workspace.md)című témakör utasításait. Győződjön meg arról, a munkaterület ugyanabban az előfizetésben üzembe helyezett az AKS-tároló létrehozásához.

Miután engedélyezte a figyelés, a fürt mérőszámok megtekintéséhez nagyjából 15 percet igénybe vehet.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Közvetlenül az AK-fürtből történő engedélyezés a portálon

Ha közvetlenül szeretné engedélyezni a figyelést a Azure Portal egyik AK-fürtjéből, tegye a következőket:

1. Az Azure Portalon válassza a **Minden szolgáltatás** elemet.

2. Az erőforrások listájában kezdje el begépelni a **tárolókat**.  A szűrők megjelenítése a bemenetet a következők alapján.

3. Válassza a **Kubernetes Services**elemet.  

    ![A Kubernetes-szolgáltatások hivatkozás](./media/container-insights-onboard/portal-search-containers-01.png)

4. A tárolók listájában válassza ki a tárolót.

5. A tároló áttekintése lapon válassza a **tárolók figyelése**lehetőséget.  

6. Ha egy meglévő Log Analytics munkaterülettel rendelkezik, amely a fürttel azonos előfizetésben található, **Azure monitor a tárolók** lapon, válassza ki azt a legördülő listából.  
    A lista preselects az alapértelmezett munkaterületre és a helyre, amely az AKS-tároló üzembe helyezése az előfizetésben.

    ![AKS-tároló állapotfigyelésének engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Ha új Log Analytics munkaterületet szeretne létrehozni a figyelési adatok fürtből való tárolásához, kövesse az [log Analytics munkaterület létrehozása](../../azure-monitor/learn/quick-create-workspace.md)című témakör utasításait. Győződjön meg arról, a munkaterület ugyanabban az előfizetésben üzembe helyezett az AKS-tároló létrehozásához.

Miután engedélyezte a figyelés, körülbelül 15 percet, mielőtt megtekintheti a fürt működési adatokat vehet igénybe.

## <a name="enable-using-an-azure-resource-manager-template"></a>Engedélyezés Azure Resource Manager sablon használatával

Ez a módszer két JSON-sablont tartalmaz. Egy sablon határozza meg, a konfigurációt a figyelés, és a másik paraméterértékeket, hogy adja meg az alábbiakat tartalmazza:

* Az AKS tároló erőforrás-azonosítója.
* Az erőforráscsoport, amely a fürt üzemel.

>[!NOTE]
>A sablonhoz telepíteni szeretné ugyanabban az erőforráscsoportban a fürttel.
>

A Log Analytics munkaterületet létre kell hozni, mielőtt engedélyezi a figyelést a Azure PowerShell vagy a parancssori felület használatával. A munkaterület létrehozásához a [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), a [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy a [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)használatával állíthatja be.

Ha ismeri a sablon segítségével üzembe helyezni erőforrásokat fogalmát, lásd:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Az Azure CLI 2.0.59 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa `az --version`. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört.

### <a name="create-and-execute-a-template"></a>Hozzon létre, és hajtsa végre a sablon

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "type": "string",
          "metadata": {
            "description": "AKS Cluster Resource ID"
          }
        },
        "aksResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the AKS resource e.g. \"East US\""
          }
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Mentse ezt a fájlt **existingClusterOnboarding. JSON** néven egy helyi mappába.

3. A következő JSON-szintaxist illessze be a fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Szerkessze a **aksResourceId** és a **aksResourceLocation** értékeit az AK-fürt **AK-áttekintés** lapján található értékek használatával. A **workspaceResourceId** értéke a log Analytics munkaterület teljes erőforrás-azonosítója, amely tartalmazza a munkaterület nevét.

    Szerkessze a **aksResourceTagValues** értékeit, hogy azok megfeleljenek az AK-fürthöz megadott meglévő címkék értékének.

5. Mentse ezt a fájlt **existingClusterParam. JSON** néven egy helyi mappába.

6. Készen áll a sablon üzembe helyezésére.

   * A Azure PowerShell használatával történő üzembe helyezéshez használja az alábbi parancsokat a sablont tartalmazó mappában:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

       ```output
       provisioningState       : Succeeded
       ```

   * Az Azure CLI-vel való üzembe helyezéshez futtassa a következő parancsokat:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

       ```output
       provisioningState       : Succeeded
       ```

       Miután engedélyezte a figyelés, a fürt mérőszámok megtekintéséhez nagyjából 15 percet igénybe vehet.

## <a name="verify-agent-and-solution-deployment"></a>Az ügynök és a megoldás a telepítés ellenőrzése

Az ügynök *06072018* -es vagy újabb verziójával ellenőrizheti, hogy az ügynök és a megoldás telepítése is sikeres volt-e. Az ügynök korábbi verzióival csak az ügynök telepítésének ellenőrzéséhez.

### <a name="agent-version-06072018-or-later"></a>Az ügynök 06072018 vagy újabb verziójára

A következő paranccsal ellenőrizheti, hogy az ügynök telepítése sikeresen megtörtént.

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenet a következő, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

A megoldás üzembe helyezése ellenőrzéséhez futtassa a következő parancsot:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A kimenet a következő, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>06072018-nél korábbi ügynökverzió

A következő parancs futtatásával ellenőrizheti, hogy a Log Analytics ügynöknek a *06072018* -es verziójának megfelelő telepítését adta-e meg:  

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenet a következő, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:  

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Konfiguráció megtekintése a CLI-vel

A `aks show` parancs használatával olyan részleteket kaphat, mint például a megoldás engedélyezve vagy sem, mi a Log Analytics munkaterület resourceID, valamint a fürt összegző információi.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Néhány perc múlva a parancs befejeződik, és a megoldás kapcsolatos adatokat JSON formátumban adja vissza.  A parancs eredményét a monitorozási bővítményt profilt kell megjelennie, és hasonlít az alábbi példa kimenetében:

```output
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>További lépések

* Ha problémákat tapasztal a megoldás bevezetésére tett kísérlet során, tekintse át a [hibaelhárítási útmutatót](container-insights-troubleshoot.md) .

* Megtudhatja, [hogyan használhatja](container-insights-analyze.md) a Azure monitor a tárolók számára, hogy az AK-fürtök és a rajtuk futó munkaterhelések állapotát és erőforrás-kihasználtságát összegyűjtse.
