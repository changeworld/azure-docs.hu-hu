---
title: Kaptár-lekérdezések optimalizálása az Azure HDInsight
description: Ez a cikk azt ismerteti, hogyan optimalizálhatja Apache Hive Hadoop-lekérdezéseit a HDInsight-ben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 144d51d08a61526ec0f183a63e1fdf5658136293
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272330"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Apache Hive lekérdezések optimalizálása az Azure HDInsight

Az Azure HDInsight számos különböző típusú fürttel és technológiával rendelkezhet Apache Hive lekérdezések futtatásával. A HDInsight-fürt létrehozásakor válassza ki a megfelelő fürtöt a számítási feladatok teljesítményének optimalizálása érdekében.

Válassza például az **interaktív lekérdezési** fürt típusa lehetőséget az ad hoc, interaktív lekérdezések optimalizálásához. Válassza az Apache **Hadoop** -fürt típusa lehetőséget a Batch-folyamatként használt kaptár-lekérdezések optimalizálásához. A **Spark** -és **HBase** -fürtök a kaptár-lekérdezéseket is futtathatják. A különböző HDInsight-fürtökön futó kaptár-lekérdezések futtatásával kapcsolatos további információkért lásd: [Mi az a Apache Hive és a HiveQL az Azure HDInsight?](hadoop/hdinsight-use-hive.md)című témakörben.

A Hadoop-HDInsight-fürtök nem alapértelmezett teljesítményre vannak optimalizálva. Ez a cikk a kaptárak leggyakoribb teljesítmény-optimalizálási módszereit ismerteti, amelyeket a lekérdezésekre alkalmazhat.

## <a name="scale-out-worker-nodes"></a>Munkavégző csomópontok vertikális felskálázása

A munkavégző csomópontok számának növelése egy HDInsight-fürtben lehetővé teszi, hogy a munka több leképezést és szűkítőt is futtasson párhuzamosan. A HDInsight kétféleképpen növelheti a méretezési lehetőségeket:

* A fürt létrehozásakor megadhatja a munkavégző csomópontok számát a Azure Portal, a Azure PowerShell vagy a parancssori felület használatával.  További információ: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md). Az alábbi képernyőfelvételen a Azure Portal munkavégző csomópontjának konfigurációja látható:
  
    ![Azure Portal fürt méretének csomópontjai](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png "scaleout_1")

* A létrehozást követően szerkesztheti a munkavégző csomópontok számát úgy, hogy az újbóli létrehozása nélkül is kibővítse a fürtöt:

    ![Azure Portal méretezési fürt mérete](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png "scaleout_2")

További információ a HDInsight skálázásáról: HDInsight- [fürtök méretezése](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Az Apache TEZ használata a Térkép csökkentése helyett

Az [Apache TEZ](https://tez.apache.org/) egy alternatív végrehajtási motor a MapReduce motorhoz. A Linux-alapú HDInsight-fürtök alapértelmezés szerint engedélyezve vannak a TEZ.

![HDInsight Apache TEZ – áttekintési diagram](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

A TEZ az alábbiak miatt gyorsabb:

* **Irányított aciklikus gráf (Dag) végrehajtása egyetlen feladatokként a MapReduce motorban**. A DAG megköveteli, hogy az egyes Mapper-készleteket egy szűkítő-készlet követi. Ez több MapReduce-feladat kikapcsolását okozza az egyes struktúra-lekérdezésekhez. A TEZ nem rendelkezik ilyen korlátozással, és feldolgozhatja a komplex DAG-t egyetlen feladatként, így minimalizálva a feladat indítási terhelését.
* **Elkerüli a szükségtelen írásokat**. Több feladat is feldolgozza ugyanazt a kaptár-lekérdezést a MapReduce motorban. Az egyes MapReduce-feladatok kimenete a köztes adatok HDFS van írva. Mivel a TEZ lekicsinyíti a feladatok számát az egyes struktúra-lekérdezésekhez, el tudja kerülni a szükségtelen írásokat.
* Az **indítási késések csökkentése**. A TEZ jobban képes az indítási késleltetés minimalizálására azáltal, hogy csökkenti az elindításához szükséges leképezések számát, és a teljes optimalizálást is javítja.
* **Tárolók újrafelhasználása**. Amikor lehetséges, a TEZ képes a tárolók újbóli felhasználására, hogy a tárolók indítása miatti késések csökkenjenek.
* **Folyamatos optimalizálási technikák**. A fordítási fázisban hagyományosan optimalizálás történt. A bemenetekkel kapcsolatban azonban további információk érhetők el, amelyek lehetővé teszik a jobb optimalizálást a futtatókörnyezet során. A TEZ folyamatos optimalizálási technikákat használ, amelyek lehetővé teszik, hogy a csomagot a futásidejű fázisba optimalizálja.

További információ ezekről a fogalmakról: [Apache TEZ](https://tez.apache.org/).

A kaptár lekérdezési TEZ a következő SET paranccsal engedélyezheti a lekérdezés előjavításával:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Struktúra particionálás

Az I/O műveletek a kaptár-lekérdezések futtatásának legnagyobb teljesítménybeli szűk keresztmetszetét jelentik. A teljesítmény javítható, ha az olvasni kívánt adat mennyisége csökkenthető. Alapértelmezés szerint a kaptár lekérdezi a teljes kaptár-táblákat. Az olyan lekérdezéseknél azonban, amelyeknek csak kis mennyiségű adat vizsgálatára van szükségük (például szűréses lekérdezések), ez a viselkedés szükségtelen terhelést okoz. A kaptár particionálása lehetővé teszi, hogy a kaptár-lekérdezések csak a szükséges mennyiségű adattal férhessenek hozzá a kaptárak tábláiban.

A struktúra particionálását úgy valósítja meg, hogy átrendezi a nyers adattárakat új címtárakba. Minden partíció saját könyvtárral rendelkezik. A particionálást a felhasználó határozza meg. Az alábbi ábrán egy struktúra-táblázat particionálását mutatjuk be az oszlop *év*szerint. A rendszer minden évben létrehoz egy új könyvtárat.

![HDInsight Apache Hive particionálás](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

Néhány particionálási szempont:

* A **partíciók particionálása** a csak néhány értékkel rendelkező oszlopokban nem lehet néhány partíciót okozni. Például a nemek szerinti particionálás csak két partíciót hoz létre (a hím és a nőstény), így csak a késést legfeljebb a fele csökkenti.
* Ne hozzon **át partíciót** – a másik végletnél – a partíció létrehozása egyedi értékkel rendelkező oszlopon (például felhasználóazonosító) több partíciót okoz. A több mint partíció a fürt namenode nagy terhelést okoz, mivel a nagy számú könyvtárat kell kezelnie.
* Az **adatdöntés elkerülése** – a particionálási kulcs kiválasztása bölcs módon, hogy az összes partíció mérete is legyen. Előfordulhat például, hogy az *állapot* oszlop particionálásakor az adateloszlás elferdíthető. Mivel Kalifornia állam csaknem 30x a Vermontban, a partíció mérete potenciálisan elferdíthető, és a teljesítmény rendkívül eltérő lehet.

Partíciós tábla létrehozásához használja a *particionált by* záradékot:

```sql
CREATE TABLE lineitem_part
      (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
PARTITIONED BY(L_SHIPDATE STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE;
```

A particionált tábla létrehozása után létrehozhat statikus particionálást vagy dinamikus particionálást.

* A **statikus particionálás** azt jelenti, hogy a megfelelő címtárakban már vannak a szilánkokra osztott adathalmazok. A statikus partíciók esetében manuálisan adja hozzá a kaptár-partíciókat a címtár helye alapján. A következő kódrészlet egy példa.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* A **dinamikus particionálás** azt jelenti, hogy azt szeretné, hogy a kaptár automatikusan hozzon létre partíciókat. Mivel már létrehozta a particionálási táblázatot az előkészítési táblából, mindössze annyit kell tennie, hogy az adatok beillesztése a particionált táblába:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

További információ: [particionált táblák](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>ORCFile formátum használata

A struktúra a különböző fájlformátumokat támogatja. Például:

* **Text (szöveg**): az alapértelmezett fájlformátum, és a legtöbb esetben működik.
* **Avro**: az együttműködési helyzetekben jól működik.
* **Ork/parketta**: a legmegfelelőbb a teljesítményhez.

Az ork (optimalizált sor oszlopos) formátuma igen hatékony módszer a struktúra-adatok tárolására. Más formátumokhoz képest az ork a következő előnyökkel jár:

* összetett típusok támogatása, beleértve a DateTime és az összetett és a félig strukturált típusokat.
* akár 70%-os tömörítés.
* minden 10 000 sort indexel, amely lehetővé teszi a sorok kihagyását.
* a futásidejű végrehajtás jelentős csökkenése.

Az ork formátum engedélyezéséhez először létre kell hoznia egy táblázatot az *ork-ként tárolt*záradékkal:

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

Ezután szúrja be az adatait az ork táblába az előkészítési táblából. Például:

```sql
INSERT INTO TABLE lineitem_orc
SELECT L_ORDERKEY as L_ORDERKEY,
         L_PARTKEY as L_PARTKEY ,
         L_SUPPKEY as L_SUPPKEY,
         L_LINENUMBER as L_LINENUMBER,
         L_QUANTITY as L_QUANTITY,
         L_EXTENDEDPRICE as L_EXTENDEDPRICE,
         L_DISCOUNT as L_DISCOUNT,
         L_TAX as L_TAX,
         L_RETURNFLAG as L_RETURNFLAG,
         L_LINESTATUS as L_LINESTATUS,
         L_SHIPDATE as L_SHIPDATE,
         L_COMMITDATE as L_COMMITDATE,
         L_RECEIPTDATE as L_RECEIPTDATE,
         L_SHIPINSTRUCT as L_SHIPINSTRUCT,
         L_SHIPMODE as L_SHIPMODE,
         L_COMMENT as L_COMMENT
FROM lineitem;
```

További információ az ork formátumról a [Apache Hive nyelvi kézikönyvben](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC)olvasható.

## <a name="vectorization"></a>Vektorizációt

A vektorizációt lehetővé teszi, hogy a kaptár egyszerre több sorba dolgozza fel az 1024-es sorok feldolgozását. Ez azt jelenti, hogy az egyszerű műveletek végrehajtása gyorsabban történik, mert kevesebb belső kód futtatására van szükség.

A vektorizációt-előtag a következő beállítással engedélyezhető a kaptár-lekérdezésben:

```hive
set hive.vectorized.execution.enabled = true;
```

További információ: a [vektoros lekérdezés végrehajtása](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Egyéb optimalizálási módszerek

Több optimalizálási módszer is megvizsgálható, például:

* **Struktúra-gyűjtő:** olyan technika, amely lehetővé teszi, hogy a lekérdezési teljesítmény optimalizálása érdekében a nagy mennyiségű adat fürtbe vagy szegmentálásba kerüljön.
* **Csatlakozás optimalizálása:** a kaptár lekérdezés-végrehajtásának optimalizálása az összekapcsolások hatékonyságának növelése és a felhasználói célzások szükségességének csökkentése érdekében. További információ: [JOIN Optimization (csatlakozás optimalizálása](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization)).
* **Növelje a szűkítőket**.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben több általános kaptár-lekérdezés optimalizálási módszerét is megtanulta. További információt a következő cikkekben talál:

* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Repülési késleltetési adatelemzések elemzése a HDInsight interaktív lekérdezés használatával](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
* [Twitter-adataik elemzése a HDInsight Apache Hive használatával](hdinsight-analyze-twitter-data-linux.md)
