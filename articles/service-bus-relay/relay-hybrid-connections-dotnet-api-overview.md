---
title: Az Azure Relay .NET Standard API-k áttekintése | Microsoft Docs
description: Ez a cikk az Azure Relay Hibrid kapcsolatok .NET Standard API áttekintését tartalmazza.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 18eaf2d2daae817107be6cdb0da9359bb5f9b4e9
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514535"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Azure Relay Hibrid kapcsolatok .NET Standard API – áttekintés

Ez a cikk néhány fontos Azure Relay Hibrid kapcsolatok .NET Standard [ügyféloldali API](/dotnet/api/microsoft.azure.relay)-t összegzi.
  
## <a name="relay-connection-string-builder-class"></a>Továbbítói kapcsolatok karakterlánc-készítő osztálya

A [RelayConnectionStringBuilder][RelayConnectionStringBuilder] osztály a továbbítási hibrid kapcsolatokra vonatkozó kapcsolódási karakterláncokat formázza. A használatával ellenőrizheti a kapcsolódási karakterlánc formátumát, vagy létrehozhat egy teljesen új, a kapcsolódási sztringet. Tekintse meg a következő kódot egy példához:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

A kapcsolódási karakterláncokat közvetlenül is átadhatja a `RelayConnectionStringBuilder` metódusnak. Ez a művelet lehetővé teszi annak ellenőrzését, hogy a kapcsolati karakterlánc érvényes formátumú-e. Ha a paraméterek bármelyike érvénytelen, a Konstruktor létrehoz egy `ArgumentException`.

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Hibrid kapcsolatok streamje

A [HybridConnectionStream][HCStream] osztály az adatok küldésére és fogadására használt elsődleges objektum egy Azure Relay-végpontról, függetlenül attól, hogy [HybridConnectionClient][HCClient]vagy [HybridConnectionListener][HCListener]dolgozik.

### <a name="getting-a-hybrid-connection-stream"></a>Hibrid kapcsolatok adatfolyamának beolvasása

#### <a name="listener"></a>Figyelő

[HybridConnectionListener][HCListener] -objektum használatával a következőképpen szerezhet be `HybridConnectionStream` objektumot:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Ügyfél

[HybridConnectionClient][HCClient] -objektum használatával a következőképpen szerezhet be `HybridConnectionStream` objektumot:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Adatfogadás

A [HybridConnectionStream][HCStream] osztály kétirányú kommunikációt tesz lehetővé. A legtöbb esetben folyamatosan megkapja az adatfolyamot. Ha szöveget olvas be a streamből, érdemes lehet egy [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) objektumot használni, amely lehetővé teszi az adatok egyszerűbb elemzését. Például az adatolvasást szövegként, nem pedig `byte[]`ként is elolvashatja.

A következő kód beolvassa az adatfolyam egyes sorait, amíg le nem kéri a megszakítást:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Adatok küldése

Miután létrejött a kapcsolatok, küldhet egy üzenetet a továbbítási végpontnak. Mivel a kapcsolatok objektum örökli a [streamet](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), az adatait `byte[]`küldi el. Az alábbi példa bemutatja, hogyan teheti meg ezt:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Ha azonban közvetlenül szeretne szöveget küldeni, anélkül, hogy minden alkalommal kódolnia kellene a sztringet, akkor a `hybridConnectionStream` objektumot egy [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) objektummal is becsomagolhatja.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Relayről, tekintse meg a következő hivatkozásokat:

* [Microsoft. Azure. Relay – dokumentáció](/dotnet/api/microsoft.azure.relay)
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Elérhető Relay API-k](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener