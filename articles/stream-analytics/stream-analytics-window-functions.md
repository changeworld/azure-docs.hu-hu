---
title: Bevezetés az Azure Stream Analytics Windowing functions használatába
description: Ez a cikk az Azure Stream Analytics-feladatokban használt négy ablakkezelő függvényt (kihúzást, átugrást, lecsúszást, munkamenetet) ismerteti.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: a0547243ddf114d5c9f7034f182a5e76d8c3e016
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369422"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Bevezetés az Stream Analytics Windowing functions használatába

A folyamatos átvitelt lehetővé tévő forgatókönyvek esetében az időbeli Windowsban tárolt adatokon végrehajtott műveletek egy gyakori minta. Stream Analytics natív módon támogatja az ablakkezelő függvényeket, így a fejlesztők a lehető legkevesebb erőfeszítéssel hozhatnak létre összetett adatfolyam-feldolgozási feladatokat.

Négy különböző időbeli időszak közül választhat: a [**kiesés**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), a [**hopping**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), a [**csúszó**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)és a [**munkamenet**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) -ablakok.  A Stream Analytics-feladatok lekérdezési szintaxisának [**Group By**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) záradékában található Window functions kifejezés használható. Az eseményeket a [ **Windows ()** függvénnyel](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics)több Windows rendszeren is összesítheti.

Az összes [ablakos](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) művelet kimenete az ablak **végén** jelenik meg. Az ablak kimenete egyetlen esemény lesz a használt összesítő függvény alapján. A kimeneti esemény az ablak végének időbélyegzője lesz, és az összes ablak függvény rögzített hosszúságú. 

![Stream Analytics Window functions – fogalmak](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Ablak kiesése
A kieséses ablak függvények az adatfolyamok különböző időszegmensekre való szegmentálására szolgálnak, és a rajtuk végrehajtott függvényeket, például az alábbi példát használják. Az átfedésmentes ablak fő sajátossága, hogy ismétlődik, nincs átfedésben, és egy esemény csak egy átfedésmentes ablakhoz tartozhat.

![Stream Analytics ablak kiesése](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Hopping-ablak
Az ugróablak típusú függvények egy adott időtartamot ugranak előre az időben. Tulajdonképpen olyan átfedésmentes ablakok, amelyek lehetnek átfedésben, az események így több ugróablak eredményhalmazához is tartozhatnak. Az ablak méretével megegyező ugrások méretének megadásához a beugró ablaknak meg kell egyeznie. 

![Stream Analytics hopping-ablak](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Ablak csúsztatása
A csúszó ablak függvényei, a kieséssel vagy a beugró ablakokkal ellentétben, **csak** egy esemény bekövetkezésekor hoznak létre kimenetet. Minden ablakhoz legalább egy esemény tartozik, amely folyamatosan halad előre egy € (epszilon) egységgel. Az ugróablakhoz hasonlóan egy esemény több csúszóablakhoz is tartozhat.

![Stream Analytics csúszó ablak](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Munkamenet-ablak
A munkamenet-ablak a hasonló időpontokban érkező eseményeket csoportosítja, és kiszűri azokat az időszakokat, ahol nincs adat. Három fő paraméterrel rendelkezik: időtúllépés, maximális időtartam és particionálókulcs (nem kötelező).

![Stream Analytics munkamenet ablak](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

A munkamenet-ablak az első esemény bekövetkezésekor kezdődik. Ha egy másik esemény kerül be a megadott időkorláton belül az utolsó betöltött eseménytől, akkor az ablak kiterjeszthető az új eseményre. Ellenkező esetben, ha az időkorláton belül nem következnek be események, az ablak bezáródik az időtúllépésnél.

Ha az események a megadott időkorláton belül maradnak, a munkamenet ablaka továbbra is meghosszabbítja a korlátot, amíg el nem éri a maximális időtartamot. A maximális időtartam-ellenőrzési intervallumok a megadott maximális időtartammal megegyező méretre vannak beállítva. Ha például a maximális időtartam 10, akkor a ellenőrzi, hogy az ablak túllépi-e a maximális időtartamot t = 0, 10, 20, 30 stb.

A partíciós kulcs megadásakor az eseményeket a kulcs és a munkamenet ablak együttesen csoportosítja az egyes csoportokra egymástól függetlenül. Ez a particionálás olyan esetekben hasznos, amikor különböző felhasználókhoz vagy eszközökhöz eltérő munkamenet-ablakok szükségesek.


## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

