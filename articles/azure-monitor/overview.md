---
title: Azure Monitor áttekintése | Microsoft Docs
description: Az Azure-szolgáltatások és -alkalmazások teljes monitorozási stratégiájának részét képező Microsoft-szolgáltatások és funkciók áttekintése.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: cec437929e2f7ccd94cf4cf94ec81f87dac2678a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669165"
---
# <a name="azure-monitor-overview"></a>Az Azure Monitor áttekintése

Azure Monitor maximalizálja az alkalmazások és szolgáltatások rendelkezésre állását és teljesítményét azáltal, hogy átfogó megoldást kínál a Felhőbeli és a helyszíni környezetek telemetria gyűjtésére, elemzésére és működésére. Ez a szolgáltatás segít megérteni azt, hogy az alkalmazásai hogyan teljesítenek, valamint proaktív módon azonosítja a működésüket befolyásoló problémákat és azokat az erőforrásokat, amelyektől függenek.

Csak néhány példa arra, hogy mit tehet a Azure Monitor a következők:

- Az alkalmazásokkal és függőségekkel kapcsolatos problémák észlelése és diagnosztizálása [Application Insightsokkal](app/app-insights-overview.md).
- Az infrastruktúrával kapcsolatos problémák korrelálása a [Azure monitor for VMS](insights/vminsights-overview.md) és [Azure monitor a tárolók esetében](insights/container-insights-overview.md).
- A monitorozási adatait [log Analytics](log-query/log-query-overview.md) a hibaelhárítás és a mélyreható diagnosztika érdekében végezheti el.
- Az [intelligens riasztásokkal](platform/alerts-smartgroups-overview.md) és az [automatizált műveletekkel](platform/alerts-action-rules.md)nagy léptékű műveletek használhatók.
- Vizualizációkat hozhat létre az Azure- [irányítópultok](learn/tutorial-logs-dashboards.md) és- [munkafüzetek](app/usage-workbooks.md)létrehozásával.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Áttekintés

A következő ábra a Azure Monitor magas szintű áttekintését nyújtja. A diagram középpontjában a metrikák és naplók adattárai találhatók, amelyek a Azure Monitor által használt két alapvető adattípus. A bal oldalon az ezeket az [adattárakat](platform/data-platform.md)feltöltő [megfigyelési adatok forrásai](platform/data-sources.md) . A jobb oldalon azokat a különböző függvényeket Azure Monitor hajtja végre, amelyeket az összegyűjtött adatok, például az elemzés, a riasztások és a külső rendszereknek való folyamatos átvitel végez.

![Az Azure Monitor áttekintése](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Az adatplatform figyelése

Az Azure Monitor által összegyűjtött összes adat a két alapvető típus, a [metrikák és a naplók](platform/data-platform.md)egyikére illeszkedik. A [metrikák](platform/data-platform-metrics.md) olyan numerikus értékek, amelyek egy adott rendszer bizonyos aspektusait írják le egy adott időpontban. Azok a könnyen használható, és képes a közel valós idejű feldolgozásához. A [naplók](platform/data-platform-logs.md) különböző típusú adatokat tárolnak a rekordokban, amelyek mindegyike különböző tulajdonságokkal rendelkezik. Telemetriai adatokat – például az események és nyomok formájában tárolja naplók emellett teljesítményadatokká úgy, hogy azt az összes kombinálható is elemzés céljából.

Számos Azure-erőforrás esetében Azure Monitor közvetlenül gyűjtött adatokat a Azure Portal áttekintő lapján láthatók. Tekintse meg például a virtuális gépeket, és láthatja, hogy a teljesítmény-mérőszámokat több diagram jeleníti meg. Kattintson bármelyik gráfra, hogy megnyissa az adatokat a Azure Portal [mérőszámok Explorerben](platform/metrics-charts.md) , amely lehetővé teszi, hogy az idő múlásával több mérőszám értékét is ábrázolja.  A diagramok megjelenítése interaktív módon, vagy egy irányítópultot, megtekintheti őket más vizualizációkat rögzítheti őket.

![Mérőszámok](media/overview/metrics.png)

A Azure Monitor által gyűjtött naplózási adatokat elemezheti a [lekérdezésekkel](log-query/log-query-overview.md) , így gyorsan lekérheti, összevonhatja és elemezheti az összegyűjtött adatokat.  A Azure Portal [log Analytics](log-query/portals.md) használatával létrehozhat és tesztelheti a lekérdezéseket, majd közvetlenül elemezheti ezeket az eszközöket, vagy a [vizualizációk](visualizations.md) vagy a [riasztási szabályok](platform/alerts-overview.md)használatával mentheti a lekérdezéseket.

A Azure Monitor az Azure Adatkezelő által használt [Kusto-lekérdezési nyelv](/azure/kusto/query/) egy olyan verzióját használja, amely az egyszerű naplózási lekérdezésekhez megfelelő, de olyan speciális funkciókat is tartalmaz, mint az összesítések, az illesztések és az intelligens elemzések. A lekérdezés nyelvét [több leckével](log-query/get-started-queries.md)is gyorsan megismerheti.  Azon felhasználók számára, akik már ismerik az [SQL](log-query/sql-cheatsheet.md) és [Splunk](log-query/splunk-cheatsheet.md) használatát, külön útmutató áll rendelkezésére.

![Naplók](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Milyen adatokat Azure Monitor gyűjteni?

A Azure Monitor különböző forrásokból származó adatokat gyűjthet. Úgy gondolja, hogy az alkalmazásait, az általa használt operációs rendszert és szolgáltatásokat, illetve a platformot magától megilletően az alkalmazásaira vonatkozó adatok figyelésére kerül. A Azure Monitor a következő rétegekből gyűjt adatokat:

- **Alkalmazás-figyelési adat**: az Ön által írt kód teljesítményével és működésével kapcsolatos adat, függetlenül annak platformján.
- **Vendég operációs rendszer figyelési adatai**: azon operációs rendszer adatai, amelyeken az alkalmazás fut. Ez az Azure-ban, egy másik felhőben vagy a helyszínen is futhat. 
- **Azure-erőforrás-figyelési információk**: az Azure-erőforrások működésével kapcsolatos információk.
- **Azure-előfizetések figyelésére vonatkozó**információk: az Azure-előfizetés működésével és kezelésével, valamint az Azure állapotával és működésével kapcsolatos adatmennyiség. 
- Az **Azure-bérlő figyelési adatait**: a bérlői szintű Azure-szolgáltatások működésével, például Azure Active Directoryával kapcsolatos információk.

Amint létrehoz egy Azure-előfizetést, és megkezdi az erőforrások, például a virtuális gépek és a webalkalmazások hozzáadását, Azure Monitor megkezdi az adatok gyűjtését.  A [Tevékenységnaplók](platform/platform-logs-overview.md) rögzítik az erőforrások létrehozását vagy módosítását. A [metrikák](platform/data-platform.md) megmutatják, hogyan végezheti el az erőforrást, és milyen erőforrásokat fogyaszt. 

Kiterjesztheti az erőforrások tényleges működéséhez gyűjtött adatokat a [diagnosztika engedélyezésével](platform/platform-logs-overview.md) és az ügynökök számítási erőforrásokhoz való [hozzáadásával](platform/agent-windows.md) . Ez összegyűjti az erőforrás belső műveletének telemetria, és lehetővé teszi különböző [adatforrások](platform/agent-data-sources.md) konfigurálását a naplók és a metrikák összegyűjtéséhez a Windows és a Linux vendég operációs rendszerből. 

A [app Services alkalmazás](app/azure-web-apps.md) vagy [a virtuális gép és a virtuálisgép-méretezési csoport alkalmazás](app/azure-vm-vmss-apps.md)figyelésének engedélyezésével engedélyezheti a Application Insights számára, hogy részletes információkat gyűjtsön az alkalmazásról, beleértve a lapok nézeteit, az alkalmazások kéréseit és a kivételeket. Ellenőrizze az alkalmazás rendelkezésre állását a [rendelkezésre állási teszt](app/monitor-web-app-availability.md) konfigurálásával a felhasználói forgalom szimulálása érdekében.

### <a name="custom-sources"></a>Egyéni források

A Azure Monitor [Adatgyűjtői API](platform/data-collector-api.md)-val bármely Rest-ügyfélről gyűjthet naplózott adatokat. Ez lehetővé teszi egyéni figyelési forgatókönyvek létrehozását és a figyelés kiterjesztését olyan erőforrásokra, amelyek nem teszik elérhetővé a telemetria más forrásokon keresztül.

## <a name="insights"></a>Insights
A monitorozási adatai csak akkor hasznosak, ha növeli a számítástechnikai környezet működésének átláthatóságát. Azure Monitor számos olyan funkciót és eszközt tartalmaz, amelyek értékes betekintést nyújtanak az alkalmazásaiba és más erőforrásokra, amelyektől függenek. Az olyan megoldások és szolgáltatások [figyelése](insights/solutions.md) , mint például a [Application Insights](app/app-insights-overview.md) és [a tárolók Azure monitor az](insights/container-insights-overview.md) alkalmazás és az adott Azure-szolgáltatások különböző szempontjainak alapos betekintést nyújtanak. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) figyeli a webalkalmazások rendelkezésre állását, teljesítményét és használatát, függetlenül attól, hogy azok a felhőben vagy a helyszínen vannak tárolva. Kihasználja a hatékony adatelemzési platformot a Azure Monitorban, így mélyreható betekintést nyújt az alkalmazás műveleteibe, és a hibák diagnosztizálására vár anélkül, hogy a felhasználó bejelentse őket. Application Insights a különböző fejlesztői eszközökhöz való kapcsolódási pontokat tartalmaz, és a Visual Studióval integrálva támogatja a DevOps folyamatokat.

![Alkalmazás-áttekintés](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz
A [tárolók Azure monitor](insights/container-insights-overview.md) a szolgáltatás az Azure Kubernetes szolgáltatásban (ak) üzemeltetett felügyelt Kubernetes-fürtökön üzembe helyezett tároló-munkaterhelések teljesítményének figyelésére szolgál. Teljesítménybeli láthatóságot biztosít a Kubernetes által a mérőszámok API-n keresztül elérhető vezérlőkből, csomópontokból és tárolóból származó memória-és processzor-metrikák összegyűjtésével. A rendszer a tárolónaplókat is gyűjti.  Miután engedélyezte a Kubernetes-fürtök figyelését, ezeket a metrikákat és naplókat a rendszer automatikusan összegyűjti a Linux rendszerhez készült Log Analytics-ügynök egy tárolós verziójával.

![Tároló állapota](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez
[Azure monitor for VMS](insights/vminsights-overview.md) az Azure-beli virtuális gépeket (VM) a Windows-és Linux-alapú virtuális gépek teljesítményének és állapotának elemzésével, beleértve a különböző folyamatokat és a más erőforrásokra és külső folyamatokra vonatkozó összekapcsolású függőségeket is. A megoldás támogatja a helyszíni vagy más felhőalapú szolgáltató által üzemeltetett virtuális gépek teljesítményének és alkalmazási függőségeinek figyelését.  


![VM-ismeretek](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Monitorozási megoldások
A Azure Monitor [figyelési megoldásai](insights/solutions.md) olyan becsomagolt logikai készletek, amelyek betekintést nyújtanak egy adott alkalmazáshoz vagy szolgáltatáshoz. Ezek közé tartozik az alkalmazás vagy szolgáltatás figyelési adatok gyűjtésére, az adatok elemzésére szolgáló [lekérdezések](log-query/log-query-overview.md) és a vizualizációk [nézeteinek](../log-analytics/log-analytics-view-designer.md) begyűjtésére szolgáló logika. A [Microsoft](insights/solutions-inventory.md) és partnerei által kínált figyelési megoldások a különböző Azure-szolgáltatások és egyéb alkalmazások figyelését teszik lehetővé.

![Monitorozási megoldások](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Válaszadás a kritikus helyzetekre
Amellett, hogy lehetővé teszi a figyelési adatok interaktív elemzését, a hatékony figyelési megoldásnak képesnek kell lennie proaktív módon válaszolni a gyűjtött adatokban azonosított kritikus feltételekre. Ez egy szöveges vagy e-mail-címre küldheti a probléma kivizsgálásához felelős rendszergazdának. Vagy elindíthat egy olyan automatizált folyamatot, amely megkísérli kijavítani a hiba feltételeit.


### <a name="alerts"></a>Riasztások
[A Azure monitor riasztásai](platform/alerts-overview.md) proaktívan értesítik Önt a kritikus feltételekről, és potenciálisan megkísérlik a javítási műveleteket. A mérőszámokon alapuló riasztási szabályok közel valós idejű riasztást biztosítanak a numerikus értékek alapján, míg a naplókon alapuló szabályok lehetővé teszik a különböző forrásokból származó adatok összetett logikáját.

A Azure Monitor riasztási szabályai a különböző szabályokban megosztható címzettek és műveletek egyedi készleteit tartalmazó [műveleti csoportok](platform/action-groups.md)használata. A követelmények alapján a műveleti csoportok olyan műveleteket hajthatnak végre, mint például a webhookok, hogy a riasztások elindítják a külső műveleteket, vagy integrálni tudják a ITSM-eszközökkel.

![Riasztások](media/overview/alerts.png)

### <a name="autoscale"></a>Automatikus méretezés
Az automatikus méretezés lehetővé teszi, hogy az alkalmazás terhelésének kezeléséhez megfelelő mennyiségű erőforrást futtasson. Lehetővé teszi, hogy olyan szabályokat hozzon létre, amelyek a Azure Monitor által összegyűjtött mérőszámokat használják, így meghatározhatja, hogy mikor kell automatikusan erőforrásokat hozzáadni a terhelés növekedéséhez, és pénzt takaríthat meg a tétlenül ülő erőforrások eltávolításával. Megadhatja a példányok minimális és maximális számát, valamint a logikát az erőforrások növelése vagy csökkentése érdekében.

![Automatikus méretezés](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Figyelési adatgyűjtés megjelenítése
Az olyan [vizualizációk](visualizations.md) , mint a diagramok és a táblázatok, hatékony eszközök a monitorozási és a különböző célközönségeknek való megjelenítéshez. A Azure Monitor saját funkciókat biztosít a figyelési adatmegjelenítéshez és más Azure-szolgáltatások kihasználása érdekében a különböző célközönségeknek való közzétételhez.

### <a name="dashboards"></a>Irányítópultok
Az [Azure-irányítópultok](../azure-portal/azure-portal-dashboards.md) lehetővé teszik a különböző típusú adatok, például a metrikák és a naplók összevonását a [Azure Portal](https://portal.azure.com)egyetlen ablaktáblájába. Igény szerint megoszthatja az irányítópultot más Azure-felhasználókkal. Az egész Azure Monitorban található elemek hozzáadhatók egy Azure-irányítópulthoz az összes naplózási lekérdezés vagy metrikák diagramjának kimenetén kívül. Létrehozhat például egy irányítópultot, amely a metrikák gráfját, a tevékenységek naplóit, a Application Insights használati diagramját, valamint egy napló lekérdezés kimenetét jeleníti meg.

![Irányítópult](media/overview/dashboard.png)

### <a name="views"></a>Nézetek
A [nézetek](../log-analytics/log-analytics-view-designer.md) vizuálisan jelennek meg a naplófájlokban Azure monitor.  Minden nézet egy olyan csempét tartalmaz, amely a kritikus adatok összefoglalása mellett a vizualizációk, például a sávok és a vonalas diagramok kombinációját mutatja be.  A figyelési megoldások olyan nézeteket tartalmaznak, amelyek egy adott alkalmazás adatait összegzik, és létrehozhatja saját nézeteit is, amelyekkel adatokat lehet bemutatni bármely naplóból származó lekérdezésből. A Azure Monitor egyéb elemeihez hasonlóan a nézetek hozzáadhatók az Azure-irányítópultokhoz.

![Nézet](media/overview/view.png)

### <a name="power-bi"></a>Power BI
A [Power bi](https://powerbi.microsoft.com) egy üzleti elemzési szolgáltatás, amely interaktív vizualizációkat biztosít különböző adatforrások között, és hatékony eszköz arra, hogy a szervezeten belül és kívül mások számára is elérhetővé tegyék az adatgyűjtést. A Power BI beállítható úgy, hogy a [rendszer automatikusan importálja a naplófájlokat a Azure monitorból](../log-analytics/log-analytics-powerbi.md) , hogy kihasználhassa ezeket a további vizualizációkat.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Az Adatintegráció és-exportálás
Gyakran a Azure Monitor más rendszerekkel való integrálására, valamint a megfigyelési adatait használó egyéni megoldások létrehozására van szükség. Más Azure-szolgáltatások együttműködik a Azure Monitor az integráció biztosításához.

### <a name="event-hub"></a>Eseményközpont
Az [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) egy streaming platform-és esemény-betöltési szolgáltatás, amely bármilyen valós idejű elemzési szolgáltatóval vagy batch-vagy Storage-adapterrel képes átalakítani és tárolni az adattárolást. A Event Hubs használatával [továbbíthatja Azure monitor-adatforrásait](platform/stream-monitoring-data-event-hubs.md) a partner Siem-hez és a figyelési eszközökhöz.


### <a name="logic-apps"></a>Logic Apps
A [Logic apps](https://azure.microsoft.com/services/logic-apps) egy olyan szolgáltatás, amely lehetővé teszi a feladatok és az üzleti folyamatok automatizálását a különböző rendszerekkel és szolgáltatásokkal integrált munkafolyamatok használatával. Olyan tevékenységek érhetők el, amelyekben a metrikák és naplók olvashatók és írhatók Azure Monitor, így különböző rendszerekkel integrálható munkafolyamatokat hozhat létre.


### <a name="api"></a>API
Több API áll rendelkezésre a metrikák és a naplók olvasására és írására Azure Monitor a generált riasztások elérése mellett. Lehetőség van a riasztások konfigurálására és lekérésére is. Ez lényegében korlátlan lehetőségeket biztosít a Azure Monitor-nal integrált egyéni megoldások létrehozásához.

## <a name="next-steps"></a>Következő lépések
További információk:

* A Azure Monitor által gyűjtött adatok [metrikái és naplói](platform/data-platform.md) .
* [Adatforrások](platform/data-sources.md) az alkalmazás különböző összetevőinek telemetria küldéséhez.
* [Naplók lekérdezése](log-query/log-query-overview.md) az összegyűjtött adatok elemzéséhez.
* [Ajánlott eljárások](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) a felhőalapú alkalmazások és szolgáltatások monitorozásához.
