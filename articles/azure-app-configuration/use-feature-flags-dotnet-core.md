---
title: Oktatóanyag a Feature Flags használatáról egy .NET Core-alkalmazásban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan implementálhatja a szolgáltatás-jelzőket a .NET Core-alkalmazásokban.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: b04fe3b6451fd7250bc3b05970d49fdb8e7003bd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899292"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Oktatóanyag: funkció-jelzők használata egy ASP.NET Core alkalmazásban

A .NET Core-szolgáltatások felügyeleti könyvtárai köznyelvi támogatást biztosítanak a .NET-vagy ASP.NET Core-alkalmazásban található szolgáltatások jelölők megvalósításához. Ezek a kódtárak lehetővé teszik a szolgáltatáshoz tartozó jelzők deklaratív hozzáadását, így nem kell manuálisan megírnia az összes `if` utasítást.

A szolgáltatás-felügyeleti kódtárak a funkciók jelző életciklusait is kezelik a színfalak mögött. Például a tárak frissítési és gyorsítótár-jelző állapota, vagy ha egy kérelem hívásakor a jelző állapot nem változtatható meg. Emellett a ASP.NET Core-függvénytár beépített integrációkat is kínál, beleértve az MVC vezérlő műveleteit, nézeteit, útvonalait és köztes integrálását.

A szolgáltatás-jelzők [hozzáadása egy ASP.net Core-alkalmazáshoz](./quickstart-feature-flag-aspnet-core.md) rövid útmutató azt mutatja be, hogyan adhatók hozzá szolgáltatások jelzői egy ASP.net Core alkalmazásban. Ez az oktatóanyag részletesen ismerteti ezeket a módszereket. A teljes referenciáért tekintse meg a [ASP.net Core szolgáltatás-felügyeleti dokumentációját](https://go.microsoft.com/fwlink/?linkid=2091410).

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * A szolgáltatások rendelkezésre állásának szabályozásához vegyen fel szolgáltatás-jelzőket az alkalmazás legfontosabb részeibe.
> * Integrálhatja az alkalmazás konfigurációját, ha a funkció-jelzők kezelésére használja.

## <a name="set-up-feature-management"></a>A szolgáltatások kezelésének beállítása

A .NET Core Feature Manager `IFeatureManager` lekéri a keretrendszer natív konfigurációs rendszerének funkcióit. Ennek eredményeképpen meghatározhatja az alkalmazás funkciójának jelzőit a .NET Core által támogatott bármely konfigurációs forrás használatával, beleértve a helyi *appSettings. JSON* fájlt vagy a környezeti változókat is. a `IFeatureManager` a .NET Core függőségi injekcióra támaszkodik. A szolgáltatás-felügyeleti szolgáltatásokat szabványos konvenciók használatával regisztrálhatja:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Alapértelmezés szerint a Feature Manager a .NET Core konfigurációs adatok `"FeatureManagement"` részéből kérdezi le a szolgáltatás jelzőit. A következő példa azt mutatja be, hogy a Feature Manager egy másik, `"MyFeatureFlags"` nevű szakaszból olvassa el a következőket:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Ha szűrőket használ a funkció jelzői között, egy további könyvtárat kell megadnia, és regisztrálnia kell. Az alábbi példa bemutatja, hogyan használható a `PercentageFilter`nevű beépített szolgáltatás-szűrő:

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Javasoljuk, hogy az alkalmazáson kívül tartsa meg a szolgáltatás jelölőit, és ezeket külön kell kezelnie. Ezzel a beállítással bármikor módosíthatja a jelző állapotokat, és ezek a módosítások azonnal érvénybe lépnek az alkalmazásban. Az alkalmazás konfigurációja központosított helyet biztosít az összes funkció jelzőjének egy dedikált portál felhasználói felületen való rendszerezéséhez és szabályozásához. Az alkalmazás konfigurációja emellett közvetlenül a .NET Core-ügyfél könyvtárain keresztül továbbítja a jelzőket az alkalmazáshoz.

A ASP.NET Core alkalmazásnak az alkalmazások konfigurációhoz való összekapcsolásának legegyszerűbb módja a konfigurációs szolgáltató `Microsoft.Azure.AppConfiguration.AspNetCore`. Az alábbi lépéseket követve használhatja ezt a NuGet-csomagot.

1. Nyissa meg a *program.cs* fájlt, és adja hozzá a következő kódot.

   ```csharp
   using Microsoft.Extensions.Configuration.AzureAppConfiguration;

   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
       WebHost.CreateDefaultBuilder(args)
              .ConfigureAppConfiguration((hostingContext, config) => {
                  var settings = config.Build();
                  config.AddAzureAppConfiguration(options => {
                      options.Connect(settings["ConnectionStrings:AppConfig"])
                             .UseFeatureFlags();
                   });
              })
              .UseStartup<Startup>();
   ```

2. Nyissa meg a *Startup.cs* , és frissítse a `Configure` metódust egy middleware hozzáadásához, amely lehetővé teszi, hogy a szolgáltatás jelölő értékei ismétlődő időközönként frissüljenek, miközben a ASP.net Core webalkalmazás továbbra is fogadja a kérelmeket.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

A szolgáltatás jelölő értékeinek időbeli változásának várhatónak kell lennie. Alapértelmezés szerint a szolgáltatás jelölő értékeit a rendszer 30 másodpercen belül gyorsítótárazza, így egy frissítési művelet akkor aktiválódik, ha a middleware kérése nem fogja frissíteni az értéket, amíg a gyorsítótárazott érték lejár. A következő kód bemutatja, hogyan módosíthatja a gyorsítótár lejárati idejét vagy a lekérdezési időközt 5 percre a `options.UseFeatureFlags()` hívásban.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Funkció jelző deklarációja

Minden egyes szolgáltatás jelölője két részből áll: egy vagy több szűrőből áll, amelyek segítségével kiértékelheti, hogy a szolgáltatás állapota be van-e *kapcsolva* (azaz ha az értéke `True`). A szűrők a használati esetet határozzák meg, ha egy szolgáltatás bekapcsolására van lehetőség.

Ha egy szolgáltatás jelölője több szűrővel rendelkezik, a rendszer átadja a szűrőlisták sorrendjét, amíg az egyik szűrő nem határozza meg, hogy a szolgáltatást engedélyezni kell. Ekkor a funkció jelzője *be van kapcsolva*, és a rendszer kihagyja a többi szűrő eredményét. Ha nincs szűrő azt jelzi, hogy a funkciót engedélyezni kell, a szolgáltatás jelzője *ki van kapcsolva*.

A Feature Manager támogatja a *appSettings. JSON* konfigurációs forrásként való használatát a funkciók jelzői számára. Az alábbi példa bemutatja, hogyan állíthatja be a szolgáltatás jelzőit egy JSON-fájlban:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Az egyezmény szerint a JSON-dokumentum `FeatureManagement` szakasza a szolgáltatás-jelölő beállításaihoz használatos. Az előző példában három funkció-jelző látható a `EnabledFor` tulajdonságban definiált szűrőkkel:

* `FeatureA` *bekapcsolva*.
* `FeatureB` *ki van kapcsolva*.
* `FeatureC` a `Percentage` nevű szűrőt adja meg `Parameters` tulajdonsággal. `Percentage` konfigurálható szűrő. Ebben a példában a `Percentage` 50 százalékos valószínűséget *ad meg a*`FeatureC` jelzőhöz.

## <a name="feature-flag-references"></a>Szolgáltatás jelölő hivatkozásai

Annak érdekében, hogy könnyen hivatkozzon a funkció jelzői a kódban, meg kell határoznia azokat `enum` változókként:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Szolgáltatás-jelző ellenőrzése

A szolgáltatások felügyeletének alapszintű mintája először ellenőrizze, hogy be van *-e állítva*a szolgáltatás jelölője. Ebben az esetben a Feature Manager ezután futtatja a funkció által tartalmazott műveleteket. Példa:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Függőséginjektálás

ASP.NET Core MVC-ben a szolgáltatás-kezelő `IFeatureManager` a függőségek befecskendezésével érheti el:

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Vezérlő műveletei

Az MVC-vezérlőkben a `FeatureGate` attribútum használatával szabályozhatja, hogy a teljes vezérlő osztály vagy egy adott művelet engedélyezve van-e. A következő `HomeController` vezérlőnek `FeatureA` kell lennie ahhoz, hogy a vezérlő osztálya által tartalmazott műveletek végrehajtása *megtörténjen:*

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

A következő `Index` művelet végrehajtásához `FeatureA` kell lennie *a* futtatásához:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Ha egy MVC vezérlő vagy művelet le van tiltva, mert a vezérlő funkció jelzője *ki van kapcsolva*, a rendszer regisztrált `IDisabledFeaturesHandler` felületet hív meg. Az alapértelmezett `IDisabledFeaturesHandler` felület 404 állapotkódot ad vissza az ügyfélnek, és nincs válasz törzse.

## <a name="mvc-views"></a>MVC-nézetek

Az MVC-nézetekben `<feature>` címkével megjelenítheti a tartalmakat, hogy engedélyezve van-e a funkció jelzője:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Ha a követelmények nem teljesülnek, akkor a helyettesítő tartalom megjelenítéséhez a `negate` attribútum használható.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

A funkció `<feature>` címkével is megjelenítheti a tartalmat, ha a lista bármely vagy összes funkciója engedélyezve van.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC-szűrők

Az MVC-szűrőket beállíthatja úgy, hogy azok a szolgáltatás jelzőjének állapota alapján legyenek aktiválva. A következő kód egy `SomeMvcFilter`nevű MVC-szűrőt hoz létre. Ez a szűrő csak akkor aktiválódik az MVC-folyamaton belül, ha `FeatureA` engedélyezve van. Ez a képesség `IAsyncActionFilter`re korlátozódik. 

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="middleware"></a>Közbensőszoftver

A funkciók jelzőit is használhatja az alkalmazás-ágak és a köztes alkalmazások feltételes hozzáadásához. A következő kód csak akkor szúr be egy middleware-összetevőt a kérelmek folyamatában, ha `FeatureA` engedélyezve van:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Ez a kód kiépíti a további általános képességet, hogy a teljes alkalmazást a szolgáltatás jelölője alapján lehessen összekapcsolni:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan implementálhatja a szolgáltatás-jelzőket a ASP.NET Core alkalmazásban a `Microsoft.FeatureManagement`-kódtárak használatával. A ASP.NET Core és az alkalmazások konfigurációjának funkció-kezelési támogatásáról az alábbi forrásokban talál további információt:

* [ASP.NET Core funkció jelölője mintakód](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [A Microsoft. FeatureManagement dokumentációja](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Funkciójelölők kezelése](./manage-feature-flags.md)
