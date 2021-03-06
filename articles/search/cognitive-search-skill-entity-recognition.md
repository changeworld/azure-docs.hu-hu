---
title: Entitás-felismerés – kognitív képesség
titleSuffix: Azure Cognitive Search
description: Különböző típusú entitások kinyerése egy alkoholtartalom-növelési folyamatból az Azure Cognitive Searchban.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6393c1eeaaa72d653704fcc52442bfb326dc2cdd
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472332"
---
#   <a name="entity-recognition-cognitive-skill"></a>Entitás-felismerés – kognitív képesség

Az **entitás-felismerési** képesség Kinyeri a szövegből származó különböző típusú entitásokat. Ez a képesség a Cognitive Services [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 50 000 karakternek kell lennie [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)alapján mérve. Ha meg kell szakítania az adatokat, mielőtt elküldené a kivonó kifejezést, érdemes lehet a [szöveg felosztása képességet](cognitive-search-skill-textsplit.md)használni.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket, és mindegyik nem kötelező.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| kategóriák    | A kinyerni kívánt kategóriák tömbje.  Lehetséges kategóriájú típusok: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Ha nincs megadva kategória, a rendszer az összes típust adja vissza.|
|defaultLanguageCode |  A bemeneti szöveg nyelvi kódja A következő nyelvek támogatottak: `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans`. Nem minden entitás-kategória támogatott az összes nyelven; lásd az alábbi megjegyzést.|
|minimumPrecision | 0 és 1 közötti érték. Ha a megbízhatósági pontszám (a `namedEntities` kimenetében) alacsonyabb ennél az értéknél, az entitás nem lesz visszaadva. Az alapértelmezett érték a 0. |
|includeTypelessEntities | Állítsa `true` értékre, ha fel szeretné ismerni azokat a jól ismert entitásokat, amelyek nem felelnek meg az aktuális kategóriáknak. Az elismert entitások a `entities` összetett kimenet mezőben lesznek visszaadva. Például a "Windows 10" egy jól ismert entitás (a termék), de mivel a "Products" nem támogatott kategória, ez az entitás szerepel az entitások kimenet mezőjében. Az alapértelmezett érték `false` |


## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemeneti név      | Leírás                   |
|---------------|-------------------------------|
| languageCode  | Választható. Az alapértelmezett érték a `"en"`.  |
| szöveg          | Az elemezni kívánt szöveg.          |

## <a name="skill-outputs"></a>Szaktudás kimenetei

> [!NOTE]
> Az entitások összes kategóriája nem támogatott az összes nyelv esetében. A fenti nyelvek teljes listája a `"Person"`, a `"Location"`és a `"Organization"` entitások kategóriáit támogatja. Csak a _de_, az _en_, az _es_, a _fr_és a _zh-hans_ támogatja a `"Quantity"`, `"Datetime"`, `"URL"`és `"Email"` típusok kinyerését. További információ: [a Text Analytics API nyelv és régió támogatása](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support).  

| Kimenet neve     | Leírás                   |
|---------------|-------------------------------|
| személyek      | Karakterláncok tömbje, amelyben minden sztring egy személy nevét jelöli. |
| locations  | Karakterláncok tömbje, amelyben minden sztring egy helyet jelöl. |
| organizations  | Karakterláncok tömbje, amelyben minden sztring egy szervezetet jelöl. |
| mennyiségek  | Karakterláncok tömbje, amelyben minden sztring egy mennyiséget jelöl. |
| Dátum  | Karakterláncok tömbje, amelyben minden karakterlánc egy DateTime értéket jelöl (ahogy a szövegben jelenik meg). |
| URLs | Karakterláncok tömbje, amelyben minden sztring URL-címet jelöl |
| e-mailek | Karakterláncok tömbje, amelyben minden karakterlánc egy e-mailt jelöl |
| namedEntities | Összetett típusok tömbje, amely a következő mezőket tartalmazza: <ul><li>category</li> <li>érték (a tényleges entitás neve)</li><li>eltolás (az a hely, ahol a szöveg található)</li><li>megbízhatóság (a magasabb érték azt jelenti, hogy inkább valódi entitásnak kell lennie)</li></ul> |
| szervezetek | Összetett típusok tömbje, amely részletes információkat tartalmaz a szövegből kinyert entitásokról a következő mezőkkel <ul><li> név (a tényleges entitás neve. Ez a "normalizált" formát jelenti.</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (az entitáshoz tartozó wikipedia oldalára mutató hivatkozás)</li><li>bingId</li><li>típus (az elismert entitás kategóriája)</li><li>altípus (csak bizonyos kategóriák esetében érhető el, ez az entitás típusának részletesebb megjelenítését teszi lehetővé)</li><li> egyezések (egy összetett gyűjtemény, amely tartalmazza)<ul><li>szöveg (az entitás nyers szövege)</li><li>eltolás (a hely hol található)</li><li>Hossz (a nyers entitás hosszának hossza)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Minta definíciója

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##  <a name="sample-input"></a>Minta bemenet

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Példa kimenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Hibák esetei
Ha a dokumentumhoz tartozó nyelvi kód nem támogatott, a rendszer hibát ad vissza, és egyetlen entitás sincs kibontva.

## <a name="see-also"></a>Lásd még

+ [Beépített szaktudás](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
