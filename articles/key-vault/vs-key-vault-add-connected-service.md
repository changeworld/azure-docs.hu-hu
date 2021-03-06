---
title: Key Vault támogatás hozzáadása a ASP.NET-projekthez a Visual Studio-Azure Key Vault használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá Key Vault támogatást egy ASP.NET vagy ASP.NET Core webalkalmazáshoz.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 1c12cf4a7bd097c6d33d032065734b477920644b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456998"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Key Vault hozzáadása a webalkalmazáshoz a Visual Studio csatlakoztatott szolgáltatásainak használatával

Ebből az oktatóanyagból megtudhatja, hogyan hozhatja létre a webes projektekhez szükséges titkokat a Visual Studióban, akár ASP.NET Core, akár bármilyen ASP.NET-projektet használ, hogy könnyen hozzá tudjon adni mindent, amire szüksége van a Azure Key Vault használatának megkezdéséhez. A Visual Studio csatlakoztatott szolgáltatások funkciójának használatával a Visual Studio automatikusan felveheti az összes olyan NuGet-csomagot és konfigurációs beállítást, amelyhez csatlakoznia kell az Azure-beli Key Vaulthoz.

A csatlakoztatott szolgáltatások által a projektben a Key Vault engedélyezéséhez szükséges módosítások részleteiért lásd: [Key Vault csatlakoztatott szolgáltatás – mi történt a ASP.net 4.7.1 Project](#how-your-aspnet-framework-project-is-modified) vagy [Key Vault Connected Service szolgáltatással – mi történt a ASP.net Core projekttel](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztráljon egy [ingyenes fiókra](https://azure.microsoft.com/pricing/free-trial/).
- A **Visual studio 2019 16,3** -es vagy újabb verziója, vagy a **visual Studio 2017 15,7** -es verziója, amelyen telepítve van a **webes fejlesztési** feladat. [Ezt innen töltheti le](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- A Visual Studio 2017 esetében a ASP.NET (nem Core) esetében a .NET-keretrendszer 4.7.1 vagy újabb fejlesztői eszközeire van szükség, amelyek alapértelmezés szerint nem települnek. A telepítéshez indítsa el a Visual Studio telepítőjét, válassza a **módosítás**lehetőséget, majd válassza az **egyes összetevők**lehetőséget, majd a jobb oldalon bontsa ki a **ASP.net és a webes fejlesztés**elemet, és válassza a **.NET-keretrendszer 4.7.1-fejlesztői eszközök**lehetőséget.
- Egy ASP.NET 4.7.1 vagy újabb, vagy ASP.NET Core 2,0 vagy újabb webes projekt nyitva van.

## <a name="add-key-vault-support-to-your-project"></a>Key Vault támogatás hozzáadása a projekthez

Mielőtt elkezdené, győződjön meg róla, hogy be van jelentkezve a Visual studióba. Jelentkezzen be ugyanazzal a fiókkal, amelyet az Azure-előfizetéséhez használ. Ezután nyisson meg egy ASP.NET 4.7.1 vagy újabb verziót, vagy ASP.NET Core 2,0 webes projektet, és hajtsa végre a következő lépéseket:

1. **Megoldáskezelőban**kattintson a jobb gombbal arra a projektre, amelyhez hozzá kívánja adni a Key Vault-támogatást, majd válassza a > **csatlakoztatott szolgáltatás** **hozzáadása** elemet.
   Megjelenik a Connected Service (Csatlakoztatott szolgáltatás) lap a projekthez adható szolgáltatásokkal.
1. Az elérhető szolgáltatások menüben válassza a **biztonságos titkok Azure Key Vault**lehetőséget.

   ![Válassza a "titkos kulcsok védelme Azure Key Vault" lehetőséget.](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Válassza ki a használni kívánt előfizetést, majd válasszon ki egy új vagy meglévő Key Vault. Ha az új Key Vault választja, megjelenik egy **szerkesztési** hivatkozás. Válassza ki az új Key Vault konfigurálásához.

   ![Válassza ki előfizetését.](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. A **szerkesztés Azure Key Vault**mezőben adja meg a Key Vault használni kívánt nevet.

1. Válasszon ki egy meglévő **erőforráscsoportot**, vagy hozzon létre egy újat egy automatikusan létrehozott egyedi névvel.  Ha más névvel rendelkező új csoportot szeretne létrehozni, akkor használhatja a [Azure Portal](https://portal.azure.com), majd a lap bezárásával és újraindításával újratöltheti az erőforráscsoportok listáját.
1. Válassza ki azt a **helyet** , ahol létre szeretné hozni a Key Vault. Ha a webalkalmazása az Azure-ban üzemel, válassza ki azt a régiót, amely a webalkalmazást üzemelteti az optimális teljesítmény érdekében.
1. Válasszon **árképzési szintet**. Részletekért lásd: [Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).
1. A konfigurációs beállítások elfogadásához kattintson **az OK gombra** .
1. Miután kiválasztott egy meglévő Key Vault, vagy konfigurált egy új Key Vault, a Visual Studio **Azure Key Vault** lapján válassza a **Hozzáadás** lehetőséget a csatlakoztatott szolgáltatás hozzáadásához.
1. Jelölje be az **ebben a Key Vaultban tárolt titkos kulcsok kezelése** lehetőséget a Key Vault **titkok** oldalának megnyitásához. Ha bezárta a lapot vagy a projektet, megnyithatja azt a [Azure Portal](https://portal.azure.com) válassza a **minden szolgáltatás** lehetőséget, majd a **Biztonság**területen válassza a **Key Vault**lehetőséget, majd válassza ki a Key Vault.
1. A létrehozott Key Vault Key Vault szakaszban válassza a **titkok**, majd a **Létrehozás/importálás**lehetőséget.

   ![Titkos kód létrehozása/importálása](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Adjon meg egy titkos kulcsot (például *keresési kifejezésként* ), és adjon meg egy karakterlánc-értéket tesztként, majd kattintson a **Létrehozás** gombra.

   ![Titkos kulcs létrehozása](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. választható Adjon meg egy másik titkot, de ezúttal egy kategóriába helyezi az IT *Secrets--keresési kifejezésként*elnevezéssel. Ez a szintaxis olyan "Secrets" kategóriát határoz meg, amely titkos "keresési kifejezésként" tartalmaz.

A titkokat most már a kódban is elérheti. A következő lépések eltérnek attól függően, hogy ASP.NET 4.7.1 vagy ASP.NET Core használ-e.

## <a name="access-your-secrets-in-code-aspnet-core"></a>A titkok elérése a kódban (ASP.NET Core)

1. Megoldáskezelő kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**lehetőséget. A **Tallózás** lapon keresse meg és telepítse a következő két NuGet-csomagot: [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és .net Core 2, a [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) kulcstartó vagy a .net Core 3 esetében adja hozzá a[Microsoft. Azure. kulcstartó. Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core)parancsot.

1. A .NET Core 2 esetében válassza a `Program.cs` fület, és módosítsa a program osztály `BuildWebHost` definícióját a következőre:

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   A .NET Core 3 esetében használja a következő kódot.

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. Ezután nyissa meg az egyik lapozófájlt, például a *index.cshtml.cs* , és írja be a következő kódot:
   1. Adjon meg egy hivatkozást a `Microsoft.Extensions.Configuration`re az alábbi irányelv használatával:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Adja hozzá a konfigurációs változót.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. Adja hozzá ezt a konstruktort, vagy cserélje le a meglévő konstruktort a következőre:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Frissítse a(z) `OnGet` metódust. Frissítse az itt látható helyőrző értékét a fenti parancsokban létrehozott titkos névvel.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Ha szeretné megerősíteni az értéket futásidőben, adjon hozzá egy kódot a *. cshtml* fájlhoz `ViewData["Message"]` megjelenítéséhez, hogy megjelenjen a titok egy üzenetben.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Az alkalmazást helyileg futtatva ellenőrizheti, hogy a titkos kulcs beszerzése sikeresen megtörtént-e a Key Vault.

## <a name="access-your-secrets-aspnet"></a>Hozzáférés a titkokhoz (ASP.NET)

Beállíthatja a konfigurációt úgy, hogy a web. config fájlhoz a `appSettings` elemben lévő, a valós értékkel lecserélt, futásidőben megadott dummy érték legyen. Ezt követően a `ConfigurationManager.AppSettings` adatstruktúrán keresztül érheti el ezt.

1. Szerkessze a web. config fájlt.  Keresse meg a appSettings címkét, adjon hozzá egy `configBuilders="AzureKeyVault"`attribútumot, és adjon hozzá egy sort:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. A *HomeController.cs*`About` metódusának szerkesztése a megerősítés értékének megjelenítéséhez.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Futtassa az alkalmazást helyileg a hibakereső alatt, váltson a **Névjegy** lapra, és ellenőrizze, hogy megjelenik-e az érték a Key Vault.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a Key Vault és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. A **írja be az ERŐFORRÁSCSOPORT nevét:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés**lehetőséget.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Key Vault a Visual studióba bejelentkezett másik Microsoft-fiók fut (például a Key Vault fut a munkahelyi fiókján, de a Visual Studio a saját fiókját használja), hibaüzenetet kap a Program.cs-fájlban. , hogy a Visual Studio nem fér hozzá a Key Vaulthoz. A probléma megoldása:

1. Lépjen a [Azure Portal](https://portal.azure.com) , és nyissa meg a Key Vault.

1. Válassza a **hozzáférési szabályzatok**, majd a **hozzáférési házirend hozzáadása**lehetőséget, és válassza ki azt a fiókot, amelyet a rendszerbiztonsági tagként jelentkezett be.

1. A Visual Studióban válassza a **fájl** > **Fiókbeállítások**lehetőséget.
Válassza a **fiók hozzáadása** lehetőséget a **minden fiók** szakaszban. Jelentkezzen be azzal a fiókkal, amelyet a hozzáférési szabályzatának elsődlegesen választott.

1. Válassza az **eszközök** > **lehetőségek lehetőséget**, és keresse meg az **Azure-szolgáltatás hitelesítése**elemet. Ezután válassza ki azt a fiókot, amelyet az imént hozzáadott a Visual studióhoz.

Az alkalmazás hibakeresése után a Visual Studio csatlakozik ahhoz a fiókhoz, amelyen a Key Vault található.

## <a name="how-your-aspnet-core-project-is-modified"></a>A ASP.NET Core-projekt módosítása

Ez a szakasz egy ASP.NET-projekt pontos módosításait mutatja be, amikor hozzáadja a Key Vault csatlakoztatott szolgáltatást a Visual Studióval.

### <a name="added-references-for-aspnet-core"></a>ASP.NET Core-referenciák hozzáadva

A Project file .NET-referenciákat és a NuGet-csomagok hivatkozásait érinti.

| Type (Típus) | Leírások |
| --- | --- |
| NuGet | Microsoft. AspNetCore. AzureKeyVault. HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Hozzáadott fájlok ASP.NET Core

- `ConnectedService.json` hozzáadva, amely a csatlakoztatott szolgáltató, a verzió és a dokumentáció hivatkozásával kapcsolatos információkat rögzíti.

### <a name="project-file-changes-for-aspnet-core"></a>A Project fájl módosításai ASP.NET Core

- Hozzáadta a csatlakoztatott szolgáltatások ItemGroup és `ConnectedServices.json` fájlt.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>a ASP.NET Core launchsettings. JSON módosításai

- A következő környezeti változó bejegyzéseket adta hozzá a IIS Express profilhoz és a webes projekt nevével egyező profilhoz:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Azure-beli változások ASP.NET Core

- Létrehozott egy erőforráscsoportot (vagy használt egy meglévőt).
- Létrehozott egy Key Vault a megadott erőforráscsoporthoz.

## <a name="how-your-aspnet-framework-project-is-modified"></a>A ASP.NET Framework-projekt módosítása

Ez a szakasz egy ASP.NET-projekt pontos módosításait mutatja be, amikor hozzáadja a Key Vault csatlakoztatott szolgáltatást a Visual Studióval.

### <a name="added-references-for-aspnet-framework"></a>A ASP.NET-keretrendszerhez hozzáadott referenciák

A Project file .NET-referenciákat és `packages.config` (NuGet-hivatkozásokat) érinti.

| Type (Típus) | Leírások |
| --- | --- |
| NET NuGet | Microsoft. Azure. kulcstartó |
| NET NuGet | Microsoft. Azure. kulcstartó. WebKey |
| NET NuGet | Microsoft.Rest.ClientRuntime |
| NET NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Hozzáadott fájlok a ASP.NET-keretrendszerhez

- `ConnectedService.json` hozzáadva, amely a csatlakoztatott szolgáltatóval, verziójával és a dokumentációra mutató hivatkozással kapcsolatos adatokat rögzíti.

### <a name="project-file-changes-for-aspnet-framework"></a>A Project fájl változásai a ASP.NET-keretrendszerben

- Hozzáadta a csatlakoztatott szolgáltatások ItemGroup és a ConnectedServices. JSON fájlt.
- A [hozzáadott hivatkozások](#added-references-for-aspnet-framework) szakaszban leírt .net-szerelvényekre mutató hivatkozások.

### <a name="webconfig-or-appconfig-changes"></a>a web. config vagy az app. config módosítása

- A következő konfigurációs bejegyzések lettek hozzáadva:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>Az Azure ASP.NET-keretrendszerbeli változásai

- Létrehozott egy erőforráscsoportot (vagy használt egy meglévőt).
- Létrehozott egy Key Vault a megadott erőforráscsoporthoz.

## <a name="next-steps"></a>Következő lépések

Ha követte ezt az oktatóanyagot, a Key Vault engedélyei a saját Azure-előfizetéssel való futtatásra vannak beállítva, de előfordulhat, hogy éles környezetben nem kívánatos. Létrehozhat egy felügyelt identitást, amellyel kezelheti Key Vault hozzáférését az alkalmazáshoz. Lásd: [Key Vault hitelesítés megadása felügyelt identitással](/azure/key-vault/managed-identity).

A Key Vault fejlesztésről a [Key Vault fejlesztői útmutatójában](key-vault-developers-guide.md)olvashat bővebben.
