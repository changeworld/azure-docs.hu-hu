---
title: 'Gyors útmutató: Bing Autosuggest ügyféloldali kódtár a Go-hoz | Microsoft Docs'
description: Ismerkedjen meg az Bing Autosuggest ügyféloldali kódtáraval, és kérje meg a keresési javaslatokat a részleges lekérdezési karakterláncok alapján.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: e826d69bbf0f94d02799e9b93b26db8d4cbbba54
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474087"
---
# <a name="quickstart-bing-autosuggest-client-library-for-go"></a>Rövid útmutató: Bing Autosuggest ügyféloldali kódtár a Go-hoz

Ismerkedjen meg az Bing Autosuggest ügyféloldali kódtáraval a Go-ban. Az alábbi lépéseket követve telepítheti a könyvtárat, és kipróbálhatja a példákat az alapszintű feladatokhoz. 

Használja az Bing Autosuggest ügyféloldali függvénytárát a Go-hoz a keresési javaslatok lekérdezéséhez részleges lekérdezési karakterláncok alapján.

[Dokumentáció](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [mintakód](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Go](https://golang.org/dl/) legújabb verziója

## <a name="setting-up"></a>Beállítás

### <a name="create-an-azure-resource"></a>Azure-erőforrás létrehozása 

Kezdje el használni az Bing Autosuggest ügyféloldali függvénytárat egy Azure-erőforrás létrehozásával. Válassza ki a megfelelő erőforrás-típust az Ön számára:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

>[!NOTE]
> Az 2019. július 1. után létrehozott, nem próbaverziós erőforrásokhoz használt végpontok az alább látható egyéni altartomány-formátumot használják. További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Ha a kulcsot és a végpontot a létrehozott erőforrás alapján hozza létre, hozzon létre két környezeti változót a hitelesítéshez:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY` – a kérések hitelesítéséhez használt erőforrás-kulcs.
* `AUTOSUGGEST_ENDPOINT` – az erőforrás-végpont API-kérelmek küldéséhez. A következőképpen fog kinézni: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Használja az operációs rendszerének utasításait.
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

A környezeti változó hozzáadása után indítsa újra a konzolablak ablakát.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Szerkessze `.bash_profile`, és adja hozzá a környezeti változót:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.
***

### <a name="create-a-new-go-project"></a>Új go-projekt létrehozása

A konzol ablakban (cmd, PowerShell, Terminal, bash) hozzon létre egy új munkaterületet a go-projekt számára, és navigáljon hozzá. A munkaterület három mappát fog tartalmazni: 

* **src** – ez a könyvtár forráskódot és csomagokat tartalmaz. A `go get` paranccsal telepített csomagok itt fognak megjelenni.
* **pkg** – ez a könyvtár tartalmazza a lefordított go csomag objektumait. Ezek a fájlok `.a` kiterjesztéssel rendelkeznek.
* **bin** – ez a könyvtár tartalmazza a `go install`futtatásakor létrehozott bináris végrehajtható fájlokat.

> [!TIP]
> További információ a [Go-munkaterület](https://golang.org/doc/code.html#Workspaces)struktúrájáról. Ez az útmutató a `$GOPATH` és `$GOROOT`beállításával kapcsolatos információkat tartalmaz.

Hozzon létre egy `my-app` nevű munkaterületet, valamint a `src`, `pkg`és `bin`szükséges alkönyvtárakat:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>Az ügyféloldali kódtár telepítése a Go-hoz

Most telepítse az ügyféloldali kódtárat a Go-hoz: 

```bash
$ go get -u <library-location-or-url>
```

vagy ha a DEP-t használja a tárházon belül, futtassa a következőket:

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>Go-alkalmazás létrehozása

Ezután hozzon létre egy `src/sample-app.go`nevű fájlt:

```bash
$ cd src
$ touch sample-app.go
```

Nyissa meg `sample-app.go` és adja hozzá a csomag nevét, és importálja a következő könyvtárakat:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Hozzon létre egy `main`nevű függvényt. Ezután hozzon létre környezeti változókat a Bing Autosuggest kulcshoz és végponthoz.

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan hajthat végre alapszintű feladatokat az Bing Autosuggest ügyféloldali kódtár használatával a Go-hoz:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [API-kérelem küldése](#send-an-api-request)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE] 
> Ez a rövid útmutató azt feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a Bing automatikus kiegészítési kulcsához, az `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`nevet, egy pedig a `BING_AUTOSUGGEST_ENDPOINT`nevű végponthoz.

A `main()` függvényben hozza létre az ügyfelet a végponttal és a kulccsal. 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>API-kérelem küldése

Ugyanebben a metódusban az ügyfél [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) metódusával küldhet lekérdezést a bingnek. Ezután ismételje meg a [javaslatok](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) válaszát, és nyomtassa ki az első javaslatot.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa a go alkalmazást az `go run [arguments]` paranccsal az alkalmazás könyvtárából.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Bing Autosuggest-oktatóanyag](../tutorials/autosuggest.md)

## <a name="see-also"></a>Lásd még:

- [Mi az a Bing Autosuggest?](../get-suggested-search-terms.md)
- [A Bing Autosuggest API 7-es verziójának referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
