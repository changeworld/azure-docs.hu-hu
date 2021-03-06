---
title: Szolgáltatás-jelzők hozzáadásának rövid útmutatója ASP.NET Core
description: Szolgáltatás-jelzők hozzáadása az alkalmazások ASP.NET Coreához és az Azure app Configuration használatával történő kezeléséhez
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: a4fbba65af5afbd27a3fd2c7d41858e33b4d812c
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163879"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Gyors útmutató: szolgáltatás-jelzők hozzáadása ASP.NET Core-alkalmazáshoz

Ebben a rövid útmutatóban egy ASP.NET Core-alkalmazás teljes körű implementációját hozza létre az Azure app Configuration használatával. Az alkalmazás konfigurációs szolgáltatásával központilag tárolhatja az összes funkció jelzőjét, és szabályozhatja az állapotukat. 

A .NET Core-szolgáltatások felügyeleti kódtárai kiterjesztik a keretrendszert átfogó funkció-jelölő támogatással. Ezek a kódtárak a .NET Core konfigurációs rendszerre épülnek. Zökkenőmentesen integrálva vannak az alkalmazások konfigurációjával a .NET Core konfigurációs szolgáltatón keresztül.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [.Net Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Feature Manager** >  **+ Hozzáadás** elemet, és vegyen fel egy `Beta`nevű szolgáltatás-jelölőt.

    > [!div class="mx-imgBorder"]
    > ![a Beta](media/add-beta-feature-flag.png) nevű funkció engedélyezése

    Most ne adjon meg `label`.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

A [.net Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) használatával hozzon létre egy új ASP.net Core MVC webalkalmazás-projektet. A Visual Studio helyett a a .NET Core parancssori felülete használatának előnye, hogy a a .NET Core parancssori felülete a Windows, macOS és Linux platformokon érhető el.

1. Hozzon létre egy új mappát a projekthez. Ebben a rövid útmutatóban nevezze el *TestFeatureFlags*.

1. Az új mappában futtassa az alábbi parancsot egy új ASP.NET Core MVC webalkalmazás-projekt létrehozásához:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Secret Manager hozzáadása

Adja hozzá a [Secret Manager eszközt](https://docs.microsoft.com/aspnet/core/security/app-secrets) a projekthez. A Secret Manager eszköz bizalmas adatokat tárol a projekt fáján kívüli fejlesztési munkához. Ez a módszer megakadályozza, hogy véletlenül megossza az alkalmazás forráskódbeli titkos kódjait.

> [!IMPORTANT]
> Jelentős eltérések léteznek a .NET Core 2. x és 3. x között.  Válassza ki a megfelelő szintaxist a környezet alapján.

1. Nyissa meg a *. csproj* fájlt.
1. Vegyen fel egy `UserSecretsId` elemet az alábbi példában látható módon, és cserélje le az értékét a saját értékére, amely általában egy GUID:

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    </Project>
    ```
    ---

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. A következő parancsok futtatásával adja hozzá a `Microsoft.Azure.AppConfiguration.AspNetCore` és a `Microsoft.FeatureManagement.AspNetCore` NuGet-csomagok hivatkozását:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Futtassa a következő parancsot a projekt csomagjainak visszaállításához:

    ```dotnetcli
    dotnet restore
    ```

1. Adjon hozzá egy **ConnectionStrings: AppConfig** nevű titkos kulcsot a Secret Managerhez.

    Ez a titok tartalmazza a kapcsolati karakterláncot az alkalmazás konfigurációs tárolójának eléréséhez. Cserélje le a `<your_connection_string>` értéket az alábbi parancsban az alkalmazás konfigurációs tárolójához tartozó kapcsolatok karakterláncára.

    Ezt a parancsot abban a könyvtárban kell végrehajtani, ahol a *.csproj* fájl található.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    A Secret Manager csak a webalkalmazás helyi tesztelésére használható. Amikor az alkalmazást [Azure app Servicera](https://azure.microsoft.com/services/app-service)telepíti, például a **kapcsolódási** karakterláncok nevű alkalmazás-beállítást használja app Service ahelyett, hogy a Secret Managert használja a kapcsolódási karakterlánc tárolásához.

    Ezt a titkot az alkalmazás konfigurációs API-jával érheti el. Egy kettőspont (:) a konfiguráció neve az App Configuration API-val minden támogatott platformon használható. Lásd: [konfiguráció környezet alapján](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. A `config.AddAzureAppConfiguration()` metódus meghívásával frissítse a `CreateWebHostBuilder` metódust az alkalmazás konfigurációjának használatához.
    
    > [!IMPORTANT]
    > a `CreateHostBuilder` a .NET Core 3,0 `CreateWebHostBuilder` helyettesíti.  Válassza ki a megfelelő szintaxist a környezet alapján.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    
    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                        .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    
    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options => {
                options.Connect(settings["ConnectionStrings:AppConfig"])
                    .UseFeatureFlags();
            });
        })
        .UseStartup<Startup>());
    ```
    ---

1. Nyissa meg a *Startup.cs*, és adjon hozzá hivatkozásokat a .net Core Feature Managerhez:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Frissítse a `ConfigureServices` metódust a szolgáltatás-jelölő támogatásának hozzáadásához a `services.AddFeatureManagement()` metódus meghívásával. Lehetőség van arra is, hogy az `services.AddFeatureFilter<FilterType>()`meghívásával bármely olyan szűrőt felvegyen, amelyet a funkció jelzői is használhatnak:

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);        
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```
    ---

1. Frissítse a `Configure` metódust egy middleware hozzáadásához, amely lehetővé teszi, hogy a szolgáltatás jelölő értékei ismétlődő időközönként frissüljenek, miközben a ASP.NET Core webalkalmazás továbbra is fogadja a kérelmeket.
    
    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }

            app.UseStaticFiles();
            app.UseCookiePolicy();
            app.UseAzureAppConfiguration();
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();
            app.UseRouting();
            app.UseAuthorization();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
            app.UseAzureAppConfiguration();
    }
    ```
    ---

1. *MyFeatureFlags.cs* -fájl hozzáadása:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Adja hozzá a *BetaController.cs* a *vezérlők* könyvtárba:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Nyissa meg a *_ViewImports. cshtml* mappát a *views (nézetek* ) könyvtárban, és adja hozzá a Feature Manager tag helpert:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Nyissa meg a *_Layout. cshtml* mappát a *nézetek*\\a *megosztott* könyvtárban, és cserélje le a `<nav>` vonalkódot `<body>` > `<header>` alá a következő kóddal:

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. Hozzon létre egy *Beta* könyvtárat a *nézetek* területen, és adja hozzá az *index. cshtml* :

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, futtassa a következő parancsot a parancs-rendszerhéjban:

    ```
    dotnet build
    ```

1. A létrehozás sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```
    dotnet run
    ```

1. Nyisson meg egy böngészőablakot, és lépjen a `https://localhost:5000`elemre, amely a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe.
    Ha dolgozik a Azure Cloud Shellban, válassza a *webes előnézet* gombot, majd a *configure (Konfigurálás*) lehetőséget.  Ha a rendszer kéri, válassza a 5000-es portot.

    ![A webes előnézet gomb megkeresése](./media/quickstarts/cloud-shell-web-preview.png)

    A böngészőnek az alábbi képhez hasonló lapot kell megjelenítenie.
    ![rövid útmutató alkalmazás elindítása helyi](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Válassza a **minden erőforrás**lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

1. Válassza a **szolgáltatásvezérlő**lehetőséget, majd módosítsa a **bétaverzió** állapotát **a be**értékre.

1. Térjen vissza a parancssorba, és szakítsa meg a futó `dotnet` folyamatot a `Ctrl-C`megnyomásával.  Indítsa újra az alkalmazást `dotnet run`használatával.

1. A böngésző oldalának frissítésével tekintheti meg az új konfigurációs beállításokat.

    ![Gyorsindítás alkalmazás elindítása helyi](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és felhasználta egy ASP.NET Core webalkalmazás funkcióinak kezelésére a [szolgáltatás-felügyeleti kódtárak](https://go.microsoft.com/fwlink/?linkid=2074664)segítségével.

- További információ a [szolgáltatások kezeléséről](./concept-feature-management.md).
- [Szolgáltatás-jelzők kezelése](./manage-feature-flags.md).
- [A szolgáltatás-jelzők használata egy ASP.net Core alkalmazásban](./use-feature-flags-dotnet-core.md).
- [Dinamikus konfiguráció használata egy ASP.NET Core alkalmazásban](./enable-dynamic-configuration-aspnet-core.md)
