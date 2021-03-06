---
title: 'Gyors útmutató: webes keresés végrehajtása Node. js-Bing Web Search REST API'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kérelmeket küldhet a Bing Web Search REST API Node. js használatával, és JSON-választ kap
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 54f4b38e01b51289319390779a140346befc6f0c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76168805"
---
# <a name="quickstart-search-the-web-using-the-bing-web-search-rest-api-and-nodejs"></a>Gyors útmutató: a webes keresés a Bing Web Search REST API és a Node. js használatával

Ezzel a rövid útmutatóval elvégezheti az első hívását a Bing Web Search API, és megkaphatja a JSON-választ. Ez a Node. js-alkalmazás egy keresési kérelmet küld az API-nak, és megjeleníti a választ. Az alkalmazás JavaScriptben való megírásakor az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

Az alábbi dolgokra szüksége lesz a rövid útmutató futtatásához:

* A [Node.js 6-os](https://nodejs.org/en/download/) vagy újabb verziója
* Egy előfizetői azonosító

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-declare-required-modules"></a>Projekt létrehozása és a szükséges modulok deklarálása

Hozzon létre egy új Node.js-projektet a kedvenc IDE-környezetében vagy szerkesztőjében.
Ezután másolja az alábbi kódrészletet egy `search.js` nevű fájlba a projektjében.

```javascript
// Use this simple app to query the Bing Web Search API and get a JSON response.
// Usage: node search.js "your query".
const https = require('https')
```

## <a name="set-the-subscription-key"></a>Az előfizetői azonosító megadása

Ez a kódrészlet az `AZURE_SUBSCRIPTION_KEY` környezeti változót használja az előfizetői azonosító tárolásához, ami célszerű megoldás az azonosítók véletlen felfedésének megakadályozására a kód telepítése során. Az előfizetési kulcs megkereséséhez nyissa meg az API-k [lapot](https://azure.microsoft.com/try/cognitive-services/my-apis/?apiSlug=search-api-v7) .

Amennyiben nem ismeri a környezeti változók használatát, vagy minél előbb futtatni szeretné az alkalmazást, sztringként is lecserélheti a `process.env['AZURE_SUBSCRIPTION_KEY']` értékét az előfizetői azonosítóra.

```javascript
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('AZURE_SUBSCRIPTION_KEY is not set.')
}
```

## <a name="create-a-function-to-make-the-request"></a>Függvény létrehozása a kérés végrehajtásához

A függvény egy biztonságos GET kérést hajt végre, és a keresési lekérdezést lekérdezési paraméterként menti az elérési úthoz. `hostname` lehet az alábbi globális végpont, vagy az erőforráshoz tartozó Azure Portal megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpont.  Az `encodeURIComponent` az érvénytelen karakterek feloldására szolgál, az előfizetői azonosító átadása pedig a fejlécben történik. A visszahívásra adott [válasz](https://nodejs.org/dist/latest-v10.x/docs/api/http.html#http_class_http_serverresponse) feliratkozik a `data` eseményre a JSON-törzs összesítéséhez, az `error` eseményre az esetleges hibák naplózásához, és az `end` eseményre az üzenet befejezési időpontjának lekéréséhez. Amikor végzett, az alkalmazás kinyomtatja az érdekesebb fejléceket és az üzenet törzsét. Kedve szerint kísérletezhet a színekkel, és beállíthatja a részletességet. `1` részletességgel remek összegzést készíthet a válaszról.

```javascript
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
```

## <a name="get-the-query"></a>A lekérdezés fogadása

A lekérdezés megkereséséhez tekintsük át a program argumentumait. Az első argumentum a csomópont elérési útja, a második a fájlnév, a harmadik pedig a lekérdezés. Ha a lekérdezés argumentuma hiányzik, akkor a „Microsoft Cognitive Services” alapértelmezett lekérdezését használjuk.

```javascript
const query = process.argv[2] || 'Microsoft Cognitive Services'
```

## <a name="make-a-request-and-print-the-response"></a>Kérés indítása és a válasz megjelenítése

Most, hogy minden beállítást megadtunk, hívjuk meg a függvényt!

```javascript
bingWebSearch(query)
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés a saját kód futtatása: `node search.js "<your query>"`.

Ha szeretné összevetni a saját kódját a miénkkel, íme az egész program:

```javascript
const https = require('https')
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('Missing the AZURE_SUBSCRIPTION_KEY environment variable')
}
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
const query = process.argv[2] || 'Microsoft Cognitive Services'
bingWebSearch(query)
```

## <a name="sample-response"></a>Mintaválasz

A Bing Web Search API válaszai JSON formátumban érkeznek vissza. A mintaválasz egyetlen eredményre van csonkolva.

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face service from Microsoft Azure. ... Cognitive Services; Face service;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás-oktatóanyag a Bing Web Search használatához](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
