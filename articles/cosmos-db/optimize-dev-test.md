---
title: Optimalizálás a Azure Cosmos DB fejlesztéséhez és teszteléséhez
description: Ez a cikk azt ismerteti, hogy Azure Cosmos DB több lehetőséget kínál a szolgáltatás ingyenes fejlesztésére és tesztelésére.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: d2ca7b7e4b637802df6a78c2493e3cc088f09881
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246681"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>A fejlesztési és tesztelési díjak optimalizálása Azure Cosmos DB

Ez a cikk a fejlesztéshez és teszteléshez használható Azure Cosmos DB különböző lehetőségeit ismerteti díjmentesen, valamint olyan technikákat, amelyekkel optimalizálhatja a költségeket a fejlesztési vagy tesztelési fiókokban.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB Emulator (helyileg letölthető verzió)

[Azure Cosmos db Emulator](local-emulator.md) egy helyi letölthető verzió, amely a Azure Cosmos db Cloud Service-t utánozza. Megírhatja és tesztelheti a Azure Cosmos DB API-kat használó kódokat, még akkor is, ha nincs hálózati kapcsolatban, és nem merül fel költségek. A Azure Cosmos DB Emulator helyi környezetet biztosít a felhőalapú szolgáltatás számára magas szintű megbízhatósággal rendelkező fejlesztési célokra. Az alkalmazást helyileg fejlesztheti és tesztelheti Azure-előfizetés létrehozása nélkül is. Ha készen áll az alkalmazás felhőben való üzembe helyezésére, frissítse a kapcsolati karakterláncot a felhőben lévő Azure Cosmos DB végponthoz való csatlakozáshoz, nincs szükség további módosításokra. [Egy CI/CD-folyamatot is beállíthat a Azure Cosmos db emulátor](tutorial-setup-ci-cd.md) felépítési feladattal az Azure DevOps a tesztek futtatásához. Első lépésként látogasson el a [Azure Cosmos db Emulator](local-emulator.md) cikkre.

## <a name="azure-cosmos-db-free-tier"></a>Ingyenes Azure Cosmos DB szintű 
A Azure Cosmos DB ingyenes szintje megkönnyíti az alkalmazások megkezdését, fejlesztését és tesztelését, vagy akár kisebb éles számítási feladatok futtatását is ingyenesen. Ha az ingyenes szintet engedélyezi egy fiókon, az első 400 RU/s és 5 GB tárterület ingyenesen elérhető a fiókban. Létrehozhat egy közös átviteli sebességű adatbázist is, amely 25 tárolóval rendelkezik, amelyek az adatbázis szintjén osztják meg az 400 RU/s-t, és mindezt az ingyenes szint (az 5 közös átviteli sebességű adatbázis egy ingyenes szintű fiókban) fedi le. Az ingyenes szinten határozatlan ideig tart a fiók élettartama, és a rendszeres Azure Cosmos DB fiók összes [előnyét és funkcióját](introduction.md#key-benefits) tartalmazza, beleértve a korlátlan tárterületet és átviteli SEBESSÉGET (ru/s), SLA-kat, magas rendelkezésre állást, kulcsrakész globális elosztást az összes Azure-régióban, és így tovább. Az Azure-előfizetések esetében akár egy ingyenes szintű fiókot is beállíthat, és a fiók létrehozásakor be kell jelentkeznie. Első lépésként [hozzon létre egy új fiókot, amely lehetővé teszi az ingyenes szintet](create-cosmosdb-resources-portal.md). További részleteket a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/) talál. 

## <a name="try-azure-cosmos-db-for-free"></a>Az Azure Cosmos DB ingyenes kipróbálása

[Próbálja ki a Azure Cosmos db for Free](https://azure.microsoft.com/try/cosmosdb/) ingyenes élményét, amely lehetővé teszi, hogy az Azure-fiókra való feliratkozás vagy a bankkártya használata nélkül kísérletezzen a felhőben Azure Cosmos DBokkal. A kipróbálási Azure Cosmos DB fiókokat a rendszer jelenleg legfeljebb 30 napig elérhetővé teszi. Bármikor megújíthatja őket. Azure Cosmos DB fiókok kipróbálásával egyszerűen kiértékelheti az alkalmazásokat Azure Cosmos DB, felépítheti és tesztelheti az alkalmazásokat, vagy használhatja a gyors útmutatókat vagy oktatóanyagokat. Létrehozhat egy bemutatót is, elvégezheti az egység tesztelését, vagy akár többrégiós fiókot is létrehozhat, és anélkül futtathat egy alkalmazást, hogy költségeket kellene kivonnia. A Try Azure Cosmos DB fiókban egy megosztott átviteli sebességű adatbázishoz legfeljebb 25 tároló és 20 000 RU/s átviteli sebesség tartozhat, vagy egy legfeljebb 5000 RU/s méretű tároló. Első lépésként tekintse [meg az ingyenes Azure Cosmos db kipróbálása](https://azure.microsoft.com/try/cosmosdb/) oldalt.

## <a name="azure-free-account"></a>Ingyenes Azure-fiók

A Azure Cosmos DBt az [ingyenes Azure-fiók](https://azure.microsoft.com/free)tartalmazza, amely egy adott időszakra ingyenesen kínál Azure-krediteket és-erőforrásokat. Kifejezetten a Azure Cosmos DB esetében ez az ingyenes fiók 5 GB tárhelyet és 400 RUs-t biztosít az egész évre kiépített átviteli sebességhez. Ez a megoldás lehetővé teszi, hogy a fejlesztők egyszerűen teszteljék Azure Cosmos DB funkcióit, vagy más Azure-szolgáltatásokkal integrálják azt nulla áron. Az ingyenes Azure-fiókkal $200 kreditet kap, amelyet az első 30 napban kell költeni. Nem számítunk fel díjat, még akkor sem, ha a szolgáltatást a frissítés megkezdése előtt elkezdi használni. Első lépésként látogasson el az [ingyenes Azure-fiók](https://azure.microsoft.com/free) oldalra.

## <a name="use-shared-throughput-databases"></a>Megosztott átviteli sebességű adatbázisok használata

Egy [megosztott átviteli sebességű adatbázisban](set-throughput.md#set-throughput-on-a-database)az adatbázisban lévő összes tároló osztozik az adatbázis kiépített átviteli sebességén (ru/s). Ha például 400 RU/s-vel rendelkező adatbázist hoz létre, és négy tárolóval rendelkezik, mind a négy tároló osztozik a 400 RU/s-ban. Fejlesztési vagy tesztelési környezetben, ahol az egyes tárolók ritkábban érhetők el, és így a minimum 400 RU/s értéknél kevesebbet igényelnek, és a tárolók egy megosztott átviteli sebességű adatbázisban való üzembe helyezése segít optimalizálni a költségeket. 

Tegyük fel például, hogy a fejlesztési vagy tesztelési fiók négy tárolóval rendelkezik. Ha négy tárolót hoz létre dedikált átviteli sebességgel (legalább 400 RU/s), akkor a teljes RU/s a 1600 RU/s lesz. Ezzel szemben, ha létrehoz egy megosztott átviteli sebességű adatbázist (minimum 400 RU/s), és elhelyezi a tárolókat, a teljes RU/s csak 400 RU/s lesz. Általánosságban elmondható, hogy a megosztott átviteli sebességű adatbázisok kiválóan alkalmasak olyan forgatókönyvek esetén, amelyeknél nincs szükség a garantált átviteli sebességre minden egyes tárolón  További információ a [megosztott átviteli sebességű adatbázisokról.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Következő lépések

Az emulátor vagy az ingyenes Azure Cosmos DB fiókok használatának első lépései a következő cikkekkel szerezhetők be:

* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ [a Azure Cosmos db-számla megismeréséről](understand-your-bill.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](optimize-cost-queries.md)
* További információ [a több régióból álló Azure Cosmos-fiókok díjainak optimalizálásáról](optimize-cost-regions.md)

