---
title: Halom-memóriaképek engedélyezése Apache Hadoop-szolgáltatásokhoz a HDInsight – Azure
description: A Linux-alapú HDInsight-fürtökön lévő Apache Hadoop-szolgáltatások számára lehetővé teheti a többszörös memóriaképek hibakeresését és elemzését.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 9134eb6922b0ed37bbe6051b138da2c7c082b175
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75658797"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Halom-memóriaképek engedélyezése Apache Hadoop-szolgáltatásokhoz Linux-alapú HDInsight

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

A kupac-memóriaképek az alkalmazás memóriájának pillanatképét tartalmazzák, beleértve a változók értékeit a memóriakép létrehozásakor. Így hasznosak lehetnek a futásidőben felmerülő problémák diagnosztizálásához.

## <a name="services"></a>Szolgáltatások

A következő szolgáltatásokhoz is engedélyezheti a heap-memóriaképeket:

* **Apache hcatalog** – Tempelton
* **Apache kaptár** -hiveserver2, metaadattár, derbyserver
* **MapReduce** – jobhistoryserver
* **Apache-fonal** – erőforráskezelő, nodemanager, timelineserver
* **Apache hdfs** -datanode, secondarynamenode, namenode

A térképhez is engedélyezheti a kupacbeli memóriaképeket, és csökkentheti a HDInsight által futtatott folyamatokat.

## <a name="understanding-heap-dump-configuration"></a>A halom-memóriakép konfigurációjának ismertetése

A heap-memóriaképek a szolgáltatás indításakor a JVM (más néven a kiválasztások vagy paraméterek) átadásával engedélyezhetők. A legtöbb [Apache Hadoop](https://hadoop.apache.org/) szolgáltatás esetében módosíthatja a szolgáltatás elindításához használt rendszerhéj-parancsfájlt, hogy átadja ezeket a beállításokat.

Az egyes parancsfájlokban a **\*\_** exportálását választja, amely a JVM átadott beállításokat tartalmazza. A **Hadoop-env.sh** parancsfájlban például a `export HADOOP_NAMENODE_OPTS=` kezdetű sor tartalmazza a NameNode szolgáltatás beállításait.

A leképezés és a folyamatok csökkentése némileg eltér, mivel ezek a műveletek a MapReduce szolgáltatás alárendelt folyamatai. A rendszer minden egyes térképet vagy csökkentési folyamatot egy gyermek tárolóban futtat, és két bejegyzést tartalmaz, amelyek tartalmazzák a JVM beállításait. Mindkettő a **mapred-site. xml fájlban**található:

* **MapReduce. admin. map. Child. Java. dönt**
* **MapReduce. admin. csökkentse. Child. Java. dönt**

> [!NOTE]  
> Javasoljuk, hogy az [Apache Ambari](https://ambari.apache.org/) használatával módosítsa a szkripteket és a mapred-site. xml-beállításokat is, mivel a Ambari kezeli a módosításokat a fürt csomópontjai között. Az [Apache Ambari használata](#using-apache-ambari) című szakaszban talál konkrét lépéseket.

### <a name="enable-heap-dumps"></a>Halomürítések engedélyezése

A következő beállítás lehetővé teszi a halom memóriaképét, ha működése OutOfMemoryError történik:

    -XX:+HeapDumpOnOutOfMemoryError

A **+** jelzi, hogy ez a beállítás engedélyezve van. Ez a beállítás alapértelmezés szerint le van tiltva.

> [!WARNING]  
> A HDInsight alapértelmezés szerint nincsenek engedélyezve a Hadoop-szolgáltatásokhoz, mivel a memóriaképek nagy méretűek lehetnek. Ha engedélyezi őket a hibaelhárításhoz, ne felejtse el letiltani őket, ha a probléma reprodukálása és a memóriakép fájljainak gyűjtése megtörtént.

### <a name="dump-location"></a>Memóriakép helye

A memóriakép fájljának alapértelmezett helye az aktuális munkakönyvtár. A következő beállítással szabályozhatja a fájl tárolási helyét:

    -XX:HeapDumpPath=/path

A `-XX:HeapDumpPath=/tmp` használata például a memóriaképek tárolását okozza a/tmp könyvtárban.

### <a name="scripts"></a>Parancsprogramok

**Működése OutOfMemoryError** esetén is aktiválhat parancsfájlt. Például egy értesítés elindításával megtudhatja, hogy a hiba történt. A következő kapcsoló használatával aktiválhat egy parancsfájlt egy __működése OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Mivel Apache Hadoop egy elosztott rendszer, a használt parancsfájlokat a fürt összes olyan csomópontján el kell helyezni, amelyen a szolgáltatás fut.
> 
> A parancsfájlnak olyan helyen kell lennie, amelyet a szolgáltatás által futtatott fiók is elérhet, és meg kell adnia a végrehajtás engedélyeit. Előfordulhat például, hogy a parancsfájlokat a `/usr/local/bin`ban szeretné tárolni, és a `chmod go+rx /usr/local/bin/filename.sh` használatával adja meg az olvasási és végrehajtási engedélyeket.

## <a name="using-apache-ambari"></a>Az Apache Ambari használata

Egy szolgáltatás konfigurációjának módosításához kövesse az alábbi lépéseket:

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net`, ahol a `CLUSTERNAME` a fürt neve.

2. A bal oldali lista használatával válassza ki a módosítani kívánt szolgáltatási területét. Például: **HDFS**. A középső területen válassza a **konfigurációk** lapot.

    ![A Ambari web képe a HDFS configs lapon kiválasztva](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. A **szűrő...** bejegyzés **használatával adja meg**a kilépést. Csak a szöveget tartalmazó elemek jelennek meg.

    ![Apache Ambari-konfiguráció szűrt listája](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Keresse meg a **\*\_** jelölje be azt a szolgáltatást, amely számára engedélyezni szeretné a heap-memóriaképeket, és adja hozzá az engedélyezni kívánt beállításokat. Az alábbi képen a `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` a **HADOOP\_NAMENODE\_** a következő bejegyzést adta meg:

    ![Apache Ambari Hadoop-namenode – dönt](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Ha a térképhez vagy a gyermek-feldolgozási folyamat csökkentéséhez a heap-memóriaképek engedélyezve vannak, keresse meg a **MapReduce. admin. map. Child. Java. eldöntés** és a **MapReduce. admin. csökkentse. Child. Java. dönt**.

    A módosítások mentéséhez használja a **Save (Mentés** ) gombot. Megadhat egy rövid megjegyzést, amely leírja a módosításokat.

5. A módosítások alkalmazása után az **Újraindítás kötelező** ikon egy vagy több szolgáltatás mellett jelenik meg.

    ![újraindítás szükséges ikon és Újraindítás gomb](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Válassza ki az összes olyan szolgáltatást, amely újraindítást igényel, és a **szolgáltatási műveletek** gomb használatával **kapcsolja be a karbantartási módot**. A karbantartási mód megakadályozza, hogy a riasztások a szolgáltatásból való indításkor legyenek generálva.

    ![A HDI karbantartási mód menüjének bekapcsolása](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Miután engedélyezte a karbantartási módot, a szolgáltatás **Újraindítási gombjának** használatával **indítsa újra az összes érvényben** lévőt

    ![Apache Ambari – az összes érintett bejegyzés újraindítása](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > Az **Újraindítási** gomb bejegyzései eltérőek lehetnek más szolgáltatások esetében.

8. A szolgáltatások újraindítása után a **szolgáltatás műveletei** gomb használatával **kapcsolja ki a karbantartási módot**. Ezzel a Ambari folytathatja a szolgáltatáshoz tartozó riasztások figyelését.