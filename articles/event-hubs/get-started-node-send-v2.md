---
title: Események küldése vagy fogadása az Azure Event Hubs a JavaScript használatával (legújabb)
description: Ez a cikk egy olyan JavaScript-alkalmazás létrehozásának bemutatóját ismerteti, amely az Azure-Event Hubs a legújabb Azure/Event-hubok 5-ös verziójának használatával küld/fogad eseményeket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: e296ae36eeeb816d8704ab03824f8cbb80082ea6
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163007"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs-version-5"></a>Események küldése vagy fogadása az Event hubokból a JavaScript használatával (Azure/Event-hubok 5-ös verzió)
Ez a rövid útmutató bemutatja, hogyan lehet eseményeket küldeni és fogadni az Event hub eseményeiről az **Azure/Event-hubok 5. verziójú JavaScript-** csomag használatával. 

> [!IMPORTANT]
> Ez a rövid útmutató az Azure/Event-hubok 5. verziójának csomagját használja. A régi Azure/Event-hubok 2-es verziójú csomagot használó gyors üzembe helyezéssel kapcsolatban lásd: [események küldése és fogadása az Azure/Event-hubok 2. verziójának használatával](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Előfeltételek
Ha még nem ismeri az Azure Event Hubs-t, a rövid útmutató elvégzése előtt tekintse meg a [Event Hubs áttekintése](event-hubs-about.md) című témakört. 

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- **Microsoft Azure előfizetés**. Az Azure-szolgáltatások, például az Azure Event Hubs használatához előfizetésre van szükség.  Ha még nem rendelkezik Azure-fiókkal, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/free/) , vagy a [fiók létrehozásakor](https://azure.microsoft.com)használhatja az MSDN-előfizetői előnyeit.
- A Node. js 8. x vagy újabb verziója. Töltse le a legújabb [hosszú távú támogatási (LTS) verziót](https://nodejs.org).  
- Visual Studio Code (ajánlott) vagy bármely más integrált fejlesztési környezet (IDE).  
- Aktív Event Hubs névtér és Event hub. A létrehozásához hajtsa végre a következő lépéseket: 

   1. A [Azure Portal](https://portal.azure.com)hozzon létre egy *Event Hubs*típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyeket az alkalmazásnak az Event hub használatával kell kommunikálnia. 
   1. A névtér és az Event hub létrehozásához kövesse az alábbi utasításokat [: az Event hub létrehozása a Azure Portal használatával](event-hubs-create.md).
   1. Folytassa az ebben a rövid útmutatóban található utasításokat követve. 
   1. Az Event hub-névtér kapcsolati karakterláncának lekéréséhez kövesse a [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)című témakör utasításait. Jegyezze fel a kapcsolódási karakterláncot, hogy a rövid útmutatóban később használhassa.
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást. Ezután szerezze be a **Event Hubs névtérhez tartozó kapcsolatok karakterláncot** a cikk utasításait követve: a [kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A rövid útmutató későbbi részében használja a kapcsolatok karakterláncát.

### <a name="install-the-npm-package"></a>A NPM-csomag telepítése
Ha Event Hubshoz szeretné telepíteni a [Node Package Manager-(NPM-) csomagot](https://www.npmjs.com/package/@azure/event-hubs), nyisson meg egy parancssort, amely az elérési út *NPM* rendelkezik, módosítsa a könyvtárat arra a mappára, ahol meg szeretné őrizni a mintákat, majd futtassa a következő parancsot:

```shell
npm install @azure/event-hubs
```

A fogadó oldalon két további csomagot kell telepítenie. Ebben a rövid útmutatóban az Azure Blob Storage-t használja az ellenőrzőpontok megőrzéséhez, hogy a program ne olvassa be a már elolvasott eseményeket. Metaadat-ellenőrzőpontokat hajt végre a fogadott üzenetekben, rendszeres időközönként egy blobban. Ezzel a módszerrel egyszerűen folytathatja az üzenetek fogadását, ahonnan abbahagyta a kapcsolatot.

Futtassa az alábbi parancsot:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Események küldése

Ebben a szakaszban egy JavaScript-alkalmazást hoz létre, amely eseményeket küld az Event hub-nak.

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com)-ot.
1. Hozzon létre egy *Send. js*nevű fájlt, és illessze be a következő kódot:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. A kódban használja a valós értékeket a következők lecseréléséhez:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. `node send.js` futtatása a fájl végrehajtásához. Ez a parancs három eseményből álló köteget küld az Event hub-nak.
1. A Azure Portal ellenőrizze, hogy az Event hub fogadta-e az üzeneteket. A **metrikák** szakaszban váltson az **üzenetek** nézetre. Frissítse a lapot a diagram frissítéséhez. Ez eltarthat néhány másodpercig, hogy megjelenjen az üzenetek fogadása.

    [![annak ellenőrzése, hogy az Event hub fogadta-e az üzeneteket](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > A teljes forráskódhoz, beleértve a további tájékoztató megjegyzéseket, lépjen a [GitHub sendEvents. js oldalára](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Gratulálunk! Már elvégezte az események elküldése az Event hubhoz.


## <a name="receive-events"></a>Események fogadása
Ebben a szakaszban egy esemény-központból származó eseményeket kap egy Azure Blob Storage ellenőrzőpont-tároló használatával egy JavaScript-alkalmazásban. Metaadat-ellenőrzőpontokat végez a fogadott üzenetekben, rendszeres időközönként egy Azure Storage-blobban. Ezzel a módszerrel egyszerűen folytathatja az üzenetek fogadását, ahonnan abbahagyta a kapcsolatot.

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Azure Storage-fiók és blob-tároló létrehozása
Hozzon létre egy Azure Storage-fiókot és egy BLOB-tárolót a következő műveletekkel:

1. [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [BLOB-tároló létrehozása a Storage-fiókban](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [A Storage-fiókhoz tartozó kapcsolódási karakterlánc lekérése](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

A fogadási kódban jegyezze fel a kapcsolódási karakterláncot és a tároló nevét.

### <a name="write-code-to-receive-events"></a>Kód írása az események fogadására

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com)-ot.
1. Hozzon létre egy *Receive. js*nevű fájlt, és illessze be a következő kódot:

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. A kódban használja a valós értékeket a következő értékek lecseréléséhez:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. A fájl végrehajtásához futtassa `node receive.js` parancsot a parancssorban. Az ablakban a fogadott eseményekről származó üzeneteket kell megjeleníteni.

    > [!NOTE]
    > A teljes forráskódhoz, beleértve a további tájékoztató megjegyzéseket, lépjen a [GitHub receiveEventsUsingCheckpointStore. js oldalára](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Gratulálunk! Most már kapott eseményeket az Event hub-ból. A fogadó program az Event hub alapértelmezett fogyasztói csoportjának összes partíciójának eseményeit fogja fogadni.

## <a name="next-steps"></a>Következő lépések
Tekintse meg ezeket a mintákat a GitHubon:

- [JavaScript-minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Írógéppel minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
