---
title: RequestBodyTooLarge hiba Apache Spark alkalmazásból – Azure HDInsight
description: NativeAzureFileSystem ... A RequestBodyTooLarge megjelenik a Apache Spark streaming alkalmazás naplójában az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894402"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge "megjelenő Apache Spark streaming app log in HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A hiba: `NativeAzureFileSystem ... RequestBodyTooLarge` megjelenik az illesztőprogram-naplóban egy Apache Spark streaming-alkalmazáshoz.

## <a name="cause"></a>Ok

A Spark-Eseménynapló fájlja valószínűleg a WASB fájl hosszának korlátját éri el.

A Spark 2,3-ben minden Spark-alkalmazás létrehoz egy Spark-Eseménynapló-fájlt. Egy Spark streaming-alkalmazás Spark-Eseménynapló-fájlja folyamatosan növekszik, amíg az alkalmazás fut. Napjainkban a WASB egy fájlja 50000 blokkos korláttal rendelkezik, és az alapértelmezett blokk mérete 4 MB. Az alapértelmezett konfigurációban tehát a maximális fájlméret 195 GB. Az Azure Storage azonban megnövelte a maximális blokkolási méretet 100 MB-ra, ami gyakorlatilag egyetlen fájlra korlátozza a 4,75 TB-ot. További információkért lásd [a blob Storage skálázhatósági és teljesítménybeli céljait](../../storage/blobs/scalability-targets.md)ismertető témakört.

## <a name="resolution"></a>Felbontás

Ehhez a hibához három megoldás érhető el:

* Növelje a blokk méretét akár 100 MB-ra. A Ambari felhasználói felületén módosítsa a HDFS konfigurációs tulajdonságot `fs.azure.write.request.size` (vagy hozza létre `Custom core-site` szakaszban). Állítsa a tulajdonságot nagyobb értékre, például: 33554432. Mentse a frissített konfigurációt, és indítsa újra az érintett összetevőket.

* A Spark-streaming feladatok rendszeres leállítása és újraküldése.

* A Spark-eseménynaplók tárolására használja a HDFS. A HDFS for Storage használata a fürtök skálázása vagy az Azure-frissítések során felmerülő Spark-események elvesztését eredményezheti.

    1. Módosítsa `spark.eventlog.dir` és `spark.history.fs.logDirectory` Ambari felhasználói felületén keresztül:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Könyvtárak létrehozása a HDFS-on:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Indítsa újra az összes érintett szolgáltatást a Ambari felhasználói felületén keresztül.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiók a felhasználói élmény javításához az Azure-Közösség és a megfelelő erőforrások összekapcsolásával: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
