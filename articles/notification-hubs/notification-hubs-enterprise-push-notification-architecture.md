---
title: Vállalati leküldéses architektúra Notification Hubs
description: Tudnivalók az Azure Notification Hubs vállalati környezetben való használatáról
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 0104547a432f7f78d74731e11926bcd82088cef7
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264033"
---
# <a name="enterprise-push-architectural-guidance"></a>Architekturális útmutatás a leküldéses értesítések vállalati használatához

A vállalatok napjainkban fokozatosan mozgó alkalmazásokat hoznak létre a végfelhasználók (külső) vagy az alkalmazottak (belső) számára. Meglévő háttérrendszer-rendszerekkel rendelkeznek, amelyek a mobil alkalmazások architektúrájának integrálására alkalmasak. Ez az útmutató arról beszél, hogyan teheti ezt az integrációt a lehetséges megoldás általános forgatókönyvekre való bevezetéséhez.

Gyakori követelmény, hogy a leküldéses értesítéseket a mobil alkalmazáson keresztül küldje el a felhasználóknak, ha a háttérrendszer egyik érdeklődési lépése. Például egy olyan bank-ügyfélnek, aki az iPhone-on egy bank banki alkalmazását szeretné értesíteni, értesítést kap, ha a fiókból vagy egy intranetes forgatókönyvből egy bizonyos összeg fölé helyezi a terhelést, és a pénzügyi részleg olyan alkalmazottja, aki Windows Phone-telefon szeretné  a jóváhagyási kérelem fogadásakor a rendszer értesítést küld.

A bankszámla-vagy jóváhagyási feldolgozás valószínűleg valamilyen háttérrendszer-rendszeren történik, amelynek el kell indítania egy leküldéses felhasználót. Több ilyen háttérrendszer is lehet, amelyek mindegyikének ugyanolyan típusú logikát kell létrehoznia, amikor egy esemény elindítja az értesítést. A bonyolultság itt rejlik több háttérrendszer integrálásával egyetlen leküldéses rendszerrel, ahol a végfelhasználók különböző értesítésekre fizethetnek, és akár több Mobile-alkalmazás is lehetséges. Például az intranetes mobil alkalmazások esetében, amelyekben az egyik mobileszköz több ilyen háttérrendszer-rendszerről is szeretne értesítést kapni. A háttérrendszer nem tudja, vagy ismernie kell a leküldéses szemantikai/technológiai ismereteket, ezért a hagyományos megoldás egy olyan összetevő bevezetése, amely a háttérrendszer lekérdezi az összes érdekes eseményt, és feladata a leküldéses üzenetek küldése az ügyfélnek.

Jobb megoldás, ha Azure Service Bus-topic/előfizetés modellt használ, ami csökkenti a bonyolultságot a megoldás skálázhatóvé tétele mellett.

Itt látható a megoldás általános architektúrája (több Mobile alkalmazással általánosítva, de egyformán alkalmazható, ha csak egy Mobile alkalmazás van)

## <a name="architecture"></a>Architektúra

![][1]

Ebben az építészeti diagramban található kulcs Azure Service Bus, amely a témakörök/előfizetések programozási modelljét tartalmazza (További információ: [Service Bus pub/sub programozás]). A fogadó, amely ebben az esetben a mobil háttér (jellemzően az [Azure Mobile Service], amely leküldi a Mobile apps-t) nem fogad üzeneteket közvetlenül a háttérrendszer-rendszerekből, hanem a [Azure Service Bus]által biztosított köztes absztrakt réteget, amely lehetővé teszi, hogy a mobil háttérrendszer üzeneteket fogadjon egy vagy több háttérrendszer-rendszerből. Létre kell hozni egy Service Bus témakört az egyes háttérrendszer-rendszerekhez, például a fiók, az HR, a pénzügy, ami alapvetően "témakörök", amely a leküldéses értesítésként küldendő üzeneteket kezdeményezi. A háttérrendszer üzeneteket küld ezekre a témakörökre. A mobil háttérrendszer egy Service Bus előfizetés létrehozásával tud előfizetni egy vagy több ilyen témakörre. Arra jogosítja fel a mobil hátteret, hogy értesítést kapjon a megfelelő háttérrendszer-rendszerről. A mobil háttérrendszer továbbra is figyeli az üzenetek előfizetéseit, és amint egy üzenet érkezik, visszavált, és értesítést küld az értesítési központnak. Az értesítési hubok végül továbbítják az üzenetet a Mobile alkalmazásnak. Itt látható a legfontosabb összetevők listája:

1. Háttérrendszer-rendszerek (LoB/örökölt rendszerek)
   * Service Bus témakör létrehozása
   * Üzenet küldése
1. Mobil háttérszolgáltatás
   * Szolgáltatás-előfizetés létrehozása
   * Üzenet fogadása (a háttérrendszer-rendszerből)
   * Értesítés küldése az ügyfeleknek (az Azure Notification hub használatával)
1. Mobilalkalmazás
   * Értesítés fogadása és megjelenítése

### <a name="benefits"></a>Előnyök

1. A fogadó (Mobile App/Service on Notification hub) és a küldő (háttérrendszer) közötti leválasztás lehetővé teszi a további háttérrendszer-rendszerek integrálását minimális változásokkal.
1. Azt is lehetővé teszi, hogy több Mobile apps is képes legyen eseményeket fogadni egy vagy több háttérrendszer-rendszerből.  

## <a name="sample"></a>Minta

### <a name="prerequisites"></a>Előfeltételek

Az alábbi oktatóanyagokat követve Ismerkedjen meg a fogalmakkal, valamint a közös létrehozási & konfigurációs lépések végrehajtásával:

1. [Service Bus pub/sub programozás] – ez az oktatóanyag ismerteti a Service Bus témakörök/előfizetések használatának részleteit, valamint azt, hogy hogyan hozhat létre olyan névteret, amely témaköröket vagy előfizetéseket tartalmaz, és hogyan küldhet & üzeneteket.
2. [Notification Hubs – univerzális Windows-oktatóanyag] – ez az oktatóanyag azt ismerteti, hogyan állítható be a Windows áruházbeli alkalmazás, és hogyan használható a Notification Hubs az értesítések regisztrálásához és fogadásához.

### <a name="sample-code"></a>Mintakód

A teljes mintakód a [Értesítési központ – minták]érhető el. Három összetevőre oszlik:

1. **EnterprisePushBackendSystem**

    a. Ez a projekt a **WindowsAzure. ServiceBus** NuGet-csomagot használja, és [Service Bus pub/sub programozás]alapul.

    b. Ez az alkalmazás egy egyszerű C# konzolos alkalmazás, amely egy LoB-rendszer szimulálására szolgál, amely elindítja az üzenetet a mobil alkalmazásnak.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    c. a `CreateTopic` a Service Bus témakör létrehozásához használatos.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. a `SendMessage` az üzenetek küldésére szolgál a Service Bus témakörben. Ez a kód egyszerűen egy véletlenszerű üzenetet küld a témakörnek a minta céljára való rendszeres időközönként. Általában van egy háttérrendszer, amely üzeneteket küld egy esemény bekövetkeztekor.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **ReceiveAndSendNotification**

    a. Ez a projekt a *WindowsAzure. ServiceBus* és a **Microsoft. Web. webjobs. publish** NuGet-csomagokat használja, és [Service Bus pub/sub programozás]alapul.

    b. A következő konzolos alkalmazás Azure- [Azure-Webjobs] fut, mert folyamatosan kell futnia az üzletági/háttérrendszer üzeneteinek figyeléséhez. Ez az alkalmazás a mobil háttérrendszer részét képezi.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    c. a `CreateSubscription` egy Service Bus-előfizetés létrehozására szolgál, amelyben a háttérrendszer üzeneteket küld. Az üzleti forgatókönyvtől függően ez az összetevő egy vagy több előfizetést hoz létre a kapcsolódó témakörökhöz (például bizonyos esetekben üzeneteket fogadhat a HR rendszertől, néhányat a pénzügyi rendszertől stb.)

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification` a témakörben szereplő üzenet olvasására használatos az előfizetés használatával, és ha az olvasás sikeres, akkor a rendszer az Azure-Notification Hubs használatával küldi el a Mobile Application-nek a következő példában egy értesítést (a Windows natív Toast-értesítést).

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. Az alkalmazás **webjobs**való közzétételéhez kattintson a jobb gombbal a megoldásra a Visual Studióban, és válassza a **Közzététel as webjobs** lehetőséget.

    ![][2]

    f. Válassza ki a közzétételi profilt, és hozzon létre egy új Azure-webhelyet, ha még nem létezik, amely üzemelteti ezt a Webjobs, és ha már rendelkezik a webhellyel, **tegye közzé**.

    ![][3]

    g. Konfigurálja úgy a feladatot, hogy "folyamatosan fusson", hogy amikor bejelentkezik a [Azure Portal] , a következőhöz hasonlóan kell megjelennie:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Ez az alkalmazás egy Windows áruházbeli alkalmazás, amely a mobil háttérrendszer részeként futó Webjobs fogad bejelentési értesítéseket, és megjeleníti azt. Ez a kód [Notification Hubs – univerzális Windows-oktatóanyag]alapul.  

    b. Győződjön meg arról, hogy az alkalmazás engedélyezve van a Toast-értesítések fogadásához.

    c. Győződjön meg arról, hogy a következő Notification Hubs regisztrációs kódot hívja meg az alkalmazás indításakor (a `HubName` és `DefaultListenSharedAccessSignature` értékek cseréje után:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

### <a name="running-the-sample"></a>A minta futtatása

1. Győződjön meg arról, hogy a Webjobs sikeresen fut, és folyamatosan ütemezve van.
2. Futtassa a **EnterprisePushMobileApp**, amely elindítja a Windows áruház alkalmazást.
3. Futtassa a **EnterprisePushBackendSystem** Console alkalmazást, amely szimulálja az LoB-hátteret, és megkezdi az üzenetek küldését, és látnia kell a következő képen láthatóhoz hasonló pirítósi értesítéseket:

    ![][5]

4. Az üzeneteket eredetileg Service Bus témakörök kaptak, amelyeket a webes feladatokban Service Bus előfizetések figyeltek. Az üzenet fogadása után egy értesítés lett létrehozva, és a rendszer elküldte a mobil alkalmazásnak. A Webjobs-naplókban megtekintheti a feldolgozást, amikor a [Azure Portal] webhelyéhez tartozó naplók hivatkozásra lép:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Értesítési központ – minták]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Service]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus pub/sub programozás]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure-Webjobs]: ../app-service/webjobs-create.md
[Notification Hubs – univerzális Windows-oktatóanyag]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure Portal]: https://portal.azure.com/
