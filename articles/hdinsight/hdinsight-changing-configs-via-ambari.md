---
title: Apache Ambari a fürt konfigurációjának optimalizálásához – Azure HDInsight
description: Az Apache Ambari webes FELÜLETének használatával konfigurálhatja és optimalizálhatja az Azure HDInsight-fürtöket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 15a2c75a7619a815655be0fd9fd3044d86acd057
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272564"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Az Apache Ambari használata a HDInsight-fürtök konfigurációjának optimalizálásához

A HDInsight [Apache Hadoop](https://hadoop.apache.org/) fürtöket biztosít nagyméretű adatfeldolgozási alkalmazásokhoz. Az összetett, több csomópontot tartalmazó fürtök kezelése, monitorozása és optimalizálása kihívást jelenthet. Az [Apache Ambari](https://ambari.apache.org/) egy webes felület a HDInsight Linux-fürtök felügyeletéhez és figyeléséhez.  Windows-fürtök esetén használja a [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

A Ambari webes felhasználói felületének bevezetését lásd: [HDInsight-fürtök kezelése az Apache Ambari Web UI használatával](hdinsight-hadoop-manage-ambari.md)

Jelentkezzen be a Ambari `https://CLUSTERNAME.azurehdidnsight.net` a fürt hitelesítő adataival. A kezdeti képernyő egy áttekintő irányítópultot jelenít meg.

![Az Apache Ambari felhasználói irányítópultja megjelenik](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

A Ambari webes felhasználói felülete a gazdagépek, a szolgáltatások, a riasztások, a konfigurációk és a nézetek kezelésére használható. A Ambari nem használható HDInsight-fürt létrehozásához, a szolgáltatások frissítéséhez, a stackek és verziók kezeléséhez, illetve a gazdagépek leszereléséhez, illetve a szolgáltatások a fürthöz való hozzáadásához.

## <a name="manage-your-clusters-configuration"></a>A fürt konfigurációjának kezelése

A konfigurációs beállítások segítenek egy adott szolgáltatás finomhangolásában. A szolgáltatás konfigurációs beállításainak módosításához válassza ki a szolgáltatást a **szolgáltatások** oldalsávról (a bal oldalon), majd keresse meg a **konfiguráció** lapot a szolgáltatás részletei lapon.

![Apache Ambari Services oldalsáv](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>NameNode Java-halom méretének módosítása

A NameNode Java-halom mérete számos tényezőtől függ, például a fürt terhelésével, a fájlok számával és a blokkok számával. Az alapértelmezett 1 GB-os méret a legtöbb fürttel jól működik, bár egyes munkaterhelések esetén több vagy kevesebb memória szükséges.

Az NameNode Java-halom méretének módosítása:

1. Válassza a **HDFS** elemet a szolgáltatások oldalsávon, és navigáljon a **konfigurációk** lapra.

    ![Apache Ambari HDFS-konfiguráció](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. A Java- **NameNode**beállításának megkeresése. A **szűrő** szövegmezővel egy adott beállítást is beírhat, és megkeresheti azt. Válassza a **toll** ikont a beállítás neve mellett.

    ![Apache Ambari NameNode Java halom mérete](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Írja be az új értéket a szövegmezőbe, majd nyomja le az **ENTER** billentyűt a módosítás mentéséhez.

    ![Ambari szerkesztése NameNode Java heap Size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. A NameNode Java-halom mérete 1 GB-ra módosul 2 GB-ról.

    ![Szerkesztett NameNode Java heap size2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Mentse a módosításokat a konfigurációs képernyő felső részén található zöld **Mentés** gombra kattintva.

    ![Ambari Ambari-mentési konfigurációk](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Apache Hive optimalizálás

A következő szakaszok ismertetik az általános Apache Hive teljesítmény optimalizálásához szükséges konfigurációs beállításokat.

1. A struktúra konfigurációs paramétereinek módosításához válassza a **struktúra** lehetőséget a szolgáltatások oldalsávon.
1. Navigáljon a **konfigurációk** lapra.

### <a name="set-the-hive-execution-engine"></a>A kaptár-végrehajtó motor beállítása

A kaptár két végrehajtó motort biztosít: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) és [Apache TEZ](https://tez.apache.org/). A TEZ gyorsabb, mint a MapReduce. A HDInsight Linux-fürtök alapértelmezett TEZ rendelkeznek. A végrehajtási motor módosítása:

1. A kaptár- **konfigurációk** lapon írja be a végrehajtó **motor** értéket a szűrő mezőbe.

    ![Apache Ambari keresési végrehajtó motor](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. Az **optimalizálási** tulajdonság alapértelmezett értéke **TEZ**.

    ![Optimalizálás – Apache TEZ Engine](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Leképezések hangolása

A Hadoop egyetlen fájlt próbálfelosztani több fájlba, és párhuzamosan dolgozza fel az eredményül kapott fájlokat. A leképezések száma a felosztások számától függ. A következő két konfigurációs paraméter a TEZ-végrehajtó motor felosztásának számát hajtja végre:

* `tez.grouping.min-size`: egy csoportosított felosztás méretének alsó korlátja, amelynek alapértelmezett értéke 16 MB (16 777 216 bájt).
* `tez.grouping.max-size`: egy csoportosított felosztás felső korlátja, amelynek alapértelmezett értéke 1 GB (1 073 741 824 bájt).

A teljesítményre vonatkozó szabályként csökkentse mindkét paramétert a késés növeléséhez, és növelje a nagyobb átviteli sebességet.

Ha például négy Mapper-feladatot szeretne beállítani 128 MB adatmérethez, mindkét paramétert 32 MB-ra (33 554 432 bájt) kell beállítania.

1. A határérték-paraméterek módosításához navigáljon a TEZ szolgáltatás **konfigurációk** lapjára. Bontsa ki az **általános** panelt, és keresse meg a `tez.grouping.max-size` és `tez.grouping.min-size` paramétereket.

1. Mindkét paraméter **33 554 432** bájtra (32 MB) állítható be.

    ![Apache Ambari TEZ-csoportosítási méretek](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Ezek a módosítások a kiszolgálón lévő összes TEZ-feladatot érintik. Az optimális eredmény eléréséhez válassza a megfelelő paraméterek értékét.

### <a name="tune-reducers"></a>Szűkítők hangolása

Az [Apache ork](https://orc.apache.org/) és a [Snappy](https://google.github.io/snappy/) egyaránt nagy teljesítményt nyújt. A kaptár azonban alapértelmezés szerint túl kevés szűkítővel rendelkezhet, ami szűk keresztmetszetet okoz.

Tegyük fel például, hogy a bemeneti adatok mérete 50 GB. Az ork formátumú, Snappy-tömörítéssel rendelkező adat 1 GB. A struktúra a következőhöz szükséges szűkítők számát becsüli meg: (a leképező és a `hive.exec.reducers.bytes.per.reducer`bemeneti bájtjainak száma).

Az alapértelmezett beállításokkal ez a példa 4 szűkítő.

A `hive.exec.reducers.bytes.per.reducer` paraméter határozza meg a redukáló által feldolgozott bájtok számát. Az alapértelmezett érték 64 MB. Az érték finomhangolása növeli a párhuzamosságot, és növelheti a teljesítményt. A túl alacsony hangolás túl sok szűkítőt is eredményezhet, ami potenciálisan hátrányosan befolyásolhatja a teljesítményt. Ez a paraméter az adott adatkövetelményektől, a tömörítési beállításoktól és más környezeti tényezőktől függ.

1. A paraméter módosításához navigáljon a struktúra- **konfigurációk** lapra, és keresse meg a beállítások lapon található, redukáló paraméterrel rendelkező **adatmennyiséget** .

    ![Apache Ambari-adatmennyiség csökkentése](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Válassza a **Szerkesztés** lehetőséget az érték 128 MB-ra (134 217 728 bájt) való módosításához, majd nyomja le az **ENTER** billentyűt a mentéshez.

    ![Ambari-adatmennyiség csökkentése – szerkesztett](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    A bemeneti méret 1 024 MB, a 128 MB adatmennyiség pedig a redukáló értéknél 8 szűkítő (1024/128).

1. Az **adatvesztési** paramétereknél az adat helytelen értéke nagy számú szűkítőt eredményezhet, ami kedvezőtlen hatással lehet a lekérdezési teljesítményre. A szűkítők maximális számának korlátozásához állítsa `hive.exec.reducers.max` a megfelelő értékre. Az alapértelmezett érték a 1009.

### <a name="enable-parallel-execution"></a>Párhuzamos végrehajtás engedélyezése

A kaptár-lekérdezések végrehajtása egy vagy több szakaszban történik. Ha a független szakaszok párhuzamosan is futtathatók, a lekérdezési teljesítményt is növeli.

1. A párhuzamos lekérdezés végrehajtásának engedélyezéséhez navigáljon a struktúra **konfigurációja** lapra, és keresse meg a `hive.exec.parallel` tulajdonságot. Az alapértelmezett értéke FALSE (hamis). Módosítsa az értéket True értékre, majd nyomja le az **ENTER** billentyűt az érték mentéséhez.

1. A párhuzamosan futtatandó feladatok számának korlátozásához módosítsa a `hive.exec.parallel.thread.number` tulajdonságot. Az alapértelmezett érték 8.

    ![Apache Hive exec párhuzamos megjelenítés](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Vektorizációt engedélyezése

A struktúra soronként dolgozza fel az adatsort. A vektorizációt a Kaptárat úgy irányítja át, hogy az adatfeldolgozást egy sor helyett 1 024 sor formájában dolgozza fel. A vektorizációt csak az ork fájlformátumra alkalmazható.

1. A vektoros lekérdezés végrehajtásának engedélyezéséhez keresse meg a kaptár **konfigurációk** lapot, és keresse meg a `hive.vectorized.execution.enabled` paramétert. Az alapértelmezett érték a kaptár 0.13.0 vagy újabb verziója esetén igaz.

1. A lekérdezés csökkentése érdekében a vektoros végrehajtás engedélyezéséhez állítsa a `hive.vectorized.execution.reduce.enabled` paramétert True (igaz) értékre. Az alapértelmezett értéke FALSE (hamis).

    ![Vektoros végrehajtás Apache Hive](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Cost-alapú optimalizálás engedélyezése (CBO)

Alapértelmezés szerint a kaptár egy olyan szabályt követ, amely egy optimális lekérdezés-végrehajtási tervet keres. A Cost-based Optimization (CBO) több csomagot értékel ki egy lekérdezés végrehajtásához, és minden csomaghoz hozzárendel egy díjat, majd meghatározza a legolcsóbb tervet a lekérdezés végrehajtásához.

A CBO engedélyezéséhez lépjen a **kaptár** > **konfigurációk** > beállítások elemre, és keresse meg a **Cost-alapú optimalizáló engedélyezése** **lehetőséget** , majd kapcsolja be a váltás gombot **a be**értékre.

![HDInsight-alapú optimalizáló](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

A következő további konfigurációs paraméterek fokozzák a kaptár-lekérdezések teljesítményét, ha a CBO engedélyezve van:

* `hive.compute.query.using.stats`

    Ha igaz értékre van állítva, a kaptár a saját metaadattár tárolt statisztikát használja az egyszerű lekérdezések, például a `count(*)`megválaszolásához.

    ![Számítási lekérdezés Apache Hive statisztikák használatával](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Az oszlop statisztikái akkor jönnek létre, ha a CBO engedélyezve van. A kaptár a metaadattár tárolt oszlopok statisztikáit használja a lekérdezések optimalizálására. Az oszlopok statisztikáinak beolvasása az egyes oszlopoknál tovább tart, ha az oszlopok száma magas. Ha false értékre van állítva, akkor ez a beállítás letiltja az oszlopok statisztikáinak beolvasását a metaadattár.

    ![Apache Hive statisztika-készlet oszlopainak statisztikája](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Az alapszintű partíciók statisztikái, például a sorok száma, az adatok mérete és a fájlméret a metaadattár-ben tárolódnak. Ha igaz értékre van állítva, a partíció statisztikái a metaadattár-ből lesznek beolvasva. Hamis érték esetén a rendszer beolvassa a fájl méretét a fájlrendszerből, és a sorok számát beolvassa a sor sémából.

    ![Struktúra-stats set Partition stats](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Közbenső tömörítés engedélyezése

A leképezési feladatok a csökkentő tevékenységek által használt köztes fájlokat hoznak létre. A köztes tömörítés a köztes fájlméretet csökkenti.

A Hadoop feladatok általában I/O-torlódások. Az adatok tömörítése felgyorsíthatja az I/O-t és a teljes hálózati átvitelt.

A rendelkezésre álló tömörítési típusok a következők:

| Formátum | Eszköz | Algoritmus | Fájlkiterjesztés | Feloszthatók? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | .gz | Nem |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Igen |
| LZO | Lzop | LZO | .lzo | Igen, ha indexelve van |
| Snappy | N/A | Snappy | Snappy | Nem |

Általános szabály, hogy a tömörítési módszer felosztható, mert a rendszer nagyon kevés leképezést hoz létre. Ha a bemeneti adatok szöveg, `bzip2` a legjobb lehetőség. Az ork formátum esetében a Snappy a leggyorsabb tömörítési lehetőség.

1. A köztes tömörítés engedélyezéséhez navigáljon a kaptár **konfigurációi** lapra, és állítsa a `hive.exec.compress.intermediate` paramétert True (igaz) értékre. Az alapértelmezett értéke FALSE (hamis).

    ![A kaptár exec tömörítése](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > A köztes fájlok tömörítéséhez válassza ki a tömörítési kodeket, amely alacsonyabb CPU-költségeket tartalmaz, még akkor is, ha a kodek nem rendelkezik magas tömörítési kimenettel.

1. A köztes tömörítési kodek beállításához adja hozzá az egyéni tulajdonságot `mapred.map.output.compression.codec` a `hive-site.xml` vagy a `mapred-site.xml` fájlhoz.

1. Egyéni beállítás hozzáadása:

    a. Navigáljon a **kaptár** > **konfigurációk** > **Advanced** > **Custom kaptár-site**.

    b. Válassza a **tulajdonság hozzáadása...** lehetőséget az egyéni struktúra – hely ablaktábla alján.

    c. A tulajdonság hozzáadása ablakban adja meg az `mapred.map.output.compression.codec` kulcsot, és `org.apache.hadoop.io.compress.SnappyCodec` értéket.

    d. Válassza a **Hozzáadás** lehetőséget.

    ![Egyéni tulajdonság hozzáadása Apache Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Ezzel tömöríti a közbenső fájlt a Snappy Compression használatával. A tulajdonság hozzáadása után az megjelenik az egyéni struktúra – hely ablaktáblán.

    > [!NOTE]  
    > Ez az eljárás módosítja a `$HADOOP_HOME/conf/hive-site.xml` fájlt.

### <a name="compress-final-output"></a>Végső kimenet tömörítése

A struktúra utolsó kimenete is tömöríthető.

1. A struktúra utolsó kimenetének tömörítéséhez navigáljon a struktúra- **konfigurációk** lapra, és állítsa a `hive.exec.compress.output` paramétert True (igaz) értékre. Az alapértelmezett értéke FALSE (hamis).

1. A kimeneti tömörítési kodek kiválasztásához adja hozzá a `mapred.output.compression.codec` egyéni tulajdonságot az egyéni struktúra – hely panelhez az előző szakasz 3. lépésében leírtak szerint.

    ![Apache Hive egyéni tulajdonság add2](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Spekulatív végrehajtás engedélyezése

A spekulációs végrehajtás bizonyos számú ismétlődő feladatot indít el a lassú futású Task Tracker észleléséhez és feketelistához, miközben az egyes feladatok eredményeinek optimalizálásával javítja a feladat teljes végrehajtását.

A spekulatív végrehajtás nem kapcsolható be nagy mennyiségű bemenettel rendelkező, hosszan futó MapReduce feladatokhoz.

* A spekulációs végrehajtás engedélyezéséhez navigáljon a kaptár **konfigurációk** lapra, és állítsa a `hive.mapred.reduce.tasks.speculative.execution` paramétert True (igaz) értékre. Az alapértelmezett értéke FALSE (hamis).

    ![A kaptár mapred csökkenti a feladatok spekulációs végrehajtását](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Dinamikus partíciók hangolása

A struktúra lehetővé teszi, hogy dinamikus partíciókat hozzon létre, amikor rekordokat szúr be egy táblázatba, az egyes partíciók elődefiniálása nélkül. Ez egy hatékony funkció, bár nagy számú partíció és nagy számú fájl létrehozását eredményezheti az egyes partíciók esetében.

1. Ahhoz, hogy a struktúra dinamikus partíciókat végezzen, a `hive.exec.dynamic.partition` paraméter értékének igaznak kell lennie (az alapértelmezett beállítás).

1. Módosítsa a dinamikus partíciós módot a *szigorú*értékre. Szigorú módban legalább egy partíciónak statikusnak kell lennie. Ez megakadályozza a lekérdezéseket a WHERE záradékban lévő partíciós szűrő nélkül, azaz az összes partíciót ellenőrző lekérdezések *szigorú* meggátolása. Keresse meg a kaptár- **konfigurációk** lapot, majd állítsa be a `hive.exec.dynamic.partition.mode`t **szigorú**értékre. Az alapértelmezett érték nem **szigorú**.

1. A létrehozandó dinamikus partíciók számának korlátozásához módosítsa a `hive.exec.max.dynamic.partitions` paramétert. Az alapértelmezett érték a 5000.

1. Ha korlátozni szeretné a dinamikus partíciók teljes számát egy csomóponton, módosítsa `hive.exec.max.dynamic.partitions.pernode`. Az alapértelmezett érték: 2000.

### <a name="enable-local-mode"></a>Helyi mód engedélyezése

A helyi mód lehetővé teszi, hogy a kaptár a feladatok összes feladatát egyetlen gépen, vagy esetenként egyetlen folyamaton hajtsa végre. Ez javítja a lekérdezési teljesítményt, ha a bemeneti adatok kicsik, és a lekérdezések feladatok elindításának terhelése a lekérdezés teljes végrehajtásának jelentős százalékát használja fel.

Helyi mód engedélyezéséhez adja hozzá a `hive.exec.mode.local.auto` paramétert az egyéni struktúra – hely panelhez, ahogy az a [köztes tömörítés engedélyezése](#enable-intermediate-compression) szakasz 3. lépésében leírtak szerint.

![Apache Hive exec mód helyi automatikus](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Egyetlen MapReduce többcsoportos beállítása

Ha ez a tulajdonság TRUE (igaz) értékre van állítva, a közös csoport – kulcsokkal rendelkező többcsoportos lekérdezések egyetlen MapReduce-feladatot hoznak létre.  

Ha engedélyezni szeretné ezt a viselkedést, adja hozzá a `hive.multigroupby.singlereducer` paramétert az egyéni struktúra – hely ablaktáblához, a [köztes tömörítés engedélyezése](#enable-intermediate-compression) szakasz 3. lépésében leírtak szerint.

![Struktúra egyetlen MapReduce többcsoportos beállítása](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>További kaptár-optimalizálások

A következő szakaszok a kaptárral kapcsolatos további optimalizálásokat ismertetik.

#### <a name="join-optimizations"></a>Csatlakozás optimalizációk

A kaptárban az alapértelmezett illesztési típus egy *shuffle illesztés*. A kaptárban a speciális leképezések beolvassák a bemenetet, és egy összekapcsolási kulcs/érték párokat bocsátanak ki egy köztes fájlba. A Hadoop rendezi és egyesíti ezeket a párokat egy véletlenszerű szakaszban. Ez a véletlenszerű szakasz költséges. Ha kiválasztja a megfelelő illesztést az adatai alapján, jelentősen növelheti a teljesítményt.

| Csatlakozás típusa | mikor | Hogyan | Struktúra beállításai | Megjegyzések |
| -- | -- | -- | -- | -- |
| Összekeverhető illesztés | <ul><li>Alapértelmezett választás</li><li>Mindig működik</li></ul> | <ul><li>Olvasás az egyik táblázat részéből</li><li>Az illesztési kulcshoz tartozó gyűjtők és rendezések</li><li>Egy gyűjtőt küld minden egyes csökkentéshez</li><li>A csatlakozás a csökkentés oldalon történik</li></ul> | Nincs szükség jelentős kaptár-beállításra | Minden alkalommal működik |
| Csatlakoztatás leképezése | <ul><li>Egy tábla fér el a memóriában</li></ul> | <ul><li>Kisméretű tábla beolvasása a memória kivonatának táblájába</li><li>Streamek a nagyméretű fájl részeként</li><li>Az egyes rekordok összekapcsolása a kivonatoló táblából</li><li>Egyedül a Mapper csatlakozik</li></ul> | `hive.auto.confvert.join=true` | Nagyon gyors, de korlátozott |
| Egyesítési gyűjtők rendezése | Ha mindkét tábla: <ul><li>Azonos sorrend</li><li>Azonos</li><li>Csatlakozás a rendezett/gyűjtő oszlophoz</li></ul> | Minden folyamat: <ul><li>Egy gyűjtő beolvasása az egyes táblákból</li><li>A legkisebb értékű sort dolgozza fel</li></ul> | `hive.auto.convert.sortmerge.join=true` | Rendkívül hatékony |

#### <a name="execution-engine-optimizations"></a>Végrehajtó motor optimalizálása

További javaslatok a kaptár-végrehajtó motor optimalizálásához:

| Beállítás | Ajánlott | HDInsight alapértelmezett értéke |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | Igaz = biztonságosabb, lassabb; hamis = gyorsabb | false |
| `tez.am.resource.memory.mb` | 4 GB felső korlát a legtöbb | Automatikusan hangolt |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20 000 |

## <a name="apache-pig-optimization"></a>Apache Pig-optimalizálás

Az [Apache Pig](https://pig.apache.org/) tulajdonságai módosíthatók a AMBARI webes kezelőfelületéről a Pig-lekérdezések finomhangolásához. A Pig tulajdonságainak módosítása a Ambari közvetlenül módosítja a Pig tulajdonságait a `/etc/pig/2.4.2.0-258.0/pig.properties` fájlban.

1. A Pig tulajdonságainak módosításához navigáljon a Pig **konfigurációk** lapra, majd bontsa ki a **speciális Pig-Properties** panelt.

1. Keresse meg a módosítani kívánt tulajdonság értékét, és írja meg a megjegyzéseit, és módosítsa azt.

1. Az új érték mentéséhez kattintson a **Save (Mentés** ) gombra az ablak jobb felső részén. Bizonyos tulajdonságok esetében előfordulhat, hogy a szolgáltatás újraindítása szükséges.

    ![Speciális Apache Pig-tulajdonságok](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> A munkamenet-szintű beállítások felülbírálják a tulajdonság értékeit a `pig.properties` fájlban.

### <a name="tune-execution-engine"></a>Végrehajtó motor hangolása

A Pig-parancsfájlok végrehajtásához két végrehajtó motor érhető el: MapReduce és TEZ. A TEZ egy optimalizált motor, amely jóval gyorsabb, mint a MapReduce.

1. A végrehajtási motor módosításához a **speciális Pig-Properties** ablaktáblán keresse meg a `exectype`tulajdonságot.

1. Az alapértelmezett érték a **MapReduce**. Módosítsa a **TEZ**.

### <a name="enable-local-mode"></a>Helyi mód engedélyezése

A Kaptárhoz hasonlóan a helyi mód is a feladatok gyorsabb, viszonylag kis mennyiségű adattal való felgyorsítására szolgál.

1. A helyi mód engedélyezéséhez állítsa a `pig.auto.local.enabled` **igaz**értékre. Az alapértelmezett értéke FALSE (hamis).

1. A `pig.auto.local.input.maxbytes` tulajdonság értékénél kisebb bemeneti adatmérettel rendelkező feladatok kisebb feladatoknak tekintendők. Az alapértelmezett érték 1 GB.

### <a name="copy-user-jar-cache"></a>Felhasználói jar-gyorsítótár másolása

A Pig a UDF által igényelt JAR-fájlokat egy elosztott gyorsítótárba másolja, hogy azok elérhetők legyenek a feladatok csomópontjai számára. Ezek a tégelyek nem változnak gyakran. Ha engedélyezve van, a `pig.user.cache.enabled` beállítás lehetővé teszi, hogy a tégelyek egy gyorsítótárba kerüljenek, hogy újra felhasználhassa őket az ugyanazon felhasználó által futtatott feladatokhoz. Ez kisebb növekedést eredményez a feladatok teljesítményében.

1. Az engedélyezéshez állítsa a `pig.user.cache.enabled` igaz értékre. Az alapértelmezett érték a false.

1. A gyorsítótárazott tégelyek alapelérési útjának megadásához állítsa a `pig.user.cache.location` az alapelérési útra. A mező alapértelmezett értéke: `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>A teljesítmény optimalizálása a memória beállításaival

A következő memória-beállítások segíthetnek a Pig-parancsfájlok teljesítményének optimalizálásában.

* `pig.cachedbag.memusage`: a táska számára lefoglalt memória mennyisége. A táska a rekordok gyűjteménye. A rekord mezők rendezett halmaza, és a mező egy adat. Ha a zsákban lévő adatmennyiség meghaladja a lefoglalt memóriát, a lemezre kerül. Az alapértelmezett érték 0,2, amely a rendelkezésre álló memória 20 százalékát jelöli. Ez a memória az alkalmazás összes csomagjai között meg van osztva.

* `pig.spill.size.threshold`: a kiömlött méretnél nagyobb zsákok (bájtban) a lemezre kerülnek. Az alapértelmezett érték 5 MB.

### <a name="compress-temporary-files"></a>Ideiglenes fájlok tömörítése

A Pig ideiglenes fájlokat hoz létre a feladatok végrehajtása során. Az ideiglenes fájlok tömörítése a fájlok lemezre való olvasásakor vagy írásakor a teljesítmény növekedését eredményezi. A következő beállítások használhatók az ideiglenes fájlok tömörítésére.

* `pig.tmpfilecompression`: true (igaz) érték esetén engedélyezi az ideiglenes fájlok tömörítését. Az alapértelmezett értéke FALSE (hamis).

* `pig.tmpfilecompression.codec`: az ideiglenes fájlok tömörítéséhez használandó tömörítési kodek. Az ajánlott tömörítési kodekek a [LZO](https://www.oberhumer.com/opensource/lzo/) és az alacsonyabb CPU-kihasználtságot használják.

### <a name="enable-split-combining"></a>Felosztott egyesítés engedélyezése

Ha ez a beállítás engedélyezve van, a kis méretű fájlok kevesebb leképezési feladathoz vannak egyesítve. Ez javítja a sok kis fájllal rendelkező feladatok hatékonyságát. Az engedélyezéshez állítsa a `pig.noSplitCombination` igaz értékre. Az alapértelmezett értéke FALSE (hamis).

### <a name="tune-mappers"></a>Leképezések hangolása

A leképezések számát a `pig.maxCombinedSplitSize`tulajdonság módosításával szabályozhatja. Ezzel a beállítással adható meg, hogy a rendszer hogyan dolgozza fel az adatok méretét egyetlen térképes feladattal. Az alapértelmezett érték a fájlrendszer alapértelmezett blokkjának mérete. Az érték növelése a Mapper-feladatok számának csökkenését eredményezi.

### <a name="tune-reducers"></a>Szűkítők hangolása

A szűkítők számát a `pig.exec.reducers.bytes.per.reducer`paraméter alapján számítja ki a rendszer. A paraméter a redukáló által feldolgozott bájtok számát adja meg, alapértelmezés szerint 1 GB. A szűkítők maximális számának korlátozásához állítsa a `pig.exec.reducers.max` tulajdonságot alapértelmezés szerint a 999 értékre.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Apache HBase-optimalizálás a Ambari webes felhasználói felületén

Az [Apache HBase](https://hbase.apache.org/) -konfiguráció a **HBase konfigurációk** lapról módosul. A következő szakaszok ismertetik a HBase teljesítményét befolyásoló fontos konfigurációs beállításokat.

### <a name="set-hbase_heapsize"></a>HBASE_HEAPSIZE beállítása

A HBase halom mérete határozza meg a *régiók* *és főkiszolgálók* által megabájtban használt maximális halom mennyiségét. Az alapértelmezett érték 1 000 MB. Ezt be kell hangolni a fürt számítási feladataihoz.

1. A módosításhoz navigáljon a HBase **konfigurációk** lap **speciális HBase-env** paneljére, és keresse meg a `HBASE_HEAPSIZE` beállítást.

1. Módosítsa az alapértelmezett értéket 5 000 MB-ra.

    ![Apache Ambari HBase memória heapsize](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Olvasási és nagy terhelések optimalizálása

A következő konfigurációk fontosak a nagy teljesítményű számítási feladatok teljesítményének javításához.

#### <a name="block-cache-size"></a>Gyorsítótár méretének letiltása

A blokk gyorsítótára az olvasási gyorsítótár. A méretet a `hfile.block.cache.size` paraméter szabályozza. Az alapértelmezett érték 0,4, amely a teljes régióbeli kiszolgáló memóriájának 40 százalékát képezi. Minél nagyobb a blokk gyorsítótárának mérete, annál gyorsabb lesz a véletlenszerű olvasás.

1. A paraméter módosításához navigáljon a **Beállítások** lapra a HBase **konfigurációk** lapon, majd keresse meg az **olvasási pufferek számára lefoglalt RegionServer%** -át.

    ![Apache HBase-gyorsítótár mérete](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Az érték módosításához kattintson a **Szerkesztés** ikonra.

#### <a name="memstore-size"></a>Memstore mérete

A rendszer az összes módosítást a *Memstore*nevezett memória-pufferben tárolja. Ez növeli a lemezre egyetlen művelet során írható összes adatmennyiséget, és a későbbiekben a legutóbbi módosítások elérését is felgyorsítja. A Memstore méretét a következő két paraméter határozza meg:

* `hbase.regionserver.global.memstore.UpperLimit`: annak a Memstore a maximális százalékos arányát határozza meg, amelyet a kombinált kiszolgáló használhat.

* `hbase.regionserver.global.memstore.LowerLimit`: meghatározza, hogy a Memstore együtt használható-e a régió-kiszolgáló minimális százalékos aránya.

A véletlenszerű olvasások optimalizálásához csökkentheti a Memstore alsó és felső korlátját.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>A lemezről való vizsgálatkor beolvasott sorok száma

A `hbase.client.scanner.caching` beállítás határozza meg a lemezről beolvasott sorok számát, ha a `next` metódust egy képolvasó hívja meg.  Az alapértelmezett érték 100. Minél nagyobb a szám, annál kevesebb az ügyfél és a régió-kiszolgáló közötti távoli hívások száma, ami gyorsabb vizsgálatot eredményez. Ez azonban növeli a memória terhelését is az ügyfélen.

![Apache HBase beolvasott sorok száma](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Ne állítsa be úgy az értéket, hogy a képolvasó következő metódusának hívása közötti idő nagyobb legyen, mint a képolvasó időkorlátja. A képolvasó időtúllépési időtartamát a `hbase.regionserver.lease.period` tulajdonság határozza meg.

### <a name="optimize-write-heavy-workloads"></a>Írási-nagy számítási feladatok optimalizálása

A következő konfigurációk fontosak a nagy mennyiségű számítási feladatok teljesítményének javításához.

#### <a name="maximum-region-file-size"></a>A régió maximális fájlmérete

A HBase belső fájlformátumban tárolja az adattárolást, amelynek neve *HFile*. A tulajdonság `hbase.hregion.max.filesize` a régió egyetlen HFile méretét határozza meg.  Ha egy régió összes HFiles összege meghaladja ezt a beállítást, a régió két régióra oszlik.

![Apache HBase HRegion max. filesize](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Minél nagyobb a régió fájlmérete, annál kisebb a felosztások száma. Megnövelheti a fájlméretet úgy, hogy meghatározza a maximális írási teljesítményt eredményező értéket.

#### <a name="avoid-update-blocking"></a>A frissítés blokkolásának elkerülése

* A (z) `hbase.hregion.memstore.flush.size` tulajdonság határozza meg azt a méretet, amelyen a Memstore kiürítése lemezre történik. Az alapértelmezett méret 128 MB.

* A HBase-régió blokk szorzóját a `hbase.hregion.memstore.block.multiplier`határozza meg. Az alapértelmezett érték a 4. A maximálisan engedélyezett érték 8.

* A HBase letiltja a frissítéseket, ha a Memstore (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bájt.

    A kiürítési méret és a szorzó alapértelmezett értékeivel a frissítések le lesznek tiltva, ha a Memstore 128 * 4 = 512 MB méretű. A frissítési blokkolások számának csökkentéséhez növelje `hbase.hregion.memstore.block.multiplier`értékét.

![Apache HBase region Block szorzó](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Memstore méretének meghatározása

A Memstore méretét a `hbase.regionserver.global.memstore.UpperLimit` és a `hbase.regionserver.global.memstore.LowerLimit` paraméterek határozzák meg. Ha ezeket az értékeket úgy állítja be, hogy azok egyenlőek legyenek az írások során (ami még gyakoribb kiürítést is eredményez), és növeli az írási teljesítményt.

### <a name="set-memstore-local-allocation-buffer"></a>Memstore helyi foglalási pufferének beállítása

A Memstore helyi kiosztási puffer használatát a `hbase.hregion.memstore.mslab.enabled`tulajdonság határozza meg. Ha engedélyezve van (igaz), ez megakadályozza a halom töredezettségét a nehéz írási művelet során. Az alapértelmezett érték: true.

![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes FELÜLETtel](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
