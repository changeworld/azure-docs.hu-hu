---
title: Azure Application Insights IP-címek gyűjteménye | Microsoft Docs
description: Az IP-címek és a térinformatikai kezelésének ismertetése az Azure Application Insights
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656517"
---
# <a name="geolocation-and-ip-address-handling"></a>Térinformatikai és IP-címek kezelése

Ez a cikk azt ismerteti, hogyan történik a térinformatikai keresés és az IP-címek kezelése Application Insights az alapértelmezett viselkedés módosításával együtt.

## <a name="default-behavior"></a>Alapértelmezett viselkedés

Alapértelmezés szerint az IP-címek ideiglenes gyűjtése történik, de a Application Insights nem tárolja őket. A folyamat alapvetően a következőképpen történik:

Az IP-címeket a rendszer a telemetria-adatmennyiség részeként küldi Application Insights. Amikor eléri a betöltési végpontot az Azure-ban, a rendszer az IP-címet használja a térinformatikai keresés végrehajtásához a [GeoLite2-ből a Maxmind-ből](https://dev.maxmind.com/geoip/geoip2/geolite2/). A keresés eredményei a következő mezők feltöltésére szolgálnak `client_City`, `client_StateOrProvince``client_CountryOrRegion`. Ezen a ponton a rendszer elveti az IP-címet, és `0.0.0.0` a `client_IP` mezőbe írja.

* Böngésző telemetria: ideiglenesen gyűjtjük a küldő IP-címét. Az IP-címet a betöltési végpont számítja ki.
* Kiszolgáló telemetria: a Application Insights modul átmenetileg gyűjti az ügyfél IP-címét. Ha `X-Forwarded-For` van beállítva, a rendszer nem gyűjti.

Ez a viselkedés úgy működik, hogy segít elkerülni a személyes adatgyűjtést. Amikor csak lehetséges, javasoljuk a személyes adatgyűjtés elkerülését. 

## <a name="overriding-default-behavior"></a>Az alapértelmezett viselkedés felülbírálása

Habár az alapértelmezett viselkedés a személyes adatok gyűjtésének csökkentése, továbbra is rugalmasságot biztosítunk az IP-címek adatainak gyűjtéséhez és tárolásához. A személyes adat (például IP-címek) tárolásának megkezdése előtt erősen ajánlott ellenőrizni, hogy ez nem oldja meg a megfelelőségi követelményeket vagy a helyi előírásokat. Ha többet szeretne megtudni a Application Insights személyes adatainak kezeléséről, tekintse meg a [személyes adatainak útmutatását](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt).

## <a name="storing-ip-address-data"></a>IP-címek tárolására szolgáló adattároló

Az IP-gyűjtés és-tárolás engedélyezéséhez a Application Insights összetevő `DisableIpMasking` tulajdonságát `true`értékre kell állítani. Ezt a tulajdonságot Azure Resource Manager-sablonokon vagy a REST API meghívásával lehet beállítani. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portál 

Ha csak egyetlen Application Insights erőforrás viselkedését kell módosítania, a legegyszerűbben az Azure Portal keresztül valósítható meg.  

1. Nyissa meg Application Insights erőforrás-> **beállításait** > **Exportálás sablon** 

    ![Sablon exportálása](media/ip-collection/export-template.png)

2. **Telepítés** kiválasztása

    ![Vörös színnel jelölt üzembe helyezés gomb](media/ip-collection/deploy.png)

3. Válassza a **Sablon szerkesztése**lehetőséget. (Ha a sablon további tulajdonságokkal vagy erőforrásokkal rendelkezik, amelyek nem jelennek meg ebben a példában a sablonban, folytassa körültekintően, hogy az összes erőforrás elfogadja a sablon központi telepítését növekményes módosításként vagy frissítésként.)

    ![Sablon szerkesztése](media/ip-collection/edit-template.png)

4. Végezze el az alábbi módosításokat a JSON-ban az erőforráshoz, majd kattintson a **Mentés**gombra:

    ![A képernyőfelvétel egy vesszőt ad hozzá a "IbizaAIExtension" után, és új sort ad hozzá a "DisableIpMasking" kifejezéssel: true](media/ip-collection/save.png)

    > [!WARNING]
    > Ha olyan hibát tapasztal, amely azt mondja:  **_az erőforráscsoport olyan helyen található, amelyet a sablon egy vagy több erőforrása nem támogat. Válasszon másik erőforráscsoportot._** Ideiglenesen válasszon egy másik erőforráscsoportot a legördülő listából, majd válassza ki újra az eredeti erőforráscsoportot a hiba elhárításához.

5. Válassza az **elfogadom** > **vásárlás**lehetőséget. 

    ![Sablon szerkesztése](media/ip-collection/purchase.png)

    Ebben az esetben semmi újat nem vásárol, csak a meglévő Application Insights erőforrás konfigurációját frissíti.

6. Miután az üzembe helyezés befejeződött, a rendszer rögzíti az új telemetria-adatgyűjtést.

    Ha még egyszer kijelöli és szerkeszti a sablont, akkor csak az alapértelmezett sablont fogja látni, és nem fogja látni az újonnan hozzáadott tulajdonságot és a hozzá tartozó értéket. Ha nem látja az IP-címeket, és szeretné megerősíteni, hogy a `"DisableIpMasking": true` be van állítva. Futtassa a következő PowerShell-t: (cserélje le a `Fabrikam-dev`t a megfelelő erőforrás-és erőforráscsoport-névvel.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Ennek eredményeképpen a rendszer a tulajdonságok listáját adja vissza. Az egyik tulajdonságnak `DisableIpMasking: true`kell olvasnia. Ha a PowerShellt az új tulajdonságnak a Azure Resource Manager használatával történő telepítése előtt futtatja, akkor a tulajdonság nem létezik.

### <a name="rest-api"></a>Rest API

A [REST API](https://docs.microsoft.com/rest/api/azure/) -adattartalom, amely ugyanezeket a módosításokat hajtja végre, a következőképpen történik:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Telemetria inicializáló

Ha az IP-címek teljes vagy egy részének rögzítéséhez rugalmasabb alternatíva szükséges, mint az `DisableIpMasking`, a [telemetria inicializáló](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) használatával az összeset vagy annak egy részét átmásolhatja egy egyéni mezőbe. 

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Ha nem fér hozzá `ISupportProperties`hoz, ellenőrizze, hogy a Application Insights SDK legújabb stabil kiadását futtatja-e. `ISupportProperties` a magas fokú kardinális értékekhez készültek, míg a `GlobalProperties` megfelelőbbek a kis-és nagyszámú értékek, például a régió neve, a környezet neve stb. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Telemetria-inicializálás engedélyezése a ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>ASP.NET Core telemetria inicializáló engedélyezése

A telemetria inicializáló ASP.net Core ugyanúgy létrehozhatja, mint a ASP.net, de az inicializálás engedélyezéséhez a következő példát használhatja a hivatkozáshoz:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>Ügyféloldali JavaScript

A kiszolgálóoldali SDK-któl eltérően az ügyféloldali JavaScript SDK nem számítja ki az IP-címet. Az ügyféloldali telemetria alapértelmezett IP-címének kiszámítása az Azure-beli betöltési végponton történik az telemetria érkezésekor. Ez azt jelenti, hogy ha ügyféloldali adatokat küld egy proxynak, majd továbbítja azt a betöltési végpontra, az IP-cím kiszámítása a proxy IP-címét és nem az ügyfelet is megjeleníti. Ha nem használ proxyt, ez nem lehet probléma.

Ha közvetlenül az ügyfélszámítógépen szeretné kiszámítani az IP-címet, akkor a számítás végrehajtásához hozzá kell adnia a saját egyéni logikáját, és az eredményt kell használnia a `ai.location.ip` címke beállításához. A `ai.location.ip` beállításakor a rendszer nem hajtja végre az IP-címek kiszámítását a betöltési végponton, és a megadott IP-cím tiszteletben van, és a Geo-keresés végrehajtásához használatos. Ebben az esetben az IP-cím alapértelmezés szerint nulla marad. 

Ha meg szeretné őrizni az egyéni logikából kiszámított teljes IP-címet, használhat olyan telemetria-inicializálást, amely átmásolja a `ai.location.ip`ben megadott IP-cím adatait egy külön egyéni mezőre. A kiszolgálóoldali SDK-któl eltérően azonban a harmadik féltől származó kódtárak vagy a saját egyéni ügyféloldali IP-gyűjtési logikája nélkül az ügyféloldali SDK nem számítja ki az IP-címet.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>A telemetria inicializáló eredményeinek megtekintése

Ha ezt követően új forgalmat indít el a webhelye számára, és körülbelül 2-5 percet vár annak biztosítására, hogy az idő betöltése megtörténjen, futtathat egy Kusto-lekérdezést, hogy megtudja, működik-e az IP-cím gyűjteménye:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Az újonnan összegyűjtött IP-címeknek a `customDimensions_client-ip` oszlopban kell szerepelniük. Az alapértelmezett `client-ip` oszlop továbbra is mind a 4 oktetttel rendelkezik, vagy csak az első három oktettet jeleníti meg attól függően, hogy az IP-cím gyűjteményt hogyan konfigurálta az összetevő szintjén. Ha a telemetria inicializáló megvalósítása után helyi tesztelést végez, és a `customDimensions_client-ip` látható érték `::1` ez a várt viselkedés. `::1` a visszacsatolási cím az IPv6-ban. Ez egyenértékű a `127.0.01` az IPv4-ben, és az az eredmény, amelyet a rendszer a localhost-ból való tesztelés során fog látni.

## <a name="next-steps"></a>További lépések

* További információ a Application Insights [személyes adatainak gyűjtéséről](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) .

* További információ arról, hogyan működik az [IP-címek gyűjteménye](https://apmtips.com/blog/2016/07/05/client-ip-address/) Application Insights. (Ez egy régebbi külső blogbejegyzés, amelyet az egyik mérnök írt. A rendszer a jelenlegi alapértelmezett viselkedést állítja be, ahol az IP-cím `0.0.0.0`ként van rögzítve, de a beépített `ClientIpHeaderTelemetryInitializer`mechanikája nagyobb mélységbe kerül.)
