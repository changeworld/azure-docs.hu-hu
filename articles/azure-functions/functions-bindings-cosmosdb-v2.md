---
title: Azure Cosmos DB a 2. x függvények kötéseit
description: Megtudhatja, hogyan használhatja az Azure Cosmos DB-eseményindítók és kötések az Azure Functions szolgáltatásban.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605758"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Azure Cosmos DB trigger és kötések Azure Functions 2. x – áttekintés

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-bindings-cosmosdb.md)
> * [2-es verzió](functions-bindings-cosmosdb-v2.md)

Ez a cikk azt ismerteti, hogyan használhatók [Azure Cosmos db](../cosmos-db/serverless-computing-database.md) kötések Azure functions 2. x-ben. Az Azure Functions támogatja a-trigger, bemeneti és kimeneti kötések az Azure Cosmos DB.

| Műveletek | Típus |
|---------|---------|
| Függvény futtatása Azure Cosmos DB dokumentum létrehozásakor vagy módosításakor | [Eseményindító](./functions-bindings-cosmosdb-v2-trigger.md) |
| Azure Cosmos DB dokumentum beolvasása | [Bemeneti kötés](./functions-bindings-cosmosdb-v2-input.md) |
| Azure Cosmos DB dokumentum módosításainak mentése  |[Kimeneti kötés](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Ez a hivatkozás a [Azure functions 2. x verziójára](functions-versions.md)mutat.  További információ a kötések használatáról az 1. x függvényeknél: [Azure Cosmos db kötések Azure functions 1. x verzióhoz](functions-bindings-cosmosdb.md).
>
> Ennek a kötésnek a DocumentDB eredetileg neve. A functions 2. x verziójában a trigger, a kötések és a csomag neve Cosmos DB.

## <a name="supported-apis"></a>Támogatott API-k

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Hozzáadás a functions-alkalmazáshoz

### <a name="functions-2x-and-higher"></a>2\. x és újabb függvények

Az trigger és a kötések használata megköveteli, hogy a megfelelő csomagra hivatkozzon. A NuGet csomag a .NET-osztály könyvtáraihoz használatos, míg a kiterjesztési köteg minden más alkalmazás típusához használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A [NuGet-csomag], 3. x verziójának telepítése | |
| C#Parancsfájl, Java, JavaScript, Python, PowerShell | A [kiterjesztési csomag] regisztrálása          | Az [Azure-eszközök bővítmény] használata ajánlott a Visual Studio Code használatával. |
| C#Parancsfájl (csak online – Azure Portal)         | Kötés hozzáadása                            | Ha frissíteni szeretné a meglévő kötési bővítményeket anélkül, hogy újra közzé kellene tennie a Function alkalmazást, tekintse [Bővítmények frissítése]című témakört. |

[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[kiterjesztési csomag]: ./functions-bindings-register.md#extension-bundles
[Bővítmények frissítése]: ./install-update-binding-extensions-manual.md
[Azure-eszközök bővítmény]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A functions 1. x alkalmazások automatikusan hivatkoznak a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomagra, 2. x verzióra.

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása Azure Cosmos DB dokumentum létrehozásakor vagy módosításakor (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB dokumentum olvasása (bemeneti kötés)](./functions-bindings-cosmosdb-v2-input.md)
- [Azure Cosmos DB dokumentum módosításainak mentése (kimeneti kötés)](./functions-bindings-cosmosdb-v2-output.md)
