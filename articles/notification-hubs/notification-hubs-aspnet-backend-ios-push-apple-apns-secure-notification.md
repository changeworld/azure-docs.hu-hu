---
title: Azure Notification Hubs biztonságos leküldéses iOS rendszerhez
description: Ismerje meg, hogyan küldhet biztonságos leküldéses értesítéseket egy iOS-alkalmazásba az Azure-ból. A Objective-C és C#a kódban írt példák
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 96d1dd514f6fb9c11d7194714337583d6b4387cf
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530748"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs biztonságos leküldés

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Áttekintés

A leküldéses értesítések támogatása Microsoft Azure lehetővé teszi egy könnyen használható, többplatformos, kibővített leküldéses infrastruktúra elérését, ami nagy mértékben leegyszerűsíti a leküldéses értesítések megvalósítását mind a fogyasztói, mind a nagyvállalati alkalmazások számára. platformok.

A szabályozási vagy biztonsági korlátozások miatt előfordulhat, hogy egy alkalmazás az értesítésben olyan dolgot is tartalmaz, amely nem továbbítható a szabványos leküldéses értesítési infrastruktúrán keresztül. Ez az oktatóanyag leírja, hogyan érheti el ugyanezt a élményt úgy, hogy bizalmas adatokat küld biztonságos, hitelesített kapcsolaton keresztül az ügyfél és az alkalmazás háttere között.

Magas szinten a folyamat a következő:

1. Az alkalmazás háttérrendszer:
   * Biztonságos adattartalmat tárol a háttér-adatbázisban.
   * Elküldi az értesítés AZONOSÍTÓját az eszköznek (nincs biztonságos információ elküldve).
2. Az alkalmazás az eszközön az értesítés fogadásakor:
   * Az eszköz kapcsolatba lép a biztonságos adattartalmat kérő háttérrel.
   * Az alkalmazás értesítésként jeleníti meg a hasznos adatokat az eszközön.

Fontos megjegyezni, hogy az előző folyamat során (és ebben az oktatóanyagban) feltételezzük, hogy az eszköz helyi tárolóban tárolja a hitelesítési jogkivonatot, miután a felhasználó bejelentkezik. Ez zökkenőmentes élményt garantál, mivel az eszköz a token használatával lekérheti az értesítés biztonságos hasznos adatait. Ha az alkalmazás nem tárolja a hitelesítési jogkivonatokat az eszközön, vagy ha ezek a jogkivonatok elévülnek, akkor az értesítés fogadásakor a felhasználónak az alkalmazás elindítását kérő általános értesítésnek kell megjelennie. Az alkalmazás ezután hitelesíti a felhasználót, és megjeleníti az értesítési adattartalmat.

Ez a biztonságos leküldéses oktatóanyag a leküldéses értesítések biztonságos küldését mutatja be. Az oktatóanyag a [felhasználók értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) oktatóanyagra épül, ezért először végre kell hajtania az oktatóanyag lépéseit.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy létrehozta és konfigurálta az értesítési központot a [Első lépések Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md)című cikkben leírtak szerint.

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Az iOS-projekt módosítása

Most, hogy úgy módosította az alkalmazást, hogy csak az adott értesítés *azonosítóját* küldje el, módosítania kell az iOS-alkalmazást az értesítés kezelésére, és vissza kell hívnia a háttérben a megjelenítendő biztonságos üzenet lekéréséhez.

A cél eléréséhez meg kell írni a logikát, hogy a biztonságos tartalmat lekérje az alkalmazás háttérből.

1. A `AppDelegate.m`ban ellenőrizze, hogy az alkalmazás regisztrálja-e a csendes értesítéseket, hogy feldolgozza a háttérből küldött értesítési azonosítót. Adja hozzá a `UIRemoteNotificationTypeNewsstandContentAvailability` lehetőséget a didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. A `AppDelegate.m` a felső részen adja meg a megvalósítási szakaszt a következő deklarációval:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Ezután adja hozzá a következő kódot a megvalósítás szakaszhoz, és cserélje le a helyőrzőt `{back-end endpoint}` a végpontra, amelyet korábban a háttérként kapott:

    ```objc
    NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    {
        // check if authenticated
        ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
        NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                completion([json objectForKey:@"Payload"], nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }
    ```

    Ez a metódus a megosztott beállításokban tárolt hitelesítő adatok használatával hívja meg az alkalmazás háttér-visszaállítását.

4. Most meg kell kezelnie a beérkező értesítést, és a fenti módszer használatával le kell kérni a megjelenítendő tartalmat. Először is engedélyezni kell, hogy az iOS-alkalmazás a háttérben fusson, amikor leküldéses értesítést kap. A **Xcode**válassza ki az alkalmazás projektjét a bal oldali panelen, majd kattintson a fő alkalmazás céljára a **célok** szakaszban a központi ablaktáblán.
5. Ezután kattintson a **funkciók** fülre a központi ablaktábla tetején, és jelölje be a **távoli értesítések** jelölőnégyzetet.

    ![][IOS1]

6. A `AppDelegate.m` adja hozzá a következő metódust a leküldéses értesítések kezeléséhez:

    ```objc
    -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
        NSLog(@"%@", userInfo);

        [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
            if (!error) {
                // show local notification
                UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                localNotification.alertBody = payload;
                localNotification.timeZone = [NSTimeZone defaultTimeZone];
                [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                completionHandler(UIBackgroundFetchResultNewData);
            } else {
                completionHandler(UIBackgroundFetchResultFailed);
            }
        }];

    }
    ```

    Vegye figyelembe, hogy a hiányzó hitelesítési fejlécek tulajdonságának vagy a háttér általi elutasításnak a kezelése ajánlott. Ezeknek az eseteknek a konkrét feldolgozása főleg a célzott felhasználói élménytől függ. Az egyik lehetőség egy általános rákérdező értesítés megjelenítése a felhasználó hitelesítéséhez a tényleges értesítés lekéréséhez.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához tegye a következőket:

1. A XCode-ben futtassa az alkalmazást egy fizikai iOS-eszközön (a leküldéses értesítések nem fognak működni a szimulátorban).
2. Az iOS-alkalmazás felhasználói felületén adja meg a felhasználónevet és a jelszót. Ezek bármilyen sztringek lehetnek, de az értékeknek is szerepelniük kell.
3. Az iOS-alkalmazás felhasználói felületén kattintson a **Bejelentkezés**elemre. Ezután kattintson a **leküldés küldése**gombra. Az értesítési központban megjelenik a biztonságos értesítés.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
