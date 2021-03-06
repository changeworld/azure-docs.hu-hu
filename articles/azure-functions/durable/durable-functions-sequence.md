---
title: Függvények láncolása Durable Functionsban – Azure
description: Megtudhatja, hogyan futtathat egy olyan Durable Functions mintát, amely függvények sorozatát hajtja végre.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562061"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Függvények láncolása Durable Functions-Hello Sequence minta

A függvények láncolása egy adott sorrendben végrehajtott függvények sorrendjének a mintáját jelöli. Egy függvény kimenetét gyakran egy másik függvény bemenetére kell alkalmazni. Ez a cikk a Durable Functions rövid útmutató ([C#](durable-functions-create-first-csharp.md) vagy [JavaScript](quickstart-js-vscode.md)) befejezése után létrehozott láncolási sorozatot ismerteti. További információ a Durable Functionsről: [Durable functions áttekintése](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>A függvények

Ez a cikk a minta alkalmazás következő funkcióit ismerteti:

* `E1_HelloSequence`: egy [Orchestrator függvény](durable-functions-bindings.md#orchestration-trigger) , amely egy sorozatban többször is meghívja a `E1_SayHello`. A `E1_SayHello`-hívások kimeneteit tárolja, és az eredményeket rögzíti.
* `E1_SayHello`: egy [tevékenység-függvény](durable-functions-bindings.md#activity-trigger) , amely paraméterként megadott egy "Hello" karakterláncot.
* `HttpStart`: egy HTTP által aktivált függvény, amely elindítja a Orchestrator egy példányát.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence Orchestrator függvény

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Az C# összes előkészítési függvénynek rendelkeznie kell egy `DurableOrchestrationContext`típusú paraméterrel, amely megtalálható a `Microsoft.Azure.WebJobs.Extensions.DurableTask` szerelvényben. Ez a környezeti objektum lehetővé teszi más *tevékenységi* függvények meghívását és a bemeneti paraméterek átadását a `CallActivityAsync` metódusának használatával.

A kód meghívja a `E1_SayHello` háromszor a különböző paraméterek értékeit. Az egyes hívások visszatérési értéke hozzáadódik a `outputs` listához, amelyet a függvény végén adnak vissza.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> A JavaScript-Durable Functions csak a 2,0 Runtime funkcióhoz érhető el.

#### <a name="functionjson"></a>function.json

Ha a Visual Studio Code-ot vagy a Azure Portalt használja a fejlesztéshez, itt látható a *function. JSON* fájl tartalma a Orchestrator függvényhez. A legtöbb Orchestrator *függvény. a JSON* -fájlok majdnem ugyanúgy néznek ki, mint ez.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

A lényeg a `orchestrationTrigger` kötés típusa. Az összes Orchestrator függvénynek ezt az trigger-típust kell használnia.

> [!WARNING]
> A Orchestrator függvények "nincs I/O" szabályának betartásához ne használjon semmilyen bemeneti vagy kimeneti kötést az `orchestrationTrigger` trigger kötésének használatakor.  Ha más bemeneti vagy kimeneti kötésekre van szükség, inkább a Orchestrator által meghívott `activityTrigger` függvények kontextusában kell használni őket. További információ: [Orchestrator Function Code megkötések](durable-functions-code-constraints.md) cikk.

#### <a name="indexjs"></a>index. js

A függvény a következő:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Minden JavaScript-előkészítési függvénynek tartalmaznia kell a [`durable-functions` modult](https://www.npmjs.com/package/durable-functions). Ez egy olyan könyvtár, amely lehetővé teszi Durable Functions a JavaScriptben való írását. Három jelentős különbség van egy összehangoló függvény és más JavaScript-függvények között:

1. A függvény egy [Generator függvény.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript).
2. A függvény a `durable-functions` modul `orchestrator` metódusának meghívásával van becsomagolva (itt `df`).
3. A függvénynek szinkronnak kell lennie. Mivel a "Orchestrator" metódus kezeli a "Context. Done" hívását, a függvénynek egyszerűen "Return" értéknek kell lennie.

A `context` objektum olyan `df` tartós előkészítési környezeti objektumot tartalmaz, amely lehetővé teszi más *tevékenységi* függvények meghívását és a bemeneti paraméterek átadását a `callActivity` metódus használatával. A kód meghívja a `E1_SayHello` háromszor a különböző paraméterek értékeit, `yield` használatával jelezve, hogy a végrehajtásnak várnia kell a visszaadott aszinkron tevékenység függvényének meghívására. Az egyes hívások visszatérési értéke hozzáadódik a `outputs` tömbhöz, amelyet a függvény végén adnak vissza.

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello Activity függvény

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

A tevékenységek a `ActivityTrigger` attribútumot használják. A megadott `IDurableActivityContext` használatával tevékenységekkel kapcsolatos műveleteket hajthat végre, például a bemeneti értéket `GetInput<T>`használatával érheti el.

`E1_SayHello` implementálása viszonylag triviális karakterlánc-formázási művelet.

Egy `IDurableActivityContext`kötése helyett közvetlenül a tevékenység függvénybe átadott típushoz köthető. Például:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.JSON

A *function. JSON* fájl a Activity függvényhez `E1_SayHello` hasonló a `E1_HelloSequence`hoz, azzal a különbséggel, hogy `orchestrationTrigger` kötési típus helyett `activityTrigger` kötési típust használ.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Az összehangoló függvények által hívott bármely függvénynek a `activityTrigger` kötést kell használnia.

`E1_SayHello` implementálása viszonylag triviális karakterlánc-formázási művelet.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

A JavaScript-figyelő függvényektől eltérően a tevékenység-függvények nem igényelnek speciális beállítást. A Orchestrator függvény által átadott bemenet a `context.bindings` objektumban található, az `activityTrigger` kötés neve alatt – ebben az esetben `context.bindings.name`. A kötési név beállítható az exportált függvény paramétereként, és közvetlenül is elérhető, ami a mintakód.

---

### <a name="httpstart-client-function"></a>HttpStart-ügyfél funkció

A Orchestrator függvény egy példányát elindíthatja egy ügyféloldali függvény használatával. A `HttpStart` HTTP által aktivált függvényt fogja használni a `E1_HelloSequence`példányainak elindításához.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

A feladatokkal való kommunikációhoz a függvénynek tartalmaznia kell egy `DurableClient` bemeneti kötést. A-ügyfelet egy előkészítés elindítására használhatja. Emellett segítséget nyújthat egy olyan HTTP-válasz visszaküldéséhez, amely URL-címeket tartalmaz az új hangolás állapotának ellenőrzéséhez.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function. JSON

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

A feladatokkal való kommunikációhoz a függvénynek tartalmaznia kell egy `durableClient` bemeneti kötést.

#### <a name="httpstartindexjs"></a>HttpStart/index. js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

`DurableOrchestrationClient` objektum beszerzéséhez használja a `df.getClient`. A-ügyfelet egy előkészítés elindítására használhatja. Emellett segítséget nyújthat egy olyan HTTP-válasz visszaküldéséhez, amely URL-címeket tartalmaz az új hangolás állapotának ellenőrzéséhez.

---

## <a name="run-the-sample"></a>Minta futtatása

A `E1_HelloSequence`-előkészítés végrehajtásához küldje el a következő HTTP POST-kérelmet a `HttpStart` függvénynek.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Az előző HTTP-kódrészlet feltételezi, hogy van egy bejegyzés a `host.json` fájlban, amely eltávolítja az alapértelmezett `api/` előtagot az összes HTTP-trigger függvény URL-címéből. A konfigurációhoz tartozó jelölést a minták `host.json` fájljában találja.

Ha például egy "myfunctionapp" nevű Function alkalmazásban futtatja a mintát, cserélje le a "{host}" kifejezést "myfunctionapp.azurewebsites.net" értékre.

Az eredmény egy HTTP 202-válasz, amely a következőhöz hasonlóan van (rövidítve):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Ezen a ponton a rendszer várólistára helyezi az előkészítést, és azonnal elindul. A `Location` fejlécben található URL-cím használható a végrehajtás állapotának vizsgálatára.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Az eredmény az előkészítés állapota. A rendszer gyorsan futtatja és befejezi, így a *befejezett* állapotú, a következőhöz hasonló választ kell látnia (rövidítve):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Amint láthatja, a példány `runtimeStatus` *befejeződik* , és a `output` a Orchestrator függvény végrehajtásának JSON-szerializált eredményét tartalmazza.

> [!NOTE]
> Hasonló indító logikát alkalmazhat más típusú triggerekhez, például `queueTrigger`hoz, `eventHubTrigger`hoz vagy `timerTrigger`hoz.

Tekintse meg a függvény-végrehajtási naplókat. A `E1_HelloSequence` függvény többször indult el és fejeződött be a hangvezérlés [megbízhatósága](durable-functions-orchestrations.md#reliability) című témakörben leírt Replay viselkedés miatt. Másfelől azonban a `E1_SayHello` három végrehajtása volt, mivel ezek a függvények végrehajtása nem kerül újra lejátszásra.

## <a name="next-steps"></a>További lépések

Ez a minta egy egyszerű függvény-láncolási előkészítést mutat be. A következő minta bemutatja, hogyan valósítható meg a ventilátor-out/Fan-in minta.

> [!div class="nextstepaction"]
> [A fan-out/Fan-in minta futtatása](durable-functions-cloud-backup.md)
