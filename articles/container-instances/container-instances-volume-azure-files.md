---
title: Azure Files kötet csatlakoztatása a tároló csoportjához
description: Megtudhatja, hogyan csatlakoztathat Azure Files kötetet az állapot megőrzéséhez Azure Container Instances
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f66890c503de8de9160f11fb28795012ae57daeb
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561337"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Azure-fájlmegosztás csatlakoztatása Azure Container Instances

Az Azure Container Instances alapértelmezetten állapot nélküli. Ha a tároló összeomlik vagy leáll, az összes állapota elvész. Ha azt szeretné, hogy az állapot a tároló élettartamán túl is megmaradjon, külső tárból kell csatlakoztatnia egy kötetet. Ahogy az ebben a cikkben is látható, Azure Container Instances csatlakoztathat [Azure Files](../storage/files/storage-files-introduction.md)használatával létrehozott Azure-fájlmegosztást. A Azure Files teljes körűen felügyelt, az Azure Storage-ban üzemeltetett fájlmegosztást kínál, amelyek az iparági szabványnak megfelelő SMB protokollon keresztül érhetők el. Az Azure-fájlmegosztás Azure Container Instances használatával olyan fájlmegosztási funkciókat biztosít, mint az Azure-fájlmegosztás Azure-beli virtuális gépekkel való használata.

> [!NOTE]
> Egy Azure Files-megosztás csatlakoztatása jelenleg csak Linux-tárolók számára engedélyezett. A platform aktuális eltéréseit az [áttekintésben](container-instances-overview.md#linux-and-windows-containers)találja.
>
> A Azure Files-megosztások tároló-példányhoz való csatlakoztatása hasonló a Docker- [kötés csatlakoztatásához](https://docs.docker.com/storage/bind-mounts/). Ügyeljen arra, hogy ha egy megosztást egy olyan tároló-könyvtárba csatlakoztat, amelyben a fájlok vagy a címtárak találhatók, akkor ezeket a fájlokat vagy címtárakat a csatlakoztatás elrejti, és a tároló futtatásakor nem érhetők el.
>

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Az Azure-fájlmegosztást először létre kell hoznia, hogy aztán megoszthassa az Azure Container Instances-példánnyal. Futtassa a következő szkriptet a fájlmegosztás tárolására szolgáló Storage-fiók létrehozásához, és magát a megosztást. A tárfiók nevének globálisan egyedinek kell lennie, ezért a szkript hozzáad egy véletlenszerű értéket az alapsztringhez.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Tároló hitelesítő adatainak lekérése

Ahhoz, hogy az Azure-fájlmegosztást kötetként csatlakoztassa az Azure Container Instancesben, három értékre lesz szüksége: a tárfiók nevére, a megosztás nevére és tárelérési kulcsra.

* **Storage-fiók neve** – ha az előző parancsfájlt használta, a rendszer a Storage-fiók nevét a `$ACI_PERS_STORAGE_ACCOUNT_NAME` változóban tárolja. A fiók nevének megtekintéséhez írja be a következőt:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Megosztás neve** – ez az érték már ismert (az előző parancsfájlban `acishare`ként van meghatározva)

* **Storage-fiók kulcsa** – ez az érték a következő parancs használatával érhető el:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Tároló üzembe helyezése és kötet csatlakoztatása – parancssori felület

Ha egy Azure-fájlmegosztást az Azure CLI használatával szeretne kötetként csatlakoztatni egy tárolóban, adja meg a megosztást és a kötet csatlakoztatási pontját, amikor létrehozza a tárolót az [az Container Create][az-container-create]paranccsal. Ha követte az előző lépéseket, a következő parancs használatával csatlakoztathatja a korábban létrehozott megosztást egy tároló létrehozásához:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

A `--dns-name-label` értékének egyedinek kell lennie abban az Azure-régióban, ahol létrehozza a Container-példányt. Frissítse az előző parancs értékét, ha a parancs végrehajtásakor a **DNS-név címkéje** hibaüzenetet kap.

## <a name="manage-files-in-mounted-volume"></a>Fájlok kezelése a csatlakoztatott köteten

A tároló elindítása után a Microsoft [ACI-hellofiles][aci-hellofiles] rendszerkép használatával üzembe helyezett egyszerű webalkalmazással hozhat létre kisméretű szövegfájlokat az Azure-fájlmegosztás számára a megadott csatlakoztatási útvonalon. Szerezze be a webalkalmazás teljes tartománynevét (FQDN) az az [Container show][az-container-show] paranccsal:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Miután az alkalmazással mentette a szöveget, használhatja a [Azure Portal][portal] vagy egy eszközt, például a [Microsoft Azure Storage Explorert][storage-explorer] a fájlmegosztás számára írt fájl vagy fájlok lekéréséhez és vizsgálatához.

## <a name="deploy-container-and-mount-volume---yaml"></a>Tároló üzembe helyezése és kötet csatlakoztatása – YAML

Emellett üzembe helyezhet egy tároló csoportot is, és csatlakoztathat egy kötetet egy tárolóban az Azure CLI-vel és egy [YAML-sablonnal](container-instances-multi-container-yaml.md). A YAML sablon általi üzembe helyezés előnyben részesített módszer a több tárolóból álló tároló csoportok telepítésekor.

A következő YAML-sablon definiál egy tároló csoportot egy `aci-hellofiles` képpel létrehozott tárolóval. A tároló csatlakoztatja a korábban kötetként létrehozott Azure-fájlmegosztás *acishare* . Ha meg van jelölve, adja meg a fájlmegosztást tároló Storage-fiók nevét és tárolási kulcsát. 

A CLI-példában a `dnsNameLabel` értéknek egyedinek kell lennie azon az Azure-régión belül, ahol létrehozza a Container-példányt. Ha szükséges, frissítse a YAML fájlban található értéket.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

A YAML sablonnal való üzembe helyezéshez mentse az előző YAML egy `deploy-aci.yaml`nevű fájlba, majd hajtsa végre az az [Container Create][az-container-create] parancsot a `--file` paraméterrel:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Tároló üzembe helyezése és a kötet csatlakoztatása – erőforrás-kezelő

A CLI és a YAML üzembe helyezése mellett üzembe helyezhet egy tároló csoportot, és egy Azure [Resource Manager-sablonnal](/azure/templates/microsoft.containerinstance/containergroups)csatlakoztathat egy kötetet egy tárolóhoz.

Először töltse ki a `volumes` tömböt a sablon `properties` szakaszának tároló csoportjában. 

Ezután minden olyan tárolóhoz, amelyhez csatlakoztatni szeretné a kötetet, töltse ki a `volumeMounts` tömböt a tároló definíciójának `properties` szakaszában.

A következő Resource Manager-sablon definiál egy tároló csoportot, amely egy, a `aci-hellofiles` képpel létrehozott tárolót hoz létre. A tároló csatlakoztatja a korábban kötetként létrehozott Azure-fájlmegosztás *acishare* . Ha meg van jelölve, adja meg a fájlmegosztást tároló Storage-fiók nevét és tárolási kulcsát. 

Ahogy az előző példákban is látható, a `dnsNameLabel` értéknek egyedinek kell lennie az Azure-régióban, ahol létrehozza a Container-példányt. Ha szükséges, frissítse a sablon értékét.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

A Resource Manager-sablonnal történő üzembe helyezéshez mentse az előző JSON-fájlt egy `deploy-aci.json`nevű fájlba, majd hajtsa végre az az [Group Deployment Create][az-group-deployment-create] parancsot a `--template-file` paraméterrel:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Több kötet csatlakoztatása

Ha több kötetet szeretne csatlakoztatni egy tároló-példányban, [Azure Resource Manager sablon](/azure/templates/microsoft.containerinstance/containergroups), YAML-fájl vagy más programozott módszer használatával kell telepítenie. Sablon vagy YAML-fájl használatához adja meg a megosztás részleteit, és adja meg a köteteket úgy, hogy kitölti a `volumes` tömböt a fájl `properties` szakaszában. 

Ha például két Azure Files *share1* és *share2* nevű megosztást hozott létre a Storage-fiók *MyStorageAccount*, akkor a Resource Manager-sablon `volumes` tömbje a következőhöz hasonlóan fog megjelenni:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Ezután a tároló csoport minden olyan tárolója esetében, amelyben a köteteket csatlakoztatni szeretné, töltse ki a `volumeMounts` tömböt a tároló definíciójának `properties` szakaszában. Így például a két kötetet csatlakoztatja, a *myvolume1* és a *myvolume2*, korábban definiálva:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Következő lépések

További mennyiségi típusok csatlakoztatása a Azure Container Instancesban:

* [EmptyDir-kötet csatlakoztatása Azure Container Instances](container-instances-volume-emptydir.md)
* [Gitrepo típusú-kötet csatlakoztatása Azure Container Instances](container-instances-volume-gitrepo.md)
* [Titkos kötet csatlakoztatása Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create