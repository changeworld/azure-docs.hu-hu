---
title: 'Gyors útmutató: Azure Time Series Insights Explorer – Azure Time Series Insights | Microsoft Docs'
description: Ismerje meg, hogyan kezdheti el a Azure Time Series Insights Explorert. A környezet nagy mennyiségű IoT-adatait és a bemutató főbb funkcióit jelenítheti meg.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 01/06/2020
ms.openlocfilehash: a905054b1b2a04fa2b7865d2c1065ccee37cffc0
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860430"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Rövid útmutató: Ismerkedés az Azure Time Series Insightsszal

Ez a Azure Time Series Insights Explorer rövid útmutató segítséget nyújt a Time Series Insights ingyenes bemutató környezetben való megkezdéséhez. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a webböngészőt nagy mennyiségű IoT-információ és az általánosan elérhető főbb funkciók megjelenítésére.

A Azure Time Series Insights egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amely leegyszerűsíti a IoT-események milliárdjainak egyidejű feltárását és elemzését. Globális áttekintést nyújt az adatairól, így gyorsan ellenőrizheti IoT-megoldását, és elkerülheti az üzleti szempontból kritikus fontosságú eszközök költséges leállását. Azure Time Series Insights segít felderíteni a rejtett trendeket, a helyszíni rendellenességeket és a kiváltó okok elemzését közel valós időben.

A további rugalmasság érdekében a hatékony [REST API](./time-series-insights-update-tsq.md) -kon és az [ügyfél-SDK](https://github.com/microsoft/tsiclient)-n keresztül Azure Time Series Insights adhat hozzá egy meglévő alkalmazáshoz. Az API-k segítségével az idősorozat-adatai tárolhatók, lefoglalhatók és felhasználhatók az Ön által választott ügyfélalkalmazások számára. Az ügyfél-SDK használatával felhasználói felületi összetevőket is hozzáadhat a meglévő alkalmazáshoz.

Ez a Time Series Insights Explorer rövid útmutató az általánosan elérhető funkciókkal kapcsolatos interaktív bemutatót kínál.

> [!IMPORTANT]
> Hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , ha még nem hozott létre egyet.

## <a name="prepare-the-demo-environment"></a>A bemutató környezet előkészítése

1. A böngészőben nyissa meg az [általános elérhetőségi bemutatót](https://insights.timeseries.azure.com/demo).

1. Ha a rendszer kéri, jelentkezzen be a Time Series Insights Explorerbe az Azure-fiókja hitelesítő adataival.

1. Megjelenik a Time Series Insights gyors bemutató oldal. A gyors bemutató elindításához kattintson a **tovább** gombra.

   [![rövid útmutató – válassza a tovább lehetőséget](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Ismerkedés a bemutató környezettel

1. Ekkor megjelenik az **időkijelölés panel** . Ezen a panelen választhatja ki a megjeleníteni kívánt időkeretet.

   [![idő kiválasztása panel](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Válasszon ki egy időkeretet, és húzza a régióba. Ezután válassza a **Keresés**lehetőséget.

   [![válasszon ki egy időkeretet](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   A Time Series Insights megjelenít egy diagramot a megadott időkerethez. A vonalas diagramon különböző műveleteket végezhet el. Például szűrheti, rögzítheti, rendezheti és halmozhatja fel.

   Az **időkijelölési panelre**való visszatéréshez kattintson a lefelé mutató nyílra az ábrán látható módon:

   [![diagram](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Új keresési kifejezés hozzáadásához a **feltételek panelen** válassza a **Hozzáadás** lehetőséget.

   [![keresési feltételek hozzáadása panel](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. A diagramon válasszon ki egy régiót, kattintson rá a jobb gombbal, majd válassza az **Explore Events** (Események áttekintése) elemet.

   [![események megismerése](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   A nyers adatok egy rácsa jelenik meg a feltárt régióból.

   [az események megismerése – a rács adatnézete ![](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Az adatszűrő kiválasztása és szűrése

1. Szerkessze a használati feltételeket a diagram értékeinek módosításához. Adjon hozzá egy másik kifejezést a különböző típusú értékek kereszthivatkozásához.

   [![kifejezés hozzáadása](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Hagyja üresen a **szűrő adatsorozat** mezőt az összes kijelölt keresési kifejezés megjelenítéséhez, vagy adjon meg egy szűrési **kifejezést az improvizált** adatsorozat-szűréshez.

   [![szűrő sorozat](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   A rövid útmutatóhoz adja meg a **Station5** értéket az állomásra jellemző hőmérséklet és nyomás összevetéséhez.

A rövid útmutató befejezése után a mintaadatokkal kísérletezve különböző vizualizációkat hozhat létre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy elvégezte az oktatóanyagot, törölje a létrehozott erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden erőforrás**lehetőséget, keresse meg a Azure Time Series Insights erőforráscsoportot.
1. Törölje a teljes erőforráscsoportot (és az abban található összes erőforrást) úgy, hogy kiválasztja az egyes erőforrások **törlését** vagy eltávolítását.

## <a name="next-steps"></a>Következő lépések

Készen áll a saját Time Series Insights környezet létrehozására:
> [!div class="nextstepaction"]
> [Time Series Insights-környezet tervezése](time-series-insights-environment-planning.md)
