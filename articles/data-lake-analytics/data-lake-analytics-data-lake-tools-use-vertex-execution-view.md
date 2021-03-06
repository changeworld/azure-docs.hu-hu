---
title: A Visual studióhoz készült Data Lake-eszközök csúcspont-végrehajtási nézete
description: Ez a cikk azt ismerteti, hogyan használható a csúcspont-végrehajtási nézet a vizsga Data Lake Analytics feladatokhoz.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: f5adbb75e6852551976aa040a1a1c723d2e3f59b
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309719"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>A Visual studióhoz készült Data Lake-eszközök csúcspont-végrehajtási nézetének használata
Megtudhatja, hogyan használhatja a csúcspont-végrehajtási nézetet Data Lake Analytics feladatok vizsgára.


## <a name="open-the-vertex-execution-view"></a>A csúcspont-végrehajtási nézet megnyitása
Nyisson meg egy U-SQL-feladatot a Visual studióhoz készült Data Lake-eszközökben. Kattintson a bal alsó sarokban található **csúcspont-végrehajtási nézet** elemre. Előfordulhat, hogy a rendszer először betölti a profilokat, és a hálózati kapcsolattól függően hosszabb időt is igénybe vehet.

![Data Lake Analytics eszközök csúcspontjának végrehajtási nézete](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>A vertex végrehajtási nézetének ismertetése
A csúcspont-végrehajtás nézet három részből áll:

![Data Lake Analytics eszközök csúcspontjának végrehajtási nézete](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

A bal oldali **csúcspont-választó** lehetővé teszi a csúcspontok kiválasztását a szolgáltatások (például az első 10 adatolvasás vagy a fázis szerinti választás) alapján. Az egyik leggyakrabban használt szűrő a **kritikus elérési úton található csúcspontokat**látja. A **kritikus elérési út** a U-SQL-feladatok csúcspontjának leghosszabb lánca. A kritikus elérési út megértése hasznos lehet a feladatok optimalizálásához, ha ellenőrzi, hogy a csúcspont a leghosszabb ideig tart-e.
  
![Data Lake Analytics eszközök csúcspontjának végrehajtási nézete](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

A felső középső ablaktáblán az **összes csúcspont futási állapota**látható.
  
![Data Lake Analytics eszközök csúcspontjának végrehajtási nézete](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Az alsó középső ablaktábla az egyes csúcspontokkal kapcsolatos információkat jeleníti meg:
* Folyamat neve: A csúcspont-példány neve. A StageName különböző részeiből áll | VertexName | VertexRunInstance. A SV7_Split [62]. v1 csúcspontja például a második futó példány (. v1, index: 0), a 62-es számú csúcson pedig a SV7_Split fázisban áll.
* Olvasott/írt összes adatmennyiség: Az adathalmazt a csúcspont olvasta/írta.
* Állapot/kilépés állapota: A csúcspont befejezésének végső állapota.
* Kilépési kód/hiba típusa: Hiba, ha a csúcspont sikertelen volt.
* Létrehozás oka: A csúcspont létrehozásának okai.
* Erőforrás-késés/folyamat késése/PN-várólista késése: a csúcspontnak az erőforrások megvárnia, az adatfeldolgozás és a várólistán való tartózkodáshoz szükséges idő.
* Folyamat/létrehozó GUID: Az aktuálisan futó csúcspont vagy annak létrehozójának GUID azonosítója.
* Verzió: a futó csúcspont N-edik példánya (a rendszer több okból is ütemezhet egy csúcspont új példányait, például feladatátvétel, számítási redundancia stb.).
* A verzió létrehozásának ideje.
* Folyamat létrehozása indítási idő/folyamat várólistán lévő idő/folyamat indítási idő/folyamat befejezése: Ha a csúcspont-folyamat elindítja a létrehozást, Amikor a csúcspont-folyamat elindul a várólistára; Ha az adott csúcspont-folyamat elindul; Ha az adott csúcspont befejeződik.

## <a name="next-steps"></a>További lépések
* A diagnosztikai információk naplózása: [Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](data-lake-analytics-diagnostic-logs.md).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Webhelynaplók elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md).
* A feladat részleteinek megtekintéséhez lásd: [Az Azure-beli és az Azure-beli adat-Lake Analytics-feladatok](data-lake-analytics-data-lake-tools-view-jobs.md) feladatának használata
