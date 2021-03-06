---
title: 'Gyors útmutató: modell betanítása és űrlapos adatok kinyerése a cURL-Form felismerő használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap-felismerő REST API a cURL használatával betaníthatja a modelleket, és kinyerheti az adatok űrlapokból való kinyerését.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 32756187852de0834afc1dc034d3f7419f0c8087
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118393"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Gyors útmutató: űrlap-felismerő modell betanítása és űrlap-adatok kinyerése a REST API és a cURL használatával

Ebben a rövid útmutatóban az Azure űrlap-felismerő REST API a cURL használatával betanítási és pontszám-űrlapok segítségével kinyerheti a kulcs-érték párokat és táblákat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőket kell tennie:
- a [fürt](https://curl.haxx.se/windows/) telepítve van.
- Legalább hat egyforma típusú formátumból álló készlet. Ezek közül öt a modell betanításához, majd a hatodik űrlappal való teszteléséhez fog használni. Az űrlapok különböző fájltípusok lehetnek, de azonos típusú dokumentumnak kell lenniük. Ehhez a rövid útmutatóhoz [minta adatkészletet](https://go.microsoft.com/fwlink/?linkid=2090451) is használhat. Töltse fel a betanítási fájlokat egy blob Storage-tároló gyökerébe egy Azure Storage-fiókban. A tesztelési fájlokat külön mappában helyezheti el.

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Űrlap-felismerő modell betanítása

Először is szüksége lesz egy Azure Storage-blobban található betanítási adathalmazra. Legalább öt kitöltött űrlapot (PDF-dokumentumot és/vagy képet) kell tartalmaznia a fő bemeneti adatokhoz hasonló típusú vagy szerkezetű űrlapokon. Vagy egyetlen üres űrlapot is használhat két kitöltött űrlap használatával. Az üres űrlap fájlnevének tartalmaznia kell a "Empty" szót. A betanítási adataival kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../build-training-data-set.md) létrehozása című témakört.

> [!NOTE]
> A címkézett adatszolgáltatással manuálisan is felcímkézheti a betanítási adatait. Ez egy összetettebb folyamat, de jobban betanított modellt eredményez. A szolgáltatással kapcsolatos további információkért tekintse meg az Áttekintés a [címkékkel](../overview.md#train-with-labels) foglalkozó szakaszát.

Ha az Azure Blob-tárolóban található dokumentumokkal szeretne betanítani egy űrlap-felismerő modellt, hívja meg az **[Egyéni modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** API-ját a következő curl-parancs futtatásával. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le a `<Endpoint>`t arra a végpontra, amelyet az űrlap-felismerő előfizetésével kapott.
1. Cserélje le a `<subscription key>`t az előző lépésből másolt előfizetési kulccsal.
1. Cserélje le a `<SAS URL>`t az Azure Blob Storage-tároló megosztott hozzáférési aláírási (SAS) URL-címére. Az SAS URL-cím lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférésű aláírás beolvasása**elemet. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás**gombra. Ezután másolja az értéket az **URL** szakaszban. A formátumnak a következőket kell tartalmaznia: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Egy `201 (Success)` választ kap a **hely** fejlécében. Ennek a fejlécnek az értéke a betanított új modell azonosítója. 

## <a name="get-training-results"></a>Képzési eredmények beolvasása

Miután elindította a vonatok műveletét, új műveletet fog használni, **[Egyéni modell beszerzésével](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)** ellenőrizhető a betanítási állapot. Adja át a modell AZONOSÍTÓját ebbe az API-hívásba a betanítási állapot megtekintéséhez:

1. Cserélje le a `<Endpoint>`t arra a végpontra, amelyet az űrlap-felismerő előfizetési kulcsával kapott.
1. `<subscription key>` cseréje az előfizetési kulccsal
1. Cserélje le a `<model ID>`t az előző lépésben kapott modell-AZONOSÍTÓra.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

A következő formátumban kap egy `200 (Success)` választ egy JSON-törzstel. Figyelje meg a `"status"` mezőt. Ez az érték `"ready"` a betanítás befejezése után. Ha a modell nem fejeződött be, a parancs újbóli futtatásával újra le kell kérdezni a szolgáltatást. Javasoljuk, hogy a hívások között egy másodperc vagy több intervallum legyen.

A `"modelId"` mező tartalmazza a betanított modell AZONOSÍTÓját. Ezt a következő lépéshez kell megadnia.

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>A kulcs-érték párok és táblák űrlapjainak elemzése

Ezután az újonnan betanított modellt fogja használni a dokumentumok elemzéséhez és a kulcs-érték párok és táblák kinyeréséhez. Hívja meg az **[elemzés űrlap](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** API-t a következő curl-parancs futtatásával. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le a `<Endpoint>`t az űrlap-felismerő előfizetési kulcsból beszerzett végpontra. Az űrlap-felismerő erőforrás- **Áttekintés** lapon találhatja meg.
1. Cserélje le a `<model ID>`t az előző szakaszban kapott modell-AZONOSÍTÓra.
1. Cserélje le a `<SAS URL>`t egy SAS URL-címmel az Azure Storage-ban található fájlra. Kövesse a betanítás szakasz lépéseit, de ahelyett, hogy a teljes blob-tároló SAS URL-címét beolvassa, szerezze be egyet az elemezni kívánt fájlhoz.
1. A `<subscription key>` helyére írja be az előfizetési kulcsot.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Egy `202 (Success)` választ kap egy **művelet – hely** fejléctel. Ennek a fejlécnek az értéke tartalmazza az elemzési művelet eredményeinek nyomon követésére használt eredmény-azonosítót. Mentse a következő lépés eredmény-AZONOSÍTÓját.

## <a name="get-the-analyze-results"></a>Az elemzés eredményeinek beolvasása

A következő API használatával kérdezheti le az elemzési művelet eredményét.

1. Cserélje le a `<Endpoint>`t az űrlap-felismerő előfizetési kulcsból beszerzett végpontra. Az űrlap-felismerő erőforrás- **Áttekintés** lapon találhatja meg.
1. Cserélje le a `<result ID>`t az előző szakaszban kapott AZONOSÍTÓra.
1. A `<subscription key>` helyére írja be az előfizetési kulcsot.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

A következő formátumban kap egy `200 (Success)` választ egy JSON-törzstel. A kimenet lerövidítve az egyszerűség kedvéért. Figyelje meg a lenti `"status"` mezőt. Az elemzési művelet befejezésekor `"succeeded"` érték lesz. Ha az elemzési művelet nem fejeződött be, újra le kell kérdezni a szolgáltatást a parancs újbóli futtatásával. Javasoljuk, hogy a hívások között egy másodperc vagy több intervallum legyen.

A fő kulcs/érték párok társításai és táblái a `"pageResults"` csomópontban találhatók. Ha a *includeTextDetails* URL paraméterrel is megadja az egyszerű szöveg kibontását, akkor a `"readResults"` csomópont megjeleníti a dokumentumban lévő összes szöveg tartalmát és pozícióit.

```json
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Az eredmények javítása

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az űrlap-felismerő REST API és a cURL használatával végezte el a modell betanítását, és egy minta forgatókönyvben futtatja azt. Következő lépésként tekintse meg a dokumentációt az űrlap-felismerő API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
