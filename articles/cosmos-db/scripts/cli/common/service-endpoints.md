---
title: Azure Cosmos-fiók létrehozása virtuális hálózati szolgáltatásbeli végpontokkal
description: Azure Cosmos-fiók létrehozása virtuális hálózati szolgáltatásbeli végpontokkal
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: ff700739e2f8c6330ea151dbe489332acea3238c
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275408"
---
# <a name="create-an-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Azure Cosmos-fiók létrehozása virtuális hálózati szolgáltatásbeli végpontokkal az Azure CLI használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0.73 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a példa egy új virtuális hálózatot hoz létre egy előtér-és háttér-alhálózattal, és `Microsoft.AzureCosmosDB`lehetővé teszi a szolgáltatási végpontok használatát. Ezután lekéri az alhálózat erőforrás-azonosítóját, és alkalmazza azt az Azure Cosmos-fiókra, és engedélyezi a fiókhoz tartozó szolgáltatási végpontokat.

> [!NOTE]
> Ez a példa egy SQL-(Core-) API-fiók használatát mutatja be. Ha más API-khoz szeretné használni ezt a mintát `enable-virtual-network` , `virtual-network-rules` alkalmazza a és a paramétereket az alábbi parancsfájlban az API-specifikus parancsfájlra.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints.sh "Create an Azure Cosmos account with service endpoints.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Létrehoz egy Azure-beli virtuális hálózatot. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Létrehoz egy alhálózatot egy Azure-beli virtuális hálózathoz. |
| [az Network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Egy Azure-beli virtuális hálózat alhálózatát adja vissza. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Létrehoz egy Azure Cosmos DB-fiókot. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

A Azure Cosmos DB CLI-vel kapcsolatos további információkért lásd: [Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

A CLI-szkriptek összes Azure Cosmos DB a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)található.
