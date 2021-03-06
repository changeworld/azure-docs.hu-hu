---
title: 'A .NET-keretrendszer oktatóanyaga: dinamikus konfiguráció az Azure-alkalmazás konfigurációjában'
description: Ebből az oktatóanyagból megtudhatja, hogyan frissítheti dinamikusan a .NET-keretrendszer alkalmazásaihoz tartozó konfigurációs információkat az Azure app Configuration használatával.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7ba3eae4ea5557b4bb1b1be4e2c79eab8f6e7988
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484876"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Oktatóanyag: dinamikus konfiguráció használata .NET-keretrendszerbeli alkalmazásokban

Az alkalmazás-konfiguráció .NET ügyféloldali kódtár támogatja az igény szerinti konfigurációs beállítások frissítését anélkül, hogy újra kellene indítani az alkalmazást. Ezt úgy teheti meg, hogy először beolvassa `IConfigurationRefresher` egy példányát a konfigurációs szolgáltató lehetőségei közül, majd a kódban bárhol meghívja a `Refresh` ezen a példányon.

A beállítások frissítésének megtartásához és a konfigurációs tárolóhoz való túl sok hívás elkerüléséhez minden beállításhoz gyorsítótárat kell használni. Amíg a beállítás gyorsítótárazott értéke lejárt, a frissítési művelet nem frissíti az értéket, még akkor is, ha az érték módosult a konfigurációs tárolóban. Az egyes kérések alapértelmezett lejárati ideje 30 másodperc, de szükség esetén felül lehet bírálni.

Ez az oktatóanyag bemutatja, hogyan valósítható meg a dinamikus konfigurációs frissítések a kódban. Ez a gyors üzembe helyezési útmutatóban bemutatott alkalmazásra épül. A folytatás előtt fejezze be [a .net-keretrendszerbeli alkalmazás létrehozását az alkalmazás konfigurálásával](./quickstart-dotnet-app.md) először.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Állítsa be a .NET-keretrendszerbeli alkalmazást, hogy frissítse a konfigurációját az alkalmazás konfigurációs tárolójában történt változásokra reagálva.
> * Adja meg a legújabb konfigurációt az alkalmazásban.
## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET-keretrendszer 4.7.1 vagy újabb verzió](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Configuration Explorer** >  **+ Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

    | Paraméter | Érték |
    |---|---|
    | TestApp:Settings:Message | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

## <a name="create-a-net-framework-console-app"></a>.NET-keretrendszer Console-alkalmazás létrehozása

1. Indítsa el a Visual studiót, és válassza a **fájl** > **új** > **projekt**lehetőséget.

1. A **create a New Project (új projekt létrehozása**) területen szűrje a **konzol** projekt típusát, és kattintson a **Console app (.NET-keretrendszer)** elemre. Kattintson a **Tovább** gombra.

1. Az **új projekt konfigurálása**területen adja meg a projekt nevét. A **keretrendszer**területen válassza a **.NET-keretrendszer 4.7.1** vagy újabb lehetőséget. Kattintson a **Létrehozás** gombra.

## <a name="reload-data-from-app-configuration"></a>Adatok újratöltése az alkalmazás konfigurációjától
1. Kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**lehetőséget. A **Tallózás** lapon keresse meg és adja hozzá a *Microsoft. Extensions. Configuration. AzureAppConfiguration* NuGet-csomagot a projekthez. Ha nem találja, jelölje be az **előzetes verzió belefoglalása** jelölőnégyzetet.

1. Nyissa meg a *program.cs*, és adjon hozzá egy hivatkozást a .net Core app Configuration Provider szolgáltatáshoz.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Két változó hozzáadása a konfigurációval kapcsolatos objektumok tárolásához.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Frissítse a `Main` metódust az alkalmazás-konfigurációhoz a megadott frissítési beállításokkal való kapcsolódáshoz.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    A `ConfigureRefresh` metódussal adhatja meg azokat a beállításokat, amelyeket a konfigurációs adatainak az alkalmazás konfigurációs tárolójával való frissítéséhez használ a frissítési művelet elindításakor. A `IConfigurationRefresher` egy példánya lekérhető `GetRefresher` metódus meghívásával a `AddAzureAppConfiguration` metódushoz megadott beállításokkal, és a példány `Refresh` metódusa használható a frissítési művelet elindításához bárhol a kódban.

    > [!NOTE]
    > A konfigurációs beállítás alapértelmezett gyorsítótár-lejárati ideje 30 másodperc, de felülbírálható úgy, hogy meghívja a `SetCacheExpiration` metódust az `ConfigureRefresh` metódus argumentumként átadott inicializálási beállításokkal.

1. Adjon hozzá egy `PrintMessage()` nevű metódust, amely elindítja a konfigurációs adatok manuális frissítését az alkalmazás konfigurációjától.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Indítsa újra a Visual studiót, hogy a módosítás érvénybe lépjen. 

1. A konzol alkalmazás létrehozásához és futtatásához nyomja le a CTRL + F5 billentyűkombinációt.

    ![Alkalmazás elindítása helyi](./media/dotnet-app-run.png)

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Válassza a **minden erőforrás**lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

1. Válassza a **Configuration Explorer**lehetőséget, és frissítse a következő kulcsok értékeit:

    | Paraméter | Érték |
    |---|---|
    | TestApp:Settings:Message | Adatok az Azure-alkalmazás konfigurációjában – frissítve |

1. A futó alkalmazásban lépjen vissza az ENTER billentyűt a frissítés elindításához, és nyomtassa ki a frissített értéket a parancssorban vagy a PowerShell-ablakban.

    ![Alkalmazás helyi frissítése](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Mivel a gyorsítótár lejárati ideje 10 másodpercre van állítva a `SetCacheExpiration` metódus használatával a frissítési művelethez megadott konfiguráció megadásakor, a konfigurációs beállítás értéke csak akkor frissül, ha a beállítás utolsó frissítése óta legalább 10 másodperc eltelt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte a .NET-keretrendszer alkalmazását, hogy dinamikusan frissítse a konfigurációs beállításokat az alkalmazás konfigurációjában. Ha meg szeretné tudni, hogyan használható az Azure felügyelt identitása az alkalmazás-konfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)
