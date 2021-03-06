---
title: Metrikák a Azure Monitorban | Microsoft Docs
description: Ismerteti a Azure Monitor metrikáit, amelyek a közel valós idejű forgatókönyvek támogatására alkalmas, könnyű figyelési adatok.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 80bbf83da17d833c4f8bb1abac9610d70e9a23cb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274826"
---
# <a name="metrics-in-azure-monitor"></a>Metrikák az Azure Monitorban

> [!NOTE]
> A Azure Monitor adatplatform két alapvető adattípuson alapul: mérőszámok és naplók. Ez a cikk a metrikákat ismerteti. A naplók részletes leírását Azure Monitor és az [adatplatformot](data-platform.md) a kettő összevetéséhez [Azure monitor naplófájlokban](data-platform-logs.md) találhat.

A Azure Monitor metrikái nagyon egyszerűek, és a közel valós idejű forgatókönyvek támogatása révén különösen hasznosak lehetnek a riasztások és a problémák gyors észlelése érdekében. Ez a cikk bemutatja, hogyan épülnek fel a metrikák, mit tehet velük, és azonosíthatja azokat a különböző adatforrásokat, amelyek mérőszámokban tárolnak adatokat.

## <a name="what-are-metrics"></a>Mik azok a metrikák?
Metrikák numerikus értékek írja le a rendszer bizonyos elemeit egy adott időpontban. A metrikák gyűjtése rendszeres időközönként történik, és a riasztásokhoz hasznos, mivel azok gyakran mintavételezésre használhatók, és a riasztások gyorsan és viszonylag egyszerű logikával is elindíthatók.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Mit tehet a Azure Monitor metrikákkal?
A következő táblázat felsorolja azokat a különböző módszereket, amelyekkel a metrikai adatokat Azure Monitor használhatja.

|  |  |
|:---|:---|
| Elemzés | A [metrikák](metrics-charts.md) segítségével elemezheti a diagramon összegyűjtött mérőszámokat, és összehasonlíthatja a különböző erőforrások metrikáit. |
| Vizualizáció | Diagram rögzítése a metrikák Intézőből egy Azure- [irányítópultra](../learn/tutorial-app-dashboards.md).<br>Hozzon létre egy [munkafüzetet](../app/usage-workbooks.md) , amely kombinálható több adatkészlettel egy interaktív jelentésben. Egy lekérdezés eredményeinek exportálása a [Grafana](grafana-plugin.md) , hogy kihasználja az irányítópultot, és kombinálja más adatforrásokkal. |
| Riasztás | Olyan [metrikai riasztási szabályt](alerts-metric.md) konfigurálhat, amely értesítést küld, vagy [automatizált műveletet hajt végre](action-groups.md) , ha a metrika értéke átlépi a küszöbértéket. |
| Automatizálás |  Az [autoscale](autoscale-overview.md) használatával növelheti vagy csökkentheti az erőforrásokat egy küszöbértéket áthaladó metrika értéke alapján. |
| Exportálás | [Átirányítja a metrikákat a naplókba](resource-logs-collect-storage.md) , hogy Azure monitor mérőszámokban lévő adatokat elemezze Azure monitor-naplókba, és hogy a metrikus értékeket 93 napnál hosszabb ideig tárolja.<br>Stream-metrikák egy [Event hub](stream-monitoring-data-event-hubs.md) -hoz, hogy átirányítsa őket a külső rendszerekre. |
| Beolvasni | Metrikai értékek elérése parancssorból PowerShell- [parancsmagok](https://docs.microsoft.com/powershell/module/az.applicationinsights) használatával<br>Az egyéni alkalmazás metrikai értékeinek elérése [REST API](rest-api-walkthrough.md)használatával.<br>Metrikai értékek elérése parancssorból a [CLI](/cli/azure/monitor/metrics)használatával. |
| Archívum | Az erőforrás teljesítmény-vagy állapotadatok [archiválása](..//learn/tutorial-archive-data.md) megfelelőségi, naplózási vagy offline jelentéskészítési célokra. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Hogyan épülnek fel Azure Monitor mérőszámok adatai?
Azure Monitor metrikák által gyűjtött adatokat egy idősorozat-adatbázis tárolja, amely az időbélyegzővel ellátott adatok elemzésére van optimalizálva. A metrikai értékek minden készlete egy idősorozat a következő tulajdonságokkal:

* Az érték gyűjtésének időpontja
* Az az erőforrás, amelyhez az érték társítva van
* Egy olyan névtér, amely a metrika kategóriához hasonlóan működik
* Metrika neve
* Maga az érték
* Egyes mérőszámok több dimenzióval rendelkezhetnek, a [többdimenziós metrikák](#multi-dimensional-metrics)részben leírtak szerint. Egyéni metrikák dimenziók legfeljebb 10 lehet.

## <a name="multi-dimensional-metrics"></a>Többdimenziós metrikák
A metrikus adatok egyik kihívása, hogy gyakran korlátozott információkkal szolgálnak az összegyűjtött értékek kontextusának biztosításához. Azure Monitor ez a kihívás többdimenziós metrikákkal foglalkozik. Dimenzió egy metrikát a név-érték párok megadásával írhatja le a metrikaérték további adatot áll. Egy metrikus _rendelkezésre álló_ lemezterület például rendelkezhet a " _C:_ , _D:_ " értékkel rendelkező _meghajtóval_ , amely lehetővé tenné a rendelkezésre álló lemezterület megtekintését az összes meghajtón vagy az egyes meghajtókon.

Az alábbi példa egy _hálózati átviteli sebességű_feltételezett metrikai adatkészleteket mutat be. Az első adatkészletet nincs dimenzió rendelkezik. A második adatkészlet a két dimenziót, az _IP-címet_ és az _irányt_tartalmazó értékeket jeleníti meg:

### <a name="network-throughput"></a>Hálózati átviteli sebessége

| Időbélyeg     | Metrikaérték |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 kb/s |
| 8/9/2017 8:15 | 1,141.4 KB/s |
| 8/9/2017 8:16 | 1,110.2 KB/s |

Ez a metrika dimenziók nélküli is csak egy alapszintű kérdésre választ, például "mennyi volt a saját hálózati átviteli sebességet egy adott időpontban?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Hálózati átviteli sebesség és a két dimenzió ("IP" és "Iránya")

| Időbélyeg     | Dimenzió "IP-címnél   | "Iránya" dimenzió | Metrikaérték|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Iránya = "Küldése"    | 646.5 kb/s |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Iránya = "Jelenik meg" | 420.1 KB/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Iránya = "Küldése"    | 150.0 KB/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Iránya = "Jelenik meg" | 115,2 KB/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Iránya = "Küldése"    | 515.2 KB/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Iránya = "Jelenik meg" | 371.1 KB/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Iránya = "Küldése"    | 155.0 KB/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Iránya = "Jelenik meg" | 100.1 KB/s |

Ez a metrika segítségével választ kaphat a kérdéseket, például a "Mi volt a hálózat átviteli sebessége az egyes IP-cím?" és "mennyi adatot küldött és fogadott?" Többdimenziós metrikák biztosítunk további elemzési és diagnosztikai érték nem többdimenziós metrikák képest.

## <a name="interacting-with-azure-monitor-metrics"></a>Azure Monitor metrikákkal való interakció
A [Metrikaböngésző](metrics-charts.md) használatával interaktív módon elemezheti a metrikus adatbázisban lévő adatokat, és az idő múlásával több mérőszám értékét ábrázolhatja. A diagramokat rögzítheti egy irányítópulton, és megtekintheti azokat más vizualizációkkal. A metrikákat az [Azure monitoring REST API](rest-api-walkthrough.md)használatával is lekérheti.

![Metrikaböngésző](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure Monitor mérőszámok forrásai
Nincsenek Azure-figyelése által gyűjtött metrikák három alapvető forrásai. A metrikák a Azure Monitor metrikus adatbázisban való gyűjtése után a forrástól függetlenül is kiértékelhető.

A **platform metrikáit** az Azure-erőforrások hozzák létre, és megtekinthetik az állapotukat és a teljesítményt. Minden típusú erőforrás [külön mérőszámok készletét hozza létre, és](metrics-supported.md) nincs szükség konfigurációra. A platform metrikáit egyperces gyakorisággal gyűjtjük az Azure-erőforrásokból, kivéve, ha a mérőszám definíciójában másképp van megadva. 

A vendég operációs rendszer **mérőszámait** egy virtuális gép vendég operációs rendszere gyűjti. Engedélyezze a vendég operációs rendszer metrikáit a Windows-alapú virtuális gépekhez [Windows diagnosztikai bővítménnyel (wad)](../platform/diagnostics-extension-overview.md) és a [InfluxData](https://www.influxdata.com/time-series-platform/telegraf/)-val rendelkező Linux rendszerű virtuális gépekhez.

Az **alkalmazás-metrikákat** Application Insights hozza létre a figyelt alkalmazásokhoz, és segít a teljesítménnyel kapcsolatos problémák észlelésében és a trendek nyomon követésében az alkalmazás használatának módjában. Ez olyan értékeket foglal magában, mint a _kiszolgáló válaszideje_ és a _böngésző kivételei_.

Az **Egyéni metrikák** olyan mérőszámok, amelyeket az automatikusan elérhető szabványos metrikák mellett határozhat meg. Egyéni metrikákat adhat meg az [alkalmazásban](../app/api-custom-events-metrics.md) , amelyet Application Insights figyel, vagy egyéni metrikákat hozhat létre egy Azure-szolgáltatáshoz az [Egyéni metrikák API](metrics-store-custom-rest-api.md)használatával.

## <a name="retention-of-metrics"></a>Mérőszámok megőrzése
Az Azure-ban a legtöbb erőforrás esetében a metrikák 93 napig tárolódnak. Bizonyos kivételek:

**Vendég operációs rendszer metrikái**
-   **Klasszikus vendég operációs rendszer metrikái**. Ezek a [Windows diagnosztikai bővítmény (wad)](../platform/diagnostics-extension-overview.md) vagy a [Linux diagnosztikai bővítmény (Lad)](../../virtual-machines/extensions/diagnostics-linux.md) által gyűjtött teljesítményszámlálók, és egy Azure Storage-fiókhoz irányíthatók. A metrikák megőrzése 14 nap.
-   **Azure monitor metrikáknak eljuttatott vendég operációs rendszer metrikái**. Ezek a [Windows diagnosztikai bővítmény (wad)](diagnostics-extension-overview.md) által gyűjtött teljesítményszámlálók, amelyeket a rendszer elküld a Azure monitor adatfogadónak, vagy a [InfluxData](https://www.influxdata.com/time-series-platform/telegraf/) - [alapú](diagnostics-extension-overview.md#data-destinations), a Linux rendszerű gépeken található. A metrikák megőrzése 93 nap.
-   **Log Analytics ügynök által gyűjtött vendég operációsrendszer-metrikák**. Ezek a Log Analytics ügynök által gyűjtött és Log Analytics munkaterületre továbbított teljesítményszámlálók. A metrikák megőrzése 31 nap, és legfeljebb 2 évig bővíthető.

**Application Insights log-alapú metrikák**. 
- A [log-alapú metrikák](../app/pre-aggregated-metrics-log-metrics.md) a jelenet mögött vannak lefordítva. Az adatmegőrzés megfelel az alapul szolgáló naplók eseményeinek megőrzésének. Application Insights erőforrások esetében a rendszer a naplókat 90 napig tárolja.


> [!NOTE]
> A hosszú távú trendek [érdekében platform-metrikákat küldhet Azure monitor erőforrásoknak log Analytics munkaterületre](resource-logs-collect-storage.md) .





## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor adatplatformról](data-platform.md).
- Tudnivalók a [Azure monitor naplózási adatainak](data-platform-logs.md)használatáról.
- Ismerje meg az Azure különböző erőforrásaihoz [elérhető figyelési információkat](data-sources.md) .
