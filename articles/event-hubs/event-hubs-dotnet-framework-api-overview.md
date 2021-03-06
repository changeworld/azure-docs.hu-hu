---
title: Az Azure Event Hubs .NET-keretrendszer API-k áttekintése | Microsoft Docs
description: Ez a cikk összefoglalja a .NET-keretrendszer ügyféloldali API-jai (Management and Runtime) néhány kulcsfontosságú Event Hubsét.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: b14759ed39037bfa172366a2ed8f8ca089786ec6
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137611"
---
# <a name="event-hubs-net-framework-api-overview"></a>Event Hubs a .NET-keretrendszer API áttekintése

Ez a cikk az Azure Event Hubs [.NET-keretrendszer ügyféloldali API](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)-jait foglalja össze. Két kategória létezik: felügyeleti és futásidejű API-k. A futásidejű API-k az üzenetek küldéséhez és fogadásához szükséges összes műveletből állnak. A kezelési műveletek lehetővé teszik a Event Hubs entitások állapotának kezelését az entitások létrehozásával, frissítésével és törlésével.

A [figyelési forgatókönyvek](event-hubs-metrics-azure-monitor.md) a felügyeletet és a futtatási időt is kiterjedhetnek. A .NET API-kra vonatkozó részletes dokumentáció: .net- [keretrendszer](/dotnet/api/microsoft.servicebus.messaging.eventhubclient), [.NET Standard](/dotnet/api/microsoft.azure.eventhubs)és [EventProcessorHost API](/dotnet/api/microsoft.azure.eventhubs.processor) -referenciák.

## <a name="management-apis"></a>Felügyeleti API-k

A következő felügyeleti műveletek végrehajtásához rendelkeznie kell a Event Hubs **névtérhez tartozó kezelési engedélyekkel** :

### <a name="create"></a>Létrehozás

```csharp
// Create the event hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>frissítés

```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>Törlés

```csharp
await namespaceManager.DeleteEventHubAsync("event hub name");
```

## <a name="run-time-apis"></a>Futásidejű API-k
### <a name="create-publisher"></a>Közzétevő létrehozása

```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("event hub name");
```

### <a name="publish-message"></a>Üzenet közzététele

```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Fogyasztó létrehozása

```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Üzenet felhasználása

```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>Event Processor Host API-k

Ezek az API-k rugalmasságot biztosítanak a munkavégző folyamatok számára, amelyek elérhetetlenné válhatnak, és a partíciók kiosztásával elérhetővé válnak.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

A [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) felület a következőképpen van definiálva:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>További lépések

Az Event Hubs-forgatókönyvekkel kapcsolatos további információkért látogasson el a következő hivatkozásokra:

* [Mi az az Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Event Hubs programozási útmutató](event-hubs-programming-guide.md)

A .NET API-referenciák itt találhatók:

* [Microsoft.ServiceBus.Messaging](/dotnet/api/microsoft.servicebus.messaging)
* [Microsoft. Azure. EventHubs. EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)
