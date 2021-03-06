---
title: Tárolási kivétel az Azure HDInsight-beli kapcsolatok alaphelyzetbe állítása után
description: Tárolási kivétel az Azure HDInsight-beli kapcsolatok alaphelyzetbe állítása után
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887223"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Forgatókönyv: tárolási kivétel az Azure HDInsight-beli kapcsolatok visszaállítása után

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem hozható létre új Apache HBase tábla.

## <a name="cause"></a>Ok

Egy tábla csonkítása során probléma merült fel a tárolási kapcsolatban. A tábla bejegyzése törölve lett a HBase metaadat-táblában. Egyetlen blob-fájl sem lett törölve.

Annak ellenére, hogy a tárolóban nem található `/hbase/data/default/ThatTable` nevű mappa-blob. A WASB illesztőprogramja megtalálta a fenti blob-fájl létezését, és nem teszi lehetővé, hogy `/hbase/data/default/ThatTable` nevű blobot hozzon létre, mert azt feltételezi, hogy a szülő mappák is léteztek, így a tábla létrehozása sikertelen lesz.

## <a name="resolution"></a>Felbontás

1. Az Apache Ambari felhasználói felületén indítsa újra az aktív HMaster. Ez lehetővé teszi, hogy a két készenléti HMaster az aktív legyen, és az új aktív HMaster újra betölti a metaadatok táblázatának adatait. Így nem fogja látni a `already-deleted` táblát a HMaster felhasználói felületén.

1. Az árva blob-fájlt a felhasználói felületi eszközökről (például a Cloud Explorerben) vagy a (z) `hdfs dfs -ls /xxxxxx/yyyyy`parancs futtatásáról találhatja meg. `hdfs dfs -rmr /xxxxx/yyyy` futtatásával törölje a blobot. Például: `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Most létrehozhat egy azonos nevű új táblát a HBase-ben.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
