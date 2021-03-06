---
title: Naplózási adatok gyűjtése
titleSuffix: Azure Cognitive Search
description: Összegyűjtheti és elemezheti a naplózási adatokat, ha engedélyezi a diagnosztikai beállításokat, majd a Kusto lekérdezési nyelv segítségével tárja fel az eredményeket.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462358"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Az Azure-Cognitive Search naplózási adatainak összegyűjtése és elemzése

A diagnosztikai vagy működési naplók betekintést nyújtanak az Azure Cognitive Search részletes műveleteibe, és hasznosak a szolgáltatás-és munkaterhelés-folyamatok figyeléséhez. Belsőleg a naplófájlok a háttérben, rövid idő alatt léteznek, elegendő a vizsgálathoz és az elemzéshez, ha támogatási jegyet ad. Ha azonban az operatív adatokra vonatkozó önirányítást szeretne, állítson be egy diagnosztikai beállítást annak megadásához, hogy a naplózási információk hol legyenek gyűjtve.

A naplók beállítása a diagnosztika és a működési Előzmények megőrzése esetén hasznos. A naplózás engedélyezése után lekérdezéseket futtathat, és jelentéseket készíthet a strukturált elemzéshez.

Az alábbi táblázat az adatok gyűjtésére és megőrzésére vonatkozó beállításokat sorolja fel.

| Erőforrás | Alkalmazási cél |
|----------|----------|
| [Küldés Log Analytics munkaterületre](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Az eseményeket és mérőszámokat egy Log Analytics munkaterületre küldi a rendszer, amely lekérdezhető a portálon, és részletes információkat adhat vissza. Bevezetés: Ismerkedés [a Azure monitor-naplókkal](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Archiválás blob Storage-val](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Az események és a metrikák archiválása egy blob-tárolóba történik, és JSON-fájlokban tárolódik. A naplók meglehetősen részletesek (óránként/percenként), ami hasznos lehet egy adott incidens kereséséhez, de a nyílt végű vizsgálathoz nem. Egy JSON-szerkesztővel megtekintheti a naplófájlok összesítéséhez és megjelenítéséhez használható nyers naplófájlt vagy Power BI.|
| [Stream az Event hub-ba](https://docs.microsoft.com/azure/event-hubs/) | Az események és mérőszámok továbbítása egy Azure Event Hubs szolgáltatásba történik. Válassza ezt alternatív adatgyűjtési szolgáltatásként a nagyon nagy naplók számára. |

A Azure Monitor naplók és a blob Storage is ingyenes szolgáltatásként érhető el, így ingyenesen kipróbálható az Azure-előfizetés élettartama. Application Insights ingyenesen regisztrálhatók és használhatók, amíg az alkalmazás adatainak mérete bizonyos korlátok között van (a részletekért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/monitor/) ).

## <a name="prerequisites"></a>Előfeltételek

Ha Log Analytics vagy Azure Storage-t használ, az erőforrásokat előre is létrehozhatja.

+ [Log Analytics-munkaterület létrehozása](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Az adatgyűjtés engedélyezése

A diagnosztikai beállítások határozzák meg a naplózott események és metrikák gyűjtésének módját.

1. A **Monitorozás** területen kattintson a **Diagnosztikai beállítások** elemre.

   ![Diagnosztikai beállítások](./media/search-monitor-usage/diagnostic-settings.png "Diagnosztikai beállítások")

1. Válassza a **+ diagnosztikai beállítás hozzáadása** lehetőséget

1. Jelölje be **log Analytics**, válassza ki a munkaterületet, és válassza a **OperationLogs** és a **AllMetrics**lehetőséget.

   ![Adatgyűjtés konfigurálása](./media/search-monitor-usage/configure-storage.png "Adatgyűjtés konfigurálása")

1. Mentse a beállítást.

1. A naplózás engedélyezése után a keresési szolgáltatással megkezdheti a naplók és mérőszámok generálását. A naplózott események és a mérőszámok elérhetővé válása előtt időbe telik.

Log Analytics esetén több percet is igénybe vehet, hogy az adatforrások elérhetők legyenek, miután az Kusto-lekérdezések futtatásával visszatérhetnek az adatforrásokhoz. További információ: [lekérdezési kérelmek figyelése](search-monitor-logs.md).

A blob Storage esetében a tárolók a blob Storage-ban való megjelenítése előtt egy órával tartanak. Nincs óránként, a tároló és a egy blob. A tárolók csak akkor jönnek létre, ha van egy tevékenység a naplóba vagy a mértékbe. Amikor az adat egy Storage-fiókba másolódik, az adat JSON-ként van formázva, és két tárolóba kerül:

+ insights-logs-operationlogs: a keresési forgalmi naplók
+ insights-mérőszámok – pt1m: metrikák

## <a name="query-log-information"></a>Lekérdezési napló adatai

A diagnosztikai naplókban két táblázat tartalmazza az Azure Cognitive Search: **AzureDiagnostics** és a **AzureMetrics**naplóit és mérőszámait.

1. A **figyelés**területen válassza a **naplók**lehetőséget.

1. Adja meg a **AzureMetrics** a lekérdezési ablakban. Ezt az egyszerű lekérdezést futtatva megismerheti az ebben a táblázatban gyűjtött adatokat. A metrikák és értékek megtekintéséhez görgessen végig a táblázaton. Figyelje meg, hogy a rekordok száma felül van, és ha a szolgáltatás egy ideig gyűjt metrikákat, érdemes lehet módosítani az időintervallumot, hogy kezelhető adatkészletet kapjon.

   ![AzureMetrics táblázat](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics táblázat")

1. Táblázatos eredményhalmaz visszaadásához adja meg a következő lekérdezést.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Ismételje meg az előző lépéseket, kezdve a **AzureDiagnostics** az összes oszlop tájékoztatási céllal való visszaküldéséhez, majd egy szelektívebb lekérdezés, amely további érdekes információkat gyűjt.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics táblázat](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics táblázat")

## <a name="log-schema"></a>Séma

Az Azure Cognitive Search naplózási adatszerkezetét tartalmazó adatstruktúrák megfelelnek az alábbi sémának. 

A blob Storage esetében minden egyes blob egyetlen, a log objektumok tömbjét tartalmazó **rekordot** tartalmaz. Minden blob az adott órában végrehajtott összes művelethez tartalmaz rekordokat.

A következő táblázat a diagnosztikai naplózással kapcsolatos leggyakoribb mezők részleges listáját tartalmazza.

| Name (Név) | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| timeGenerated |dátum/idő |"2018-12-07T00:00:43.6872559Z" |A művelet időbélyeg |
| resourceId |sztring |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK /<br/> A MICROSOFT. KERESÉS/SEARCHSERVICES/SEARCHSERVICE" |Az erőforrás-azonosító |
| operationName |sztring |"Query.Search" |A művelet neve |
| operationVersion |sztring |"2019-05-06" |A használt api-verzió |
| category |sztring |"OperationLogs" |állandó |
| resultType |sztring |"Sikeres" |A lehetséges értékek: sikeres vagy sikertelen |
| resultSignature |int |200 |HTTP-eredménykód |
| durationMS |int |50 |Ennyi ezredmásodpercig tart a művelet időtartama |
| properties |objektum |az alábbi táblázatban foglaltuk össze |A művelet-specifikus adatokat tartalmazó objektum |

### <a name="properties-schema"></a>Tulajdonságok séma

Az alábbi tulajdonságok az Azure Cognitive Search-ra vonatkoznak.

| Name (Név) | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| Description_s |sztring |"GET /indexes('content')/docs" |A művelet végpont |
| Documents_d |int |42 |Feldolgozott dokumentumok száma |
| IndexName_s |sztring |"test-index" |A művelethez társított az index neve |
| Query_s |sztring |"?search=AzureSearch&$count=true&api-version=2019-05-06" |A lekérdezési paraméterek |

## <a name="metrics-schema"></a>Metrikák séma

A rendszer a lekérdezési kérelmek esetében rögzíti a metrikákat, és egy percen belül méri őket. Minden mérőszám minimális, maximális és átlagos értékek száma percenként. További információ: [lekérdezési kérelmek figyelése](search-monitor-queries.md).

| Name (Név) | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| resourceId |sztring |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK /<br/>A MICROSOFT. KERESÉS/SEARCHSERVICES/SEARCHSERVICE" |az erőforrás-azonosító |
| metricName |sztring |"Késés" |a metrika neve |
| time |dátum/idő |"2018-12-07T00:00:43.6872559Z" |a művelet időbélyeg |
| átlag |int |64 |A nyers minták átlagos értéke a metrika időintervallumában, a mérőszámtól függően másodpercben vagy százalékban megadva. |
| minimum |int |37 |A nyers minták minimális értéke a metrikai időintervallumban, az egység másodpercben megadva. |
| maximum |int |78 |A nyers minták maximális értéke a metrikai időintervallumban, az egység másodpercben megadva.  |
| összeg |int |258 |A nyers minták teljes értéke a metrika időintervallumában, a mértékegységben megadva.  |
| count |int |4 |Egy csomópontról a naplóba kibocsátott metrikák száma egy percen belül.  |
| timegrain |sztring |"PT1M" |A metrika időbeli kiőrlése ISO 8601-ben. |

A lekérdezések végrehajtása általában ezredmásodpercben történik, így csak a másodpercben megadott mérték jelenik meg a mérőszámban, például a QPS.

A másodpercenkénti **keresési lekérdezések** esetében a minimális érték az adott percben regisztrált keresési lekérdezések másodpercenkénti legalacsonyabb értéke. Ugyanez vonatkozik a maximális értéknél. A teljes perc átlagos, esetében az összesítést. Előfordulhat például, hogy egy percen belül egy ilyen mintázattal rendelkezik: egy másodperces magas terhelés, amely a SearchQueriesPerSecond maximális értéke, majd az átlagos terhelés 58 másodperce, végül egy másodperc csak egy lekérdezéssel, amely a minimum.

A **szabályozott keresési lekérdezések aránya**, a minimum, a maximum, az átlag és az összes érték azonos értékű: a megadott keresési lekérdezések százalékos aránya, a keresési lekérdezések teljes száma egy percen belül.

## <a name="view-raw-log-files"></a>Nyers naplófájlok megtekintése

A blob Storage a naplófájlok archiválására szolgál. A naplófájl megtekintéséhez bármely JSON-szerkesztőt használhat. Ha még nem rendelkezik ilyennel, javasoljuk, hogy a [Visual Studio Code](https://code.visualstudio.com/download)-ot ajánljuk.

1. Azure Portal nyissa meg a Storage-fiókját. 

2. A bal oldali navigációs panelen kattintson a **Blobok**elemre. Látnia kell az elemzéseket – **naplók – operationlogs** és elemzések – **mérőszámok – pt1m**. Ezeket a tárolókat az Azure Cognitive Search hozza létre, amikor a naplózási adatként a blob Storage-ba exportálja őket.

3. Kattintson a mappa-hierarchiára, amíg el nem éri a. JSON fájlt.  A fájl letöltéséhez használja a helyi menüt.

Miután letöltötte a fájlt, nyissa meg egy JSON-szerkesztőben a tartalom megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, tekintse át a keresési szolgáltatás figyelésének alapjait, és ismerkedjen meg a teljes körű felügyeleti funkciókkal.

> [!div class="nextstepaction"]
> [Műveletek és tevékenységek figyelése az Azure Cognitive Search](search-monitor-usage.md)