---
title: Apache Hive használata a PowerShell-lel a HDInsight-Azure-ban
description: Apache Hive-lekérdezések futtatása a PowerShell használatával Apache Hadoop az Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: deaa934b257fab74830d75e308a283e7608dc590
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552593"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Apache Hive lekérdezések futtatása a PowerShell használatával

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ebből a dokumentumból megmutatjuk, hogyan használhatja a Azure PowerShellt Apache Hive lekérdezések futtatására egy Apache Hadoop a HDInsight-fürtön.

> [!NOTE]  
> Ez a dokumentum nem tartalmazza a példákban használt HiveQL-utasítások részletes leírását. Az ebben a példában használt HiveQL kapcsolatos további információkért tekintse meg a [Apache Hive Apache Hadoop használata a HDInsight](hdinsight-use-hive.md)-ben című témakört.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).

* A PowerShell az [modul](https://docs.microsoft.com/powershell/azure/overview) telepítve van.

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

Azure PowerShell olyan *parancsmagokat* biztosít, amelyek lehetővé teszik a kaptár-lekérdezések távoli futtatását a HDInsight. Belsőleg a parancsmagok REST-hívásokat végeznek a HDInsight-fürt [webhcaten](https://cwiki.apache.org/confluence/display/Hive/WebHCat) .

A következő parancsmagok használhatók a kaptár-lekérdezések távoli HDInsight-fürtben való futtatásakor:

* `Connect-AzAccount`: Azure PowerShell hitelesíti az Azure-előfizetését.
* `New-AzHDInsightHiveJobDefinition`: a megadott HiveQL utasítások használatával hoz létre egy *feladatdefiníció-definíciót* .
* `Start-AzHDInsightJob`: elküldi a HDInsight, és elindítja a feladatot. A rendszer egy *feladatütemezés* visszaadását adja vissza.
* `Wait-AzHDInsightJob`: a feladattípust használja a feladatok állapotának vizsgálatához. Megvárja, amíg a feladatok befejeződik, vagy túllépi a várakozási időt.
* `Get-AzHDInsightJobOutput`: a feladatok kimenetének lekérésére szolgál.
* `Invoke-AzHDInsightHiveJob`: a HiveQL utasítások futtatásához használatos. Ez a parancsmag blokkolja a lekérdezés befejeződését, majd visszaadja az eredményeket.
* `Use-AzHDInsightCluster`: beállítja a `Invoke-AzHDInsightHiveJob` parancshoz használandó aktuális fürtöt.

A következő lépések bemutatják, hogyan használhatja ezeket a parancsmagokat a feladatok futtatásához a HDInsight-fürtben:

1. Szerkesztő használatával mentse a következő kódot `hivejob.ps1`ként.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Nyisson meg egy új **Azure PowerShell** parancssort. Módosítsa a könyvtárakat a `hivejob.ps1` fájl helyére, majd futtassa a következő parancsot a szkript futtatásához:

        .\hivejob.ps1

    A parancsfájl futtatásakor meg kell adnia a fürt nevét és a HTTPS/fürt rendszergazdai fiókjának hitelesítő adatait. A rendszer kérheti, hogy jelentkezzen be az Azure-előfizetésbe.

3. Amikor a feladatok befejeződik, az a következő szöveghez hasonló adatokat ad vissza:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Ahogy korábban említettük, `Invoke-Hive` használható egy lekérdezés futtatására, és megvárni a válaszra. A következő parancsfájl használatával megtekintheti, hogyan működik a Meghívási struktúra:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A kimenet a következő szöveghez hasonlít:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > A hosszú HiveQL lekérdezések esetében használhatja a Azure PowerShell **itt-Strings** parancsmagot vagy a HiveQL parancsfájl-fájlokat. A következő kódrészlet azt mutatja be, hogyan használható a `Invoke-Hive` parancsmag egy HiveQL parancsfájl futtatásához. A HiveQL parancsfájlt fel kell tölteni a wasbs://-be.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > További információ az **itt-sztringekről**: a <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">Windows PowerShell használata itt – karakterláncok</a>.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a rendszer nem ad vissza információt a feladatok befejezésekor, tekintse meg a hibák naplóit. Ha meg szeretné tekinteni a feladattal kapcsolatos hibákat, adja hozzá a következőt a `hivejob.ps1` fájl végéhez, mentse, majd futtassa újra.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Ez a parancsmag a STDERR írt adatokat adja vissza a feladatok feldolgozásakor.

## <a name="summary"></a>Összefoglalás

Amint láthatja, a Azure PowerShell egyszerű módszert kínál a kaptár-lekérdezések futtatására egy HDInsight-fürtben, figyelheti a feladat állapotát, és lekérheti a kimenetet.

## <a name="next-steps"></a>Következő lépések

Általános információk a HDInsight-struktúrával kapcsolatban:

* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)

További információ a Hadoop a HDInsight-ben való használatával kapcsolatos egyéb módszerekről:

* [A MapReduce használata a HDInsight Apache Hadoop használatával](hdinsight-use-mapreduce.md)
