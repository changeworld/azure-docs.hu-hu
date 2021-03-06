---
title: Értesítések küldése az Univerzális Windows-platformon alapuló alkalmazásoknak az Azure Notification Hubs használatával | Microsoft Docs
description: Az Azure Notification Hubs használatával leküldéses értesítéseket küldhet egy univerzális Windows-platformra.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/05/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: ec0181dd75cd656859967c30dc3941175407413a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894994"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-by-using-azure-notification-hubs"></a>Oktatóanyag: Értesítések küldése az Univerzális Windows-platformon alapuló alkalmazásoknak az Azure Notification Hubs használatával

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Ebben az oktatóanyagban egy értesítési központot hoz létre leküldéses értesítések küldéséhez az Univerzális Windows-platformra (UWP) épülő alkalmazásba. Egy üres Windows Áruházbeli alkalmazást fog létrehozni, amely leküldéses értesítéseket fogad a Windows leküldéses értesítési szolgáltatásának (WNS) használatával. Ezt követően az értesítési központ használatával leküldéses értesítéseket küldhet az alkalmazást futtató összes eszközre.

> [!NOTE]
> Az oktatóanyag teljes kódját megtalálja a [GitHubon](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/UwpSample).

Hajtsa végre a következő lépéseket:

> [!div class="checklist"]
> * Alkalmazás létrehozása a Windows Áruházban
> * Értesítési központ létrehozása
> * Windows-mintaalkalmazás létrehozása
> * Tesztértesítések küldése

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes Azure-fiókot a](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Kezdés előtt.
- Microsoft Visual Studio 2017 vagy újabb verzió. Az oktatóanyagban szereplő példa a [Visual Studio 2019](https://www.visualstudio.com/products)-et használja.
- [UWP-alkalmazások telepített fejlesztőeszközei](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- Aktív Windows Áruházbeli fiók
- Győződjön meg arról, hogy az **alkalmazások és más küldők értesítésének beolvasása** beállítás engedélyezve van. 
    - A **Beállítások** ablak elindítása a számítógépen.
    - Válassza ki **a** rendszercsempét.
    - Válassza ki az **értesítések & műveletek** elemet a bal oldali menüben. 
    - Győződjön meg arról, hogy az **alkalmazások és más küldők értesítése** beállítás engedélyezve van. Ha nincs engedélyezve, engedélyezze.

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, UWP-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

## <a name="create-an-app-in-windows-store"></a>Alkalmazás létrehozása a Windows Áruházban

A leküldéses értesítések UWP-alkalmazásokba irányuló küldéséhez társítsa az alkalmazást a Windows Áruházhoz. Ezután konfigurálja az értesítési központot a WNS-sel való integrációra.

1. Nyissa meg a [Windows fejlesztőközpontját](https://partner.microsoft.com/dashboard/windows/first-run-experience), jelentkezzen be Microsoft-fiókjával, majd kattintson a **Create a new app** (Új alkalmazás létrehozása) gombra.

    ![Új alkalmazás gomb](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Írja be az alkalmazás nevét, majd válassza a **Reserve product name** (Terméknév lefoglalása) lehetőséget. Ezzel létrehoz egy új Windows Áruházbeli regisztrációt az alkalmazás számára.

    ![Áruházbeli alkalmazásnév](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Bontsa ki a **termékmenedzsment**csomópontot, válassza a **WNS/MPNS**, majd az **élő szolgáltatások webhely**lehetőséget. Jelentkezzen be Microsoft-fiókjába. Az alkalmazás regisztrálása lap egy új lapon nyílik meg. másik lehetőségként közvetlenül a [saját alkalmazások](https://apps.dev.microsoft.com) lapra navigálhat, és kiválaszthatja az alkalmazás nevét az oldal eléréséhez.

    ![A WNS/MPNS lap](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Jegyezze fel az **alkalmazás titkos kulcsának** jelszavát és a **csomag biztonsági azonosítóját (SID)** .

    >[!WARNING]
    >Az alkalmazáskulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Ezeket az értékeket ne ossza meg senkivel, és ne terjessze az alkalmazással.

## <a name="create-a-notification-hub"></a>Értesítési központ létrehozása

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>A központ WNS-beállításainak konfigurálása

1. Az **értesítési beállítások** kategóriában válassza a **Windows (WNS)** lehetőséget.
2. Adja meg a **Package SID** (Csomag biztonsági azonosítója) és a **Security Key** (Biztonsági kulcs) előző szakaszban feljegyzett értékét.
3. Kattintson az eszköztár **Mentés** elemére.

    ![A Csomag biztonsági azonosítója és a Biztonsági kulcs mezők](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Ezzel konfigurálta az értesítési központot a WNS-sel folytatott együttműködésre, és rendelkezésére állnak a kapcsolati sztringek az alkalmazás regisztrálásához és értesítések küldéséhez.

## <a name="create-a-sample-windows-app"></a>Windows-mintaalkalmazás létrehozása

1. A Visual Studióban nyissa meg a **fájl** menüt, válassza az **új**, majd a **projekt**elemet.
2. A **create a New Project (új projekt létrehozása** ) párbeszédpanelen hajtsa végre a következő lépéseket:

    1. A felső keresőmezőbe írja be a **Windows Universal**kifejezést.
    2. A keresési eredmények között válassza az **üres alkalmazás (univerzális Windows)** lehetőséget, majd kattintson a **tovább**gombra.

       ![New Project (Új projekt) párbeszédpanel](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)

    3. Az **új projekt konfigurálása** párbeszédpanelen adja meg a **projekt nevét**és a projektfájl **helyét** .
    4. Kattintson a **Létrehozás** gombra.

3. A **target** (cél) és a **minimum** (minimális) platformverzió esetében fogadja el az alapértelmezett értékeket, majd kattintson az **OK** gombra.
4. Megoldáskezelőban kattintson a jobb gombbal a Windows áruházbeli alkalmazás projektre, válassza a **Közzététel**lehetőséget, majd válassza **az alkalmazás hozzárendelése az áruházhoz**lehetőséget. Megjelenik az **Associate Your App with the Windows Store** (Alkalmazás társítása a Windows Áruházzal) varázsló.
5. A varázslóban jelentkezzen be Microsoft-fiókjával.
6. Jelölje ki a 2. lépésben regisztrált alkalmazást, majd válassza a **Next** (Tovább), végül az **Associate** (Társítás) lehetőséget. Ezzel hozzáadja a szükséges Windows Áruházbeli regisztrációs adatokat az alkalmazásjegyzékhez.
7. A Visual Studióban kattintson a jobb gombbal a megoldásra, majd válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget. Megnyílik a **Manage NuGet Packages** (NuGet-csomagok kezelése) ablak.
8. A keresőmezőben adja meg a **WindowsAzure.Messaging.Managed** kifejezést, válassza az **Install** (Telepítés) lehetőséget, és fogadja el a használati feltételeket.

    ![A NuGet-csomagok kezelése ablak][20]

    A művelet a [Microsoft.Azure.NotificationHubs NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs) használatával letölti és telepíti a Windows rendszerhez készült Azure Notification Hubs kódtárat, és hozzáad egy rá mutató referenciát is.
9. Nyissa meg a `App.xaml.cs` projektfájlt, és adja hozzá a következő utasításokat:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. A projekt `App.xaml.cs` fájljában keresse meg a `App` osztályt, és adja hozzá a következő `InitNotificationsAsync` metódus definícióját. Cserélje le a `<your hub name>` elemet a Azure Portalban létrehozott értesítési központ nevére, és cserélje le a `<Your DefaultListenSharedAccessSignature connection string>`t az értesítési központ **hozzáférési** szabályzatok oldaláról az `DefaultListenSharedAccessSignature` kapcsolati sztringre:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
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

    Ez a kód lekéri a csatorna URI azonosítóját az alkalmazás számára a WNS-ből, majd regisztrálja a csatorna URI azonosítóját az értesítési központban.

    >[!NOTE]
    > Cserélje le az `hub name` helyőrzőt az értesítési központ Azure Portalban megjelenő nevére. A kapcsolati karakterlánc helyőrzőjét is cserélje le az értesítési központ előző szakaszának **hozzáférési házirendek** lapján beszerzett `DefaultListenSharedAccessSignature` kapcsolati karakterláncra.

11. A `App.xaml.cs``OnLaunched`-eseménykezelő tetején adja hozzá a következő hívást az új `InitNotificationsAsync` metódushoz:

    ```csharp
    InitNotificationsAsync();
    ```

    Ez a művelet garantálja, hogy a csatorna URI-ja regisztrálva lesz az értesítési központban az alkalmazás minden indításakor.

12. Az alkalmazás futtatásához nyomja le a billentyűzet **F5** billentyűjét. Ekkor megjelenik a regisztrációs kulcsot tartalmazó párbeszédpanel. A párbeszédpanel bezárásához kattintson **az OK**gombra.

    ![A regisztráció sikerült](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

Az alkalmazás készen áll bejelentési értesítéseket fogadni.

## <a name="send-test-notifications"></a>Tesztértesítések küldése

Ha gyorsan le szeretné tesztelni, hogyan fogadja az alkalmazás az értesítéseket, értesítéseket küldhet az [Azure Portalról](https://portal.azure.com/).

1. Az Azure Portalon váltson az Áttekintés lapra, majd kattintson az eszköztár **Tesztküldés** gombjára.

    ![Tesztküldés gomb](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. A **Tesztküldés** ablakban hajtsa végre az alábbi műveleteket:
    1. A **Platformok** beállításnál válassza a **Windows** lehetőséget.
    2. Az **Értesítés típusa** beállításnál válassza a **Bejelentés** lehetőséget.
    3. Kattintson a **Küldés** gombra.

        ![A Tesztküldés panel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. A Küldés művelet eredményét az ablak alján található **Eredmény** listában láthatja. Emellett egy figyelmeztető üzenet is megjelenik.

    ![A Küldés művelet eredménye](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
4. Az asztalon megjelenik egy értesítés, amely a **Tesztüzenet** szöveget tartalmazza.

    ![Értesítési üzenet](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)

## <a name="next-steps"></a>Következő lépések
A portálon vagy a konzolon keresztül küldött értesítéseket küldött az összes Windows-eszközre. Ha szeretné megtudni, hogy hogyan küldhet leküldéses értesítéseket adott eszközökre, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott eszközökre](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[toast catalog]: https://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: https://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: https://msdn.microsoft.com/library/windows/apps/hh779719.aspx
