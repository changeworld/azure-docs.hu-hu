---
title: Az C# ügyféloldali kódtár Bing Custom Search
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: b722fd34a78f1e9c2f4a660c205cf4a1e163a5d7
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252890"
---
Ismerkedjen meg C#a Bing Custom Search ügyféloldali függvénytárával. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Bing Custom Search API lehetővé teszi, hogy testreszabott, ad-ingyenes keresési élményeket hozzon létre az Ön számára fontos témakörökhöz. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch)található.

Használja a Bing Custom Search ügyféloldali függvénytárát C# a következőhöz:
* Keresési eredmények keresése a weben a Bing Custom Search-példányból.

[Dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-dotnet) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) | [minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Előfeltételek

- Egy Bing Custom Search példány. További információért tekintse [meg a rövid útmutató: az első Bing Custom Search példány létrehozása](../../quick-start.md) című témakört.
- Microsoft [.net Core](https://www.microsoft.com/net/download/core)
- A [Visual Studio 2017-es vagy újabb](https://www.visualstudio.com/downloads/) verziójának bármely kiadása
- Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
- A [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) NuGet-csomag. 
    - **Megoldáskezelő** a Visual Studióban kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** menüpontot a menüből. Telepítse az `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot. A NuGet Custom Search csomag telepítésekor a következő szerelvények is települnek:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre C# egy új Console-alkalmazást a Visual Studióban. Ez után adja hozzá a projekthez az alábbi csomagokat.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. Az alkalmazás fő metódusában hozza létre a keresési ügyfelet az API-kulccsal.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>A keresési kérelem elküldése és válasz fogadása
    
1. Keresési lekérdezést küldhet az ügyfél `SearchAsync()` metódusának használatával, és mentheti a választ. Ne felejtse el lecserélni a `YOUR-CUSTOM-CONFIG-ID`t a példány konfigurációs azonosítójával (az azonosítót a [Bing Custom Search portálon](https://www.customsearch.ai/)találja). Ez a példa az "Xbox" kifejezést keresi.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. A `SearchAsync()` metódus egy `WebData` objektumot ad vissza. Az objektum használatával megismételheti a talált `WebPages`. Ez a kód megtalálja az első weboldal eredményt, és megjeleníti a weboldal `Name` és `URL` tulajdonságát.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](../../tutorials/custom-search-web-page.md)
