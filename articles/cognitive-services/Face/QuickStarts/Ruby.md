---
title: 'Rövid útmutató: Arcfelismerés egy képen a REST API és a Ruby használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a Ruby használatával a Face REST API segítségével azonosíthatja a képekkel kapcsolatos arcokat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 892e7e0fe362e25cf1ca69146bd27a77baf41f24
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169438"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-ruby"></a>Rövid útmutató: Arcfelismerés egy képen a REST API és a Ruby használatával

Ebben a rövid útmutatóban az Azure Face REST API a Ruby használatával ismeri fel az emberi arcokat egy képben.

## <a name="prerequisites"></a>Előfeltételek

- Egy Face előfizetési kulcs. A [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api)ingyenes próbaverziós előfizetési kulcsot is kaphat. Vagy kövesse a [Cognitive Services fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Face szolgáltatásra való előfizetéshez és a kulcs beszerzése című témakör utasításait.
- Kódszerkesztő, például [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="write-the-script"></a>A parancsfájl írása

Hozzon létre egy új, _faceDetection. RB_fájlt, és adja hozzá a következő kódot. Ez a kód meghívja a Face API egy adott rendszerkép URL-címére.

```ruby
require 'net/http'

# replace <My Endpoint String> in the URL below with the string from your endpoint.
uri = URI('https://<My Endpoint String>.com/face/v1.0/detect')
uri.query = URI.encode_www_form({
    # Request parameters
    'returnFaceId' => 'true',
    'returnFaceLandmarks' => 'false',
    'returnFaceAttributes' => 'age,gender,headPose,smile,facialHair,glasses,' +
        'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

imageUri = "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg"
request.body = "{\"url\": \"" + imageUri + "\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body
```

Az előfizetési kulccsal frissítenie kell a `request['Ocp-Apim-Subscription-Key']` értéket, és módosítania kell a `uri` karakterláncot, hogy az tartalmazza a megfelelő végpontot.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

Előfordulhat, hogy módosítani szeretné a `imageUri` mezőt úgy, hogy a saját bemeneti képére mutasson. Érdemes lehet módosítani a `returnFaceAttributes` mezőt is, amely megadja a lekérdezni kívánt Arcfelismerés-attribútumokat.

## <a name="run-the-script"></a>A szkript futtatása

Futtassa a Ruby parancsfájlt a következő paranccsal:

```shell
ruby faceDetection.rb
```

Meg kell jelennie a konzolon kinyomtatott, észlelt arc típusú információk JSON-karakterláncának. A következő szöveg egy sikeres JSON-válasz példáját szemlélteti.

```json
[
  {
    "faceId": "e93e0db1-036e-4819-b5b6-4f39e0f73509",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "37c7c4bc-fda3-4d8d-94e8-b85b8deaf878",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Ruby-szkriptet írt, amely meghívja az Azure Face Service-t, hogy felderítse a képekben lévő arcokat, és visszaadja az attribútumait További információért olvassa el a Face API dokumentációját.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)