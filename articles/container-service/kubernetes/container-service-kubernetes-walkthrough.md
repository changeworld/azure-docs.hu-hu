---
title: ELAVULT Rövid útmutató – Azure Kubernetes-fürt Linux rendszerhez
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre az Azure CLI segítségével Kubernetes-fürtöt Linux-tárolók esetén az Azure Container Service-ben.
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: cfd0f8a9a3180b14b4da9dc61e252054fe06628c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274173"
---
# <a name="deprecated-deploy-kubernetes-cluster-for-linux-containers"></a>ELAVULT Kubernetes-fürt üzembe helyezése Linux-tárolók esetén

> [!TIP]
> Az Azure Kubernetes szolgáltatást használó rövid útmutató frissített verziójáért lásd: gyors [üzembe helyezés Azure Kubernetes-szolgáltatás (ak) típusú fürt](../../aks/kubernetes-walkthrough.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Ebben a rövid útmutatóban egy Kubernetes-fürtöt helyezünk üzembe az Azure CLI használatával. Ezután egy webes előtérrendszert és egy Redis-példányt magában foglaló többtárolós alkalmazást helyezünk üzembe és futtatunk a fürtön. Miután végeztünk ezzel, az alkalmazás elérhető lesz az interneten. 

Az ebben a dokumentumban használt mintaalkalmazás Python nyelven van megírva. Az itt ismertetett fogalmakkal és lépésekkel bármely tárolórendszerkép üzembe helyezhető egy Kubernetes-fürtön. A projekthez kapcsolódó kód, Docker-fájl és előre létrehozott Kubernetes-jegyzékfájlok a [GitHubon](https://github.com/Azure-Samples/azure-voting-app-redis.git) érhetőek el.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-vote.png)

Ez a rövid útmutató feltételezi, hogy ismeri a Kubernetes alapvető fogalmait. A Kubernetesszel kapcsolatos részletes információkért lásd a [Kubernetes dokumentációját]( https://kubernetes.io/docs/home/).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westeurope* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Kimenet:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Kubernetes-fürt létrehozása az Azure Container Service szolgáltatásban az [az acs create](/cli/azure/acs#az-acs-create) parancs használatával. A következő példa egy *myK8sCluster* nevű fürtöt hoz létre egy Linux-főcsomóponttal és három Linux-ügyfélcsomóponttal.

```azurecli-interactive
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8sCluster --generate-ssh-keys
```

Egyes esetekben – például korlátozott próbaverziónál – az Azure-előfizetés korlátozott hozzáféréssel rendelkezik az Azure-erőforrásokhoz. Ha az üzembe helyezés az elérhető magok korlátozott száma miatt hiúsul meg, csökkentse az alapértelmezett ügynökök számát az `--agent-count 1` az [az acs create](/cli/azure/acs#az-acs-create) parancshoz történő hozzáadásával. 

Néhány perc múlva befejeződik a parancs végrehajtása, és visszaadja a fürttel kapcsolatos adatokat JSON formátumban. 

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Kubernetes-fürtök kezeléséhez használja a [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) eszközt, a Kubernetes parancssori ügyfelét. 

Ha az Azure Cloud Shellt használja, a kubectl már telepítve van. Ha helyileg szeretné telepíteni, használja az [az acs kubernetes install-cli](/cli/azure/acs/kubernetes) parancsot.

A kubectl a Kubernetes-fürthöz való csatlakozásra konfigurálásához futtassa az [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) parancsot. Ebben a lépésben a rendszer hitelesítő adatokat tölt le, és konfigurálja a Kubernetes parancssori felületét azok használatára.

```azurecli-interactive
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

Kimenet:

```output
NAME                    STATUS                     AGE       VERSION
k8s-agent-14ad53a1-0    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-1    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-2    Ready                      10m       v1.6.6
k8s-master-14ad53a1-0   Ready,SchedulingDisabled   10m       v1.6.6
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes-jegyzékfájl meghatározza a fürt célállapotát, például azt, hogy milyen tárolórendszerképeknek kell futniuk. Ebben a példában egy jegyzékfájlt használunk az Azure Vote alkalmazás futtatásához szükséges összes objektum létrehozásához. 

Hozzon létre egy `azure-vote.yml` nevű fájlt, és másolja bele a következő YAML-kódot. Ha az Azure Cloud Shellben dolgozik, ez a fájl a vi vagy a Nano segítségével hozható létre, ugyanúgy, mint egy virtuális vagy fizikai rendszeren.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Az alkalmazást a [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) paranccsal futtathatja.

```console
kubectl create -f azure-vote.yml
```

Kimenet:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a rendszer létrehoz egy [Kubernetes-szolgáltatást](https://kubernetes.io/docs/concepts/services-networking/service/), amely közzéteszi az alkalmazás-előteret az interneten. A folyamat eltarthat pár percig. 

A folyamat állapotának monitorozásához használja [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot a `--watch` argumentummal.

```console
kubectl get service azure-vote-front --watch
```

Kezdetben az **azure-vote-front** szolgáltatás *EXTERNAL-IP* értéke *pending* állapotú. Miután az EXTERNAL-IP cím *pending* állapotról egy *IP-címre* változik, a `CTRL-C` billentyűparanccsal állítsa le a kubectl figyelési folyamatát. 
  
```output
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Most a külső IP-címre léphet az Azure Vote alkalmazás megtekintéséhez.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-vote.png)  

## <a name="delete-cluster"></a>A fürt törlése
Ha a fürtre már nincs szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban előre létrehozott tárolórendszerképekkel hoztunk létre egy üzemelő Kubernetes-példányt. A kapcsolódó alkalmazáskód, Docker-fájl és Kubernetes-jegyzékfájl a GitHubon érhetőek el.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Kubernetes-fürtöt és azon egy többtárolós alkalmazást helyezett üzembe. 

Az Azure Container Service-szel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [ACS-alapú Kubernetes-fürt kezelése](./container-service-tutorial-kubernetes-prepare-app.md)
