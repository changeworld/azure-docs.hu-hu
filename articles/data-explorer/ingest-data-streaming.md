---
title: Adatfolyamok betöltésének használata az Azure-ba való adatbevitelhez Adatkezelő
description: Ismerje meg, hogyan tölthetők be (betöltési) az Azure Adatkezelő az adatfolyam-feldolgozás használatával.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: d7d2bcf487c37fbb523b648d5aa4c572add5dfa9
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297084"
---
# <a name="streaming-ingestion-preview"></a>Folyamatos átvitel (előzetes verzió)

Az adatfolyam-betöltést akkor érdemes használni, ha kis késésre van szüksége, és a betöltési idő kevesebb, mint 10 másodperc a különböző mennyiségi adatmennyiségek esetében. A rendszer a sok tábla működési feldolgozásának optimalizálására használatos egy vagy több adatbázisban, ahol az egyes táblákba irányuló adatstream viszonylag kicsi (néhány rekord másodpercenként), de a teljes adatfeldolgozási kötet magas (több ezer rekord másodpercenként). 

Az adatfolyamok betöltése helyett tömeges betöltést használhat, ha az adatmennyiség másodpercenként 1 MB-onként nő. A különböző betöltési módszerekkel kapcsolatos további információkért olvassa el az [adatfeldolgozás áttekintése című témakört](/azure/data-explorer/ingest-data-overview) .

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Jelentkezzen be a [webes felhasználói felületre](https://dataexplorer.azure.com/).
* [Azure adatkezelő-fürt és-adatbázis](create-cluster-database-portal.md) létrehozása

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Folyamatos átvitel engedélyezése a fürtön

> [!WARNING]
> A gőzölés betöltésének engedélyezése előtt tekintse át a [korlátozásokat](#limitations) .

1. A Azure Portal nyissa meg az Azure Adatkezelő-fürtöt. A **Beállítások**területen válassza a **konfigurációk**lehetőséget. 
1. A **konfigurációk** ablaktáblán válassza **a** be lehetőséget a **streaming**betöltésének engedélyezéséhez.
1. Kattintson a **Mentés** gombra.
 
    ![folyamatos átvitel](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. A [webes felhasználói felületen](https://dataexplorer.azure.com/)adja meg az adatfolyam-betöltési [szabályzatot](/azure/kusto/management/streamingingestionpolicy) olyan táblázat (ok) ra vagy adatbázis (ok) ra, amely a folyamatos átviteli adatot fogja fogadni. 

    > [!NOTE]
    > * Ha a házirend az adatbázis szintjén van meghatározva, a rendszer az adatbázisban lévő összes táblát engedélyezi a folyamatos átvitelhez.
    > * Az alkalmazott házirend csak az újonnan betöltött adatmennyiségre és az adatbázis más tábláira hivatkozhat.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Adatfolyamok betöltésének használata a fürtön tárolt adatbevitelhez

Két támogatott adatfolyam-betöltési típus létezik:


* Az adatforrásként használt [**Event hub**](/azure/data-explorer/ingest-data-event-hub)
* Az **Egyéni** betöltéshez olyan alkalmazást kell írnia, amely az egyik Azure adatkezelő ügyféloldali kódtárat használja. Lásd: streaming betöltési [minta](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) egy minta alkalmazáshoz.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Válassza ki a megfelelő adatfolyam-betöltési típust

|   |Eseményközpont  |Egyéni betöltés  |
|---------|---------|---------|
|Adatfeldolgozási kezdeményezés és a lekérdezéshez rendelkezésre álló adatmennyiség közötti késleltetés   |    hosszabb késleltetés     |   rövidebb késleltetés      |
|Fejlesztési terhelés    |   gyors és egyszerű telepítés, nincs fejlesztési terhelés    |   magas fejlesztési terhelés az alkalmazás számára a hibák kezeléséhez és az adatkonzisztencia biztosításához     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Adatfolyam-betöltés letiltása a fürtön

> [!WARNING]
> A streaming betöltésének letiltása néhány órát is igénybe vehet.

1. A streaming betöltési [szabályzatának](/azure/kusto/management/streamingingestionpolicy) eldobása az összes kapcsolódó táblából és adatbázisból. A streaming betöltési házirendjének eltávolítása elindítja a betöltési adatátvitelt a kezdeti tárterületről az oszlopos tárolóban lévő állandó tárolóba (egységekben vagy szegmensekben). Az adatáthelyezés a kezdeti tárolóban lévő adatmennyiségtől és a fürt által használt processzor és memória mennyiségétől függően néhány másodperctől akár néhány órára is eltarthat.
1. A Azure Portal nyissa meg az Azure Adatkezelő-fürtöt. A **Beállítások**területen válassza a **konfigurációk**lehetőséget. 
1. A **konfigurációk** ablaktáblán válassza ki a **ki** lehetőséget a **streaming**betöltésének letiltásához.
1. Kattintson a **Mentés** gombra.

    ![Folyamatos átvitel](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Korlátozások

* Az adatfolyam betöltése nem támogatja az [adatbázis-kurzorokat](/azure/kusto/management/databasecursor) vagy [az adatleképezést](/azure/kusto/management/mappings). Csak az [előre létrehozott](/azure/kusto/management/create-ingestion-mapping-command) adatleképezés támogatott. 
* A streaming betöltési teljesítménye és a kapacitása nagyobb a virtuális gépek és a fürtök méretével. Az egyidejű betöltések legfeljebb hat betöltésre korlátozódnak. Például 16 Magos SKU esetében, például a D14 és a L16 esetében a maximálisan támogatott terhelés 96 egyidejű betöltés. Két fő SKU esetében, például a D11 esetében a maximálisan támogatott terhelés 12 egyidejű betöltés.
* A betöltési kérések adatméretre vonatkozó korlátozása 4 MB.
* A séma frissítései, például a táblák létrehozása és módosítása, valamint a betöltési leképezések, akár öt percet is igénybe vehetnek a streaming betöltési szolgáltatás számára.
* Az adatfolyamok betöltésének engedélyezése egy fürtön, még akkor is, ha az adatok nem a folyamatos átvitelen keresztül kerülnek betöltésre, a a fürt helyi SSD-lemezének részét képezi a betöltési adatok átviteléhez, és csökkenti a gyors gyorsítótár számára elérhető tárterületet
* Az adatfolyam-betöltési adatmennyiség [nem állítható](/azure/kusto/management/extents-overview#extent-tagging) be.

## <a name="next-steps"></a>További lépések

* [Az Azure Adatkezelő lekérdezése](web-query-data.md)
