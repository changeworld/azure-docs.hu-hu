---
title: Metaadatok a GenerateAnswer API-val – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker lehetővé teszi metaadatok hozzáadását kulcs/érték párok formájában a kérdés-válasz készletekhez. Az eredményeket felhasználói lekérdezésekre szűrheti, és további, a követési beszélgetésekbe felhasználható információk tárolására is használható.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 6a8cbabfd4e47c50d2c2e6f4a23c50a931e645a0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220643"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Válasz kérése a GenerateAnswer API-val és a metaadatokkal

Ha az előre jelzett választ egy felhasználó kérdéséhez szeretné lekérni, használja a GenerateAnswer API-t. Ha közzétesz egy tudásbázist, láthatja, hogyan használhatja ezt az API-t a **közzétételi** oldalon. Az API-t úgy is beállíthatja, hogy metaadatok alapján szűrje a válaszokat, és a végponton tesztelje a tudásbázist a test Query string paraméterrel.

QnA Maker lehetővé teszi metaadatok hozzáadását kulcs-érték párok formájában a kérdések és válaszok csoportjaihoz. Ezt az információt használhatja az eredmények felhasználói lekérdezésekre való szűrésére, valamint a követési beszélgetések során használható további információk tárolására. További információ: [Tudásbázis](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Kérdések és válaszok tárolása QnA-entitással

Fontos tisztában lenni azzal, hogy a QnA Maker hogyan tárolja a kérdés-és adatválaszait. Az alábbi ábrán egy QnA entitás látható:

![QnA entitás ábrája](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Minden QnA entitás egyedi és állandó AZONOSÍTÓval rendelkezik. Az AZONOSÍTÓval egy adott QnA-entitás frissítését végezheti el.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Válaszok előrejelzése a GenerateAnswer API-val

A [GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) -t a robotban vagy az alkalmazásban használhatja a Tudásbázis felhasználói kérdésekkel való lekérdezéséhez, hogy a legjobb egyezést kapja a kérdés-és a válasz-készletekben.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Közzététel a GenerateAnswer-végpont beszerzéséhez

Miután közzétette a tudásbázist, vagy a [QnA Maker portálról](https://www.qnamaker.ai)vagy az [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)használatával, megtekintheti az GenerateAnswer-végpont részleteit.

A végpont részleteinek beszerzése:
1. Jelentkezzen be itt: [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. A **saját Tudásbázisban**válassza a Tudásbázisban a **kód megtekintése** lehetőséget.
    ![képernyőkép a Tudásbázisról](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. A GenerateAnswer-végpont részleteinek beolvasása.

    ![A végpont részleteinek képernyőképe](../media/qnamaker-how-to-metadata-usage/view-code.png)

A végpont adatait a Tudásbázis **Settings (beállítások** ) lapjáról is lekérheti.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer-kérelem konfigurálása

A GenerateAnswer HTTP POST-kéréssel hívható meg. A GenerateAnswer meghívását bemutató mintakód: gyors [útmutató.](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)

A POST kérelem a következőket használja:

* Szükséges [URI-paraméterek](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Szükséges fejléc-tulajdonság, `Authorization`, biztonsági
* A [törzs szükséges tulajdonságai](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

A GenerateAnswer URL-címének formátuma a következő:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Ne feledje, hogy állítsa be `Authorization` HTTP-fejléc tulajdonságát a karakterlánc értékével `EndpointKey` egy záró szóközzel, a végpont kulcsát pedig a **Beállítások** lapon találja.

A JSON-törzs például a következőképpen néz ki:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

További információ a [rankerType](../concepts/best-practices.md#choosing-ranker-type).

Az előző JSON-kérelem csak a 30%-os vagy a küszöbérték feletti válaszokat kérte.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer válasz tulajdonságai

A [Válasz](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) egy JSON-objektum, amely tartalmazza a válasz megjelenítéséhez szükséges összes információt, valamint a beszélgetés következő bekapcsolását, ha van ilyen.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

Az előző JSON egy, a 38,5%-os pontszámmal válaszol.

## <a name="use-qna-maker-with-a-bot-in-c"></a>QnA Maker használata a robottalC#

A bot Framework hozzáférést biztosít a QnA Maker tulajdonságaihoz a [GETANSWER API](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)-val:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Az előző JSON-kérelem csak a 30%-os vagy a küszöbérték feletti válaszokat kérte.

A támogatási robotnak van [egy példája](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) ezzel a kóddal.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>QnA Maker használata robottal a Node. js-ben

A bot Framework hozzáférést biztosít a QnA Maker tulajdonságaihoz a [GETANSWER API](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)-val:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Az előző JSON-kérelem csak a 30%-os vagy a küszöbérték feletti válaszokat kérte.

A támogatási robotnak van [egy példája](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) ezzel a kóddal.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Metaadatok használata egyéni metaadat-címkék alapján történő szűréshez

A metaadatok hozzáadása lehetővé teszi a válaszok szűrését a metaadatok címkéi alapján. Adja hozzá a metaadatok oszlopot a **nézet beállításai** menüből. Metaadatok hozzáadása a tudásbázishoz a metaadatok **+** ikon kiválasztásával adhat hozzá metaadat-párokat. Ez a pár egy kulcsból és egy értékből áll.

![A metaadatok hozzáadásának képernyőképe](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Eredmények szűrése a strictFilters a metaadatok címkéi számára

Vegye figyelembe, hogy "Ha ez a Hotel be van zárva?" kérdésre a "Paradise" (paradicsom) nevű étterem esetében a szándék vonatkozik.

Mivel az eredmények csak a "Paradise" étterem esetében szükségesek, beállíthat egy szűrőt a GenerateAnswer hívásában az "étterem neve" metaadatokban. A következő példa ezt mutatja be:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Kérdések és válaszok eredményeinek használata a beszélgetési környezet megőrzése érdekében

A GenerateAnswer válasza tartalmazza az egyeztetett kérdés és a válaszfájl megfelelő metaadat-információit. Ezt az információt használhatja az ügyfélalkalmazás számára, hogy az előző beszélgetés kontextusát tárolja a későbbi beszélgetések során való használatra.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Csak kérdések egyeztetése szöveg szerint

Alapértelmezés szerint a QnA Maker kérdésekkel és válaszokkal keres. Ha csak kérdésekkel szeretne keresni, válasz létrehozásához használja a `RankerType=QuestionOnly` a GenerateAnswer kérelem POST törzsében.

A közzétett Tudásbázisban `isTest=false`vagy a tesztelési Tudásbázisban kereshet a `isTest=true`használatával.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Gyakori HTTP-hibák

|Kód|Magyarázat|
|:--|--|
|2xx|Sikeres|
|400|kérés paraméterei helytelen, ami azt jelenti, a szükséges paraméterek: hiányzó, hibás vagy túl nagy|
|400|kérelem törzse nem megfelelő, ami azt jelenti, a JSON-ja hiányzik, hibás vagy túl nagy|
|401|Érvénytelen kulcs|
|403|-Tiltott nem rendelkezik megfelelő engedélyekkel|
|404|KB nem létezik.|
|410|Ez az API elavult, és már nem érhető el|

## <a name="next-steps"></a>Következő lépések

A **közzétételi** oldal olyan információkat is tartalmaz, amelyekkel a Poster vagy a curl segítségével [választ](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) kaphat.

> [!div class="nextstepaction"]
> [Tudásbázis-robot létrehozása](../tutorials/integrate-qnamaker-luis.md)
