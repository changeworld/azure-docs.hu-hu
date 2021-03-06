---
title: Leküldéses értesítések hozzáadása a Xamarin. iOS-alkalmazáshoz
description: Megtudhatja, hogyan küldhet leküldéses értesítéseket a Xamarin. iOS-alkalmazáshoz Azure App Service használatával.
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f9c70491d06f61931ebabda859ff3a86ed035b44
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249281"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Leküldéses értesítések hozzáadása a Xamarin. iOS-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket ad hozzá a [Xamarin. iOS gyors üzembe helyezési](app-service-mobile-xamarin-ios-get-started.md) projekthez, hogy a rendszer minden egyes rekord beszúrásakor leküldéses értesítést küldjön az eszköznek.

Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, szüksége lesz a leküldéses értesítési bővítmény csomagra. További információért lásd: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

## <a name="prerequisites"></a>Előfeltételek

* Fejezze be a [Xamarin. iOS](app-service-mobile-xamarin-ios-get-started.md) gyors üzembe helyezési oktatóanyagát.
* Egy fizikai iOS-eszköz. Az iOS-szimulátor nem támogatja a leküldéses értesítéseket.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Az alkalmazás regisztrálása leküldéses értesítésekhez az Apple fejlesztői portálon

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>A Mobile App beállítása leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>A kiszolgálói projekt frissítése leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>A Xamarin. iOS-projekt konfigurálása

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz

1. A **QSTodoService**-ben adja hozzá a következő tulajdonságot, hogy a **AppDelegate** tudja beszerezni a mobil ügyfelet:

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. Adja hozzá a következő `using` utasítást a **AppDelegate.cs** fájl elejéhez.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. A **AppDelegate**-ben felülbírálja a **FinishedLaunching** eseményt:

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. Ugyanebben a fájlban bírálja felül a `RegisteredForRemoteNotifications` eseményt. Ebben a kódban egy egyszerű sablonról szóló értesítést regisztrál, amelyet a-kiszolgáló az összes támogatott platformon el fog juttatni.

    A Notification Hubs-sablonokkal kapcsolatos további információkért lásd: [sablonok](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. Ezt követően felülbírálja a **DidReceivedRemoteNotification** eseményt:

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

Az alkalmazás most már frissítve van a leküldéses értesítések támogatásához.

## <a name="test"></a>Leküldéses értesítések tesztelése az alkalmazásban

1. Nyomja le a **Futtatás** gombot a projekt felépítéséhez és az alkalmazás iOS-kompatibilis eszközön való elindításához, majd kattintson **az OK** gombra a leküldéses értesítések fogadásához.

   > [!NOTE]
   > Explicit módon el kell fogadnia a leküldéses értesítéseket az alkalmazásból. Ez a kérelem csak az alkalmazás futásának első indításakor fordul elő.

2. Az alkalmazásban írjon be egy feladatot, majd kattintson a plusz ( **+** ) ikonra.
3. Ellenőrizze, hogy érkezett-e értesítés, majd kattintson **az OK** gombra az értesítés elvetéséhez.
4. Ismételje meg a 2. lépést, és azonnal lépjen ki az alkalmazásból, majd ellenőrizze, hogy megjelenik-e az értesítés.

Sikeresen elvégezte az oktatóanyagot.
