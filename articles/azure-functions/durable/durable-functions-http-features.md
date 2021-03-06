---
title: HTTP-szolgáltatások a Durable Functions-Azure Functions
description: Ismerje meg a Azure Functions Durable Functions bővítményének integrált HTTP-funkcióit.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: a7d8891c6f925cfac326685f01ba5f6149a1b233
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278063"
---
# <a name="http-features"></a>HTTP-funkciók

A Durable Functions számos funkcióval rendelkezik, amelyekkel könnyedén beépítheti a tartós és az entitásokat a HTTP-munkafolyamatokban. Ez a cikk részletesen ismerteti ezeket a funkciókat.

## <a name="exposing-http-apis"></a>HTTP API-k kimutatása

A bevezetések és az entitások HTTP-kérelmek használatával hívhatók és kezelhetők. A Durable Functions bővítmény a beépített HTTP API-kat teszi elérhetővé. Emellett API-kat is biztosít a munkafolyamatokkal és entitásokkal való interakcióhoz a HTTP által aktivált függvények között.

### <a name="built-in-http-apis"></a>Beépített HTTP API-k

A Durable Functions bővítmény automatikusan hozzáadja a HTTP API-kat a Azure Functions gazdagéphez. Ezekkel az API-kkal bármilyen kód írása nélkül használhatja és kezelheti a munkafolyamatokat és az entitásokat.

A következő beépített HTTP API-k támogatottak.

* [Új előkészítés indítása](durable-functions-http-api.md#start-orchestration)
* [Lekérdezés-előkészítési példány](durable-functions-http-api.md#get-instance-status)
* [Összehangoló példány leállítása](durable-functions-http-api.md#terminate-instance)
* [Külső esemény küldése egy előkészítési folyamatnak](durable-functions-http-api.md#raise-event)
* [Előkészítési előzmények törlése](durable-functions-http-api.md#purge-single-instance-history)
* [Műveleti esemény küldése egy entitásnak](durable-functions-http-api.md#signal-entity)
* [Entitás állapotának beolvasása](durable-functions-http-api.md#get-entity)
* [Az entitások listájának lekérdezése](durable-functions-http-api.md#list-entities)

A Durable Functions bővítmény által elérhető beépített HTTP API-k teljes leírását a [http API](durable-functions-http-api.md) -k című cikkben találja.

### <a name="http-api-url-discovery"></a>HTTP API URL-cím felderítése

A koordináló [ügyfél-kötés](durable-functions-bindings.md#orchestration-client) olyan API-kat tesz elérhetővé, amelyekkel kényelmes http-válasz hasznos adatokat lehet elérni. Létrehozhat például egy olyan választ, amely egy adott előkészítési példányhoz tartozó felügyeleti API-kra mutató hivatkozásokat tartalmaz. Az alábbi példák egy HTTP-trigger függvényt mutatnak be, amely bemutatja, hogyan használhatja ezt az API-t egy új előkészítési példányhoz:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index. js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function. JSON**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

A Orchestrator függvény elindítása a korábban bemutatott HTTP-trigger függvények használatával bármely HTTP-ügyfél használatával megtehető. A következő cURL-parancs egy `DoWork`nevű Orchestrator-függvényt indít el:

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

A következő példa egy olyan előkészítési válaszra, amely az AZONOSÍTÓjának `abc123`. Néhány részlet el lett távolítva az egyértelműség érdekében.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Az előző példában a `Uri`ban végződő mezők mindegyike egy beépített HTTP API-nak felel meg. Ezekkel az API-kkal kezelheti a cél-előkészítési példányt.

> [!NOTE]
> A webhook URL-címeinek formátuma attól függ, hogy a Azure Functions gazdagép melyik verzióját futtatja. Az előző példa a Azure Functions 2,0 gazdagépre mutat.

Az összes beépített HTTP API leírását a [http API-referenciában](durable-functions-http-api.md)tekintheti meg.

### <a name="async-operation-tracking"></a>Aszinkron művelet követése

A korábban említett HTTP-válasz úgy lett kialakítva, hogy segítse a hosszú ideig futó HTTP aszinkron API-k megvalósítását Durable Functions. Ezt a mintát más néven a *lekérdezési fogyasztói mintának*nevezzük. Az ügyfél/kiszolgáló folyamat a következőképpen működik:

1. Az ügyfél egy HTTP-kérést bocsát ki egy hosszú ideig futó folyamat indításához, például egy Orchestrator függvényhez.
1. A cél HTTP-trigger egy HTTP 202 választ ad vissza, amelynek a helye fejléce "statusQueryGetUri" értékkel rendelkezik.
1. Az ügyfél lekérdezi az URL-címet a Location (hely) fejlécben. Az ügyfél továbbra is megtekinti a HTTP 202-válaszokat egy Location fejléctel.
1. A példány befejeződése vagy meghibásodása esetén a Location fejlécben lévő végpont a HTTP 200 értéket adja vissza.

Ez a protokoll lehetővé teszi a hosszan futó folyamatok koordinálását olyan külső ügyfelekkel vagy szolgáltatásokkal, amelyek lekérdezik a HTTP-végpontot, és követik a hely fejlécét. A minta ügyfél-és kiszolgáló-implementációja is be van építve a Durable Functions HTTP API-khoz.

> [!NOTE]
> Alapértelmezés szerint a [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/) által biztosított összes HTTP-alapú művelet támogatja a normál aszinkron műveleti mintát. Ez a funkció lehetővé teszi a hosszan futó tartós funkciók beágyazását egy Logic Apps munkafolyamat részeként. Az aszinkron HTTP-minták Logic Apps támogatásáról a [Azure Logic apps munkafolyamat-műveletek és eseményindítók dokumentációjában](../../logic-apps/logic-apps-workflow-actions-triggers.md)talál további információt.

> [!NOTE]
> A feladatokkal való interakció bármely Function típusból elvégezhető, nem csak a HTTP által aktivált függvényekből.

A következő témakörben talál további információt arról, hogyan kezelheti a folyamatokat és entitásokat az ügyféloldali API-k használatával: a [példányok kezelése című cikk](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>HTTP API-k fogyasztása

A [Orchestrator függvény kód megkötései](durable-functions-code-constraints.md)című témakörben leírtaknak megfelelően a Orchestrator függvények nem tudják közvetlenül végrehajtani az I/O-műveleteket. Ehelyett általában az I/O-műveleteket elvégező [tevékenységi funkciókat](durable-functions-types-features-overview.md#activity-functions) hívja meg.

A Durable Functions 2,0-től kezdődően a koordinálások natív módon használhatják a HTTP API-kat a [hangvezérelt trigger kötés](durable-functions-bindings.md#orchestration-trigger)használatával.

A következő mintakód egy Orchestrator függvényt mutat be, amely egy kimenő HTTP-kérést tesz:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

A "HTTP hívása" művelettel a következő műveleteket végezheti el a Orchestrator függvényekben:

* HTTP API-k közvetlen hívása a hangkezelő függvényekből, néhány korlátozással később.
* Az ügyféloldali HTTP 202-állapot lekérdezési mintáinak automatikus támogatása.
* Az [Azure által felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md) használatával a hitelesítő http-hívásokat más Azure-végpontokhoz is elvégezheti.

A HTTP-API-k közvetlenül a Orchestrator függvényekből való használatának lehetősége a gyakori forgatókönyvek bizonyos készletének kényelmét szolgálja. Ezeket a funkciókat saját maga is végrehajthatja a Activity functions használatával. Sok esetben a tevékenység-függvények nagyobb rugalmasságot biztosítanak.

### <a name="http-202-handling"></a>HTTP 202-kezelő

A "HTTP hívása" API automatikusan megvalósíthatja a lekérdezési fogyasztói minta ügyféloldali oldalát. Ha egy nevű API HTTP 202-választ ad vissza egy Location fejléctel, a Orchestrator függvény automatikusan lekérdezi a hely erőforrását, amíg nem kap választ a 202-től eltérő válaszra. Ez a válasz a Orchestrator függvény kódjába kerül.

> [!NOTE]
> A Orchestrator függvények natív módon támogatják a kiszolgálóoldali lekérdezési fogyasztói mintát is, az [aszinkron műveletek nyomon követése](#async-operation-tracking)című témakörben leírtak szerint. Ez a támogatás azt jelenti, hogy az egyik Function alkalmazásban az összehangolás egyszerűen koordinálja a Orchestrator függvényeket más Function-alkalmazásokban. Ez hasonló a [beszerelési](durable-functions-sub-orchestrations.md) koncepcióhoz, de támogatja az alkalmazások közötti kommunikációt. Ez a támogatás különösen hasznos a szolgáltatás stílusú alkalmazások fejlesztéséhez.

### <a name="managed-identities"></a>Felügyelt identitások

A Durable Functions natív módon támogatja a Azure Active Directory (Azure AD) jogkivonatokat fogadó API-hívások engedélyezését. Ez a támogatás az [Azure által felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md) használatával szerzi be ezeket a jogkivonatokat.

A következő kód egy .NET Orchestrator-függvény példája. A függvény hitelesített hívásokat kezdeményez a virtuális gépek újraindításához a Azure Resource Manager [Virtual machines REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines)használatával.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

---

Az előző példában a `tokenSource` paraméter úgy van konfigurálva, hogy [Azure Resource Manager](../../azure-resource-manager/management/overview.md)Azure ad-jogkivonatokat szerezzen be. A jogkivonatokat az erőforrás URI-ja `https://management.core.windows.net`azonosítja. A példa azt feltételezi, hogy az aktuális Function alkalmazás helyileg fut, vagy felügyelt identitású Function alkalmazásként lett telepítve. A helyi identitást vagy a felügyelt identitást feltételezi, hogy jogosult a virtuális gépek kezelésére a megadott erőforráscsoport `myRG`.

Futásidőben a konfigurált jogkivonat-forrás automatikusan egy OAuth 2,0 hozzáférési tokent ad vissza. A forrás Ezután hozzáadja a jogkivonatot tulajdonosi jogkivonatként a kimenő kérelem engedélyezési fejlécébe. Ez a modell a következő okok miatt javítja az engedélyezési fejlécek manuális hozzáadását a HTTP-kérelmekhez:

* A rendszer automatikusan kezeli a jogkivonat-frissítést. Nem kell aggódnia a lejárt jogkivonatok miatt.
* A tokeneket a rendszer soha nem tárolja tartós előkészítési állapotban.
* Nem kell kódot írnia a tokenek beszerzésének kezeléséhez.

Az előre [lefordított C# RestartVMs-mintában](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs)részletesebb példát is talál.

A felügyelt identitások nem korlátozódnak az Azure erőforrás-kezelésre. A felügyelt identitások használatával olyan API-k érhetők el, amelyek elfogadják az Azure AD tulajdonosi jogkivonatait, beleértve a Microsoft és a partnerektől származó webalkalmazások Azure-szolgáltatásait is. Egy partner webalkalmazása akár egy másik Function-alkalmazás is lehet. Az Azure AD-hitelesítést támogató Microsoft Azure-szolgáltatások listáját az Azure [ad-hitelesítést támogató Azure-szolgáltatások](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)című témakörben tekintheti meg.

### <a name="limitations"></a>Korlátozások

A HTTP API-k meghívásának beépített támogatása kényelmi funkció. Az összes forgatókönyv esetében nem megfelelő.

A Orchestrator függvények által küldött HTTP-kérések és a rájuk adott válaszok szerializálva vannak és állandók üzenetsor-üzenetként. Ez a várólista-kezelési viselkedés biztosítja a HTTP [-hívások megbízhatóságát és biztonságát a rendezési ismétlésekhez](durable-functions-orchestrations.md#reliability). Azonban a várakozási sorba állítási viselkedés korlátai is vannak:

* Minden HTTP-kérelemhez a natív HTTP-ügyfélhez képest további késleltetés szükséges.
* A várólista-üzenetbe nem illeszkedő nagyméretű kérelmek vagy válaszüzenetek jelentősen csökkenthetik a folyamat teljesítményét. Az üzenetek blob Storage-ba történő kiszervezésének terhelése az esetleges teljesítmény romlását okozhatja.
* A folyamatos átviteli, a darabolási és a bináris adattartalom nem támogatott.
* A HTTP-ügyfél viselkedésének testre szabása korlátozott.

Ha a korlátozások bármelyike érintheti a használati esetet, vegye figyelembe a tevékenységek és a nyelvspecifikus HTTP-ügyféloldali kódtárak használatát a kimenő HTTP-hívások elvégzéséhez.

> [!NOTE]
> Ha Ön .NET-fejlesztő, akkor érdemes tudni, hogy miért használja a szolgáltatás a **DurableHttpRequest** és a **DurableHttpResponse** típust a beépített .net **HttpRequestMessage** és **HttpResponseMessage** típus helyett.
>
> Ez a tervezési lehetőség szándékos. Az elsődleges ok az, hogy az egyéni típusok segítenek biztosítani, hogy a felhasználók ne tegyenek helytelen feltételezéseket a belső HTTP-ügyfél által támogatott viselkedések tekintetében. A Durable Functionsra jellemző típusok szintén megkönnyítik az API-tervezés egyszerűsítését. Emellett könnyebben elérhetővé tehetik az olyan speciális szolgáltatásokat, mint a [felügyelt identitások integrálása](#managed-identities) és a [lekérdezési fogyasztói minta](#http-202-handling). 

### <a name="extensibility-net-only"></a>Bővíthetőség (csak .NET)

A folyamat belső HTTP-ügyfelének viselkedését [Azure functions .net-függőségi befecskendezés](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection)használatával lehet testreszabni. Ez a képesség hasznos lehet a kisméretű viselkedési változások elvégzéséhez. Emellett hasznos lehet a HTTP-ügyfelet az ál-objektumok befecskendezésével tesztelni.

Az alábbi példa a függőségi befecskendezés használatát mutatja be az SSL-tanúsítványok érvényesítésének letiltásához a Orchestrator függvények esetében, amelyek külső HTTP-végpontokat hívnak meg.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A tartós entitások megismerése](durable-functions-entities.md)
