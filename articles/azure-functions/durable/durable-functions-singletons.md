---
title: Durable Functions – Azure
description: Az Durable Functions bővítmény használata Azure Functionshoz.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262809"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions)

A háttérben felmerülő feladatok esetében gyakran biztosítania kell, hogy egy adott Orchestrator csak egy példánya fusson egyszerre. Ezt a fajta egyedi viselkedést [Durable functions](durable-functions-overview.md) úgy is biztosíthatja, hogy egy adott Orchestrator rendel hozzá egy adott példányhoz, amikor létrehozza azt.

## <a name="singleton-example"></a>Egyszeres példa

Az alábbi példa egy olyan HTTP-trigger függvényt mutat be, amely létrehoz egy egypéldányos háttér-előkészítési feladatot. A kód biztosítja, hogy csak egy példány létezik egy adott példány-AZONOSÍTÓhoz.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

> [!NOTE]
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett `OrchestrationClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia `IDurableOrchestrationClient`helyett. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**function. JSON**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index. js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

---

Alapértelmezés szerint a példány-azonosítók véletlenszerűen generált GUID azonosítók. Az előző példában azonban a példány AZONOSÍTÓját átadja a rendszer az URL-ből származó útvonal-adatok között. A kód `GetStatusAsync`(C#) vagy `getStatus` (JavaScript) meghívásával ellenőrizze, hogy a megadott azonosítójú példány már fut-e. Ha nem fut ilyen példány, a rendszer létrehoz egy új példányt az AZONOSÍTÓval.

> [!NOTE]
> Ebben a példában a verseny feltétele lehetséges. Ha a **HttpStartSingle** két példánya egyidejű végrehajtást hajt végre, mindkét függvényhívás sikeres lesz, de a rendszer csak egy hanghívási példányt fog elindulni. A követelményektől függően előfordulhat, hogy ez nem lenne lehetséges mellékhatása. Ezért fontos annak biztosítása, hogy ne lehessen egyszerre két kérelmet végrehajtani az trigger-függvényt.

A Orchestrator függvény implementációjának részletei valójában nem számítanak. Ez lehet egy normál Orchestrator-függvény, amely megkezdi és befejeződik, vagy az is lehet, hogy örökre fut (azaz egy [örök](durable-functions-eternal-orchestrations.md)kialakítás). A lényeg az, hogy egyszerre csak egy példány fut egyszerre.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg a rendszerelőkészítések natív HTTP-funkcióit](durable-functions-http-features.md)
