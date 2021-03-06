---
title: Proxyk használata Azure Functions
description: A Azure Functions-proxyk használatának áttekintése
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 09e4616bc7cbb4361ad067ed64984ed95e9a20c5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849190"
---
# <a name="work-with-azure-functions-proxies"></a>Azure Functions-proxyk használata

Ez a cikk a Azure Functions-proxyk konfigurálását és működését ismerteti. Ezzel a funkcióval olyan végpontokat adhat meg a Function alkalmazásban, amelyeket egy másik erőforrás implementál. Ezekkel a proxykkal a nagyméretű API-k több Function-alkalmazásba is kioszthatók (mint a Service-architektúrában), miközben továbbra is egyetlen API-felületet mutatnak be az ügyfelek számára.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> A standard függvények számlázása a proxy végrehajtására vonatkozik. További információ: [Azure functions díjszabása](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Proxy létrehozása

Ebből a szakaszból megtudhatja, hogyan hozhat létre proxyt a functions portálon.

1. Nyissa meg a [Azure Portalra], majd nyissa meg a Function alkalmazást.
2. A bal oldali panelen válassza az **új proxy**lehetőséget.
3. Adja meg a proxy nevét.
4. Konfigurálja a függvény alkalmazásban elérhető végpontot az **útválasztási sablon** és a **http-metódusok**megadásával. Ezek a paraméterek a [http-eseményindítók]szabályainak megfelelően viselkednek.
5. Állítsa be a **háttérbeli URL-címet** egy másik végpontra. Ez a végpont lehet függvény egy másik Function alkalmazásban, vagy bármely más API lehet. Az értéknek nem kell statikusnak lennie, és az [Alkalmazásbeállítások] és [paramétereit is hivatkozhat az eredeti ügyfél-kérelemből].
6. Kattintson a  **Create** (Létrehozás) gombra.

A proxy már létezik új végpontként a Function alkalmazásban. Az ügyfél szemszögéből a Azure Functions egy HttpTrigger egyenértékű. Az új proxy kipróbálható úgy, hogy átmásolja a proxy URL-címét, és teszteli a kedvenc HTTP-ügyfelével.

## <a name="modify-requests-responses"></a>Kérelmek és válaszok módosítása

A Azure Functions-proxyk segítségével módosíthatja a háttérbeli kérelmeket és válaszokat. Ezek az átalakítások használhatnak változókat a [Változók használata]definiált módon.

### <a name="modify-backend-request"></a>A háttérbeli kérelem módosítása

Alapértelmezés szerint a háttér-kérelem az eredeti kérelem másolata van inicializálva. A háttérbeli URL-cím beállítása mellett módosíthatja a HTTP-metódust, a fejléceket és a lekérdezési karakterlánc paramétereit. A módosított értékek hivatkozhatnak az [Alkalmazásbeállítások] és [paramétereit is hivatkozhat az eredeti ügyfél-kérelemből].

A háttérbeli kérelmek a proxy részleteit tartalmazó lap *kérelmek felülbírálásának* kibontásával módosíthatók a portálon. 

### <a name="modify-response"></a>Válasz módosítása

Alapértelmezés szerint az ügyfél válasza a háttérbeli válasz másolata lesz inicializálva. Módosíthatja a válasz állapotkódot, az OK kifejezését, a fejléceket és a törzset. A módosított értékek hivatkozhatnak az [Alkalmazásbeállítások], [paramétereit is hivatkozhat az eredeti ügyfél-kérelemből], valamint [a háttérbeli válasz paramétereit].

A háttérbeli kérelmek a proxy részleteit tartalmazó lap *Válasz felülbírálásának* kibontásával módosíthatók a portálon. 

## <a name="using-variables"></a>Változók használata

A proxy konfigurációjának nem kell statikusnak lennie. Felkérheti, hogy az eredeti ügyfél-kérelemből, a háttér-válaszból vagy az alkalmazás beállításaiból származó változókat használjon.

### <a name="reference-localhost"></a>Hivatkozás helyi függvények
A `localhost` használatával közvetlenül is hivatkozhat egy függvényre az egyazon függvény alkalmazásban, kétirányú proxy kérés nélkül.

`"backendurl": "https://localhost/api/httptriggerC#1"` az útvonalon helyi HTTP által aktivált függvényre hivatkozik `/api/httptriggerC#1`

 
>[!Note]  
>Ha a függvény *függvény-, rendszergazdai vagy sys* -engedélyezési szinteket használ, meg kell adnia a kódot és a clientId az eredeti függvény URL-címének megfelelően. Ebben az esetben a hivatkozás a következőhöz hasonló lesz: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` javasoljuk, hogy ezeket a kulcsokat az [Alkalmazásbeállítások] között tárolja, és hivatkozni lehessen rájuk a proxyn. Ezzel elkerülhető a forráskódban tárolt titkos kódok tárolása. 

### <a name="request-parameters"></a>Hivatkozási kérelem paraméterei

A kérési paramétereket bemenetként használhatja a háttérbeli URL tulajdonsághoz, illetve a kérelmek és válaszok módosításának részeként. Bizonyos paraméterek az alapproxy konfigurációjában megadott útválasztási sablonnal köthetők, mások pedig a bejövő kérelem tulajdonságaiból származhatnak.

#### <a name="route-template-parameters"></a>Útválasztási sablon paraméterei
Az útválasztási sablonban használt paraméterek név szerint hivatkozhatók. A paraméterek neve kapcsos zárójelbe ({}) van csatolva.

Ha például egy proxy útválasztási sablonnal (például `/pets/{petId}`) rendelkezik, a háttér URL-cím a `{petId}`értékét is tartalmazza, ahogy az `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Ha az útválasztási sablon egy helyettesítő karakterrel végződik (például `/api/{*restOfPath}`), akkor az érték `{restOfPath}` a bejövő kérelemben szereplő hátralévő elérésiút-szegmens karakterlánc-ábrázolása.

#### <a name="additional-request-parameters"></a>További kérések paraméterei
Az útválasztási sablon paraméterei mellett a következő értékek is használhatók a konfigurációs értékekben:

* **{Request. Method}** : az eredeti kérelemben használt http-metódus.
* **{Request. headers.\<HeaderName\>}** : az eredeti kérelemből olvasható fejléc. Cserélje le *\<HeaderName\>* az olvasni kívánt fejléc nevére. Ha a kérelem nem tartalmazza a fejlécet, az érték az üres karakterlánc lesz.
* **{Request. querystring.\<ParameterName\>}** : egy lekérdezési karakterlánc paraméter, amely az eredeti kérelemből olvasható. Cserélje le *\<ParameterName\>* az olvasni kívánt paraméter nevére. Ha a paraméter nem szerepel a kérelemben, az érték az üres karakterlánc lesz.

### <a name="response-parameters"></a>Hivatkozás háttér-válasz paraméterei

A válasz paramétereit az ügyfélre adott válasz módosításának részeként lehet használni. A konfigurációs értékek a következő értékeket használhatják:

* **{backend. Response. statusCode}** : a háttér-válaszon VISSZAadott http-állapotkód.
* **{backend. Response. statusReason}** : a háttér-válaszon VISSZAadott http-ok kifejezése.
* **{backend. Response. headers.\<HeaderName\>}** : a háttér-válaszból olvasható fejléc. Cserélje le *\<HeaderName\>* az olvasni kívánt fejléc nevére. Ha a válasz nem tartalmazza a fejlécet, az érték az üres karakterlánc lesz.

### <a name="use-appsettings"></a>Hivatkozási alkalmazás beállításai

A Function alkalmazáshoz [definiált Alkalmazásbeállítások](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) is hivatkozhatók, ha a beállítási nevet a százalék jelek (%) értékre helyezi.

Az *https://%ORDER_PROCESSING_HOST%/api/orders* háttérbeli URL-címe például "% ORDER_PROCESSING_HOST%" lesz, a ORDER_PROCESSING_HOST beállítás értéke helyett.

> [!TIP] 
> Ha több központi telepítéssel vagy tesztelési környezettel rendelkezik, a háttérbeli gazdagépekre vonatkozó Alkalmazásbeállítások használata. Így biztos lehet benne, hogy mindig az adott környezet hátterére van szüksége.

## <a name="debugProxies"></a>Proxyk hibáinak megoldása

Ha a jelölőt `"debug":true` bármely proxyhoz hozzáadja a `proxies.json`, akkor a hibakeresési naplózást is engedélyeznie kell. A naplók tárolása `D:\home\LogFiles\Application\Proxies\DetailedTrace` történik, és a speciális eszközökön (kudu) keresztül érhető el. A HTTP-válaszok egy `Proxy-Trace-Location` fejlécet is tartalmaznak, amely egy URL-címet tartalmaz a naplófájl eléréséhez.

A proxykat az ügyfél oldaláról lehet felvenni, ha hozzáad egy `Proxy-Trace-Enabled` fejlécet `true`hoz. Ez egy nyomkövetést is naplóz a fájlrendszerben, és a válaszban fejlécként visszaküldi a nyomkövetési URL-címet.

### <a name="block-proxy-traces"></a>Proxy nyomkövetésének letiltása

Biztonsági okokból előfordulhat, hogy nem kívánja engedélyezni, hogy a szolgáltatás hívása nyomot állítson elő. A bejelentkezési hitelesítő adataik nélkül nem férhetnek hozzá a nyomkövetési tartalmakhoz, de a nyomkövetés generálja az erőforrásokat, és elérhetővé teszi a funkció-proxyk használatát.

A nyomkövetést teljes egészében letilthatja, ha `"debug":false`t ad hozzá a `proxies.json`egy adott proxyhoz.

## <a name="advanced-configuration"></a>Speciális konfiguráció

A konfigurált proxyk a Function app-címtár gyökerében található *proxys. JSON* fájlban tárolódnak. Ezt a fájlt manuálisan szerkesztheti, és az alkalmazás részeként telepítheti, ha a függvények által támogatott [központi telepítési módszereket](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) használja. 

> [!TIP] 
> Ha nem állított be egyik telepítési módszert sem, akkor a portálon a *proxys. JSON* fájllal is dolgozhat. Nyissa meg a Function alkalmazást, válassza a **platform szolgáltatások**elemet, majd válassza a **app Service Editor**lehetőséget. Ezzel megtekintheti a Function alkalmazás teljes fájljának szerkezetét, majd módosításokat végezhet.

A *proxys. JSON* -t egy proxy objektum határozza meg, amely névvel ellátott proxykat és azok definícióit alkotja. Ha a szerkesztő támogatja azt, akkor egy [JSON-sémára](http://json.schemastore.org/proxies) hivatkozhat a kód befejezéséhez. Egy példaként szolgáló fájl a következőhöz hasonló lehet:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Minden proxynak van egy felhasználóbarát neve, például a *proxy1* az előző példában. A megfelelő proxy-definíciós objektumot a következő tulajdonságok határozzák meg:

* **matchCondition**: kötelező – a proxy végrehajtását kiváltó kérelmeket meghatározó objektum. Két olyan tulajdonságot tartalmaz, amelyek a [HTTP-eseményindítók]vannak megosztva:
    * _metódusok_: a proxy által válaszoló http-metódusok tömbje. Ha nincs megadva, a proxy az útvonalon lévő összes HTTP-metódusra válaszol.
    * _Route_: kötelező – meghatározza az útválasztási sablont, amely szabályozza, hogy a proxy mely URL-címekre válaszol. A http-eseményindítók eltérően nincs alapértelmezett érték.
* **backendUri**: annak a háttér-erőforrásnak az URL-címe, amelyre a kérést proxynak kell lennie. Ez az érték hivatkozhat az alkalmazás beállításaira és a paraméterekre az eredeti ügyfél-kérelem alapján. Ha ez a tulajdonság nem szerepel, Azure Functions válaszol a HTTP 200 OK-val.
* **requestOverrides**: olyan objektum, amely meghatározza a háttér-kérelem átalakításait. Lásd: [requestOverrides objektum definiálása].
* **responseOverrides**: olyan objektum, amely meghatározza az ügyfél válaszának átalakításait. Lásd: [responseOverrides objektum definiálása].

> [!NOTE] 
> A Azure Functions-proxyk *Route* tulajdonsága nem tartja tiszteletben a függvényalkalmazás gazdagép konfigurációjának *routePrefix* tulajdonságát. Ha olyan előtagot szeretne megadni, mint például a `/api`, azt az *Route* tulajdonságba kell foglalni.

### <a name="disableProxies"></a>Egyéni proxyk letiltása

Az egyes proxykat letilthatja `"disabled": true` hozzáadásával a proxyhoz a `proxies.json` fájlban. Ennek hatására a matchCondition a 404-as visszaküldésére irányuló kérések lesznek.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="applicationSettings"></a>Alkalmazásbeállítások

A proxy viselkedését több Alkalmazásbeállítások is szabályozhatja. Ezek mind a [functions app Settings dokumentációjában](./functions-app-settings.md) szerepelnek.

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a>Fenntartott karakterek (karakterlánc formázása)

A proxyk a \ Escape szimbólum használatával beolvassák az összes karakterláncot a JSON-fájlból. A proxyk a kapcsos zárójeleket is értelmezik. Tekintse meg az alábbi példák teljes listáját.

|Karakter|Escape-karakter|Példa|
|-|-|-|
|vagy|{{vagy}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>RequestOverrides objektum definiálása

A requestOverrides objektum a kérelemben a háttér-erőforrás hívásakor végrehajtott módosításokat határozza meg. Az objektumot a következő tulajdonságok határozzák meg:

* **háttér. Request. Method**: a háttér hívásához használt http-metódus.
* **backend. Request. querystring.\<ParameterName\>** : egy lekérdezési karakterlánc paraméter, amely beállítható a háttér felé irányuló híváshoz. Cserélje le *\<ParameterName\>* a beállítani kívánt paraméter nevére. Vegye figyelembe, hogy ha az üres karakterláncot adja meg, a paraméter továbbra is szerepel a háttér-kérelemben.
* **backend. Request. headers.\<HeaderName\>** : egy fejléc, amely beállítható a háttér felé irányuló híváshoz. Cserélje le *\<HeaderName\>* a beállítani kívánt fejléc nevére. Ha az üres karakterláncot adja meg, a fejléc nem szerepel a háttér-kérelemben.

Az értékek hivatkozhatnak az alkalmazás beállításainak és paramétereinek az eredeti ügyfél-kérelem alapján.

A konfiguráció például a következőhöz hasonló lehet:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>ResponseOverrides objektum definiálása

A requestOverrides objektum az ügyfélnek visszaadott válaszon végrehajtott módosításokat határozza meg. Az objektumot a következő tulajdonságok határozzák meg:

* **Response. statusCode**: az ügyfélnek visszaadott http-állapotkód.
* **Response. statusReason**: az ügyfélnek visszaadott http-ok kifejezése.
* **Response. Body**: az ügyfélnek visszaadott törzs karakterlánc-ábrázolása.
* **Response. headers.\<HeaderName\>** : egy fejléc, amely beállítható az ügyfélre adott válaszra. Cserélje le *\<HeaderName\>* a beállítani kívánt fejléc nevére. Ha üres karakterláncot ad meg, a válasz nem tartalmazza a fejlécet.

Az értékek az alkalmazás beállításait, az eredeti ügyfélalkalmazás paramétereit, valamint a háttérbeli válasz paramétereit is hivatkozhatják.

A konfiguráció például a következőhöz hasonló lehet:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> Ebben a példában a válasz törzse közvetlenül van beállítva, így nincs szükség `backendUri` tulajdonságra. A példa bemutatja, hogyan használhatja a Azure Functions-proxykt az API-k modellezéséhez.

[Azure Portalra]: https://portal.azure.com
[HTTP-eseményindítók]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[RequestOverrides objektum definiálása]: #requestOverrides
[ResponseOverrides objektum definiálása]: #responseOverrides
[Alkalmazásbeállítások]: #use-appsettings
[Változók használata]: #using-variables
[paramétereit is hivatkozhat az eredeti ügyfél-kérelemből]: #request-parameters
[a háttérbeli válasz paramétereit]: #response-parameters
