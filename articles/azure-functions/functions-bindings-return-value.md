---
title: Visszatérési érték használata egy Azure-függvényből
description: További információ a Azure Functions visszatérési értékeinek kezeléséről
author: craigshoemaker
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 7ba104e288204dfbf3d24f5783bf69682a286553
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480569"
---
# <a name="using-the-azure-function-return-value"></a>Az Azure Function Return értékének használata

Ez a cikk azt ismerteti, hogyan működnek a visszatérési értékek egy függvényen belül.

A visszatérési értékkel rendelkező nyelveken a függvény [kimeneti kötését](./functions-triggers-bindings.md#binding-direction) a visszatérési értékhez kötheti:

* Egy C# osztály-függvénytárban alkalmazza a kimeneti kötési attribútumot a metódus visszatérési értékére.
* A javában alkalmazza a kimeneti kötési jegyzetet a Function metódusra.
* Más nyelveken a *function. JSON* fájl `name` tulajdonságát állítsa `$return`.

Ha több kimeneti kötés is van, akkor a visszatérési értéket csak az egyikre használja.

A C# és C# a parancsfájlban az adatok kimeneti kötésbe való küldésének alternatív módjai `out` paraméterek és [gyűjtő objektumok](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az alábbi C# kód egy kimeneti kötés visszatérési értékét használja, amelyet egy aszinkron példa követ:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Itt látható a *function. JSON* fájlban található kimeneti kötés:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Itt látható a C# szkript kódja, amelyet egy aszinkron példa követ:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="ftabfsharp"></a>[F#](#tab/fsharp)

Itt látható a *function. JSON* fájlban található kimeneti kötés:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Íme a F# kódot:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Itt látható a *function. JSON* fájlban található kimeneti kötés:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

A JavaScriptben a visszatérési érték a `context.done`második paramétere:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Itt látható a *function. JSON* fájlban található kimeneti kötés:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Itt látható a Python-kód:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="javatabjava"></a>[Java](#tab/java)

A következő Java-kód egy kimeneti kötés visszatérési értékét használja:

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Functions kötési hibák kezelése](./functions-bindings-errors.md)
