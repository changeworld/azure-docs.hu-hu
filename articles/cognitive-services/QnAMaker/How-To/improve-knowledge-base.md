---
title: Tudásbázis fejlesztése – QnA Maker
titleSuffix: Azure Cognitive Services
description: Az aktív tanulással javíthatja a Tudásbázis minőségét. Áttekintheti, elfogadhatja vagy elutasíthatja a meglévő kérdések eltávolítása vagy módosítása nélkül.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: diberry
ms.openlocfilehash: dea2bf3b34ca336f3932dd85bf587184ab6881db
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220687"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Aktív tanulás használata a tudásbázis továbbfejlesztéséhez

Az [aktív tanulás](../Concepts/active-learning-suggestions.md) lehetővé teszi a Tudásbázis minőségének javítását azáltal, hogy alternatív kérdéseket tesz fel a felhasználói beadványok alapján a kérdés és a válasz párok számára. Ezeket a javaslatokat megtekintheti, vagy hozzáadhatja őket a meglévő kérdésekhez, vagy elutasíthatja őket.

A Tudásbázis nem változik automatikusan. A módosítások érvénybe léptetéséhez el kell fogadnia a javaslatokat. Ezek a javaslatok kérdéseket tesznek fel, de nem módosítják vagy nem távolítják el a meglévő kérdéseket.


## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>A futtatókörnyezet verziójának frissítése az aktív tanulás használatára

Az aktív tanulást a futtatókörnyezet 4.4.0 és újabb verziói támogatják. Ha a Tudásbázis egy korábbi verzión lett létrehozva, [frissítse a futtatókörnyezetet](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) a szolgáltatás használatára.

## <a name="turn-on-active-learning-to-see-suggestions"></a>A javaslatok megjelenítéséhez kapcsolja be az aktív tanulást

Az aktív tanulás alapértelmezés szerint ki van kapcsolva. A javasolt kérdések megtekintéséhez kapcsolja be a következőt:. Az aktív tanulás bekapcsolását követően el kell küldenie az adatokat az ügyfélalkalmazástól a QnA Maker. További információ: [építészeti folyamat a GenerateAnswer és a Train API-k egy robotból való használatához](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. A Tudásbázis közzétételéhez válassza a **Közzététel** lehetőséget. Az aktív tanulási lekérdezések csak a GenerateAnswer API-előrejelzési végpontról lesznek összegyűjtve. A QnA Maker portál teszt ablaktáblájára irányuló lekérdezések nem érintik az aktív tanulást.

1. Az aktív tanulás bekapcsolásához a QnA Maker-portálon nyissa meg a jobb felső sarokban, válassza ki a **nevét**, és lépjen a [**Szolgáltatásbeállítások**](https://www.qnamaker.ai/UserSettings)menüpontra.

    ![Kapcsolja be az aktív tanulás javasolt kérdéseit a szolgáltatás beállításai lapról. Válassza ki a felhasználónevét a jobb felső menüben, majd válassza a Szolgáltatásbeállítások elemet.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Keresse meg a QnA Maker szolgáltatást, majd állítsa be az **aktív tanulást**.

    > [!div class="mx-imgBorder"]
    > [![a szolgáltatás beállításai lapon kapcsolja be az aktív tanulási funkciót. Ha nem tudja váltani a szolgáltatást, előfordulhat, hogy frissítenie kell a szolgáltatást.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Az előző képen megadott pontos verzió csak példaként jelenik meg. A verzió eltérő lehet.

    Az **aktív tanulás** engedélyezése után a Tudásbázis rendszeres időközönként új kérdéseket javasol a felhasználó által benyújtott kérdések alapján. Az **aktív tanulást** letilthatja a beállítás újbóli bekapcsolásával.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Aktív tanulási javaslat elfogadása a Tudásbázisban

Az aktív tanulás megváltoztatja a tudásbázist vagy Search Service a javaslat jóváhagyása után, majd a Mentés és a betanítás lehetőségre. Ha jóváhagyja a javaslatot, azt a rendszer alternatív kérdésként adja hozzá.

1. A javasolt kérdések megtekintéséhez a Tudásbázis **szerkesztése** lapon válassza a **megtekintési beállítások**, majd az **aktív tanulási javaslatok megjelenítése**lehetőséget.

    [![a portál szerkesztés szakaszában válassza a javaslatok megjelenítése lehetőséget, hogy megtekintse az aktív tanulás új kérdéseit.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. A tudásbázist a kérdések és válaszok párok alapján szűrheti, hogy csak a javaslatok jelenjenek meg a **szűrési javaslatok**lehetőség kiválasztásával.

    [![a Filter by Suggestions (szűrési javaslatok) váltógomb használatával csak az aktív tanulás javasolt kérdéses alternatívákat tekintheti meg.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Minden QnA pár az új kérdéses alternatívákat javasolja, `✔`, hogy elfogadják a kérdést vagy `x` a javaslatok elutasításához. Jelölje be a jelölőnégyzetet a kérdés hozzáadásához.

    [![válassza ki vagy utasítsa el az aktív tanulás javasolt kérdésének alternatívákat a zöld pipa vagy a vörös törlési megjelölés kiválasztásával.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Az összes _javaslat_ hozzáadásához vagy törléséhez kattintson az összes **hozzáadása** vagy az **összes elutasítása** lehetőségre a környezetfüggő eszköztáron.

1. Válassza a **Mentés és a betanítás** lehetőséget a Tudásbázis módosításainak mentéséhez.

1. Válassza a **Közzététel** lehetőséget, hogy a módosítások elérhetők legyenek a [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration)-ból.

    Ha 5 vagy több hasonló lekérdezés van csoportosítva, 30 percenként QnA Maker javasolja az elfogadására vagy elutasítására vonatkozó alternatív kérdéseket.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Építészeti folyamat a GenerateAnswer és a Train API-k egy robotból való használatához

A bot vagy más ügyfélalkalmazás a következő építészeti folyamatot használja az aktív tanulás használatához:

* A robot megkapja a GenerateAnswer API-val [kapott választ a Tudásbázisban](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) , a `top` tulajdonságot használva számos választ kaphat.
* A bot explicit visszajelzést határoz meg:
    * A saját [egyéni üzleti logikájának](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)használatával kiszűrheti az alacsony pontszámot.
    * A bot vagy az ügyfél alkalmazásban a lehetséges válaszok megjelenítése a felhasználó számára, és a felhasználó kiválasztott válaszának beolvasása.
* A robot a [betanítási API](#train-api) [használatával visszaküldi a kiválasztott választ a QnA Makerra](#bot-framework-sample-code) .


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>A GenerateAnswer kérelemben szereplő Top tulajdonsággal több egyező választ kaphat

Ha egy kérdésre QnA Maker választ küld, a JSON törzs `top` tulajdonsága beállítja a visszaadott válaszok számát.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>A score (pontszám) tulajdonság és az üzleti logika használata a felhasználók számára megjelenített válaszok listájának lekéréséhez

Ha az ügyfélalkalmazás (például egy csevegési robot) megkapja a választ, a rendszer az első 3 kérdést adja vissza. A pontszámok közötti közelség elemzéséhez használja a `score` tulajdonságot. A közelségi tartományt a saját üzleti logikája határozza meg.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Ügyfélalkalmazás követése, ha a kérdések hasonló pontszámokkal rendelkeznek

Az ügyfélalkalmazás megjeleníti azokat a kérdéseket, amelyekkel a felhasználó kiválaszthatja a céljuknak leginkább megfelelő _egyetlen kérdést_ .

Miután a felhasználó kiválasztja az egyik meglévő kérdést, az ügyfélalkalmazás a QnA Maker 's Train API-val visszajelzést küld a felhasználónak. Ez a visszajelzés befejezi az aktív tanulási visszajelzési ciklust.

## <a name="train-api"></a>API betanítása

Az aktív tanulási visszajelzéseket a rendszer a Train API POST kérelemmel QnA Maker küldi el. Az API-aláírás:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP-kérelem tulajdonsága|Name (Név)|Típus|Cél|
|--|--|--|--|
|URL-útvonal paraméter|Tudásbázis-azonosító|sztring|A Tudásbázis GUID azonosítója.|
|Egyéni altartomány|QnAMaker-erőforrás neve|sztring|Az erőforrás neve a QnA Maker egyéni altartománya lesz. Ez a Tudásbázis közzététele után a beállítások lapon érhető el. `host`jelenik meg.|
|Fejléc|Content-Type|sztring|Az API-nak eljuttatott törzs adathordozó-típusa. Alapértelmezett érték: `application/json`|
|Fejléc|Engedélyezés|sztring|A végpont kulcsa (EndpointKey XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX).|
|Post törzs|JSON-objektum|JSON|A betanítási visszajelzés|

A JSON-törzs több beállítással rendelkezik:

|JSON-törzs tulajdonság|Típus|Cél|
|--|--|--|--|
|`feedbackRecords`|tömb|Visszajelzések listája.|
|`userId`|sztring|A javasolt kérdéseket elfogadó személy felhasználói azonosítója. A felhasználói azonosító formátuma. Például egy e-mail-cím lehet érvényes felhasználói azonosító az architektúrában. Választható.|
|`userQuestion`|sztring|A felhasználó lekérdezésének pontos szövege. Kötelező.|
|`qnaID`|szám|A [GenerateAnswer válaszban](metadata-generateanswer-usage.md#generateanswer-response-properties)található kérdés azonosítója. |

A JSON-törzs például a következőképpen néz ki:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

A sikeres válasz 204 állapotot ad vissza, és nem a JSON-válasz törzsét.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Batch – sok visszajelzési rekord egyetlen hívásba

Az ügyféloldali alkalmazásban, például egy robotban tárolhatja az adatokat, majd több rekordot is küldhet egyetlen JSON-törzsbe a `feedbackRecords` tömbben.

A JSON-törzs például a következőképpen néz ki:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Robot Framework-mintakód

A robot Framework kódjának meg kell hívnia a Train API-t, ha a felhasználó lekérdezését az aktív tanuláshoz kell használni. A kód kétféleképpen írható:

* Annak megállapítása, hogy kell-e lekérdezést használni az aktív tanuláshoz
* Lekérdezés küldése vissza a QnA Maker Train API-nak az aktív tanuláshoz

Az [Azure bot Sample](https://aka.ms/activelearningsamplebot)-ben mindkét tevékenység programozása megtörtént.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Példa C# kód a Train API-hoz a robot Framework 4. x használatával

Az alábbi kód bemutatja, hogyan küldhet vissza adatokat QnA Makerra a Train API-val. Ez a [teljes mintakód](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) a githubon érhető el.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Példa Node. js-kód a Train API-hoz a robot Framework 4. x használatával

Az alábbi kód bemutatja, hogyan küldhet vissza adatokat QnA Makerra a Train API-val. Ez a [teljes mintakód](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) a githubon érhető el.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Az aktív tanulás az exportált Tudásbázisban lett mentve

Ha az alkalmazás aktív tanulást engedélyez, és az alkalmazást exportálja, a TSV-fájl `SuggestedQuestions` oszlopa megőrzi az aktív tanulási adatait.

A `SuggestedQuestions` oszlop implicit, `autosuggested`és explicit `usersuggested` visszajelzések JSON-objektuma. Egy példa erre a JSON-objektumra a `help` egy felhasználó által küldött kérdéséhez:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Az Alters API letöltése lehetőséggel áttekintheti ezeket a módosításokat a REST vagy a Language-alapú SDK-k használatával:
* [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


Ha újraimportálja az alkalmazást, az aktív tanulás továbbra is gyűjti az adatokat, és javaslatokat javasol a tudásbázishoz.



## <a name="best-practices"></a>Ajánlott eljárások

Az aktív tanulás használata esetén ajánlott eljárások: [ajánlott eljárások](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Metaadatok használata a GenerateAnswer API-val](metadata-generateanswer-usage.md)
