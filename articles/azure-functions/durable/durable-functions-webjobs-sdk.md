---
title: Durable Functions futtatása webjobsként – Azure
description: Megtudhatja, hogyan lehet kódot és konfigurálást Durable Functions a webjobs-ben való futtatáshoz a webjobs SDK használatával.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232744"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Durable Functions futtatása webjobsként

Alapértelmezés szerint a Durable Functions az Azure Functions Runtime használatával futtatja a rendszerindítást. Bizonyos esetekben azonban előfordulhat, hogy az eseményeket figyelő kód felett több szabályozásra van szükség. Ebből a cikkből megtudhatja, hogyan implementálhatja a kialakítást a webjobs SDK használatával. A függvények és a webjobs-feladatok részletesebb összehasonlítását lásd: a [függvények és a Webjobs összehasonlítása](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

A [Azure functions](../functions-overview.md) és a [Durable functions](durable-functions-overview.md) BŐVÍTMÉNY a [webjobs SDK](../../app-service/webjobs-sdk-how-to.md)-ra épül. A webjobs SDK-beli feladat-gazdagép a Azure Functions futtatókörnyezete. Ha a viselkedést olyan módon kell vezérelni, amely nem lehetséges a Azure Functionsban, a webjobs SDK-val saját kezűleg is fejlesztheti és futtathatja Durable Functions.

A webjobs SDK 3. x verziójában a gazdagép a `IHost`implementációja, a 2. x verzióban pedig a `JobHost` objektumot használja.

A láncolási Durable Functions minta egy webjobs SDK 2. x verziójában érhető el: töltse le vagy klónozással nyissa meg a [Durable functions tárházat](https://github.com/azure/azure-functions-durable-extension/), és lépjen a *minták\\webjobssdk\\láncolási* mappájába.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már ismeri a webjobs SDK-t C# , a Azure functions és az Durable functions. Ha szüksége van a témakörök bevezetésére, tekintse meg a következő forrásokat:

* [Ismerkedés a webjobs SDK-val](../../app-service/webjobs-sdk-get-started.md)
* [Az első függvény létrehozása a Visual Studio használatával](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

A cikkben szereplő lépések végrehajtásához:

* [Telepítse a Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) -et az **Azure-fejlesztési** számítási feladattal.

  Ha már rendelkezik a Visual Studióval, de nem rendelkezik ilyen számítási feladattal, adja hozzá a számítási feladatot úgy, hogy az **eszközök > ** **eszközök és szolgáltatások lekérése**lehetőséget választja.

  (Ehelyett használhatja a [Visual Studio Code](https://code.visualstudio.com/) -ot, de néhány útmutató a Visual studióra vonatkozik.)

* Telepítse és futtassa az [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) 5,2-es vagy újabb verzióját. Egy másik lehetőség az *app. config* fájl frissítése egy Azure Storage-beli kapcsolódási karakterlánccal.

## <a name="webjobs-sdk-versions"></a>Webjobs SDK-verziók

Ez a cikk azt ismerteti, hogyan lehet létrehozni egy webjobs SDK 2. x projektet (Azure Functions 1. x-es verzióval egyenértékű). További információ a 3. x verzióról: [Webjobs SDK 3. x](#webjobs-sdk-3x) a cikk későbbi részében.

## <a name="create-a-console-app"></a>Konzolalkalmazás létrehozása

A Durable Functions webjobsként való futtatásához először létre kell hoznia egy konzol alkalmazást. A webjobs SDK-projekt csak a megfelelő NuGet-csomagokkal rendelkező Console app-projekt.

A Visual Studio **új projekt** párbeszédpanelen válassza a **klasszikus Windows asztali** > **Console app (.NET-keretrendszer)** lehetőséget. A Project-fájlban a `TargetFrameworkVersion` `v4.6.1`kell lennie.

A Visual studióhoz tartozik egy Webjobs-sablon is, amelyet a **Cloud** > **Azure webjobs (.NET-keretrendszer)** lehetőség kiválasztásával használhat. Ez a sablon számos csomagot telepít, amelyek némelyike esetleg nem szükséges.

## <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

A webjobs SDK, a Core kötések, a naplózási keretrendszer és a tartós feladatok bővítményéhez NuGet-csomagok szükségesek. Ezek a csomagok a **Package Manager konzol** parancsai, a legújabb stabil verziószámok a cikk írásának dátumától számítva:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

A naplózási szolgáltatók is szükségesek. Az alábbi parancsok az Azure Application Insights-szolgáltatót és a `ConfigurationManager`telepítik. A `ConfigurationManager` lehetővé teszi, hogy az alkalmazás beállításaiból szerezze be a Application Insights Instrumentation-kulcsot.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

A következő parancs telepíti a konzol szolgáltatóját:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost-kód

A-konzol alkalmazás létrehozása és a szükséges NuGet-csomagok telepítése után készen áll a Durable Functions használatára. Ezt a JobHost-kód használatával teheti meg.

A Durable Functions-bővítmény használatához hívja meg a `UseDurableTask`t a `Main` metódusban található `JobHostConfiguration` objektumon:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

A `DurableTaskExtension` objektumban megadható tulajdonságok listáját a [Host. JSON](../functions-host-json.md#durabletask)fájlban tekintheti meg.

A `Main` módszer a naplózási szolgáltatók beállítására is szolgál. A következő példa a konzolt és a Application Insights szolgáltatót konfigurálja.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Functions

A webjobs-feladatok kontextusában a Durable Functions a Azure Functions kontextusában különbözik a Durable Functionstől. Fontos, hogy tisztában legyenek a kódok írásához szükséges különbségekkel.

A webjobs SDK nem támogatja a következő Azure Functions funkciókat:

* [Függvénynév attribútum](#functionname-attribute)
* [HTTP-trigger](#http-trigger)
* [Durable Functions HTTP Management API](#http-management-api)

### <a name="functionname-attribute"></a>Függvénynév attribútum

Egy webjobs SDK-projektben a függvény metódusának neve a függvény neve. A `FunctionName` attribútum csak Azure Functionsban használatos.

### <a name="http-trigger"></a>HTTP eseményindító

A webjobs SDK nem rendelkezik HTTP-triggerrel. A minta projekt előkészítési ügyfele egy időzítő triggert használ:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>HTTP-kezelési API

Mivel nem rendelkezik HTTP-triggerrel, a webjobs SDK-nak nincs [http-kezelési API](durable-functions-http-api.md)-je.

A webjobs SDK-projektekben metódusokat hívhat meg a előkészítési ügyfél objektumon ahelyett, hogy HTTP-kérelmeket küld. A következő módszerek megfelelnek a HTTP Management API-val elvégezhető három feladatnak:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

A minta projektben a Orchestrator függvény elindítja a hanghívási ügyfél függvényt, majd egy olyan hurokba kerül, amely két másodpercenként meghívja a `GetStatusAsync`:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Minta futtatása

Durable Functions úgy állította be, hogy Webjobs fusson, és most már tisztában van azzal, hogy ez miben különbözik a futó Durable Functions önálló Azure Functions. Ezen a ponton hasznosnak bizonyulhat a minta működésének meglátása.

Ez a szakasz áttekintést nyújt a [minta projekt](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining)futtatásáról. A webjobs SDK-projekt helyi futtatásának és az Azure-Webjobs való üzembe helyezésének részletes leírását lásd: Ismerkedés [a Webjobs SDK-val](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Helyi futtatás

1. Győződjön meg arról, hogy a tárolási emulátor fut (lásd az [Előfeltételek](#prerequisites)című részt).

1. Ha a projekt helyi futtatásakor szeretné megtekinteni a Application Insights naplókat:

    a. Hozzon létre egy Application Insights erőforrást, és használja az **általános** alkalmazás típusát.

    b. Mentse a kialakítási kulcsot az *app. config* fájlban.

1. Futtassa a projektet.

### <a name="run-in-azure"></a>Futtatás az Azure-ban

1. Hozzon létre egy webalkalmazást és egy Storage-fiókot.

1. A webalkalmazásban mentse a Storage kapcsolati karakterláncot egy `AzureWebJobsStorage`nevű alkalmazás-beállításban.

1. Hozzon létre egy Application Insights erőforrást, és használja az **általános** alkalmazás típusát.

1. Mentse a kialakítási kulcsot egy `APPINSIGHTS_INSTRUMENTATIONKEY`nevű alkalmazás-beállításban.

1. Üzembe helyezés Webjobs.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

Ez a cikk a webjobs SDK 2. x projekt fejlesztését ismerteti. Ha [Webjobs SDK 3. x](../../app-service/webjobs-sdk-get-started.md) projektet fejleszt, ez a szakasz segít megérteni a különbségeket.

A legfontosabb változás a .net Core használata a .NET-keretrendszer helyett. Webjobs SDK 3. x projekt létrehozásához az utasítások megegyeznek a következő kivételekkel:

1. Hozzon létre egy .NET Core Console alkalmazást. A Visual Studio **új projekt** párbeszédpanelen válassza a **.net Core** > **Console app (.net Core)** lehetőséget. A projektfájl megadja, hogy a `TargetFramework` `netcoreapp2.x`.

1. Válassza ki a kiadási verzió webjobs SDK 3. x verzióját a következő csomagok közül:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. A *appSettings. JSON* fájlban állítsa be a Storage-kapcsolatok karakterláncát és a Application Insights-kialakítási kulcsot a .net Core konfigurációs keretrendszer használatával. Például:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Ehhez módosítsa a `Main` metódus kódját. Például:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Következő lépések

A webjobs SDK-val kapcsolatos további tudnivalókért tekintse meg a [Webjobs SDK használatát](../../app-service/webjobs-sdk-how-to.md)ismertető témakört.
