---
title: Adatfolyamok leképezése
description: Az adatfolyamatok Azure Data Factoryban való leképezésének áttekintése
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/19/2019
ms.openlocfilehash: 210c1814325e689dd70af9caa7fad08deed933e1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243795"
---
# <a name="what-are-mapping-data-flows"></a>Mik azok a leképezési adatfolyamok?

Az adatfolyamatok leképezése vizuálisan tervezett adatátalakítások Azure Data Factoryban. Az adatforgalom lehetővé teszi, hogy az adatmérnökök programkód írása nélkül fejlesszenek grafikus Adatátalakítási logikát. Az eredményül kapott adatfolyamatok olyan Azure Data Factory-folyamatokon belül lesznek végrehajtva, amelyek felskálázást használó Spark-fürtöket használnak. Az adatfolyam-tevékenységek a meglévő Data Factory ütemezési, vezérlési, folyamat-és figyelési képességein keresztül is működőképesek.

Az adatforgalom leképezése teljes körű vizuális élményt biztosít, és nem igényel kódolást. Az adatfolyamatok a saját végrehajtási fürtön futnak a kibővíthető adatfeldolgozáshoz. Azure Data Factory kezeli az adatáramlási feladatok összes fordítását, az elérési út optimalizálását és végrehajtását.

## <a name="getting-started"></a>Bevezetés

Az adatfolyamatok létrehozásához jelölje ki a **gyári erőforrások**területen a plusz jelre, majd válassza az **adatfolyam**lehetőséget. 

![Új adatfolyam](media/data-flow/newdataflow2.png "új adatfolyam")

Ekkor megjelenik az adatáramlási vászon, ahol létrehozhatja az átalakítási logikát. A forrás-átalakítás konfigurálásának megkezdéséhez válassza a **forrás hozzáadása** lehetőséget. További információ: forrás- [átalakítás](data-flow-source.md).

## <a name="data-flow-canvas"></a>Adatfolyam-vászon

Az adatfolyam-vászon három részből áll: a felső sáv, a gráf és a konfigurációs panel. 

![Vászon](media/data-flow/canvas1.png "Vászon")

### <a name="graph"></a>Graph

A gráf megjeleníti az átalakítási adatfolyamot. Megjeleníti a forrásadatok vonalát, mivel az egy vagy több mosogatóba áramlik. Új forrás hozzáadásához válassza a **forrás hozzáadása**elemet. Új átalakítás hozzáadásához válassza a meglévő átalakítás jobb alsó sarkában látható plusz jelre.

![Vászon](media/data-flow/canvas2.png "Vászon")

### <a name="azure-integration-runtime-data-flow-properties"></a>Az Azure Integration Runtime adatforgalmának tulajdonságai

![Hibakeresés gomb](media/data-flow/debugbutton.png "Hibakeresés gomb")

Amikor megkezdi az Adatáramlások használatát az ADF-ben, be kell kapcsolnia a "hibakeresés" kapcsolót a böngésző felhasználói felületének felső részén lévő adatfolyamatokhoz. Ezzel egy Azure Databricks-fürtöt fog használni az interaktív hibakereséshez, az adatelőzetesekhez és a folyamat-hibakeresési végrehajtáshoz. Az egyéni [Azure Integration Runtime](concepts-integration-runtime.md)kiválasztásával megadhatja a használni kívánt fürt méretét. A hibakeresési munkamenet az utolsó adatelőnézet vagy az utolsó hibakeresési folyamat végrehajtása után akár 60 perccel is életben marad.

Ha adatáramlási tevékenységekkel működővé tenni a folyamatokat, az ADF a "Futtatás" tulajdonságban a [tevékenységhez](control-flow-execute-data-flow-activity.md) társított Azure Integration Runtime fogja használni.

Az alapértelmezett Azure Integration Runtime egy 4 magos, egyetlen feldolgozó csomópont-fürt, amely lehetővé teszi az adatmegtekintést és a hibakeresési folyamatok gyors végrehajtását minimális költségek mellett. Nagyobb Azure IR konfiguráció beállítása, ha nagyméretű adatkészleteken végez műveleteket.

A fürt erőforrásainak (VM-EK) készletének fenntartásához az ADF-et kell megadnia a Azure IR adatfolyam tulajdonságai között. Ez gyorsabb feladatok végrehajtását eredményezi a későbbi tevékenységek esetén.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Az Azure Integration Runtime és az adatáramlási stratégiák

##### <a name="execute-data-flows-in-parallel"></a>Az adatfolyamatok párhuzamos végrehajtása

Ha párhuzamosan hajtja végre az adatfolyamatokat, az ADF külön Azure Databricks fürtöket helyez el minden tevékenység végrehajtásához az egyes tevékenységekhez csatolt Azure Integration Runtime beállításai alapján. Az ADF-folyamatok párhuzamos végrehajtásának kialakításához adja hozzá az adatfolyam-tevékenységeket a felhasználói felület elsőbbségi korlátozásai nélkül.

Ebből a három lehetőségből ez a lehetőség valószínűleg a lehető legrövidebb idő alatt fog megjelenni. Azonban az egyes párhuzamos adatfolyamok külön fürtökön lesznek végrehajtva, így az események rendezése nem determinisztikus.

Ha a folyamatokon belül párhuzamosan hajtja végre az adatfolyam-tevékenységeket, azt javasoljuk, hogy ne használja az ÉLETTARTAMot. Ennek az az oka, hogy az adatforgalom párhuzamos végrehajtása ugyanazon Azure Integration Runtime használatával egyszerre több meleg készlet-példányt eredményez az adatelőállító számára.

##### <a name="overload-single-data-flow"></a>Egyetlen adatfolyam túlterhelése

Ha egyetlen adatfolyamaton belül helyezi el az összes logikát, akkor a rendszer az ADF-t egyetlen Spark-fürtbeli példányon hajtja végre ugyanazon a feladatok végrehajtási környezetében.

Ez a lehetőség valószínűleg nehezebben követhető és elhárítható, mert az üzleti szabályok és az üzleti logika össze lesz keverve. Ez a beállítás szintén nem nyújt sok újrahasználhatóságot.

##### <a name="execute-data-flows-serially"></a>Az adatfolyamatok soros végrehajtása

Ha a folyamat soros verziójában hajtja végre az adatfolyam-tevékenységeket, és beállította az ÉLETTARTAMot a Azure IR konfigurációban, akkor az ADF újra felhasználja a számítási erőforrásokat (VM), ami gyorsabb végrehajtást eredményez. Minden egyes végrehajtáshoz továbbra is új Spark-kontextust fog kapni.

Ebből a három lehetőségből ez valószínűleg a leghosszabb időt fogja igénybe venni a végpontok között. Azonban a logikai műveletek tiszta elkülönítését teszi lehetővé az egyes adatfolyamok lépéseiben.

### <a name="configuration-panel"></a>Konfigurációs panel

A konfigurációs panel megjeleníti az aktuálisan kiválasztott átalakításhoz tartozó beállításokat. Ha nincs kiválasztva átalakítás, az adatfolyamatot jeleníti meg. A teljes adatfolyam-konfigurációban szerkesztheti a nevet és a leírást az **általános** lapon, vagy hozzáadhat paramétereket a parameters ( **Paraméterek** ) lapon. További információ: [az adatfolyam paramétereinek leképezése](parameters-data-flow.md).

Minden átalakításhoz legalább négy konfigurációs lap tartozik.

#### <a name="transformation-settings"></a>Átalakítási beállítások

Az egyes átalakítások konfigurációs paneljének első lapja az adott átalakításra vonatkozó beállításokat tartalmazza. További információ: az átalakítás dokumentációs lapja.

![Forrás beállításai lap](media/data-flow/source1.png "Forrás beállításai lap")

#### <a name="optimize"></a>Optimalizálás

Az **optimalizálás** lap a particionálási sémák konfigurálásához szükséges beállításokat tartalmazza.

![Optimalizálás](media/data-flow/optimize1.png "Optimalizálás")

Az alapértelmezett beállítás az **aktuális particionálást használja**, amely arra utasítja a Azure Data Factoryot, hogy a Spark-on futó adatfolyamatok natív particionálási sémáját használják. A legtöbb esetben ezt a beállítást javasoljuk.

Vannak olyan példányok, amelyekben érdemes lehet módosítani a particionálást. Ha például az átalakításokat egyetlen fájlba szeretné exportálni a-tóban, válassza ki a **különálló partíciót** egy fogadó átalakításban.

Egy másik eset, amikor a particionálási sémákat a teljesítmény optimalizálása érdekében érdemes szabályozni. A particionálás beállítása lehetővé teszi az adatok elosztását a számítási csomópontok és az adatkörnyezet-optimalizálások között, amelyek mind pozitív, mind negatív hatással lehetnek a teljes adatfolyam-teljesítményre. További információ: az [adatfolyam teljesítményének útmutatója](concepts-data-flow-performance.md).

Ha módosítani szeretné a particionálást bármely transzformáción, válassza az **optimalizálás** fület, és válassza a **particionálás beállítása** választógombot. Ekkor megjelenik egy sor particionálási lehetőséggel. A particionálás legjobb módszere az adatmennyiségek, a jelölt kulcsok, a null értékek és a kardinális alapján eltérő lesz. 

Az ajánlott eljárás az alapértelmezett particionálás, majd a különböző particionálási beállítások kipróbálása. Tesztelheti a folyamat hibakeresési futtatását, és megtekintheti az egyes átalakítási csoportok végrehajtási idejét és a partíciók használatát a figyelés nézetből. További információ: [az adatfolyamatok figyelése](concepts-data-flow-monitoring.md).

A következő particionálási beállítások érhetők el.

##### <a name="round-robin"></a>Ciklikus multiplexelés 

A ciklikus multiplexelés egy egyszerű partíció, amely automatikusan elosztja az adategységeket a partíciók között. A ciklikus időszeletelést akkor érdemes használni, ha nem rendelkezik a megfelelő kulcsokkal a szilárd, intelligens particionálási stratégia megvalósításához. Megadhatja a fizikai partíciók számát.

##### <a name="hash"></a>Kivonat

Azure Data Factory az oszlopok kivonatát fogja létrehozni, hogy egységes partíciókat hozzon létre, így a hasonló értékekkel rendelkező sorok ugyanahhoz a partícióhoz fognak esni. Ha a kivonatoló kapcsolót használja, tesztelje a lehetséges partíciók döntését. Megadhatja a fizikai partíciók számát.

##### <a name="dynamic-range"></a>Dinamikus tartomány

A dinamikus tartomány a megadott oszlopok vagy kifejezések alapján a Spark dinamikus tartományokat fogja használni. Megadhatja a fizikai partíciók számát. 

##### <a name="fixed-range"></a>Rögzített tartomány

Hozzon létre egy olyan kifejezést, amely rögzített tartományt biztosít a particionált adatoszlopokban lévő értékek számára. Ha el szeretné kerülni a partíciók eldöntését, érdemes megismernie az adatait, mielőtt ezt a beállítást használja. A kifejezéshez megadott értékek a Partition függvény részeként lesznek felhasználva. Megadhatja a fizikai partíciók számát.

##### <a name="key"></a>Paraméter

Ha jól ismeri az Ön adatait, a kulcsfontosságú particionálás jó stratégia lehet. A kulcsok particionálásakor a rendszer létrehozza a partíciókat az oszlop minden egyedi értékéhez. A partíciók száma nem állítható be, mert a szám az adatok egyedi értékein alapul.

#### <a name="inspect"></a>Vizsgálata

Az **ellenőrzés** lapon megtekintheti az átalakítás alatt álló adatfolyam metaadatait. Láthatja az oszlopok számát, a megváltoztatott oszlopokat, a hozzáadott oszlopokat, az adattípusokat, az oszlopok sorrendjét és az oszlopok hivatkozásait. A **vizsgálat** a metaadatok csak olvasható nézete. Nem kell engedélyezni a hibakeresési módot a metaadatok megjelenítéséhez a **vizsgálat** ablaktáblán.

![Vizsgálata](media/data-flow/inspect1.png "Vizsgálata")

Amikor átalakításokon keresztül módosítja az adatok alakját, a metaadatok változásai a **vizsgálat** panelen jelennek meg. Ha nincs definiált séma a forrás-átalakításban, akkor a metaadatok nem lesznek láthatók a **vizsgálat** ablaktáblán. A metaadatok hiánya gyakori a séma-drift forgatókönyvekben.

#### <a name="data-preview"></a>Adatelőnézet

Ha a hibakeresési mód be van kapcsolva, az **adatelőnézet** lap interaktív pillanatképet biztosít az adatokról az egyes transzformációk során. További információ: az [adatelőnézet hibakeresési módban](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Felső sáv

A felső sáv olyan műveleteket tartalmaz, amelyek hatással vannak a teljes adatfolyamra, például a mentésre és az érvényesítésre. A Graph és a konfigurációs üzemmód közötti váltás a **Graph megjelenítése** és a Graph billentyűk **elrejtése** gomb használatával is lehetséges.

![Gráf elrejtése](media/data-flow/hideg.png "Gráf elrejtése")

Ha elrejti a gráfot, az **előző** és a **következő** gombokon keresztül böngészhet az átalakítási csomópontokon.

![Előző és következő gomb](media/data-flow/showhide.png "előző és következő gomb")

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan hozhat létre [forrás-átalakítást](data-flow-source.md).
* Megtudhatja, hogyan hozhat létre adatfolyamatokat [hibakeresési módban](concepts-data-flow-debug-mode.md).
