---
title: 'Teljesítmény hangolása: Spark, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: A Spark teljesítményének finomhangolására vonatkozó irányelvek Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a70b8112af201a49e7eece8b689e75102ec55880
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327545"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Teljesítmény hangolása: Spark, HDInsight & Azure Data Lake Storage Gen2

A Spark teljesítményének finomhangolásakor figyelembe kell vennie a fürtön futó alkalmazások számát.  Alapértelmezés szerint 4 alkalmazást futtathat egyszerre a HDI-fürtön (Megjegyzés: az alapértelmezett beállítás a módosítás tárgya).  Dönthet úgy, hogy kevesebb alkalmazást használ, így felülbírálhatja az alapértelmezett beállításokat, és több fürtöt is használhat ezekhez az alkalmazásokhoz.  

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen2-fiók**. A létrehozásával kapcsolatos utasításokért tekintse meg a rövid útmutató [: Azure Data Lake Storage Gen2 Storage-fiók létrehozása](data-lake-storage-quickstart-create-account.md)című témakört.
* **Azure HDInsight-fürt** Data Lake Storage Gen2 fiókhoz való hozzáféréssel. Lásd: [Azure Data Lake Storage Gen2 használata az Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Győződjön meg arról, hogy engedélyezi Távoli asztal a fürt számára.
* **Spark-fürt futtatása Data Lake Storage Gen2on**.  További információ: a [HDInsight Spark-fürt használata az adatok elemzéséhez Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Teljesítmény-finomhangolási irányelvek a Data Lake Storage Gen2**.  Az általános teljesítménnyel kapcsolatos fogalmakat lásd: [Data Lake Storage Gen2 teljesítmény-finomhangolási útmutató](data-lake-storage-performance-tuning-guidance.md) 

## <a name="parameters"></a>Paraméterek

A Spark-feladatok futtatásakor a legfontosabb beállítások a Data Lake Storage Gen2 teljesítményének növelésére használhatók:

* **NUM-végrehajtók** – a végrehajtás alatt álló egyidejű feladatok száma.

* **Végrehajtó – memória** – az egyes végrehajtók számára lefoglalt memória mennyisége.

* **Végrehajtó – magok** – az egyes végrehajtók számára lefoglalt magok száma.                     

**NUM-végrehajtók** A NUM-végrehajtók a párhuzamosan futtatható feladatok maximális számát fogják beállítani.  A párhuzamosan futtatható feladatok tényleges számát a fürtben elérhető memória-és processzor-erőforrások kötik.

**Végrehajtó – memória** Ez az egyes végrehajtók számára lefoglalt memória mennyisége.  Az egyes végrehajtók számára szükséges memória a feladatoktól függ.  Összetett műveletek esetén a memóriának nagyobbnak kell lennie.  Az olyan egyszerű műveletekhez, mint az olvasás és az írás, a memória követelményei alacsonyabbak lesznek.  Az egyes végrehajtók memóriájának mennyisége megtekinthető a Ambari.  A Ambari-ben navigáljon a Spark elemre, és tekintse meg a konfigurációk lapot.  

**Végrehajtó – magok** Ezzel beállítja a végrehajtók által használt magok számát, ami meghatározza a végrehajtó által futtatható párhuzamos szálak számát.  Ha például a végrehajtó-magok = 2, akkor minden végrehajtó két párhuzamos feladatot tud futtatni a végrehajtóban.  A szükséges végrehajtói magok a feladatoktól függenek.  Az I/O-feladatok nem igénylik a nagy mennyiségű memóriát, így minden végrehajtó több párhuzamos feladatot képes kezelni.

Alapértelmezés szerint a Spark on HDInsight-on való futtatásakor két virtuális fonal-mag van definiálva mindegyik fizikai mag számára.  Ez a szám egy jó párhuzamosságot és a több szálon átváltott környezetek mennyiségét biztosítja.  

## <a name="guidance"></a>Útmutatás

Ha a Spark analitikus számítási feladatait a Data Lake Storage Gen2ban lévő adatokkal kívánja használni, javasoljuk, hogy a legújabb HDInsight-verziót használja a legjobb teljesítmény eléréséhez a Data Lake Storage Gen2. Ha a feladata nagyobb I/O-igényű, a teljesítmény javítása érdekében bizonyos paraméterek is konfigurálhatók.  A Data Lake Storage Gen2 egy nagy mértékben méretezhető tárolási platform, amely képes a magas átviteli sebesség kezelésére.  Ha a feladatnak többnyire olvasási vagy írási művelete van, akkor a Data Lake Storage Gen2 az I/O-hoz és a-ból való párhuzamosság növelése növelheti a teljesítményt.

Az I/O-igényes feladatok esetében néhány általános módszer az egyidejűség növelésére.

**1. lépés: a fürtön futó alkalmazások számának meghatározása** – tudnia kell, hogy hány alkalmazás fut a fürtön, beleértve az aktuálisat is.  Az egyes Spark-beállítások alapértelmezett értékei feltételezik, hogy jelenleg 4 alkalmazás fut egyszerre.  Ezért az egyes alkalmazásokhoz csak a fürt 25%-a lesz elérhető.  A jobb teljesítmény érdekében felülbírálhatja az alapértelmezett értékeket a végrehajtók számának megváltoztatásával.  

**2. lépés: a végrehajtó – memória beállítása** – az első beállítás a végrehajtó – memória.  A memória a futtatni kívánt feladatoktól függ.  A párhuzamosságot úgy növelheti, hogy kevesebb memóriát foglal le a végrehajtón.  Ha a feladatok futtatásakor nem jelenik meg a memória kivételei, akkor növelje a paraméter értékét.  Egy másik lehetőség, hogy több memóriát kell használnia egy olyan fürt használatával, amely nagyobb mennyiségű memóriával rendelkezik, vagy növeli a fürt méretét.  További memória több végrehajtót is lehetővé tesz, ami nagyobb párhuzamosságot jelent.

**3. lépés: a végrehajtó-magok beállítása** – olyan I/O-igényű számítási feladatokhoz, amelyek nem rendelkeznek összetett műveletekkel, érdemes nagy számú végrehajtói magot kezdeni a párhuzamos feladatok számának növelésére.  A végrehajtó-magok beállítása jó kezdés.   

    executor-cores = 4
A végrehajtói magok számának növelése nagyobb párhuzamosságot biztosít, így különböző végrehajtói magokkal kísérletezhet.  Az összetettebb műveletekkel rendelkező feladatok esetében csökkentse a magok számát a végrehajtón.  Ha a végrehajtó-magok értéke 4-es nagyobb, akkor a szemetet nem lehet hatékonyan és csökkenhet a teljesítmény.

**4. lépés: a szálbeli memória mennyiségének meghatározása a fürtben** – ez az információ a Ambari-ben érhető el.  Navigáljon a FONALhoz, és tekintse meg a konfigurációk lapot.  Ebben az ablakban a szál memóriája jelenik meg.  
Vegye figyelembe, hogy az ablakban az alapértelmezett fonal-tároló mérete is látható.  A fonal tárolójának mérete megegyezik a memória/végrehajtó paraméter értékével.

    Total YARN memory = nodes * YARN memory per node
**5. lépés: a NUM-végrehajtók kiszámítása**

**Memória-megkötés kiszámítása** – a NUM-végrehajtók paramétert a memória vagy a CPU korlátozza.  A memória megkötését az alkalmazáshoz rendelkezésre álló fonal-memória mennyisége határozza meg.  A FONALak teljes memóriáját kell elosztania, és azt a végrehajtó által használt memória alapján kell megosztania.  A megkötést az alkalmazások számának megfelelően ki kell méretezni, ezért az alkalmazások száma alapján osztjuk.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**CPU-megkötés kiszámítása** – a rendszer kiszámítja a CPU-korlátozást, mivel a teljes virtuális magok száma a végrehajtón belüli magok számával egyenlő.  Minden fizikai mag esetében 2 virtuális mag van.  A memória megkötéséhez hasonlóan meg kell osztani az alkalmazások számát.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**NUM-végrehajtók beállítása** – a NUM-végrehajtók paramétert a rendszer a memória korlátozásának és a CPU-megkötés minimális értékének megadásával határozza meg. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Ha nagyobb számú NUM-végrehajtót állít be, a teljesítmény nem feltétlenül növekszik.  Érdemes figyelembe vennie, hogy további végrehajtók hozzáadása további terhelést eredményez minden további végrehajtónál, ami potenciálisan csökkentheti a teljesítményt.  A NUM-végrehajtók a fürt erőforrásaihoz vannak kötve.    

## <a name="example-calculation"></a>Példa a számításra

Tegyük fel, hogy jelenleg 8 D4v2-csomópontból álló fürttel rendelkezik, amely 2 alkalmazást futtat, beleértve a futtatni kívánt programot.  

**1. lépés: a fürtön futó alkalmazások számának meghatározása** – tudja, hogy a fürtön 2 alkalmazás van, beleértve a futtatni kívánt alkalmazást is.  

**2. lépés: a végrehajtó-memória beállítása** – ebben a példában megállapítjuk, hogy a végrehajtói memória 6gb-je elegendő az I/O-igényes feladatokhoz.  

    executor-memory = 6GB
**3. lépés: a végrehajtó-magok beállítása** – mivel ez egy I/O-igényes művelet, beállíthatja a magok számát az egyes végrehajtók számára a 4 értékre.  A magoknak a 4-es értéknél nagyobbra állítása nem okoz problémát a Garbage-gyűjtés során.  

    executor-cores = 4
**4. lépés: a szálbeli memória mennyiségének meghatározása a fürtben** – a Ambari navigálva megtudhatja, hogy minden D4v2 25 GB-os memóriával rendelkezik.  Mivel 8 csomópont van, a rendelkezésre álló szál memóriája 8-szor van megszorozva.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**5. lépés: a NUM-végrehajtók kiszámítása** – a NUM-végrehajtók paramétert úgy kell meghatározni, hogy a memória megkötésének minimális számát és a CPU-korlátot a Spark-on futó alkalmazások száma alapján osztja el.    

A **memória megkötésének kiszámítása** – a memória megkötésének kiszámítása a memória által lefuttatott memória teljes száma alapján történik.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**CPU-megkötés kiszámítása** – a rendszer kiszámítja a CPU-megkötést, mivel a teljes fonál magjai a végrehajtó magok száma szerint vannak elosztva.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**NUM-végrehajtók beállítása**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

