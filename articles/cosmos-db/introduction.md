---
title: Alapvető ismeretek az Azure Cosmos DB-ről
description: Az Azure Cosmos DB ismertetése. Ez a globálisan elosztott, többmodelles adatbázis az alacsony késés, a rugalmas skálázhatóság és a magas rendelkezésre állás jegyében készült, valamint natív támogatást biztosít a NoSQL-adatokhoz.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 2a09d5af013e804f33327855fb7b9f2104bc225c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240393"
---
# <a name="welcome-to-azure-cosmos-db"></a>Üdvözli az Azure Cosmos DB

A mai alkalmazások nagyon rugalmasak és mindig online állapotban vannak. Az alacsony késés és a magas rendelkezésre állás érdekében ezeknek az alkalmazásoknak a példányait a felhasználókhoz közel lévő adatközpontokban kell központilag telepíteni. Az alkalmazásoknak valós időben kell válaszolniuk a használat nagy változásaira a csúcsidőben, az egyre növekvő mennyiségű adatmennyiséget kell tárolniuk, és ezeket az adatokat a felhasználók számára elérhetővé kell tenniük ezredmásodpercben.

Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Egy gombnyomással a Cosmos DB lehetővé teszi, hogy rugalmasan és egymástól függetlenül méretezheti az átviteli sebességet és a tárterületet a globálisan tetszőleges számú Azure-régióban. Rugalmasan méretezheti az átviteli sebességet és a tárterületet, és kihasználhatja az olyan gyors, egyszámjegyű ezredmásodperces adatelérés előnyeit, mint például az SQL, a MongoDB, a Cassandra, a Tables vagy a Gremlin. A Cosmos DB átfogó [szolgáltatói szerződéseket](https://aka.ms/acdbsla) (SLA-kat) biztosít az átviteli sebesség, a késés, a rendelkezésre állás és a konzisztencia garantálása érdekében, ami semmilyen más adatbázis-szolgáltatást sem biztosít.

Ha ingyenes Azure-előfizetéssel, díjmentesen, vagy a [Azure Cosmos db ingyenes](optimize-dev-test.md#azure-cosmos-db-free-tier) szinten szeretné [kipróbálni a Azure Cosmos DBT](https://azure.microsoft.com/try/cosmosdb/) , akkor az első 400 ru/s és 5 GB tárhellyel rendelkező fiókot is igénybe veheti.

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB ingyenes kipróbálása](https://azure.microsoft.com/try/cosmosdb/)

![Az Azure Cosmos DB a Microsoft globálisan elosztott adatbázis-szolgáltatása rugalmas horizontális felskálázási képességgel, garantáltan alacsony késéssel, öt konzisztenciamodellel, valamint átfogó garantált SLA-kkal.](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Főbb előnyök

### <a name="turnkey-global-distribution"></a>Kulcsrakész globális terjesztés

A Cosmos DB lehetővé teszi, hogy világszerte gyorsan reagáló és magasan elérhető alkalmazásokat építsen ki. Cosmos DB transzparens módon replikálja az adatait, bárhol is legyenek a felhasználók, így a felhasználók kommunikálhatnak a számukra legközelebb eső adatreplikával.

A Cosmos DB segítségével bármikor hozzáadhat vagy eltávolíthat egy Azure-régiót a Cosmos-fiókjához egy kattintással. A Cosmos DB zökkenőmentesen replikálja az adatait a Cosmos-fiókjához társított összes régióba, miközben az alkalmazás továbbra is nagyon elérhető marad, a szolgáltatás *több-* vezérelt képességeinek köszönhetően. További információt a [globális terjesztéssel](distribute-data-globally.md) foglalkozó cikkben talál.

### <a name="always-on"></a>Always on

Az Azure-infrastruktúrával és az átlátszó, [több főkiszolgálós replikációval](global-dist-under-the-hood.md)való szoros integráció révén Cosmos db [99,999%-os magas rendelkezésre állást](high-availability.md) biztosít mind az olvasási, mind az írási műveletekhez. A Cosmos DB lehetővé teszi, hogy programozott módon (vagy a portálon keresztül) meghívja a Cosmos-fiók regionális feladatátvételét. Ezzel a képességgel biztosítható, hogy az alkalmazás a regionális katasztrófa esetén feladatátvételre legyen kialakítva.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Az átviteli sebesség és a tárterület rugalmas skálázhatósága világszerte

A transzparens horizontális particionálással és a több főkiszolgálós replikációval tervezett Cosmos DB példátlanul rugalmas méretezhetőséget biztosít az írások és olvasások számára, szerte a világon. Akár több ezer, akár több, akár több millió kérés/másodperc közötti, egyetlen API-hívással is méretezhető, és csak a szükséges átviteli sebességért (és tárterületért) kell fizetnie. Ez a funkció segítséget nyújt a számítási feladatok váratlan tüskékkel való ellátásához anélkül, hogy a csúcsra kellene felépíteni. További információ: [particionálás Cosmos db](partitioning-overview.md), [kiépített átviteli sebesség a tárolók és adatbázisok](set-throughput.md)számára, valamint a [kiépített átviteli sebesség méretezése globálisan](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Garantált alacsony késés a esetek 99% percentilisnál, világszerte

A Cosmos DB használatával rendkívül rugalmas, bolygó-méretezési alkalmazásokat hozhat létre. Az új, több főkiszolgálós replikációs protokollal és a retesz-Free és [Write-optimalizált adatbázismotor](index-policy.md)esetében a Cosmos db 10 – MS késést garantál mind a esetek 99% percentilis, mind a világ minden tájáról. Ez a funkció lehetővé teszi az adatfeldolgozást és a villámgyors lekérdezéseket a rendkívül rugalmas alkalmazások számára.

### <a name="precisely-defined-multiple-consistency-choices"></a>Pontosan meghatározott, több konzisztencia-választás

Globálisan elosztott alkalmazások Cosmos DB-ben történő létrehozásakor a [konzisztencia, a rendelkezésre állás, a késés és az átviteli sebesség közötti rendkívüli kompromisszumok](consistency-levels-tradeoffs.md)nem szükségesek. Az Cosmos DB több főkiszolgálós replikációs protokollját gondosan úgy tervezték, hogy [öt jól meghatározott következetességi lehetőséget](consistency-levels.md) kínáljon - *erős*, korlátozott elavulás, *munkamenet*, *konzisztens előtag*és *végleges* – egy intuitív programozási modellhez *, amely*alacsony késést és magas rendelkezésre állást biztosít a globálisan elosztott alkalmazások számára.

### <a name="no-schema-or-index-management"></a>Nincs séma-vagy indexelési felügyelet

Az adatbázis-séma és az indexek szinkronizálása az alkalmazás sémájával különösen fájdalmas a globálisan elosztott alkalmazások esetében. A Cosmos DB nem kell foglalkoznia a séma vagy az indexelés kezelésével. Az adatbázismotor teljes mértékben séma-független.  Mivel nincs szükség séma-és indexelési felügyeletre, a sémák áttelepítése során nem kell aggódnia az alkalmazás leállása miatt. Cosmos DB [automatikusan indexeli az összes adatforrást](index-policy.md) , és gyors lekérdezéseket szolgáltat.

### <a name="battle-tested-database-service"></a>A tesztelt adatbázis-szolgáltatás csata

Cosmos DB egy alapszintű szolgáltatás az Azure-ban. Közel egy évtizednél Cosmos DBt számos Microsoft-termék felhasználta a kritikus fontosságú alkalmazások globális méretekben történő felhasználásához, többek között a Skype, az Xbox, az Office 365, az Azure és sok más szolgáltatáshoz. Napjainkban Cosmos DB az egyik leggyorsabban bővülő szolgáltatás az Azure-ban, amelyet számos külső ügyfél és olyan kritikus fontosságú alkalmazás használ, amely rugalmas skálázást, kulcsrakész globális elosztást, több főkiszolgálós replikálást igényel a kis késleltetés és a magas rendelkezésre állás érdekében olvasások és írások.

### <a name="ubiquitous-regional-presence"></a>Mindennapos regionális jelenlét

A Cosmos DB az összes Azure-régióban elérhető, beleértve a nyilvános felhőben 54 + régiókat, az [Azure China 21Vianet](https://www.azure.cn/en-us/), az Azure germanyot, a Azure Governmentt és a védelmi minisztérium (DoD) Azure Government. Tekintse [meg Cosmos db regionális jelenlétét](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Alapértelmezés szerint biztonságos, vállalati használatra kész

A Cosmos DB a [megfelelőségi szabványok széles körének](compliance.md)tanúsítására szolgál. Emellett a Cosmos DBban lévő összes adatforgalom inaktív és mozgásban van titkosítva. A Cosmos DB a sorok szintjének engedélyezését és szigorú biztonsági normák betartását biztosítja.

### <a name="significant-tco-savings"></a>Jelentős TCO-megtakarítás

Mivel a Cosmos DB egy teljes körűen felügyelt szolgáltatás, már nem kell felügyelni és üzemeltetni az adatbázis-szoftverek összetett, többcsomópontos üzembe helyezését és frissítését, fizetnie kell a támogatást, a licencelést vagy a műveleteket, vagy az adatbázist ki kell építenie a maximális számítási feladatokhoz. További információ: [a Cost optimalizálása Cosmos DBával](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Iparági vezető átfogó SLA-kat

A Cosmos DB az első és egyetlen szolgáltatás, amely az [iparágban vezető átfogó SLA-](https://azure.microsoft.com/support/legal/sla/cosmos-db/) kat kínál, amely magában foglalja a 99,999%-os magas rendelkezésre állást, olvasási és írási késést a esetek 99% percentilis, a garantált átviteli sebesség és a konzisztencia tekintetében.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Globálisan elosztott operatív elemzés és AI natív módon beépített Apache Spark

A [Sparkot](spark-connector.md) közvetlenül a Cosmos db tárolt adattárolón is futtathatja. Ez a funkció lehetővé teszi, hogy az alacsony késleltetésű, működés közbeni elemzéseket globális szinten hajtsa végre anélkül, hogy ez hatással lenne a közvetlenül a Cosmos DBon működő tranzakciós munkaterhelésekre További információ: [globálisan elosztott operatív elemzések](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Alkalmazások fejlesztése a Cosmos DB népszerű, nyílt forráskódú szoftverek (OSS) API-k használatával

A Cosmos DB API-k közül választhat a Cosmos-adatbázisban tárolt adataival való munkához. Alapértelmezés szerint az [SQL](how-to-sql-query.md) (Core API) segítségével kérdezheti le a Cosmos-adatbázist. A Cosmos DB a [Cassandra](cassandra-introduction.md), a [MongoDB](mongodb-introduction.md), a [Gremlin](graph-introduction.md) és az [Azure Table Storage](table-introduction.md)API-jait is implementálja. A gyakran használt NoSQL (pl. MongoDB, Cassandra, Gremlin) az ügyfelek illesztőprogramjait (és eszközeit) közvetlenül a Cosmos-adatbázisba irányíthatja. A gyakran használt NoSQL API-k által támogatott vezetékes protokollok támogatásával a Cosmos DB a következőket teszi lehetővé:

* Az alkalmazást egyszerűen áttelepítheti Cosmos DB az alkalmazás logikájának jelentős részeinek megőrzése mellett.
* Tartsa hordozható alkalmazásait, és folytassa továbbra is a Cloud vendor-agnosztikusot.
* Teljes körűen felügyelt felhőalapú szolgáltatás a közös NoSQL API-kra vonatkozó piacvezető, pénzügyi támogatású SLA-kkal. 
* Igény szerint rugalmasan méretezheti az adatbázisok kiosztott átviteli sebességét és tárterületét, és csak a szükséges átviteli sebességért és tárterületért kell fizetnie. Ez jelentős költségmegtakarítást eredményez.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Az Azure Cosmos DB előnyeit kihasználó megoldások

Az olyan [webes, mobil-, játék-és IoT-alkalmazások](use-cases.md) , amelyek nagy mennyiségű adatot, olvasási és írási műveleteket igényelnek [globális szinten](distribute-data-globally.md) , a különböző adatokhoz közel valós válaszidő esetén, Cosmos db [garantált magas rendelkezésre állást](https://azure.microsoft.com/support/legal/sla/cosmos-db/), magas átviteli sebességet, kis késést és hangolt konzisztenciát biztosítanak. Ismerje meg, hogyan használhatók a Azure Cosmos DB a [IoT és a telematika](use-cases.md#iot-and-telematics), a [kiskereskedelmi és a marketing](use-cases.md#retail-and-marketing), a [játékok](use-cases.md#gaming) és a [webes és mobil alkalmazások](use-cases.md#web-and-mobile-applications)létrehozásához.

## <a name="next-steps"></a>További lépések

További információ a Cosmos DB alapfogalmai: [kulcsrakész globális terjesztés](distribute-data-globally.md) , [particionálás](partitioning-overview.md) és [kiépített átviteli sebesség](request-units.md).

Az alábbi rövid útmutatókkal könnyedén elkezdheti az Azure Cosmos DB használatát:

* [Bevezetés az Azure Cosmos DB SQL API használatába](create-sql-api-dotnet.md)
* [Ismerkedés a Azure Cosmos DB API-MongoDB](create-mongodb-nodejs.md)
* [Bevezetés az Azure Cosmos DB Cassandra API használatába](create-cassandra-dotnet.md)
* [Bevezetés az Azure Cosmos DB Gremlin API használatába](create-graph-dotnet.md)
* [Bevezetés az Azure Cosmos DB Table API használatába](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB ingyenes kipróbálása](https://azure.microsoft.com/try/cosmosdb/)
