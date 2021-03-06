---
title: Bevezetés a Azure Cosmos DB API-MongoDB
description: Megtudhatja, hogyan tárolhat és lekérdezheti a nagy mennyiségű adatmennyiséget a Azure Cosmos DB használatával a Azure Cosmos DB API-MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/1/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 670020d276b9d4a868f24eb4a3f522581060adca
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754979"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

Az [Azure Cosmos DB](introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Az Azure Cosmos DB az [iparág legjobb szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/cosmos-db/) által biztosított [teljes körű, globális terjesztést](distribute-data-globally.md) kínál, valamint [a teljesítmény és a tárterület rugalmas méretezését](partition-data.md) világszerte, az esetek 99%-ában egyszámjegyű ezredmásodperces késéseket és garantált magas rendelkezésre állást. Az Azure Cosmos DB [automatikusan indexeli az adatokat](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a felhasználónak sémákat és indexeket kellene kezelnie. Többmodelles szolgáltatás, amely támogatja a dokumentumokat, a kulcs-értékeket, a diagramokat és az oszlopos adatmodelleket. Alapértelmezés szerint a Cosmos DB az SQL API használatával kommunikálhat. Emellett a Cosmos DB szolgáltatás a Common NoSQL API-k számára is megvalósítja a vezetékes protokollokat, beleértve a Cassandra, a MongoDB, a Gremlin és az Azure Table Storaget. Ez lehetővé teszi, hogy ismerős NoSQL-ügyfélszoftvereit és eszközeit használja a Cosmos-adatbázissal való kommunikációhoz.

## <a name="wire-protocol-compatibility"></a>Huzal protokoll kompatibilitása

Azure Cosmos DB a Common NoSQL-adatbázisok, például a Cassandra, a MongoDB, a Gremlin és az Azure Tables Storage protokollokat implementálja. Azáltal, hogy a vezetékes protokollok natív megvalósítását közvetlenül és hatékonyan biztosítja a Cosmos DB belül, lehetővé teszi a NoSQL-adatbázisok meglévő ügyféloldali SDK-k, illesztőprogramjainak és eszközeinek, hogy a Cosmos DB transzparens módon használhassák a műveleteket. A Cosmos DB nem használja az adatbázisok egyik forráskódját sem a NoSQL-adatbázisok vezeték nélküli API-jai biztosításához.

Alapértelmezés szerint a Azure Cosmos DB API-MongoDB használatával létrehozott új fiókok kompatibilisek az MongoDB Wire protokoll 3,6-es verziójával. Minden olyan MongoDB-ügyfél-illesztőprogram, amely megérti ezt a protokoll-verziót, natív módon csatlakozhat Cosmos DBhoz.

![MongoDB-hez készült Azure Cosmos DB API](./media/mongodb-introduction/cosmosdb-mongodb.png)

## <a name="key-benefits"></a>Főbb előnyök

Az Cosmos DB fő előnye, hogy teljes körűen felügyelt, globálisan elosztott adatbázis-szolgáltatásként szolgál, [itt](introduction.md)olvashat. Emellett a népszerű NoSQL API-k natív módon implementálása révén a Cosmos DB a következő előnyöket nyújtja:

* Az alkalmazást egyszerűen áttelepítheti Cosmos DB az alkalmazás logikájának jelentős részeinek megőrzése mellett.
* Tartsa hordozható alkalmazásait, és folytassa továbbra is a Cloud vendor-agnosztikusot.
* A Cosmos DB által működtetett, közös NoSQL API-kra vonatkozó piacvezető, pénzügyi támogatású SLA-k beszerzése.
* Igény szerint rugalmasan méretezheti a Cosmos-adatbázisok kiépített átviteli sebességét és tárterületét, és csak a szükséges átviteli sebességért és tárterületért kell fizetnie. Ez jelentős költségmegtakarítást eredményez.
* Kulcsrakész, globális elosztás a több főkiszolgálós replikációval.

## <a name="cosmos-dbs-api-for-mongodb"></a>Cosmos DB API-MongoDB

Kövesse a gyors útmutatókat, és hozzon létre egy Azure Cosmos-fiókot, és telepítse át a meglévő MongoDB alkalmazást Azure Cosmos DB használatára, vagy hozzon létre egy újat:

* [Egy meglévő MongoDB Node. js-webalkalmazás migrálása](create-mongodb-nodejs.md).
* [Webalkalmazás létrehozása a MongoDB és a .NET SDK-hoz készült Azure Cosmos DB API-val](create-mongodb-dotnet.md)
* [A MongoDB és a Java SDK Azure Cosmos DB API-ját használó konzolos alkalmazás létrehozása](create-mongodb-java.md)

## <a name="next-steps"></a>Következő lépések

Íme, pár hivatkozás az első lépések megtételéhez:

* Kövesse a [MongoDB-alkalmazás csatlakoztatása Azure Cosmos db](connect-mongodb-account.md) oktatóanyagot, amelyből megtudhatja, hogyan kérheti le a fiók kapcsolati karakterláncának adatait.
* Kövesse a [Studio 3T használata Azure Cosmos db](mongodb-mongochef.md) oktatóanyagot, amelyből megtudhatja, hogyan hozhat létre kapcsolatot a Cosmos-adatbázis és a MongoDB-alkalmazás között a Studio 3T.
* Kövesse az [MongoDB-adatimportálás Azure Cosmos db](mongodb-migrate.md) oktatóanyagba című témakört, amely az adatait egy Cosmos-adatbázisba importálja.
* Kapcsolódjon egy Cosmos-fiókhoz a [Robo 3T](mongodb-robomongo.md)használatával.
* Ismerje meg, hogyan [konfigurálhatja a globálisan elosztott alkalmazások olvasási beállításait](../cosmos-db/tutorial-global-distribution-mongodb.md).

<sup>Megjegyzés: Ez a cikk az Azure Cosmos DB egy olyan szolgáltatását ismerteti, amely a MongoDB-adatbázisokkal való, vezetékes protokoll-kompatibilitást biztosít. A Microsoft nem futtat MongoDB-adatbázisokat a szolgáltatás biztosításához. Azure Cosmos DB nem kapcsolódik a MongoDB, Inc. szolgáltatáshoz.</sup>
