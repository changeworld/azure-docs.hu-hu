---
title: Időtúllépések az Azure HDInsight "hbase hbck" parancsával
description: Időtúllépési hiba az "hbase hbck" paranccsal a régió hozzárendeléseinek kijavításakor
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887189"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Forgatókönyv: időtúllépés a "hbase hbck" paranccsal az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A régió-hozzárendelések kijavításakor a `hbase hbck` paranccsal időtúllépések merülhetnek fel.

## <a name="cause"></a>Ok

Előfordulhat, hogy a `hbck` parancs használatakor időtúllépési problémák merülhetnek fel, ha több régió "átmenetes" állapotban van hosszú ideje. Ezeket a régiókat kapcsolat nélküli üzemmódban láthatja a HBase Master felhasználói felületen. Mivel nagy számú régiót próbálnak áttérni, HBase Master lehet, hogy időtúllépés történt, és nem lehet ismét online állapotba hozni ezeket a régiókat.

## <a name="resolution"></a>Felbontás

1. Jelentkezzen be a HDInsight HBase-fürtbe SSH használatával.

1. Futtassa `hbase zkcli` parancsot Apache ZooKeeper rendszerhéjhoz való kapcsolódáshoz.

1. Futtassa `rmr /hbase/regions-in-transition` vagy `rmr /hbase-unsecure/regions-in-transition` parancsot.

1. Kilépés `hbase zkcli` rendszerhéjból `exit` parancs használatával.

1. Az Apache Ambari felhasználói felületén indítsa újra az aktív HBase Master szolgáltatást.

1. Futtassa a következő parancsot: `hbase hbck -fixAssignments`.

1. Figyelje meg, hogy a "régió az átmenetben" HBase Master felhasználói felülete nem ragadja meg a régiót.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

- Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
