---
title: 'Gyors útmutató: Digitális tinta felismerése a tinta-felismerő REST API és a Node. js-sel'
titleSuffix: Azure Cognitive Services
description: A szabadkézi felismerő API-val megkezdheti a digitális tollvonások felismerését ebben a rövid útmutatóban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: a37f2b7044fcba04ca18093aa73563961e9e35de
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448141"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Gyors útmutató: Digitális tinta felismerése a kézírás-felismerő REST API és a JavaScripttel

Ezzel a rövid útmutatóval megkezdheti a tinta-felismerő API használatát a digitális tollvonásokon. Ez a JavaScript-alkalmazás egy, a JSON-formátumú szabadkézi adatokat tartalmazó API-kérést küld, és megjeleníti a választ.

Habár ez az alkalmazás JavaScript nyelven íródott, és a böngészőben fut, az API egy REST-alapú webszolgáltatás, amely a legtöbb programozási nyelvvel kompatibilis.

Általában az API-t egy Digital unking-alkalmazásból hívja meg. Ez a rövid útmutató egy JSON-fájlból a következő kézírásos minta adatait küldi el: tollvonási adatok.

![egy kézzel írott szöveg képe](../media/handwriting-sample.jpg)

Ennek a rövid útmutatónak a forráskódja a [githubon](https://go.microsoft.com/fwlink/?linkid=2089905)érhető el.

## <a name="prerequisites"></a>Előfeltételek

- Egy webböngésző
- Ebben a rövid útmutatóban a jelen rövid útmutatóban szereplő tollvonási adatsorok a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json)találhatók.

### <a name="create-an-ink-recognizer-resource"></a>Ink-felismerő erőforrás létrehozása

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. A kedvenc IDE vagy szerkesztőben hozzon létre egy új `.html` fájlt. Ezután adja hozzá az alapszintű HTML-kódot a kódhoz, amelyet később fogunk hozzáadni.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. A `<body>` címkén belül adja hozzá a következő HTML-kódot:
    1. Két szöveges terület a JSON-kérelem és-válasz megjelenítéséhez.
    2. Egy gomb a `recognizeInk()` függvény meghívásához, amelyet később fog létrehozni.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>A példa JSON-adatbázis betöltése

1. A `<script>` címkén belül hozzon létre egy változót a sampleJson. Ezután hozzon létre egy `openFile()` nevű JavaScript-függvényt, amely megnyitja a fájlkezelőt, így kiválaszthatja a JSON-fájlt. Ha a `Recognize ink` gombra kattint, meghívja ezt a függvényt, és megkezdi a fájl olvasását.
2. A fájl aszinkron feldolgozásához használjon egy `FileReader` objektum `onload()` függvényét. 
    1. Cserélje le a fájlban lévő összes `\n` vagy `\r` karaktert egy üres karakterlánccal. 
    2. A szöveg konvertálása érvényes JSON-re a `JSON.parse()` használatával
    3. Frissítse a `request` szövegmezőt az alkalmazásban. A JSON-karakterláncot a `JSON.stringify()` használatával formázhatja. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Kérelem küldése a tinta-felismerő API-nak

1. A `<script>` címkén belül hozzon létre egy `recognizeInk()`nevű függvényt. Ez a függvény később meghívja az API-t, és frissíti a választ tartalmazó lapot. Adja hozzá a kódot az alábbi lépésekkel a függvényen belül. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Hozzon létre változókat a végpont URL-címéhez, az előfizetési kulcshoz és a JSON-mintahoz. Ezután hozzon létre egy `XMLHttpRequest` objektumot az API-kérelem elküldéséhez. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Hozza létre a Return függvényt a `XMLHttpRequest` objektumhoz. Ez a függvény elemzi egy sikeres kérelem API-válaszát, és megjeleníti azt az alkalmazásban. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Hozza létre a Request objektum Error függvényét. Ez a függvény naplózza a hibát a konzolon. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Hozzon létre egy függvényt a kérelem objektum `onreadystatechange` tulajdonságához. A kérési objektum készültségi állapotának megváltozásakor a rendszer alkalmazza a fenti visszatérési és Error függvényeket.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Küldje el az API-kérést. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és állítsa be a `content-type`t `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Futtassa az alkalmazást, és tekintse meg a választ

Ez az alkalmazás a böngészőben is futtatható. A sikeres válaszokat JSON formátumban adja vissza a rendszer. A JSON-válasz a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json)is megtalálható:

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://go.microsoft.com/fwlink/?linkid=2089907)

Ha szeretné megtudni, hogyan működik a Ink-felismerési API egy digitális, a GitHubon futó alkalmazásban, tekintse meg az alábbi példákat a GitHubon:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
