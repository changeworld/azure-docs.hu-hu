---
title: Mi a Apache Hadoop és a MapReduce – Azure HDInsight
description: Bevezetés a HDInsight, valamint a Apache Hadoop Technology stackbe és összetevőkbe.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 7e8dd69b7c58e090c30ea1aa59feddab610dd3c5
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78244875"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Mi Apache Hadoop az Azure HDInsight?

Az [Apache Hadoop](https://hadoop.apache.org/) volt a big data jellegű adatkészletek fürtökön végzett elosztott feldolgozásának, tárolásának és elemzésének eredeti nyílt forráskódú keretrendszere. A Hadoop-ökoszisztéma olyan kapcsolódó szoftvereket és segédprogramokat tartalmaz, mint például az Apache Hive, az Apache HBase, a Spark, a Kafka és sok más.

Az Azure HDInsight egy teljes körűen felügyelt, teljes spektrumú, nyílt forráskódú elemzési szolgáltatás a felhőben a vállalatok számára. Az Azure HDInsight-beli Apache Hadoop-fürt típusa lehetővé teszi a HDFS, a fonal-erőforrások kezelését, valamint egy egyszerű MapReduce programozási modellt, amellyel párhuzamosan dolgozhat és elemezheti a Batch-adatokat.

A HDInsight elérhető Hadoop Technology stack-összetevők megtekintéséhez tekintse meg a [HDInsight által elérhető összetevőket és verziókat](../hdinsight-component-versioning.md). További tudnivalók a HDInsightban használt Hadoopról [az Azure-szolgáltatások HDInsightra vonatkozó oldalán](https://azure.microsoft.com/services/hdinsight/) olvashatók.

## <a name="what-is-mapreduce"></a>Mi az a MapReduce?

Apache Hadoop MapReduce olyan szoftver-keretrendszer, amely nagy mennyiségű adat feldolgozására szolgáló feladatokat ír. A bemeneti adatok felosztása független adattömbökbe történik. Minden adathalmaz feldolgozása párhuzamosan történik a fürt csomópontjai között. A MapReduce-feladatok két függvényből állnak:

* **Mapper**: a bemeneti adatokat használja, elemzi (általában szűrési és rendezési műveletekkel), és rekordok (kulcs-érték párok) bocsát ki.

* **Szűkítő**: felhasználja a Mapper által kibocsátott rekordok, és olyan összegző műveletet hajt végre, amely létrehoz egy kisebb, kombinált eredményt a Mapper adataiból.

A következő ábrán látható egy alapszintű Word Count MapReduce-feladat példája:

 ![HDI.WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Ennek a feladatoknak a kimenete annak a száma, hogy az egyes szavak hányszor fordultak elő a szövegben.

* A Mapper a bemeneti szöveg minden sorát bemenetként veszi át, és megszakítja a szavakat. Egy kulcs/érték párokat bocsát ki, minden alkalommal, amikor egy szó bekövetkezik a szót, majd egy 1. A kimenet rendezése megtörténik, mielőtt elküldi azt a redukáló felé.
* A szűkítő összesíti ezeket az egyes szavakat, és egyetlen kulcs/érték párokat bocsát ki, amely a szót az előfordulások összege követve tartalmazza.

A MapReduce több nyelven is megvalósítható. A Java a leggyakoribb implementáció, és a jelen dokumentumban bemutató célokra szolgál.

## <a name="development-languages"></a>Fejlesztési nyelvek

A Java és a Java virtuális gép alapú nyelvek vagy keretrendszerek közvetlenül MapReduce-feladatokként futtathatók. A dokumentumban használt példa egy Java MapReduce alkalmazás. A nem Java nyelveken (például C#, Python vagy önálló végrehajtható fájlok) a **Hadoop streaminget**kell használniuk.

A Hadoop stream a leképező és a redukáló adatátvitelt az STDIN és az STDOUT fölé továbbítja. A leképező és a redukáló adatokat tartalmazó sort olvas az STDIN-ből, és a kimenetet az STDOUT értékre írja. A leképező és a szűkítő által beolvasott vagy kibocsátott soroknak a TAB karakterrel tagolt kulcs/érték párok formátumában kell szerepelniük:

    [key]/t[value]

További információ: [Hadoop streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Az Hadoop streaming HDInsight-vel való használatát bemutató példákért tekintse meg a következő dokumentumot:

* [MapReduce C# -feladatok fejlesztése](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Következő lépések

* [Apache Hadoop-fürt létrehozása a HDInsight-ben](apache-hadoop-linux-create-cluster-get-started-portal.md)
