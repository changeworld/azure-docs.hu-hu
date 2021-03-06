---
title: Az Azure Notification Hubs használata a PHP-vel
description: Ismerje meg, hogyan használható az Azure Notification Hubs a PHP háttérrendszer használatával.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9a77a9d9c8b2d71197089f66d81e07d56c780e11
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263846"
---
# <a name="how-to-use-notification-hubs-from-php"></a>A Notification Hubs használata PHP-ból

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

A Java/PHP/Ruby háttérrendszer összes Notification Hubs funkcióját az értesítési központ REST felületének használatával érheti el az MSDN témakör [Notification HUBS REST API](https://msdn.microsoft.com/library/dn223264.aspx)-k című részében leírtak szerint.

Ebben a témakörben a következőket mutatjuk be:

* REST-ügyfél létrehozása a PHP Notification Hubs szolgáltatásaihoz;
* Kövesse az [első lépések oktatóanyagot](notification-hubs-ios-apple-push-notification-apns-get-started.md) a választható mobil platformhoz, és alkalmazza a háttérrendszer részét a PHP-ben.

## <a name="client-interface"></a>Ügyfél felülete

A fő ügyfél felülete ugyanazokat a módszereket nyújtja, mint a [.net Notification HUBS SDK](https://msdn.microsoft.com/library/jj933431.aspx), amely lehetővé teszi, hogy közvetlenül lefordítsa az ezen a helyen jelenleg elérhető oktatóanyagokat és mintákat, és a Közösség az interneten is részt vesz.

A [PHP REST burkoló minta]elérhető összes kód megtalálható.

Például egy ügyfél létrehozásához:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

IOS natív értesítés küldése:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Megvalósítás

Ha még nem tette meg, kövesse az [Első lépéseket ismertető oktatóanyag] az utolsó szakaszhoz, ahol a háttérrendszer megvalósítására van szükség.
Azt is megteheti, hogy a [PHP REST burkoló minta] is használhatja a kódot, és közvetlenül a [teljes oktatóanyag](#complete-tutorial) szakaszra lép.

A teljes REST-burkoló megvalósításának minden részletét az [MSDN webhelyén](https://msdn.microsoft.com/library/dn530746.aspx)találja. Ebben a szakaszban a Notification Hubs REST-végpontokhoz való hozzáféréshez szükséges fő lépések PHP-megvalósítását ismertetjük:

1. Kapcsolati sztring elemzése
2. Az engedélyezési jogkivonat előállítása
3. HTTP-hívás végrehajtása

### <a name="parse-the-connection-string"></a>Kapcsolati sztring elemzése

Itt látható a fő osztály, amely az ügyfelet alkalmazza, amelynek a konstruktora elemzi a következő kapcsolatot:

    ```php
    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }
    ```

### <a name="create-a-security-token"></a>Biztonsági jogkivonat létrehozása

Az [sas biztonsági jogkivonat létrehozásával](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token)kapcsolatos információkért tekintse meg az Azure dokumentációját.

Adja hozzá a `generateSasToken` metódust a `NotificationHub` osztályhoz a jogkivonat létrehozásához az aktuális kérelem URI-ja és a kapcsolati karakterláncból kinyert hitelesítő adatok alapján.

    ```php
    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }
    ```

### <a name="send-a-notification"></a>Értesítés küldése

Először is Definiáljon egy olyan osztályt, amely egy értesítést jelöl.

    ```php
    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }
    ```

Ez az osztály egy natív értesítési törzs tárolója, vagy egy sablonra vonatkozó értesítés esetén a tulajdonságok halmaza, valamint egy olyan fejléc, amely formátumot (natív platformot vagy sablont) és platform-specifikus tulajdonságokat (például Apple lejárat tulajdonságot és WNS) tartalmaz. fejlécek).

A rendelkezésre álló lehetőségekért tekintse meg a [Notification HUBS REST API-k dokumentációját](https://msdn.microsoft.com/library/dn495827.aspx) és az egyes értesítési platformok formátumait.

Ezt az osztályt felfegyverezve most már megírhatjuk a küldési értesítési módszereket a `NotificationHub` osztályon belül:

    ```php
    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "fcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 
    ```

A fenti módszerek HTTP POST-kérést küldenek az értesítési központ `/messages` végpontjának, és a megfelelő törzstel és fejlécekkel küldik el az értesítést.

## <a name="complete-tutorial"></a>Az oktatóanyag befejezése

Most már elvégezheti az első lépéseket ismertető oktatóanyagot az értesítés PHP-háttérből való elküldésével.

Inicializálja a Notification Hubs ügyfelet (a [Első lépéseket ismertető oktatóanyag]leírtak szerint helyettesítse be a kapcsolatok karakterláncát és a hub nevét):

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Ezután adja hozzá a küldési kódot a cél mobil platformtól függően.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows áruház és Windows Phone-telefon 8,1 (nem Silverlight)

    ```php
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);
    ```

### <a name="ios"></a>iOS

    ```php
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);
    ```

### <a name="android"></a>Android

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("fcm", $message);
    $hub->sendNotification($notification, null);
    ```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone-telefon 8,0 és 8,1 Silverlight

    ```php
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);
    ```

### <a name="kindle-fire"></a>Kindle Fire

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

A PHP-kód futtatásával azonnal megjelenik egy értesítés, amely megjelenik a megcélzott eszközön.

## <a name="next-steps"></a>Következő lépések

Ebben a témakörben bemutatjuk, hogyan hozhat létre egy egyszerű Java REST-ügyfelet Notification Hubs számára. Ebből a menüből:

* Töltse le a teljes [PHP REST burkoló minta], amely tartalmazza a fenti kódot.
* Folytassa a Notification Hubs címkézési funkció megismerésével kapcsolatos tudnivalókat [a legfrissebb hírek oktatóanyagban]
* További információ az egyéni felhasználók értesítéseinek leküldéséről [a felhasználók értesítése oktatóanyagban]

További információ: a [php fejlesztői központ](https://azure.microsoft.com/develop/php/)is.

[PHP REST burkoló minta]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Első lépéseket ismertető oktatóanyag]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
