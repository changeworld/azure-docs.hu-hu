---
title: Az Azure Monitor dokumentációjának újdonságai
description: A Azure Monitor dokumentációjának frissítése havonta frissül.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: b42acdf64612da6837bc67752f7a22169ddef7e2
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673322"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Az Azure Monitor dokumentációjának újdonságai
Ez a cikk felsorolja Azure Monitor új vagy jelentős mértékben frissített cikkeket. A rendszer minden hónap első hetében frissíti az előző hónapból származó cikkek frissítéseinek befoglalásával.

## <a name="march-2020"></a>Március 2020

### <a name="agents"></a>Ügynökök
Több frissítés a diagnosztikai bővítmény tartalmának újraírása részeként.

- [Az Azure monitoring-ügynökök áttekintése](platform/agents-overview.md) – átstrukturált táblázatok az egyes ügynökök egyedi funkcióinak jobb tisztázásához.
- [Azure Diagnostics bővítmény áttekintése](platform/diagnostics-extension-overview.md) – teljes újraírás.
- A [blob Storage használata az IIS-hez és a Table Storage](platform/diagnostics-extension-logs.md) -hoz a frissítéshez és a tisztasághoz Azure monitor általános újraírással kapcsolatos eseményekhez.
- [Telepítse és konfigurálja a Windows Azure Diagnostics bővítményt (wad)](platform/diagnostics-extension-windows-install.md) – új cikk. 
- [Windows Diagnostics bővítmény sémája](platform/diagnostics-extension-schema-windows.md) – átrendezve.
- [Adatok küldése a Windows Azure Diagnostics bővítményből az azure Event Hubsba](platform/diagnostics-extension-stream-event-hubs.md) – teljes mértékben újraírható és frissítve.
- [Diagnosztikai adatgyűjtés tárolása és megtekintése az Azure Storage-ban](platform/diagnostics-extension-to-storage.md) – teljes mértékben újraírható és frissíthető.
- [Log Analytics virtuálisgép-bővítmény a Windowshoz](../virtual-machines/extensions/oms-windows.md) – jobban tisztázza log Analytics ügynökkel való kapcsolatot.
- [Azure monitor virtuálisgép-bővítmény a Linux](../virtual-machines/extensions/oms-linux.md) rendszerhez – jobban tisztázza log Analytics-ügynökkel való kapcsolatot.




### <a name="application-insights"></a>Application Insights
- [A kapcsolatok karakterláncai az Azure Application Insightsban](app/sdk-connection-string.md) – új cikk.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat

#### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz
- [Azure Active Directory integrálása az Azure Kubernetes szolgáltatással](../aks/azure-ad-integration.md) – vegye figyelembe, hogy egy ügyfélalkalmazás létrehozásával támogatja a RBAC-kompatibilis fürtöt a tárolók Azure monitor támogatásához.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez
- [Azure monitor for VMS (GA) gyakori kérdések](insights/vminsights-ga-release-faq.md) – a teljesítményadatok tárolási módjának módosítása.

#### <a name="office-365"></a>Office 365
- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – frissített elavulás dátuma.


### <a name="logs"></a>Naplók
- Azure Monitor-új cikkben [található naplók optimalizálása](log-query/query-optimization.md) .
- [Azure monitor naplók használatának és költségeinek kezelése](platform/manage-cost-storage.md) – továbbfejlesztett minta-lekérdezések, amelyek segítenek a használat megértésében.

### <a name="metrics"></a>Mérőszámok
- [Azure monitor a platform metrikái exportálhatók a diagnosztikai beállítások](platform/metrics-supported-export-diagnostic-settings.md) – hozzáadott szakasz a nullák és a nulla értékek viselkedésének változása esetén.


### <a name="visualizations"></a>Vizualizációk
Több új cikk a Designer for munkafüzetek átalakítási útmutatójában.

- [Azure monitor a Designer és a munkafüzetek közötti áttérési útmutató](platform/view-designer-conversion-overview.md) – új cikk.
- [Azure monitor tervező átalakítása munkafüzetekbe konvertálási beállítások](platform/view-designer-conversion-options.md) – új cikk.
- [Azure monitor megtekintheti a tervezőt munkafüzetekhez csempe-konverziók](platform/view-designer-conversion-tiles.md) – új cikk.
- [Azure monitor megtekintheti a tervezőt a munkafüzetek átalakításának összegzése és a hozzáférés](platform/view-designer-conversion-access.md) – új cikk alapján.
- [Azure monitor tervező átalakítása munkafüzetekhez gyakori feladatok konvertálása](platform/view-designer-conversion-tasks.md) – új cikk.
- [Azure monitor tervező átalakítása munkafüzetek átalakítására – példák](platform/view-designer-conversion-examples.md) – új cikk.




## <a name="january-2020"></a>2020. január

### <a name="general"></a>Általános kérdések
- [Mi figyeli a Azure Monitor?](monitor-reference.md) – Új cikk.

### <a name="agents"></a>Ügynökök
- [A naplófájlok adatainak összegyűjtése az Azure log Analytics agenttel – a](platform/log-analytics-agent.md) hálózati tűzfalra vonatkozó követelmények tábla frissítése.


### <a name="alerts"></a>Riasztások
- [Műveleti csoportok létrehozása és kezelése a Azure Portalban](platform/action-groups.md) – a már nem szükséges v2-függvények eltávolításának beállítása.
- [Metrikai riasztás létrehozása Resource Manager-sablonnal](platform/alerts-metric-create-templates.md) – példa a *ignoreDataBefore* paraméterhez.  A többszörös feltételi szabályokkal kapcsolatos korlátozásokat adott meg.
- [Log Analytics riasztás használata REST API](platform/api-alerts.md) – JSON-példa kijavítva.


### <a name="application-insights"></a>Application Insights
- Az [Application Insights által használt IP-címek és a log Analytics](app/ip-addresses.md) – a rendelkezésre állási teszt szakasz frissítése a bejövő portszabály hozzáadásával az Azure hálózati biztonsági csoportokat használó forgalom engedélyezéséhez.
- [Az Azure Application Insights Profiler kapcsolatos problémák elhárítása](app/profiler-troubleshooting.md) – frissített általános hibaelhárítás.
- [Telemetria mintavételezés az Azure Application Insightsban](app/sampling.md) – frissített és átstrukturált, hogy javítsa az olvashatóságot az ügyfelek visszajelzései alapján.


### <a name="data-security"></a>Adatbiztonság
- [Azure monitor ügyfél által felügyelt kulcs konfigurálása](platform/customer-managed-keys.md) – új cikk.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat

#### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz
- [Azure monitor konfigurálása a containers Agent adatgyűjtéshez](insights/container-insights-agent-config.md) – további részletek az ügynök frissítéséhez az Azure Red Hat OpenShift, és további információkkal bővült az ügynök verziófrissítésének módszerei.
- [Teljesítmény-riasztások létrehozása a tárolók Azure monitor számára](insights/container-insights-alerts.md) – felülvizsgált információk és frissített lépések a munkaterületen tárolt teljesítményadatok riasztásának létrehozásához munkaterületen belüli riasztások használatával.
- [Kubernetes-figyelés a Azure monitor for containers szolgáltatással](insights/container-insights-analyze.md) – a Windows Kubernetes-fürtök támogatásáról szóló áttekintő cikk és a elemzésre vonatkozó cikk frissítése.
- [Azure Red Hat OpenShift-fürtök konfigurálása az Azure monitor for containers](insights/container-insights-azure-redhat-setup.md) szolgáltatással – további részletek az ügynök frissítéséhez az Azure Red Hat OpenShift, és további információkkal bővült az ügynök verziófrissítésének módszerei.
- [Hibrid Kubernetes-fürtök konfigurálása Azure monitor for containers](insights/container-insights-hybrid-setup.md) használatával – frissítve a biztonságos port hozzáadásának támogatásával: a 10250 a Kubelet cAdvisor.
- [A containers agent Azure monitor kezelése – az](insights/container-insights-manage-agent.md) Azure Red Hat OpenShift és a metrikák leselejtezésének a viselkedésével és konfigurálásával kapcsolatos frissített részletek a más típusú Kubernetes-fürtökhöz képest.
- [Azure monitor konfigurálása a containers Prometheus-integrációhoz](insights/container-insights-prometheus-integration.md) – frissített részletek az Azure Red Hat OpenShift származó metrika-leselejtezési viselkedéssel és konfigurációval kapcsolatban, más típusú Kubernetes-fürtökhöz képest.
- A [tárolók Azure monitorának frissítése a metrikák számára](insights/container-insights-update-metrics.md) – frissített részletek az Azure Red Hat OpenShift a metrika-lekaparás működésével és konfigurálásával, valamint más típusú Kubernetes-fürtökhöz képest.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez
- [Azure monitor for VMS (GA) – gyakori kérdések](insights/vminsights-ga-release-faq.md) – további információk a munkaterület és az ügynökök új verzióra való frissítéséről.

#### <a name="office-365"></a>Office 365
- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – további részletek és gyakori kérdések az Office 365-megoldás Azure sentinelben való áttelepítéséről. A bevezetési szakasz el lett távolítva.



### <a name="logs"></a>Naplók
- [Log Analytics munkaterületek kezelése Azure monitor](platform/manage-access.md) – nem műveletekre vonatkozó frissítések.
- [Azure monitor naplók használatának és költségeinek kezelése](platform/manage-cost-storage.md) – az adatmennyiség kiszámításának pontosítása a díjszabási modell szakaszban.
- [Azure Resource Manager-sablonokkal létrehozhat és konfigurálhat egy log Analytics-munkaterületet](platform/template-workspace-configuration.md) frissített sablont új díjszabási szintek használatával.


### <a name="platform-logs"></a>Platformnaplók
- [Az Azure-tevékenység naplójának összegyűjtése diagnosztikai beállításokkal – Azure monitor](platform/diagnostic-settings-legacy.md) – további információ a módosult tulajdonságokkal kapcsolatban.
- [Exportálja az Azure-tevékenység naplóját](platform/activity-log-export.md) – frissítve a felhasználói felület változásaihoz. 





## <a name="december-2019"></a>2019. december

### <a name="agents"></a>Ügynökök
- [Linux rendszerű számítógépek Összekapcsolásának Azure monitor](platform/agent-linux.md) – új cikk.

### <a name="alerts"></a>Riasztások
- [Metrikus riasztás létrehozása Resource Manager-sablonnal](platform/alerts-metric-create-templates.md) – példa az egyéni metrikára.
- Dinamikus [küszöbértékekkel rendelkező riasztások létrehozása Azure monitor](platform/alerts-dynamic-thresholds.md) – hozzáadott szakasz a dinamikus küszöbértékű diagramok értelmezéséhez.
- [A riasztások és az értesítések monitorozásának áttekintése az Azure-ban](platform/alerts-overview.md) – frissített Resource Graph-lekérdezés.
- A [metrikus riasztások támogatott erőforrásai Azure monitor](platform/alerts-metric-near-real-time.md) – a metrikák és a dimenziók frissítése támogatott.
- [Váltás az örökölt log Analytics riasztások API-ból az új Azure-riasztások API-ba](platform/alerts-log-api-switch.md) – Megjegyzés hozzáadva a módosított riasztás nevéhez.
- [Ismerje meg, hogyan működnek a metrikus riasztások Azure Monitorban.](platform/alerts-metric-overview.md) – Támogatott erőforrástípusok hozzáadása a nagy léptékű figyeléshez.

### <a name="application-insights"></a>Application Insights
- [Application Insights a Worker Service-alkalmazásokhoz (nem HTTP-alkalmazásokhoz)](app/worker-service.md) – az C# alapértelmezett naplózási szint hozzáadva a kódhoz. Frissített csomag-hivatkozási verzió.
- [ApplicationInsights. config hivatkozás – Azure](app/configuration-with-applicationinsights-config.md) -ban frissített mintakód.
- [Az Azure Application Insights automatizálása a PowerShell](app/powershell.md) használatával – frissítés Resource Manager-sablonra.
- [Azure Monitor Application Insights NuGet-csomagok](app/nuget.md) – frissített csomag verziószáma.
- [Hozzon létre egy új Azure Application Insights-erőforrást](app/create-new-resource.md) , amely a globálisan egyedi névvel lett hozzáadva.
- [Diagnosztizálás élő metrikastream – Azure Application Insights](app/live-stream.md) – frissített ASP.net Core SDK-verzióra vonatkozó követelmény.
- [Application Insights](app/eventcounters.md) – frissített kategória és tábla customMetrics.
- [Ismerje meg a Java-nyomkövetési naplókat az Azure Application Insights](app/java-trace-logs.md) – a Java-ügynök naplózási küszöbértékének konfigurációját.
- Az [Application Insights által használt IP-címek és a log Analytics](app/ip-addresses.md) a élő METRIKASTREAM frissített IP-címei.
- Az [Azure app Services teljesítményének figyelése](app/azure-web-apps.md) – a ASP.net Core 3,0 támogatásának támogatása. 
- [Python-alkalmazások figyelése Azure monitor (előzetes verzió)](app/opencensus-python.md) – a OpenCensus Python-séma az Azure-ba való hozzárendelésének pontosítása. Séma figyelése
- [Kibocsátási megjegyzések az Azure Application Insightshoz](app/release-notes.md) – megjegyzések a régebbi kiadásokhoz.
- [Telemetria-csatornák Az Azure-ban Application Insights](app/telemetry-channels.md) – az elvetett adatmennyiségnek az elveszett kapcsolat hosszabb időszaka során történő frissített időtartama.
- [Telemetria mintavételezés az Azure Application Insightsban](app/sampling.md) – javított kódrészlet az egyéni TelemetryInitializer.
- A [Java-webprojektek Application Insightsának hibáinak megoldása](app/java-troubleshoot.md) – eltávolított utasítás, amely nem támogatja a függőségi gyűjteményt a JDK 9-ben.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat
- [Azure monitor a tárolók gyakori kérdéseivel](insights/container-insights-faq.md) kapcsolatban – kérdésekkel bővült a képek és a nevek mezői.
- [Azure SQL Analytics megoldás a Azure monitor](insights/azure-sql.md) -frissítve adatbázisban vár felügyelt példányok támogatása.
- [Azure monitor konfigurálása a containers Agent adatgyűjtéshez](insights/container-insights-agent-config.md) – hozzáadott beállítás a enrich_container_logshoz.
- [Hibrid Kubernetes-fürtök konfigurálása Azure monitor for containers](insights/container-insights-hybrid-setup.md) – hibaelhárítás szakasz.
- [Active Directory replikáció állapotának figyelése Azure monitor](insights/ad-replication-status.md) -.NET-keretrendszerre vonatkozó előfeltétel-frissítéssel.
- [Network Performance monitor megoldás az Azure-ban](insights/network-performance-monitor.md) – támogatott régiók.
- [A Active Directory-környezet optimalizálása Azure monitor](insights/ad-assessment.md) -.NET-keretrendszer előfeltételeinek frissítésével.
- [A SQL Server-környezet optimalizálása Azure monitor](insights/sql-assessment.md) -.NET-keretrendszer előfeltételeinek frissítésével.
- [Optimalizálja System Center Operations Manager környezetét az Azure log Analytics](insights/scom-assessment.md) -.NET-keretrendszer előfeltételeinek frissítésével.
- [Támogatott kapcsolatok az Azure Log Analytics it-szolgáltatásmenedzsmenti csatolóával](platform/itsmc-connections.md) – új York-i előfeltétel-ügyfél-azonosító és az ügyfél titkos kulcsa.

### <a name="logs"></a>Naplók
- [Az Azure log Analytics munkaterület törlése és helyreállítása](platform/delete-workspace.md) – PowerShell-metódus hozzáadva.
- [Megtervezheti a Azure monitor naplók üzembe helyezését](platform/design-logs-deployment.md) – a munkaterületek terhelési arányának növelését.

### <a name="metrics"></a>Mérőszámok
- [Azure monitor a platform metrikái exportálhatók a diagnosztikai beállítások használatával](platform/metrics-supported-export-diagnostic-settings.md) – új cikk.

### <a name="platform-logs"></a>Platformnaplók
Több cikk is frissült a tartalom átszervezésének részeként a platform naplóiban a diagnosztikai beállítások használatával a műveletnapló konfigurálására szolgáló új funkció alapján.

- [Azure-beli erőforrás-naplók archiválása a Storage-fiókba](platform/resource-logs-collect-storage.md)
- [Azure Activity log esemény sémája](platform/activity-log-schema.md)
- [Azure Monitor szolgáltatási korlátok](service-limits.md)
- [Azure-beli tevékenység-naplók összegyűjtése és elemzése Log Analytics munkaterületen](platform/activity-log-collect.md)
- [Az Azure-tevékenység naplójának összegyűjtése diagnosztikai beállításokkal (előzetes verzió) – Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Azure-beli tevékenységek naplóinak begyűjtése egy Log Analytics munkaterületre az Azure-bérlők között](platform/activity-log-collect-tenants.md)
- [Azure-beli erőforrás-naplók gyűjtése Log Analytics munkaterületen](platform/resource-logs-collect-workspace.md)
- [Diagnosztikai beállítás létrehozása az Azure-ban Resource Manager-sablon használatával](platform/diagnostic-settings-template.md)
- [Diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban](platform/diagnostic-settings.md)
- [Az Azure-tevékenység naplójának exportálása](platform/activity-log-export.md)
- [Az Azure platform naplófájljainak áttekintése](platform/platform-logs-overview.md)
- [Azure monitoring-adatstreamek továbbítása az Event hub szolgáltatásba](platform/stream-monitoring-data-event-hubs.md)
- [Azure platform-naplók továbbítása egy Event hubhoz](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Rövid útmutatók és oktatóanyagok

- [Metrikai diagram létrehozása Azure monitor](learn/tutorial-metrics-explorer.md) – új cikkben.
- [Erőforrás-naplók összegyűjtése Azure-erőforrásokból és Azure monitor](learn/tutorial-resource-logs.md) -új cikk elemzése.
- [Azure-erőforrás figyelése Azure monitor](learn/quick-monitor-azure-resource.md) -új cikkel.
   
## <a name="next-steps"></a>Következő lépések

- Ha szeretne hozzájárulni Azure Monitor dokumentációhoz, tekintse meg a [docs közreműködői útmutatóját](https://docs.microsoft.com/contribute/).