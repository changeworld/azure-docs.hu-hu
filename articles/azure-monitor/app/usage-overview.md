---
title: Használat elemzése az Azure Application Insights használatával | Microsoft docs
description: Ismerje meg a felhasználókat, és hogy mit csinálnak az alkalmazással.
ms.topic: conceptual
ms.date: 09/19/2019
ms.openlocfilehash: 9f34267a1820f8b2365a41569bd3c8eaed9f2f9c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275645"
---
# <a name="usage-analysis-with-application-insights"></a>Használatelemzés az Application Insights szolgáltatással

A webes vagy mobil alkalmazások mely funkciói a legnépszerűbbek? A felhasználók a céljaikat az alkalmazással érik el? Kiesnek bizonyos pontokon, és később visszatérnek?  Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) segítségével hatékony információkhoz juthat az alkalmazás használatáról. Minden alkalommal, amikor frissíti az alkalmazást, megvizsgálhatja, hogy milyen jól működik a felhasználók számára. Ezzel az ismerettel a következő fejlesztési ciklusokra vonatkozó adatvezérelt döntéseket hozhat.

## <a name="send-telemetry-from-your-app"></a>Telemetria küldése az alkalmazásból

A legjobb megoldás a Application Insights telepítésével érhető el az App Server kódjában és a weblapjain. Az alkalmazás ügyfél-és kiszolgáló-összetevői telemetria küldenek a Azure Portalnak elemzés céljából.

1. **Kiszolgáló kódja:** Telepítse a megfelelő modult a [ASP.net](../../azure-monitor/app/asp-net.md), az [Azure](../../azure-monitor/app/app-insights-overview.md), a [Java](../../azure-monitor/app/java-get-started.md), a [Node. js](../../azure-monitor/app/nodejs.md)vagy [más](../../azure-monitor/app/platforms.md) alkalmazáshoz.

    * *Nem szeretné telepíteni a kiszolgálói kódot? Egyszerűen [hozzon létre egy Azure Application Insights-erőforrást](../../azure-monitor/app/create-new-resource.md ).*

2. **Weblap kódja:** Adja hozzá a következő szkriptet a weboldalához a záró ``</head>``előtt. Cserélje le a rendszerállapot-kulcsot a Application Insights erőforrás megfelelő értékére:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    A webhelyek figyeléséhez szükséges speciális konfigurációk megismeréséhez tekintse meg a [JavaScript SDK-referenciát ismertető cikket](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

3. **Mobile App code:** Az App Center SDK használatával gyűjthet eseményeket az alkalmazásból, majd az események másolatait elküldheti az elemzéshez az [útmutató](../../azure-monitor/learn/mobile-center-quickstart.md)alapján Application Insights.

4. **Telemetria beolvasása:** Futtassa a projektet hibakeresési módban néhány percre, majd keresse meg az eredményeket a Application Insights áttekintés paneljén.

    Tegye közzé alkalmazását az alkalmazás teljesítményének figyeléséhez, és Ismerje meg, hogy a felhasználók hogyan használják az alkalmazást.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Felhasználói és munkamenet-azonosító belefoglalása a telemetria
A felhasználók az idő múlásával követhetik nyomon a felhasználókat, Application Insights az azonosítását. Az Events eszköz az egyetlen olyan használati eszköz, amelyhez nincs szükség felhasználói AZONOSÍTÓra vagy munkamenet-AZONOSÍTÓra.

A felhasználói és munkamenet-azonosítók küldésének megkezdése [ezzel a folyamattal](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>A használati demográfia és a statisztika megismerése
Megtudhatja, hogy mikor használják a felhasználók az alkalmazást, milyen lapokat érdeklik leginkább, hol találhatók a felhasználók, milyen böngészőket és operációs rendszereket használnak. 

A felhasználók és a munkamenetek jelentés az adatait lapok vagy egyéni események alapján szűri, és azokat a tulajdonságok, például a hely, a környezet és a lap alapján szegmentálja. Saját szűrőket is hozzáadhat.

![Felhasználók](./media/usage-overview/users.png)  

A jobb oldali elemzések érdekes mintákat mutatnak az adathalmazban.  

* A **felhasználók** jelentés a kiválasztott időszakokban lévő lapokhoz hozzáférő egyedi felhasználók számát számolja. A Web Apps esetében a felhasználók a cookie-k használatával számítanak. Ha valaki különböző böngészőkkel vagy ügyfélszámítógépekkel fér hozzá a webhelyhez, vagy törli a cookie-kat, akkor a rendszer többször is megszámolja őket.
* A **munkamenetek** jelentés megszámolja a webhelyhez hozzáférő felhasználói munkamenetek számát. A munkamenet egy adott felhasználó által végzett tevékenység, amely több mint fél óra inaktivitási időtartammal leállt.

[További információ a felhasználókról, a munkamenetekről és az események eszközeiről](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Megőrzés – hány felhasználó érkezik vissza?

Az adatmegőrzés segítségével megismerheti, hogy a felhasználók milyen gyakran térnek vissza az alkalmazás használatára, azon felhasználók kohorszai alapján, akik bizonyos üzleti műveleteket hajtottak végre egy adott időszakban. 

- Annak megismerése, hogy a felhasználók miként térhetnek vissza másokhoz 
- A valós felhasználói adathalmazok alapján alkotott hipotézisek 
- Annak megállapítása, hogy probléma van-e az adatmegőrzéssel a termékben 

![Megőrzés](./media/usage-overview/retention.png) 

A felső megőrzési vezérlők lehetővé teszik meghatározott események és időtartományok meghatározását a megőrzés kiszámításához. A középső gráf a megadott időtartomány alapján vizuálisan ábrázolja a teljes megőrzési arányt. Az alsó diagram az egyes adatmegőrzési időszakot jelöli. Ez a részletességi szint lehetővé teszi, hogy megtudja, mit csinálnak a felhasználók, és mi befolyásolhatja a visszatérő felhasználók részletesebb részletességét.  

[További információ az adatmegőrzési eszközről](usage-retention.md)

## <a name="custom-business-events"></a>Egyéni üzleti események

Ha szeretné megismerni, hogy a felhasználók mit tesznek az alkalmazással, hasznos lehet a kód sorait beszúrni az egyéni események naplózására. Ezek az események a részletes felhasználói műveletekkel, például az adott gombokra kattintva követhetik nyomon a jelentős üzleti eseményeket, például a vásárlást vagy a játék megnyerését. 

Bár bizonyos esetekben az oldalletöltések hasznos eseményeket jelenthetnek, általában nem igaz. A felhasználó megnyithatja a termék oldalát a termék megvásárlása nélkül. 

Adott üzleti események esetében a felhasználók előrehaladását a webhelyről is elvégezheti. Megtudhatja, hogy milyen beállítások állnak rendelkezésre a különböző lehetőségekhez, és hogy hol vannak kiesésük vagy nehézségei. Ezzel az ismerettel tájékozott döntéseket hozhat a fejlesztési várakozó prioritásokkal kapcsolatban.

Az eseményeket az alkalmazás ügyféloldali oldaláról lehet naplózni:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Vagy a kiszolgáló oldalán:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Ezekhez az eseményekhez tulajdonságokat is csatolhat, így szűrheti vagy feloszthatja az eseményeket a portálon való vizsgálat során. Emellett az egyes eseményekhez, például a névtelen felhasználói AZONOSÍTÓhoz is csatolni kell a tulajdonságok standard készletét, amely lehetővé teszi egy adott felhasználó tevékenységi sorrendjének nyomon követését.

További információ az [Egyéni eseményekről](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) és a [tulajdonságokról](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Szeletek és kockák eseményei

A felhasználók, a munkamenetek és az események eszközökön egyéni eseményeket adhat meg a felhasználó, az esemény neve és a tulajdonságok alapján.
![Felhasználók](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>A telemetria megtervezése az alkalmazással

Ha az alkalmazás minden funkcióját megtervezi, gondolja át, hogyan fogja mérni a sikerességét a felhasználókkal. Döntse el, hogy milyen üzleti eseményeket kell rögzítenie, és az események követési hívásait az alkalmazásba az elejétől.

## <a name="a--b-testing"></a>A | B tesztelés
Ha nem tudja, hogy egy adott szolgáltatás melyik változata lesz sikeres, szabadítson fel mindkettőt, hogy minden elérhető legyen a különböző felhasználók számára. Mérje fel az egyes műveletek sikerességét, majd váltson át egy egységes verzióra.

Ehhez a technikához külön tulajdonságértékeket kell csatolni az alkalmazás egyes verziói által eljuttatott összes telemetria. Ezt úgy teheti meg, hogy meghatározza a tulajdonságokat az aktív TelemetryContext. Ezek az alapértelmezett tulajdonságok minden olyan telemetria-üzenethez hozzáadódnak, amelyet az alkalmazás küld – nem csak az egyéni üzeneteket, hanem a standard telemetria is.

A Application Insights portálon szűrje és ossza meg az adatait a tulajdonságértékek alapján, hogy összehasonlítsa a különböző verziókat.

Ehhez [állítson be egy telemetria-inicializálást](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer):

**ASP.NET-alkalmazások**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

A webalkalmazás-inicializáló, például a Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**Alkalmazások ASP.NET Core**

> [!NOTE]
> Az inicializálás `ApplicationInsights.config` vagy `TelemetryConfiguration.Active` használatával történő hozzáadása nem érvényes ASP.NET Core alkalmazásokhoz. 

[ASP.net Core](asp-net-core.md#adding-telemetryinitializers) alkalmazások esetében az új `TelemetryInitializer` hozzáadásához vegye fel azt a függőség-injektálási tárolóba, az alábbi ábrán látható módon. Ez a `Startup.cs` osztály `ConfigureServices` metódusában történik.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Minden új TelemetryClients automatikusan hozzáadja a megadott tulajdonságérték értékét. Az egyes telemetria-események felülbírálják az alapértelmezett értékeket.

## <a name="next-steps"></a>Következő lépések
   - [Felhasználók, munkamenetek, események](usage-segmentation.md)
   - [Tölcsérek](usage-funnels.md)
   - [Megőrzés](usage-retention.md)
   - [Felhasználói folyamatok](usage-flows.md)
   - [Munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
   - [Felhasználói környezet hozzáadása](usage-send-user-context.md)
