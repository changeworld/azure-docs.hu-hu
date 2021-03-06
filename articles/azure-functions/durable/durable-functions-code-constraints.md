---
title: Tartós Orchestrator-kódokra vonatkozó korlátozások – Azure Functions
description: A koordináló függvény újrajátszása és a kód megkötései az Azure Durable Functions számára.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4ed604302ca187ad4953e865d68dc73030a37c02
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562139"
---
# <a name="orchestrator-function-code-constraints"></a>Orchestrator megkötések

Durable Functions a [Azure functions](../functions-overview.md) kiterjesztése, amely lehetővé teszi az állapot-nyilvántartó alkalmazások kiépítését. A [Orchestrator függvénnyel](durable-functions-orchestrations.md) más tartós függvények végrehajtását is elvégezheti a Function alkalmazásban. A Orchestrator-függvények állapot-, megbízható és potenciálisan hosszú ideig futók.

## <a name="orchestrator-code-constraints"></a>Vezénylői kódkorlátozások

A Orchestrator függvények az [események beszerzését](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) használják a megbízható végrehajtás és a helyi változó állapotának fenntartása érdekében. A Orchestrator kód [újrajátszása](durable-functions-orchestrations.md#reliability) a Orchestrator függvényben írható kód típusára vonatkozó korlátozásokat hoz létre. A Orchestrator functions-nek például *determinisztikus*kell lennie: egy Orchestrator függvény többször is le lesz játszva, és minden alkalommal ugyanazt az eredményt kell létrehoznia.

### <a name="using-deterministic-apis"></a>Determinisztikus API-k használata

Ez a szakasz néhány egyszerű útmutatót tartalmaz, amelyek segítségével biztosíthatja a kód determinisztikus.

A Orchestrator függvények bármilyen API-t hívhatnak meg a cél nyelvein. Fontos azonban, hogy a Orchestrator functions csak a determinisztikus API-kat hívja meg. A *DETERMINISZTIKUS API* egy olyan API, amely mindig ugyanazokat az értékeket adja vissza, mint az azonos bemenet, függetlenül attól, hogy mikor vagy milyen gyakran hívják.

A következő táblázat példákat mutat be olyan API-kra, amelyeket el kell kerülnie, mert *nem* determinisztikus. Ezek a korlátozások csak a Orchestrator függvényekre érvényesek. Más függvények típusai nem rendelkeznek ilyen korlátozásokkal.

| API-kategória | Ok | Áthidaló megoldás |
| ------------ | ------ | ---------- |
| Dátumok és időpontok  | Az aktuális dátumot vagy időpontot visszaadó API-k determinált, mert a visszaadott érték eltér az egyes visszajátszás esetén. | Használja a`CurrentUtcDateTime` API-t a .NET-ben vagy a `currentUtcDateTime` API-ban a JavaScriptben, amely biztonságos a Replay számára. |
| GUID azonosítók és UUID-ket  | A véletlenszerű GUID vagy UUID értéket visszaadó API-k determinált, mert a generált érték különbözik az egyes visszajátszás esetén. | Használjon `NewGuid` a .NET-ben, vagy a JavaScriptben `newGuid` a véletlenszerű GUID azonosítók biztonságos létrehozásához. |
| Véletlenszerű számok | A véletlenszerű számokat visszaadó API-k determinált, mert a generált érték különbözik az egyes ismétlésekhez. | Egy tevékenység függvénnyel véletlenszerű számokat adhat vissza egy előkészítési művelethez. A tevékenység-függvények visszatérési értékei mindig biztonságosak a visszajátszás esetén. |
| Kötések | A bemeneti és kimeneti kötések általában I/O-műveletek, és determinált. A Orchestrator függvénynek nem szabad közvetlenül használnia még a koordináló [ügyfelet](durable-functions-bindings.md#orchestration-client) és az [entitás-ügyfél](durable-functions-bindings.md#entity-client) kötéseit. | Bemeneti és kimeneti kötések használata az ügyfélen vagy a tevékenységi függvényeken belül. |
| Network (Hálózat) | A hálózati hívások külső rendszereket érintenek, és determinált. | Hálózati hívások létrehozásához használja a Activity functions funkciót. Ha HTTP-hívást kell létrehoznia a Orchestrator függvényből, használhatja a [tartós http API-kat](durable-functions-http-features.md#consuming-http-apis)is. |
| API-k blokkolása | Az olyan API-k blokkolása, mint a .NET és a hasonló API-k `Thread.Sleep`, a Orchestrator függvények teljesítmény-és skálázási problémáit okozhatják, és kerülendő A Azure Functions fogyasztási tervben a szükségtelen futtatókörnyezeti díjakat is eredményezhetnek. | A rendelkezésre álló API-k blokkolására alternatívákat használhat. Például a `CreateTimer` használatával késleltetheti a hangelőkészítési végrehajtást. A [tartós időzítő](durable-functions-timers.md) késései nem számítanak bele egy Orchestrator függvény végrehajtási idejébe. |
| Aszinkron API-k | A Orchestrator-kódnak soha nem kell elindítania az aszinkron műveleteket, kivéve a `IDurableOrchestrationContext` API vagy a `context.df` objektum API-ját használva. Nem használhatja például `Task.Run`, `Task.Delay`és `HttpClient.SendAsync` a .NET-ben, illetve a `setTimeout` és a `setInterval` JavaScriptben. Az állandó feladatok keretrendszere egyetlen szálon futtatja a Orchestrator-kódot. Más aszinkron API-k által hívható más szálakkal nem tud működni. | A Orchestrator függvény csak tartós aszinkron hívásokat hajt végre. A Activity functions szolgáltatásnak más aszinkron API-hívásokat kell végeznie. |
| Aszinkron JavaScript-függvények | A JavaScript Orchestrator függvények nem deklarálható `async`ként, mert a Node. js futtatókörnyezet nem garantálja, hogy az aszinkron függvények determinisztikus. | A JavaScript-Orchestrator funkcióinak deklarálása szinkron létrehozó függvényekként. |
| Többszálú API-k | Az állandó feladat-keretrendszer egyetlen szálon futtatja az Orchestrator-kódot, és nem tud más szálakkal kommunikálni. Az új szálaknak egy előkészítési folyamatba való bevezetéséhez determinált végrehajtás vagy holtpont is vezethet. | A Orchestrator függvények szinte soha nem használhatnak többszálú API-kat. A .NET-ben például ne használja a `ConfigureAwait(continueOnCapturedContext: false)`; Ez biztosítja a feladat folytatását a Orchestrator függvény eredeti `SynchronizationContext`ján. Ha ilyen API-k szükségesek, csak a tevékenységi funkciókra korlátozzák a használatukat. |
| Statikus változók | Kerülje a nem állandó statikus változók használatát a Orchestrator függvények esetében, mert az értékek idővel változhatnak, ami a determinált futásidejű viselkedését eredményezi. | Használjon állandókat, vagy korlátozza a statikus változók használatát a tevékenység funkcióihoz. |
| Környezeti változók | Ne használjon környezeti változókat a Orchestrator functions szolgáltatásban. Az értékek idővel változhatnak, ami a determinált futásidejű viselkedését eredményezi. | A környezeti változókat csak az ügyfél-vagy a tevékenységi függvényekből kell hivatkozni. |
| Végtelen hurkok | Kerülje a végtelen hurkokat a Orchestrator functions szolgáltatásban. Mivel a tartós feladatok keretrendszere a folyamat előrehaladásának előrehaladtával menti a végrehajtási előzményeket, egy végtelen hurok miatt előfordulhat, hogy egy Orchestrator-példány elfogyott a memóriából. | Végtelen hurkos forgatókönyvek esetén használjon olyan API-kat, mint például a .NET vagy a `continueAsNew` a JavaScriptben a függvény végrehajtásának újraindításához és a korábbi végrehajtási előzmények elvetéséhez `ContinueAsNew`. |

Habár a megkötések alkalmazása megnehezíti az első lépéseket, a gyakorlatban könnyen követhető.

A tartós feladat-keretrendszer megkísérli az előző szabályok megsértésének észlelését. Ha jogsértést talál, a keretrendszer **NonDeterministicOrchestrationException** kivételt jelez. Ez az észlelési viselkedés azonban nem fogja tudni felfogni az összes megsértést, és nem függ tőle.

## <a name="versioning"></a>Verziókezelés

A tartós előkészítést a napok, hónapok, évek vagy akár [örökké](durable-functions-eternal-orchestrations.md)is futtathatja. A nem befejezetlen rendszerfolyamatokat befolyásoló Durable Functions alkalmazások által végrehajtott minden kód megszakadhat, ha megszakítja az újrajátszás viselkedését. Ezért fontos, hogy körültekintően tervezze meg a kód frissítését. A kód verziószámozásának részletes ismertetését a [verziószámozást ismertető cikkben](durable-functions-versioning.md)találja.

## <a name="durable-tasks"></a>Tartós feladatok

> [!NOTE]
> Ez a szakasz a tartós feladatok keretrendszerének belső megvalósítási részleteit ismerteti. Ezen információk ismerete nélkül is használhat tartós függvényeket. Ez csak az újrajátszás viselkedésének megismerésére szolgál.

A Orchestrator függvények biztonságos várakozási feladatait időnként *tartós feladatoknak*nevezzük. A tartós feladatok keretrendszere létrehozza és kezeli ezeket a feladatokat. Ilyenek például a **CallActivityAsync**, a **WaitForExternalEvent**és a **CreateTimer** által visszaadott feladatok a .net Orchestrator függvényekben.

Ezek a tartós feladatok belsőleg kezelhetők a .NET-ben lévő `TaskCompletionSource` objektumok listájával. Az újrajátszás során ezek a feladatok a Orchestrator-kód végrehajtásának részeként jönnek létre. Készen állnak, mert a diszpécser enumerálja a megfelelő előzményi eseményeket.

A feladatokat a rendszer szinkron módon hajtja végre egyetlen szál használatával, amíg az összes előzmény újra nem lett játszva. Az előzmények végének újrajátszása által nem befejezett tartós feladatok megfelelő műveleteket hajtanak végre. Előfordulhat például, hogy egy várólistán lévő hívni egy üzenetet.

Ez a szakasz a futásidejű viselkedés leírásában segít megérteni, hogy miért nem használható a Orchestrator függvény `await` vagy `yield` nem tartós feladatban. Két ok van: a diszpécser szál nem várja meg, amíg a feladat befejeződik, és a feladat visszahívása valószínűleg megsértheti a Orchestrator függvény nyomkövetési állapotát. A rendszer bizonyos futásidejű ellenőrzéseket végez a fenti szabálysértések észlelése érdekében.

Ha többet szeretne megtudni arról, hogy a tartós feladat-keretrendszer hogyan hajtja végre a Orchestrator functions szolgáltatást, tekintse [meg a tartós feladat forráskódját a githubon](https://github.com/Azure/durabletask). Különösen lásd: [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) és [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan hívhat meg alfolyamatokat](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Útmutató a verziószámozás kezeléséhez](durable-functions-versioning.md)
