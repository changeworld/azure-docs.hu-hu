---
title: A Change feed kalkulátor használata – Azure Cosmos DB
description: Ismerje meg, hogy miként elemezheti a változási hírcsatorna-feldolgozók állapotát a Change feed kalkulátor használatával
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: maquaran
ms.openlocfilehash: 8bd024fae7496db6c9cb6410df26975fde1984f7
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585288"
---
# <a name="use-the-change-feed-estimator"></a>A módosítási hírcsatorna kalkulátor használata

Ez a cikk azt ismerteti, hogyan figyelheti meg a [változási hírcsatorna processzor](./change-feed-processor.md) példányainak állapotát, ahogy beolvasták a változási csatornát.

## <a name="why-is-monitoring-progress-important"></a>Miért fontos a figyelési folyamat?

A Change feed processzor olyan mutatóként működik, amely a [változási csatornán](./change-feed.md) halad előre, és a delegált implementációban végez módosításokat. 

A módosítási hírcsatorna-feldolgozó üzembe helyezése az elérhető erőforrások, például a processzor, a memória, a hálózat stb. alapján feldolgozhatja a módosításokat egy adott sebességgel.

Ha ez a sebesség lassabb, mint a változásoknak az Azure Cosmos-tárolóban történt változásai, akkor a processzor elmarad.

Ennek a forgatókönyvnek az azonosítása segít megérteni, hogy szükség van-e a Change feed processzor üzembe helyezésének skálázására.

## <a name="implement-the-change-feed-estimator"></a>A Change feed kalkulátor implementálása

A [change feed processzorhoz](./change-feed-processor.md)hasonlóan a Change feed kalkulátor leküldéses modellként működik. A kalkulátor méri a legutóbb feldolgozott elem (a címbérletek állapota által meghatározott) és a tároló legutóbbi változása közötti különbséget, és leküldi ezt az értéket egy delegált értékre. A mérés elvégzésének időköze az alapértelmezett 5 másodperces értékkel is testreszabható.

Ha például a változási csatorna processzora a következőképpen van definiálva:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="StartProcessorEstimator":::

A becslések inicializálásának helyes módja, ha azt méri, hogy a processzor a következőhöz hasonló lenne `GetChangeFeedEstimatorBuilder`:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="StartEstimator":::

Ahol a processzor és a kalkulátor is ugyanazt a `leaseContainer` és azonos nevet használja.

A másik két paraméter a delegált, amely egy számot fog kapni, amely azt jelzi, hogy hány **módosítást kell olvasni** a processzor, valamint azt az időintervallumot, amelyen a mérést el szeretné végezni.

A becslést fogadó delegált példa:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="EstimationDelegate":::

Ezt a becslést elküldheti a figyelési megoldásnak, és azt is megtudhatja, hogyan viselkedik az előrehaladás az idő múlásával.

> [!NOTE]
> A módosítási hírcsatorna-kalkulátor nem szükséges a módosítási csatorna processzorának részeként telepíteni, és nem lehet ugyanannak a projektnek a része. Független lehet, és egy teljesen más példányban futtatható. Csak ugyanazt a nevet és címbérleti konfigurációt kell használnia.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat olvashat a hírcsatorna-feldolgozó szolgáltatással kapcsolatos változásokról:

* [A hírcsatorna-feldolgozó változásának áttekintése](change-feed-processor.md)
* [Hírcsatorna-processzor kezdő időpontjának módosítása](how-to-configure-change-feed-start-time.md)
