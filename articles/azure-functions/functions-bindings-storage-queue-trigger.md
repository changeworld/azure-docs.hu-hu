---
title: Azure üzenetsor-tárolási trigger a Azure Functionshoz
description: Ismerje meg, hogyan futtathat Azure-függvényeket az Azure üzenetsor-tárolás adatváltozásaival.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277374"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Azure üzenetsor-tárolási trigger a Azure Functionshoz

A várólista-tároló-trigger egy függvényt futtat, ahogy az üzenetek bekerülnek az Azure üzenetsor-tárolóba.

## <a name="encoding"></a>Encoding

A függvények *Base64* kódolású karakterláncot várnak. A kódolási típus összes módosítását (az adatgyűjtés *Base64* kódolású karakterláncként való előkészítéséhez) a hívó szolgáltatásban kell végrehajtani.

## <a name="example"></a>Példa

A várólista-trigger használatával elindíthat egy függvényt, ha új elem érkezik egy várólistán. A várólista-üzenet bemenetként van megadva a függvénynek.

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely lekérdezi a `myqueue-items` várólistát, és minden alkalommal beírja a naplót, amikor a várólista-elemek feldolgozása történik.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az alábbi példa egy üzenetsor-trigger kötést mutat be egy *function. JSON* fájlban és [ C# a kötést használó script (. CSX)](functions-reference-csharp.md) kódban. A függvény lekérdezi a `myqueue-items` várólistát, és minden alkalommal egy naplót ír a várólista-elemek feldolgozásakor.

Itt látható a *function. JSON* fájl:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Íme a C#-szkriptkódot:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

A [használat](#usage) szakasz ismerteti `myQueueItem`, amelyet a function. json fájl `name` tulajdonsága nevez el.  Az [üzenet metaadatainak szakasza](#message-metadata) a többi megjelenített változót ismerteti.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy várólista-trigger kötését mutatja be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény lekérdezi a `myqueue-items` várólistát, és minden alkalommal egy naplót ír a várólista-elemek feldolgozásakor.

Itt látható a *function. JSON* fájl:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

> [!NOTE]
> A name paraméter azt a JavaScript-kódban `context.bindings.<name>`t tükrözi, amely tartalmazza a várólista-elemek hasznos adatait. Ezt a hasznos adatot a függvény második paramétereként is átadja.

A következő JavaScript-kódot:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

A [használat](#usage) szakasz ismerteti `myQueueItem`, amelyet a function. json fájl `name` tulajdonsága nevez el.  Az [üzenet metaadatainak szakasza](#message-metadata) a többi megjelenített változót ismerteti.

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa azt szemlélteti, hogyan lehet beolvasni egy függvénynek egy trigger használatával átadott üzenetsor-üzenetet.

A Storage-várólista triggere a *function. JSON* fájlban van definiálva, ahol a *Type* értéke `queueTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

A kód  *_\_init_\_. a. a. a. a. a.* a paramétert `func.ServiceBusMessage`ként deklarálja, amely lehetővé teszi a várólista-üzenet olvasását a függvényben.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

A következő Java-példa egy Storage üzenetsor-kiváltó függvényt mutat be, amely naplózza az elindított üzenetet az üzenetsor `myqueuename`ba.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat az üzenetsor-trigger konfigurálásához:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Az attribútum konstruktora a figyelni kívánt várólista nevét adja meg, ahogy az az alábbi példában is látható:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  A `Connection` tulajdonság beállításával megadhatja a használni kívánt Storage-fiók kapcsolódási karakterláncát tartalmazó Alkalmazásbeállítások értékét az alábbi példában látható módon:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Teljes példa: [példa](#example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Egy másik módszert biztosít a használni kívánt Storage-fiók megadására. A konstruktor egy olyan Alkalmazásbeállítás nevét veszi fel, amely egy tárolási kapcsolatot tartalmazó karakterláncot tartalmaz. Az attribútum a paramétert, a metódus vagy az osztály szintjén alkalmazhatók. Az alábbi példa bemutatja az osztály és metódust:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A használandó Storage-fiók a következő sorrendben van meghatározva:

* A `QueueTrigger` attribútum `Connection` tulajdonsága.
* A `StorageAccount` attribútum a `QueueTrigger` attribútummal megegyező paraméterre lett alkalmazva.
* A függvényre alkalmazott `StorageAccount` attribútum.
* Az osztályra alkalmazott `StorageAccount` attribútum.
* A "AzureWebJobsStorage" alkalmazás beállításai.

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `QueueTrigger` jegyzet hozzáférést biztosít a függvényt kiváltó várólistához. A következő példa az üzenetsor-üzenetet elérhetővé teszi a függvény számára a `message` paraméterrel.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Tulajdonság    | Leírás |
|-------------|-----------------------------|
|`name`       | Deklarálja a paraméter nevét a függvény aláírásában. A függvény indításakor ennek a paraméternek az értéke az üzenetsor üzenetének tartalmát jeleníti meg. |
|`queueName`  | Deklarálja a várólista nevét a Storage-fiókban. |
|`connection` | A Storage-fiók kapcsolódási karakterláncára mutat. |

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `QueueTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a| `queueTrigger`értékre kell állítani. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction**| n/a | Csak a *function. JSON* fájlban. `in`értékre kell állítani. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**név** | n/a |Annak a változónak a neve, amely a függvény kódjában található üzenetsor-elemek tartalmát tartalmazza.  |
|**queueName** | **QueueName**| A lekérdezni kívánt várólista neve. |
|**kapcsolat** | **Kapcsolat** |Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a `connection` "MyStorage" értékre állítja, a functions futtatókörnyezet egy "MyStorage" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Storage-kapcsolatok karakterláncot használja az `AzureWebJobsStorage`nevű alkalmazás-beállításban.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

Az üzenet adatai egy metódus-paraméter (például `string paramName`) használatával érhetők el. A következő típusokhoz köthető:

* Objektum – a függvények futtatókörnyezete deszerializál egy JSON-adattartalmat a kódban definiált tetszőleges osztály egy példányára. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Ha `CloudQueueMessage`hoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az üzenet adatai egy metódus-paraméter (például `string paramName`) használatával érhetők el. A `paramName` a *function. json*`name` tulajdonságában megadott érték. A következő típusokhoz köthető:

* Objektum – a függvények futtatókörnyezete deszerializál egy JSON-adattartalmat a kódban definiált tetszőleges osztály egy példányára. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Ha `CloudQueueMessage`hoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A várólista-elem hasznos tartalma `context.bindings.<NAME>`on keresztül érhető el, ahol a `<NAME>` megegyezik a *function. JSON*fájlban megadott névvel. Ha a hasznos adat JSON, az érték deszerializálása egy objektumba történik.

# <a name="python"></a>[Python](#tab/python)

Nyissa meg az üzenetsor-üzenetet a [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python)típussal megadott paraméterrel.

# <a name="java"></a>[Java](#tab/java)

A [QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) jegyzet hozzáférést biztosít a függvényt kiváltó üzenetsor-üzenethez.

---

## <a name="message-metadata"></a>Üzenet metaadatai

A várólista-trigger számos [metaadat-tulajdonságot](./functions-bindings-expressions-patterns.md#trigger-metadata)biztosít. Ezek a tulajdonságok a kötési kifejezésekben való használata más kötések részeként vagy a kód paramétereiben használható. A tulajdonságok a [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) osztály tagjai.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`QueueTrigger`|`string`|Várólista-adattartalom (ha érvényes karakterlánc). Ha az üzenetsor-üzenet hasznos karakterlánc, `QueueTrigger` a *function. JSON*fájl `name` tulajdonsága által megnevezett változóval megegyező értékkel rendelkezik.|
|`DequeueCount`|`int`|Azon alkalmak száma, amikor az üzenet el lett küldve.|
|`ExpirationTime`|`DateTimeOffset`|Az az időpont, ameddig az üzenet lejár.|
|`Id`|`string`|Üzenetsor-üzenet azonosítója.|
|`InsertionTime`|`DateTimeOffset`|Az az idő, ameddig az üzenet hozzá lett adva a várólistához.|
|`NextVisibleTime`|`DateTimeOffset`|Az az időpont, amikor a következő üzenet látható lesz.|
|`PopReceipt`|`string`|Az üzenet pop-nyugtája.|

## <a name="poison-messages"></a>Üzenetek megmérgezve

Ha a várólista-aktiválási függvény meghiúsul, Azure Functions újrapróbálkozik a függvényt egy adott üzenetsor-üzenetnél akár ötször is, az első próbálkozást is beleértve. Ha mind az öt kísérlet meghiúsul, a functions Runtime egy *&lt;originalqueuename >-Poison*nevű várólistába helyez egy üzenetet. Írhat egy függvényt, amely az üzenetek törlését végzi a méreg-várólistából úgy, hogy naplózza azokat, vagy értesítést küld, amely manuális beavatkozást igényel.

Ha manuálisan szeretné kezelni a Megmérgező üzeneteket, keresse meg az üzenetsor [dequeueCount](#message-metadata) .

## <a name="polling-algorithm"></a>Lekérdezési algoritmus

A várólista-trigger egy véletlenszerű exponenciális visszakapcsolási algoritmust valósít meg, amely csökkenti az üresjárati üzenetsor lekérdezésének hatását a tárolási tranzakciós költségekre.

Az algoritmus a következő logikát használja:

- Ha a rendszer egy üzenetet talál, a futtatókörnyezet két másodpercet vár, majd egy másik üzenetet keres.
- Ha nem talál üzenetet, a rendszer körülbelül négy másodpercig vár, mielőtt újra próbálkozik.
- A várakozási sor üzenetének későbbi sikertelen próbálkozásai után a várakozási idő továbbra is növekszik, amíg el nem éri a maximális várakozási időt, ami alapértelmezés szerint egy percet mutat.
- A maximális várakozási idő a [Host. JSON fájl](functions-host-json.md#queues)`maxPollingInterval` tulajdonságán keresztül konfigurálható.

Helyi fejlesztés esetén a maximális lekérdezési időköz alapértelmezett értéke két másodperc.

A számlázást illetően a futtatókörnyezet által végzett lekérdezés "ingyenes", és nem számít bele a fiókjába.

## <a name="concurrency"></a>Egyidejűség

Ha a várólista-üzenetek több várólistára várnak, a várólista-trigger lekéri az üzenetek kötegét, és egyidejűleg hívja meg a függvények feldolgozását. Alapértelmezés szerint a köteg mérete 16. Ha a feldolgozás alatt álló szám 8, a futtatókörnyezet lekéri egy másik köteget, és elindítja az üzenetek feldolgozását. Így az egyazon virtuális gépeken (VM-ben) végrehajtott, egyidejű feldolgozás alatt álló üzenetek maximális száma 24. Ez a korlát külön vonatkozik az egyes virtuális gépeken futó minden egyes üzenetsor által aktivált függvényre. Ha a függvény alkalmazása több virtuális gépre is méretezhető, minden virtuális gép megvárja az eseményindítókat, és megkísérli futtatni a függvényeket. Ha például egy függvény alkalmazás 3 virtuális gépre van kibővítve, az egy üzenetsor által aktivált függvény egyidejű példányainak alapértelmezett maximális száma 72.

A köteg mérete és az új köteg beolvasásának küszöbértéke a [Host. JSON fájlban](functions-host-json.md#queues)állítható be. Ha a függvény alkalmazásban a várólista által aktivált függvények párhuzamos végrehajtását szeretné csökkenteni, beállíthatja a Batch méretét 1-re. Ez a beállítás csak akkor teszi feleslegessé a párhuzamosságot, ha a Function alkalmazás egyetlen virtuális gépen (VM) fut. 

A várólista-trigger automatikusan megakadályozza, hogy a függvény többször dolgozza fel a várólista-üzeneteket; a függvények nem írhatók idempotens.

## <a name="hostjson-properties"></a>a Host. JSON tulajdonságai

A [Host. JSON](functions-host-json.md#queues) fájl olyan beállításokat tartalmaz, amelyek vezérlik a várólista-trigger működését. A rendelkezésre álló beállításokkal kapcsolatos részletekért tekintse meg a [Host. JSON-beállítások](functions-bindings-storage-queue-output.md#hostjson-settings) szakaszt.

## <a name="next-steps"></a>Következő lépések

- [Írási várólista tárolási üzenetei (kimeneti kötés)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
