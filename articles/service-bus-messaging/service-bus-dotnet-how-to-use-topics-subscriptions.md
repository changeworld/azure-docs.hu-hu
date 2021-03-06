---
title: Ismerkedés az Azure Service Bus-üzenettémákkal és előfizetésekkel | Microsoft Docs
description: Írjon Service Bus-üzenettémákat és előfizetéseket használó C# .NET Core-konzolalkalmazást.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/27/2019
ms.author: aschhab
ms.openlocfilehash: 3fba1d62b9347303d630c80733c4fbfa279b5296
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560100"
---
# <a name="get-started-with-service-bus-topics"></a>Bevezetés a Service Bus-üzenettémák használatába

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez az oktatóanyag a következő lépéseken vezet végig:

1. Írjon egy .NET Core-konzolalkalmazást, hogy elküldjön egy üzenetkészletet az üzenettémához.
2. Írjon egy .NET Core-konzolalkalmazást, hogy üzeneteket tudjon fogadni az előfizetésből.

## <a name="prerequisites"></a>Előfeltételek

1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [Visual Studio-vagy MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Kövesse a rövid útmutató lépéseit [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket a témakörbe](service-bus-quickstart-topics-subscriptions-portal.md) a következő feladatok elvégzéséhez:
    1. Hozzon létre egy Service Bus **névteret**.
    2. A **kapcsolatok karakterláncának**beolvasása.
    3. Hozzon létre egy **témakört** a névtérben.
    4. Hozzon létre **egy előfizetést** a témakörben a névtérben.
3. [Visual Studio 2017 3-as frissítés (verziószám: 15.3, 26730.01)](https://www.visualstudio.com/vs) vagy újabb.
4. [NET Core SDK](https://www.microsoft.com/net/download/windows), 2.0-s vagy újabb verzió.
 
## <a name="send-messages-to-the-topic"></a>Üzenet küldése az üzenettémához

A Visual Studio használatával írjon C# konzolalkalmazást az üzenetek üzenettémához való elküldéséhez.

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual Studiót, majd hozzon létre egy új **Konzolalkalmazás (.NET Core)** projektet.

### <a name="add-the-service-bus-nuget-package"></a>A Service Bus NuGet-csomag hozzáadása

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Kattintson a **Browse** (Tallózás) fülre, keressen rá a **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** kifejezésre, majd válassza ki a **Microsoft.Azure.ServiceBus** elemet. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.
   
    ![NuGet-csomag kiválasztása][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Kód írása az üzenetek üzenettémához való küldéséhez

1. A Program.cs fájlban adja hozzá a következő `using` utasításokat a névtér-definíció elejéhez, az osztálydeklaráció elé:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. A `Program` osztályban deklarálja az alábbi változókat. A `ServiceBusConnectionString` változó értékének állítsa be a névtér létrehozásakor kapott kapcsolati sztringet, a `TopicName` értékének pedig az üzenettéma létrehozásakor használt nevet:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. Cserélje le a `Main()` metódust a következő **aszinkron** `Main` metódusra, amely aszinkron módon küld üzeneteket az SendMessagesAsync metódussal, amelyet a következő lépésben fog hozzáadni. 

    ```csharp
    public static async Task Main(string[] args)
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```
5. Közvetlenül a `Main` metódus után adja hozzá a következő `SendMessagesAsync()` metódust a `numberOfMessagesToSend` által megadott számú (aktuális értéke: 10) üzenet elküldéséhez:

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. A küldő Program.cs fájlnak így kell kinéznie.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            public static async Task Main(string[] args)
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);
    
                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");
    
                // Send messages.
                await SendMessagesAsync(numberOfMessages);
    
                Console.ReadKey();
    
                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. Futtassa a programot, és ellenőrizze az Azure Portalon: kattintson az üzenettéma nevére a névtér **Áttekintés** ablakában. Megjelenik az üzenettéma **Alapok** képernyője. Arra figyeljen, hogy az előfizetésnek az ablak alsó részén megjelenő **Üzenetek száma** értéke jelenleg **10**. Valahányszor a küldő alkalmazást az üzenetek (a következő szakaszban leírt módon történő) fogadása nélkül futtatja, ez az érték 10-zel növekszik. Azt is vegye figyelembe, hogy az üzenettéma aktuális mérete minden alkalommal megnöveli az **Alapok** ablak **Jelenlegi** értékét, amikor az alkalmazás üzeneteket ad hozzá az üzenettémához.
   
      ![Üzenet mérete][topic-message]

## <a name="receive-messages-from-the-subscription"></a>Üzenet fogadása az előfizetéstől

Az elküldött üzenetek fogadásához hozzon létre egy másik .NET Core Console-alkalmazást, és telepítse a **Microsoft. Azure. ServiceBus** NuGet-csomagot, hasonlóan az előző küldő alkalmazáshoz.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Kód írása az üzenetek előfizetéstől történő fogadásához

1. A Program.cs fájlban adja hozzá a következő `using` utasításokat a névtér-definíció elejéhez, az osztálydeklaráció elé:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. A `Program` osztályban deklarálja az alábbi változókat. A `ServiceBusConnectionString` változó értékének állítsa be a névtér létrehozásakor kapott kapcsolati sztringet, a `TopicName` értékének az üzenettéma létrehozásakor használt nevet, a `SubscriptionName` értékének pedig az üzenettéma-előfizetés létrehozásakor használt nevet:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. Cserélje le a `Main()` metódust a következő **aszinkron** `Main` metódusra. A következő lépésben felvenni kívánt `RegisterOnMessageHandlerAndReceiveMessages()` metódust hívja meg. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();    
    }
   ```
5. Közvetlenül a `Main()` metódus után adja hozzá a következő metódust az üzenetkezelő regisztrálásához és a küldő alkalmazás által elküldött üzenetek fogadásához:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Közvetlenül az előző metódus után adja hozzá a következő `ProcessMessagesAsync()` metódust a fogadott üzenetek feldolgozásához:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Végül adja hozzá a következő metódust az esetlegesen előforduló kivételek kezeléséhez:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. A fogadó Program.cs fájlnak így kell kinéznie:
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            public static async Task Main(string[] args)
            {    
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);
        
                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");
        
                // Register subscription message handler and receive messages in a loop
                RegisterOnMessageHandlerAndReceiveMessages();
        
                Console.ReadKey();
        
                await subscriptionClient.CloseAsync();    
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. Futtassa a programot, majd ellenőrizze ismét a portálon. Figyelje meg, hogy a **Üzenetek száma** és a **Jelenlegi** értéke most **0**.
   
    ![A témakör hossza][topic-message-receive]

Gratulálunk! A .NET Standard kódtár használatával létrehozott egy üzenettémát és egy előfizetést, elküldött 10 üzenetet, és fogadta is azokat.

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>Következő lépések

Tekintse meg Service Bus [GitHub-adattárunkat, ahol további példákat talál](https://github.com/Azure/azure-service-bus/tree/master/samples), amelyek a Service Bus üzenetkezelési szolgáltatásának néhány speciális funkcióját mutatják be.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
