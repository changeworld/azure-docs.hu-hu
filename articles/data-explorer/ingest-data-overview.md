---
title: Azure Adatkezelő adatfeldolgozás
description: Ismerje meg az Azure-Adatkezelő betöltési (Load) adatainak különböző módszereit
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 4846a19c403cce16bed704ed4e7c70499f3b5d13
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246395"
---
# <a name="azure-data-explorer-data-ingestion"></a>Azure Adatkezelő adatfeldolgozás

Az adatgyűjtési folyamat az adatrekordok egy vagy több forrásból való betöltésére szolgál az Azure Adatkezelő-beli tábla létrehozásához vagy frissítéséhez. A betöltést követően az adatmennyiség elérhetővé válik a lekérdezéshez. Az alábbi ábra az Azure-Adatkezelő működésének teljes folyamatát mutatja be, beleértve az adatfeldolgozást is.

![Az adatfolyam](media/ingest-data-overview/data-flow.png)

Az adatfeldolgozásért felelős Azure Adatkezelő adatkezelési szolgáltatás a következő funkciókat biztosítja:

1. **Adatok lekérése**: adatok lekérése külső forrásokból (Event Hubs) vagy az Azure-üzenetsor betöltési kéréseinek olvasása.

1. **Kötegelt feldolgozás**: a kötegelt adatok ugyanabba az adatbázisba és táblázatba áramlanak a betöltési teljesítmény optimalizálása érdekében.

1. **Ellenőrzés**: az előzetes ellenőrzés és a formátum átalakítása, ha szükséges.

1. **Adatkezelés**: a séma egyeztetése, az adatok rendszerezése, indexelése, kódolása és tömörítése.

1. Adatfeldolgozási **folyamat adatmegőrzési pontja**: a betöltési terhelés kezelése a motoron, és az átmeneti hibák miatti újrapróbálkozások kezelésére.

1. **Az adatbevitel véglegesítve**: a lekérdezéshez elérhetővé teszi az adatmennyiséget.

## <a name="ingestion-methods"></a>Betöltési módszerek

Az Azure Adatkezelő több betöltési módszert is támogat, amelyek mindegyike saját céljával, előnyökkel és hátrányokkal rendelkezik. Az Azure Adatkezelő a közös szolgáltatásokhoz, az SDK-k használatával történő programozott betöltéshez, valamint a motorhoz való közvetlen hozzáféréshez nyújt lehetőséget a felderítési célokra.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Betöltés folyamatok, összekötők és beépülő modulok használatával

Az Azure Adatkezelő jelenleg a következőket támogatja:

* Event Grid folyamat, amelyet a Azure Portal felügyeleti varázslójával kezelhet. További információ: Azure-Blobok betöltése az [azure Adatkezelőba](ingest-data-event-grid.md).

* Az Event hub folyamata, amely a Azure Portal felügyeleti varázslójával kezelhető. További információ: az Event hub adatainak beolvasása az [Azure Adatkezelőba](ingest-data-event-hub.md).

* Logstash beépülő modul: adatok beolvasása [a Logstash-ből az Azure Adatkezelőba](ingest-data-logstash.md).

* A Kafka-összekötővel kapcsolatban lásd: [adatok beolvasása a Kafka-ből az Azure Adatkezelőba](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Betöltés az Integration Services használatával

* Azure Data Factory (ADF), egy teljes körűen felügyelt adatintegrációs szolgáltatás az Azure-ban analitikus számítási feladatokhoz, a [támogatott adattárakkal és-formátumokkal](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)történő adatmásoláshoz az Azure Adatkezelőba és az-ból. További információ: [adatok másolása Azure Data Factoryról az Azure Adatkezelőba](/azure/data-explorer/data-factory-load-data).

### <a name="programmatic-ingestion"></a>Programozott betöltés

Az Azure Adatkezelő a lekérdezésekhez és az adatfeldolgozáshoz használható SDK-kat biztosít. A programozott betöltés a betöltési költségek csökkentése érdekében van optimalizálva, a tárolási tranzakciók minimalizálása és a betöltési folyamat után.

**Elérhető SDK-k és nyílt forráskódú projektek**:

A Kusto olyan ügyféloldali SDK-t kínál, amely az alábbiakkal végezheti el az adatgyűjtést és-lekérdezéseket:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programozott**betöltési technikák:

* Adatok betöltése az Azure Adatkezelő adatkezelési szolgáltatással (nagy átviteli sebesség és megbízható betöltés):

    [**Batch**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) -betöltés (az SDK által biztosított): az ügyfél feltölti az Azure Blob Storage-ba (az Azure adatkezelő adatkezelési szolgáltatás által kijelölt), és értesítést küld egy Azure-üzenetsor számára. A kötegelt betöltés a nagy mennyiségű, megbízható és olcsó adatfeldolgozáshoz ajánlott módszer.

* Az adatfeldolgozás közvetlenül az Azure Adatkezelő Engine-be (a feltáráshoz és a prototípusokhoz legmegfelelőbb):

  * **Beágyazott**betöltés: a sávon kívüli adatot tartalmazó vezérlési parancs (. betöltés inline) ad hoc tesztelési célokra szolgál.

  * Betöltés **a lekérdezésből**: vezérlési parancs (. set,. set-vagy-append,. set-vagy-replace), amely lekérdezési eredményekre mutat, a jelentések vagy kisebb ideiglenes táblák generálására szolgál.

  * Betöltés **a Storage**szolgáltatásból: a (. betöltés a (z) rendszerbe való betöltése) a külsőleg tárolt adatok (például az Azure Blob Storage) lehetővé teszik az adatok hatékony tömeges betöltését.

**Különböző metódusok késése**:

| Módszer | Késés |
| --- | --- |
| **Beágyazott betöltés** | Azonnali |
| **Betöltés a lekérdezésből** | Lekérdezési idő + feldolgozási idő |
| **Betöltés a tárolóból** | Letöltési idő + feldolgozási idő |
| **Várólistán lévő betöltés** | Kötegelt feldolgozás ideje + feldolgozási idő |
| |

A feldolgozási idő az adatok méretétől függ, és kevesebb, mint néhány másodperc. A kötegelt feldolgozás ideje az alapértelmezett érték 5 perc.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>A legmegfelelőbb betöltési módszer kiválasztása

Mielőtt elkezdi az adatgyűjtést, kérdezze meg a következő kérdéseket.

* Hol találhatók az adataim? 
* Mi az adatformátum, és hogyan módosítható? 
* Mik a lekérdezni kívánt mezők? 
* Mi a várt adatmennyiség és a sebesség? 
* Hány eseménytípus várható (a táblák számának megfelelően)? 
* Milyen gyakran várható az esemény sémájának módosítása? 
* Hány csomópont hozza elő az adatmennyiséget? 
* Mi a forrás operációs rendszer? 
* Mik a késési követelmények? 
* Használható az egyik meglévő felügyelt betöltési folyamat is? 

Az olyan meglévő infrastruktúrával rendelkező szervezetek esetében, amelyek egy olyan üzenetkezelő szolgáltatáson alapulnak, mint az Event hub és a IoT Hub, az összekötők valószínűleg a legmegfelelőbb megoldást használják. A várólistára helyezett betöltés a nagy adatmennyiségek esetében megfelelő.

## <a name="supported-data-formats"></a>Támogatott adatformátumok

A lekérdezésből bekövetkező összes betöltési módszernél formázza az adatot úgy, hogy az Azure Adatkezelő képes legyen elemezni. 
* A támogatott adatformátumok a következők: TXT, CSV, TSV, TSVE, PSV, SCSV, rendszerállapot-kimutatás, JSON (line-elválasztva, többsoros), Avro, ork és parketta. 
* Támogatja a ZIP-és a GZIP-tömörítést.

> [!NOTE]
> Az adatgyűjtés során az adattípusok a céltábla oszlopai alapján lesznek kikövetkeztetve. Ha egy rekord hiányos, vagy egy mező nem értelmezhető a szükséges adattípussal, a rendszer null értékekkel tölti fel a megfelelő táblázat oszlopait.

## <a name="ingestion-recommendations-and-limitations"></a>Betöltési javaslatok és korlátozások

* A betöltött adatok tényleges adatmegőrzési szabályzata az adatbázis adatmegőrzési házirendjéből származik. Részletekért lásd: [adatmegőrzési szabályzat](/azure/kusto/concepts/retentionpolicy) . Az adatfeldolgozáshoz **tábla** -betöltési vagy **adatbázis** -betöltési engedélyek szükségesek.
* A betöltés legfeljebb 5 GB méretű fájlméretet támogat. A javaslat a fájlok 100 MB és 1 GB közötti betöltésére szolgál.

## <a name="schema-mapping"></a>Séma-hozzárendelés

A séma-hozzárendelés segíti a forrásadatok mezőinek kötését a céltábla oszlopaihoz.

* A [CSV-megfeleltetés](/azure/kusto/management/mappings?branch=master#csv-mapping) (nem kötelező) az összes sorszám-alapú formátummal működik. A Betöltés parancs paraméterrel vagy [előre létrehozott](/azure/kusto/management/create-ingestion-mapping-command) paranccsal végezhető el a betöltési parancs paraméterének használatával.
* A [JSON-megfeleltetés](/azure/kusto/management/mappings?branch=master#json-mapping) (kötelező) és az Avro- [leképezés](/azure/kusto/management/mappings?branch=master#avro-mapping) (kötelező) a betöltési parancs paraméterrel hajtható végre. Emellett előre létrehozhatók a [táblában](/azure/kusto/management/create-ingestion-mapping-command) , és a betöltési parancs paraméterében is szerepelhetnek.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatok beolvasása az Event hub-ből az Azure-ba Adatkezelő](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Adatbevitel Event Grid-előfizetéssel az Azure-ba Adatkezelő](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [A Kafka adatainak betöltése az Azure-ba Adatkezelő](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Adatbevitel az Azure Adatkezelő Python Library használatával](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Adatbevitel az Azure Adatkezelő Node Library használatával](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Adatbevitel az Azure Adatkezelő .NET Standard SDK-val (előzetes verzió)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Adatok beolvasása a Logstash-ből az Azure-ba Adatkezelő](ingest-data-logstash.md)
