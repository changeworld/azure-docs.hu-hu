---
title: Adatfolyamok vizuális figyelésének leképezése
description: Azure Data Factory adatfolyamatok vizuális monitorozása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: 93d92286fa9eecbc64229059274cc8f9ed99e21e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928275"
---
# <a name="monitor-data-flows"></a>Adatfolyamatok figyelése



Miután befejezte az adatfolyamok kiépítését és hibakeresését, ütemeznie kell, hogy az adatfolyamatot egy folyamat kontextusában hajtsa végre a program. Az eseményindítók használatával ütemezhet Azure Data Factory folyamatot. Vagy használhatja az aktiválás most lehetőséget a Azure Data Factory pipeline Builder-ből úgy, hogy egyetlen futtatású végrehajtást hajtson végre az adatfolyamatok folyamaton belüli teszteléséhez.

A folyamat végrehajtásakor nyomon követheti a folyamatot, valamint az adatfolyamatban található összes tevékenységet, beleértve az adatfolyam tevékenységeit is. Kattintson a figyelés ikonra a bal oldali Azure Data Factory felhasználói felület paneljén. Egy, az alábbihoz hasonló képernyő jelenik meg. A Kiemelt ikonok lehetővé teszik a folyamat tevékenységeinek részletezését, beleértve az adatfolyam tevékenységeit is.

![Adatfolyam-monitorozás](media/data-flow/mon001.png "Adatfolyam-figyelés")

Ezen a szinten láthatja a statisztikát is, beleértve a futtatási időpontokat és az állapotot. A futtatási azonosító a tevékenység szintjén eltér a futtatási AZONOSÍTÓtól a folyamat szintjén. A futtatási azonosító az előző szinten a folyamathoz szükséges. A szemüvegre kattintva részletes információkat talál az adatfolyam végrehajtásáról.

![Adatfolyam-monitorozás](media/data-flow/mon002.png "Adatfolyam-figyelés")

Ha a grafikus csomópont figyelése nézetben látható, az Adatfolyam-diagram egyszerűsített, csak megtekintésre szolgáló verziója jelenik meg.

![Adatfolyam-monitorozás](media/data-flow/mon003.png "Adatfolyam-figyelés")

## <a name="view-data-flow-execution-plans"></a>Adatfolyam-végrehajtási tervek megtekintése

Ha az adatfolyamot a Sparkban hajtja végre, Azure Data Factory meghatározza a kódok optimális elérési útját az adatfolyam teljes egészében. Emellett a végrehajtási útvonalak különböző kibővíthető csomópontokon és adatpartíción is előfordulhatnak. Ezért a figyelési gráf a folyamat kialakítását jelképezi, figyelembe véve az átalakítások végrehajtási útvonalát. Ha az egyes csomópontokra kattint, megjelenik a "Csoportosítások" kifejezés, amely a fürtön együtt végrehajtott kódot jelképezi. Az időzítések és a megjelenő számadatok ezeket a csoportokat jelölik, a terv egyes lépéseivel szemben.

![Adatfolyam-monitorozás](media/data-flow/mon004.png "Adatfolyam-figyelés")

* Ha a figyelés ablakában a Megnyitás területre kattint, az alsó ablaktáblában található statisztika megjeleníti az egyes fogadók időzítését és sorainak számát, valamint az átalakítási vonal számára a fogadó adatokhoz vezető átalakításokat.

* Az egyes átalakítások kiválasztásakor további visszajelzéseket fog kapni a jobb oldali panelen, amely megjeleníti a partíciós statisztikát, az oszlopok számát, a torzítást (azaz a partíciók között elosztott adatok egyenletes eloszlását), valamint a csúcsosságát (a tüskés az adatok).

* Ha a csomópont nézetben a fogadóra kattint, megjelenik az oszlop leszármazása. Három különböző módszer áll rendelkezésre az oszlopok összegyűjtéséhez a fogadóban lévő összes adatfolyamban. Ezek a következők:

  * Számított: az oszlopot a feltételes feldolgozáshoz vagy egy kifejezésen belül használja az adatfolyamatban, de a fogadóban nem
  * Származtatott: az oszlop egy új oszlop, amelyet a folyamat során generált, azaz nem volt jelen a forrásban.
  * Leképezve: az oszlop a forrástól származik, és a leképezést egy fogadó mezőhöz rendeli hozzá
  * Adatfolyam állapota: a végrehajtás aktuális állapota
  * Fürt indítási ideje: a JIT Spark számítási környezet beolvasásához szükséges idő az adatfolyam-végrehajtáshoz
  * Átalakítások száma: hány átalakítási lépést hajt végre a folyamaton belül
  
![Adatfolyam-monitorozás](media/data-flow/monitornew.png "Adatfolyam-figyelés – új")  
  
## <a name="monitor-icons"></a>Ikonok figyelése

Ez az ikon azt jelenti, hogy az átalakítási adatgyűjtés már gyorsítótárazva van a fürtön, így az időzítések és a végrehajtás elérési útja figyelembe vette a következőt:

![Adatfolyam-monitorozás](media/data-flow/mon004.png "Adatfolyam-figyelés")

Emellett a zöld kör ikonjai is megjelennek az átalakításban. Ezek a mosdók számát jelölik, amelyekben az adatforgalom beáramlik.
