---
title: 'Gyors útmutató: hozzon létre egy webalkalmazást, amely elindítja a magával ragadó olvasótC#'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban létrehozhat egy webalkalmazást a semmiből, és hozzáadhatja a magával ragadó olvasó API funkcióját.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 8dd8459922caa9f765d59bc28fbf050b86834b46
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845241"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Gyors útmutató: hozzon létre egy webalkalmazást, amely elindítjaC#az olvasót ()

A teljes [olvasó](https://www.onenote.com/learningtools) egy olyan, integráltan kialakított eszköz, amely bevált technikákat valósít meg az olvasási szövegértés javítására.

Ebben a rövid útmutatóban egy webalkalmazást hoz létre a semmiből, és integrálja a magával ragadó olvasót a saját olvasó SDK használatával. Ebben a [rövid útmutatóban](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)egy teljes körű működő minta érhető el.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](./how-to-create-immersive-reader.md) . A minta projekt tulajdonságainak konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.

## <a name="create-a-web-app-project"></a>Webalkalmazás-projekt létrehozása

Hozzon létre egy új projektet a Visual Studióban a ASP.NET Core webalkalmazás-sablon használatával, beépített modell-vezérlővel, és ASP.NET Core 2,1. Nevezze el a "QuickstartSampleWebApp" projektet.

![Új projekt](./media/quickstart-csharp/1-createproject.png)

![Új projekt konfigurálása](./media/quickstart-csharp/2-configureproject.png)

![Új ASP.NET Core webalkalmazás](./media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Hitelesítés beállítása

### <a name="configure-authentication-values"></a>Hitelesítési értékek konfigurálása

Kattintson a jobb gombbal a projektre a _megoldáskezelő_ , majd válassza a **felhasználói titkok kezelése**lehetőséget. Ekkor megnyílik egy _Secrets. JSON_nevű fájl. Ezt a fájlt a verziókövetés nem ellenőrzi. További információkat [itt](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows) talál. Cserélje le a _Secrets. JSON_ fájl tartalmát a következőre, és adja meg a magával ragadó olvasó erőforrásának létrehozásakor megadott értékeket.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Adja hozzá a Microsoft. IdentityModel. clients. ActiveDirectory NuGet-csomagot

A következő kód a **Microsoft. IdentityModel. clients. ActiveDirectory** NuGet-csomag objektumait használja, ezért hozzá kell adnia egy hivatkozást az adott csomaghoz a projektben.

Nyissa meg a NuGet csomagkezelő konzolt a **Tools-> NuGet csomagkezelő-> Package Manager konzolon** , és futtassa a következő parancsot:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>A vezérlő frissítése a jogkivonat beszerzéséhez 

Nyissa meg a _Controllers\HomeController.cs_, és adja hozzá a következő kódot a fájl elején található _using_ utasítások után.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Most a vezérlőt úgy konfigurálja, hogy az Azure AD-értékeket beszerezze a _Secrets. JSON_fájlból. A _HomeController_ osztály tetején a ```public class HomeController : Controller {```után adja hozzá a következő kódot.

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>Minta tartalmának hozzáadása
Először nyissa meg a _Views\Shared\Layout.cshtml_. A sor ```</head>```előtt adja hozzá a következő kódot:

```html
@RenderSection("Styles", required: false)
```

Most hozzáadunk egy minta tartalmat ehhez a webalkalmazáshoz. Nyissa meg a _Views\Home\Index.cshtml_ , és cserélje le az összes automatikusan generált kódot a következő mintára:

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

Figyelje meg, hogy az összes szöveg **lang** attribútummal rendelkezik, amely a szöveg nyelveit írja le. Ez az attribútum segíti a magával ragadó olvasót a megfelelő nyelvi és nyelvtani funkciók biztosításában.

## <a name="add-javascript-to-handle-launching-the-immersive-reader"></a>JavaScript hozzáadása a magára ejtő olvasó indításának kezeléséhez

A részletes olvasó függvénytár olyan funkciókat biztosít, mint például a magával ragadó olvasó elindítása és a magára ejtő olvasó gombok megjelenítése. További információkat [itt](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference) talál.

A _Views\Home\Index.cshtml_alján adja hozzá a következő kódot:

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

A menüsávban válassza a **hibakeresés > a hibakeresés elindítása**lehetőséget, vagy nyomja le az **F5** billentyűt az alkalmazás elindításához.

A böngészőben a következőknek kell megjelennie:

![Minta alkalmazás](./media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>A lebilincselő olvasó elindítása

Ha a "magára olvasó" gombra kattint, megjelenik a megjelenő, az oldalon található tartalommal ellátott olvasó.

![Immersive Reader](./media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a [Node. js](./quickstart-nodejs.md) rövid útmutatóját, amelyből megtudhatja, hogy mit tehet a magával az olvasó SDK-val a Node. js használatával
* Tekintse meg a [Python-oktatóanyagot](./tutorial-python.md) , amelyből megtudhatja, hogy mit tehet a részletes olvasó SDK-val a Python használatával
* Tekintse meg az [iOS-oktatóanyagot](./tutorial-ios-picture-immersive-reader.md) , amelyből megtudhatja, mit tehet a gyors
* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](./reference.md)