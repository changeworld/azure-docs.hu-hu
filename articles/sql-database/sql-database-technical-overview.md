---
title: Mi az Azure SQL Database szolgáltatás?
description: 'Ismerkedjen meg SQL Database: a felhőben a Microsoft RDBMS-kezelő rendszer technikai részletei és képességei.'
keywords: bevezetés az sql-be,az sql bemutatása,mi az sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: fd9e41418eac670bd1cb52be40dbd25c17af6fac
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255690"
---
# <a name="what-is-the-azure-sql-database-service"></a>Mi az Azure SQL Database szolgáltatás?

A Azure SQL Database felügyelt szolgáltatásként biztosított, általános célú, összehasonlítható adatbázis. Ezzel egy magas rendelkezésre állású és nagy teljesítményű adattárolási réteget hozhat létre az Azure-beli alkalmazások és megoldások számára. A SQL Database a különböző modern felhőalapú alkalmazások számára megfelelő választás lehet, mivel lehetővé teszi a kapcsolati és [nem kapcsolati struktúrák](sql-database-multi-model-features.md), például a diagramok, a JSON, a térbeli és az XML feldolgozását.

Ez a [Microsoft SQL Server adatbázismotor](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)legújabb stabil verziójára épül. Használhatja a speciális lekérdezés-feldolgozási funkciókat, például [a nagy teljesítményű memóriában lévő technológiákat és az](sql-database-in-memory.md) [intelligens lekérdezések feldolgozását](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json). Valójában a SQL Server legújabb képességei SQL Database, majd SQL Server önmagára. A legújabb SQL Server képességek javítása és frissítése nélkül, több millió adatbázison keresztül tesztelve. 

A SQL Database lehetővé teszi a teljesítmény egyszerű meghatározását és méretezését két különböző beszerzési modellen belül: egy [virtuális mag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md) és egy [DTU-alapú vásárlási modellt](sql-database-service-tiers-dtu.md). A SQL Database egy teljes körűen felügyelt szolgáltatás, amely beépített magas rendelkezésre állást, biztonsági másolatokat és egyéb gyakori karbantartási műveleteket tartalmaz. A Microsoft az SQL és az operációs rendszer kódjának összes javítását és frissítését kezeli. Nem kell kezelnie a mögöttes infrastruktúrát.

> [!NOTE]
> A vonatkozó feltételek és azok definíciói a [SQL Database kifejezések szószedetében](sql-database-glossary-terms.md)találhatók.

## <a name="deployment-models"></a>Üzembe helyezési modellek

Az Azure SQL Database az alábbi lehetőségeket kínálja az Azure SQL-adatbázisok üzembe helyezésére:

![Telepítési lehetőségek diagramja](./media/sql-database-technical-overview/deployment-options.png)

- Az [önálló adatbázisok](sql-database-single-database.md) egy teljes körűen felügyelt, elkülönített adatbázist jelentenek. Ezt a lehetőséget akkor használhatja, ha olyan modern Felhőbeli alkalmazásokkal és szolgáltatásokkal rendelkezik, amelyeknek egyetlen megbízható adatforrásra van szükségük. Az önálló adatbázisok a [Microsoft SQL Server adatbázismotor](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)egy [tárolt adatbázisához](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) hasonlóak.
- A [felügyelt példány](sql-database-managed-instance.md) a [Microsoft SQL Server adatbázismotor](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)teljes mértékben felügyelt példánya. Olyan adatbázisokat tartalmaz, amelyek együtt használhatók. Ezzel a beállítással egyszerűen áttelepítheti a helyszíni SQL Server-adatbázisokat az Azure-felhőbe, illetve olyan alkalmazásokhoz, amelyeknek az adatbázis-kezelő funkcióit kell használniuk SQL Server az adatbázismotor biztosítja.
- A [rugalmas készlet](sql-database-elastic-pool.md) olyan [önálló adatbázisok](sql-database-single-database.md) gyűjteménye, amelyek erőforrásainak, például a processzornak vagy a memóriának a közös készletét tartalmazzák. Az önálló adatbázisok egy rugalmas készletbe helyezhetők és kivehetők.

> [!IMPORTANT]
> A SQL Database és SQL Server közötti szolgáltatások közötti különbségek megismeréséhez, valamint a különböző Azure SQL Database telepítési lehetőségek közötti különbségekről lásd: [SQL Database szolgáltatások](sql-database-features.md).

SQL Database kiszámítható teljesítményt nyújt több erőforrástípus, szolgáltatási réteg és számítási méret alapján. Dinamikus skálázhatóságot biztosít leállás nélkül, beépített intelligens optimalizálással, globális skálázhatósággal és rendelkezésre állással, valamint speciális biztonsági beállításokkal. Ezek a funkciók lehetővé teszik, hogy a virtuális gépek és az infrastruktúra kezelése helyett az alkalmazások gyors fejlesztésére és a piacra kerülési idő felgyorsítására összpontosítsanak. A SQL Database szolgáltatás jelenleg a világ 38-es adatközpontjában található, így az adatbázist az Ön közelében lévő adatközpontban futtathatja.

## <a name="scalable-performance-and-pools"></a>Méretezhető teljesítmény és készletek

Megadhatja a hozzárendelt erőforrások mennyiségét. 
- Az önálló adatbázisok esetében minden adatbázis el van különítve a többitől, és hordozható. Mindegyiknek saját garantált mennyiségű számítási, memória-és tárolási erőforrása van. Az adatbázishoz rendelt erőforrások mennyisége dedikált az adatbázishoz, és az Azure-ban más adatbázisokkal nem lesz megosztva. Az [önálló adatbázisok erőforrásainak dinamikus méretezése](sql-database-single-database-scale.md) felfelé és lefelé is elvégezhető. Az önálló adatbázis beállítás különböző számítási, memória-és tárolási erőforrásokat biztosít a különböző igényekhez. Például az 1 – 80 virtuális mag, vagy 32 GB – 4 TB lehet. Az önálló adatbázisok [nagy kapacitású-szolgáltatási rétege](sql-database-service-tier-hyperscale.md) lehetővé teszi, hogy a gyors biztonsági mentési és visszaállítási képességekkel bővítse a 100 TB-ot.
- A rugalmas készletek használatával a készletben lévő összes adatbázis által megosztott erőforrásokat rendelhet hozzá. Létrehozhat egy új adatbázist, vagy áthelyezheti a meglévő önálló adatbázisokat egy erőforrás-készletbe, így maximalizálhatja az erőforrások használatát és pénzt takaríthat meg. Ez a beállítás lehetővé teszi a [rugalmas készlet erőforrásainak](sql-database-elastic-pool-scale.md) felfelé és lefelé történő dinamikus méretezését.
- Felügyelt példányok esetén minden felügyelt példány el van különítve a garantált erőforrásokkal rendelkező más példányokból. Felügyelt példányon belül a példány adatbázisai megosztanak egy erőforrás-készletet. A [felügyelt példányok erőforrásainak dinamikus méretezése](sql-database-managed-instance-resource-limits.md) felfelé és lefelé is elvégezhető.

Az általános célú szolgáltatási szinten az első alkalmazását egy kis méretű, egyetlen adatbázison is létrehozhatja. Ezt követően manuálisan vagy programozott módon módosíthatja a szolgáltatási szintet az üzleti szempontból kritikus szolgáltatási rétegre, hogy megfeleljen a megoldás igényeinek. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak a szükséges erőforrásokért kell fizetnie.

A *dinamikus méretezhetőség* különbözik az automatikus *méretezéstől*. Automatikus skálázás esetén a szolgáltatás adott feltételek alapján, automatikusan méretez, míg a dinamikus méretezhetőség lehetővé teszi a manuális méretezést, amely nem jár állásidővel. Az egyetlen adatbázis-beállítás támogatja a manuális dinamikus méretezhetőséget, de nem automatikus méretezést. Az automatikus élmény érdekében érdemes rugalmas készleteket használni, amelyek lehetővé teszik, hogy az adatbázisok egyedi adatbázis-igények alapján osszák meg az erőforrásokat a készletekben. Egy másik lehetőség a parancsfájlok használata, amelyek segítségével automatizálható egy adott adatbázis skálázhatósága. Példa: [egyetlen adatbázis figyelése és méretezése a PowerShell használatával](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Vásárlási modellek

SQL Database a következő vásárlási modelleket kínálja:
- A [virtuális mag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) segítségével kiválaszthatja a virtuális mag számát, a memória mennyiségét, valamint a tárterület mennyiségét és sebességét. A virtuális mag-alapú vásárlási modell azt is lehetővé teszi, hogy a megtakarítások megszerzéséhez a [SQL Server Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) is használja. További információ a Azure Hybrid Benefitről: a cikk későbbi, "gyakori kérdések" című szakasza.
- A [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) a számítási, a memória-és az I/O-erőforrások keverékét kínálja három szolgáltatási szinten a nagy mennyiségű adatbázis-munkaterhelések támogatásához. Az egyes szintjein belüli számítási méretek különböző mennyiségű erőforrást biztosítanak, amelyhez további tárolási erőforrásokat adhat hozzá.
- A [kiszolgáló nélküli modell](sql-database-serverless.md) automatikusan méretezi a számítási feladatok igénye alapján történő számítást, és a másodpercenként felhasznált számítások mennyiségére vonatkozó számlákat. A kiszolgáló nélküli számítási rétegek automatikusan szüneteltetik az adatbázisokat az inaktív időszakok során, amikor csak a tárterületet számlázzák, és automatikusan folytatják az adatbázisokat, amikor a tevékenység visszatér.

### <a name="service-tiers"></a>Szolgáltatásszintek

A Azure SQL Database három szolgáltatási szintet kínál, amelyek különböző típusú alkalmazásokhoz készültek:
- [Általános célú/standard](sql-database-service-tier-general-purpose.md) szolgáltatási szint általános számítási feladatokhoz tervezve. Költségvetés-alapú, kiegyensúlyozott számítási és tárolási lehetőségeket kínál.
- [Üzletileg kritikus/prémium](sql-database-service-tier-business-critical.md) szintű szolgáltatási szint a nagy tranzakciós sebességgel és a legalacsonyabb késleltetésű I/O-OLTP alkalmazásokhoz tervezve. Több elkülönített replika használatával a hibák legmagasabb rugalmasságát nyújtja.
- A [nagy kapacitású](sql-database-service-tier-hyperscale.md) szolgáltatási réteg rendkívül nagy OLTP-adatbázishoz készült, és lehetővé teszi a tárolási és a számítási kapacitások automatikus méretezését.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Rugalmas készletek az erőforrások kihasználtságának maximalizálásához

Számos vállalkozás és alkalmazás számára elegendő az, ha önálló adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. A kiszámíthatatlan használati szokások miatt a költségek és az üzleti modell is nehezen kezelhető. A [rugalmas készletek](sql-database-elastic-pool.md) megoldást jelentenek erre a problémára. A teljesítményadatokat egy készlethez kell hozzárendelni, és nem egy önálló adatbázishoz. A készlet kollektív teljesítménybeli erőforrásaiért kell fizetnie, nem pedig egyetlen adatbázis teljesítményére.

   ![Az alapszintű, standard és prémium kiadásokban rugalmas készleteket bemutató ábra](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Rugalmas készletekkel nem kell összpontosítania az adatbázis teljesítményének felfelé és lefelé állítására, mivel az erőforrások igénye ingadozik. A rugalmas készletbe helyezett adatbázisok szükség szerint használják fel a teljesítmény-erőforrásokat. A készletezett adatbázisok felhasználják, de nem lépik túl a készlet korlátait, így a díjszabás akkor is kiszámítható marad, ha az egyes adatbázisok használata nem.

[Hozzáadhat és eltávolíthat adatbázisokat a készlethez](sql-database-elastic-pool-manage-portal.md), az alkalmazást egy maroknyi adatbázisból több ezerre, az Ön által felügyelt Költségvetésen belül. A készlet adatbázisai számára elérhető erőforrások minimális és maximális mennyiségét is szabályozhatja, így biztosíthatja, hogy a készlet egyetlen adatbázisa se használja fel a készlet összes erőforrását, és hogy minden készletezett adatbázisnak garantált minimális erőforrása legyen. A rugalmas készleteket használó szoftveres (SaaS-) alkalmazások tervezési mintáinak megismeréséhez lásd: [tervezési minták több-bérlős SaaS-alkalmazásokhoz SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)használatával.

A szkriptek segítik a rugalmas készletek megfigyelését és méretezését. Példaként lásd: a [PowerShell használata egy rugalmas SQL-készlet figyelésére és méretezésére Azure SQL Databaseban](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> A felügyelt példányok nem támogatják A rugalmas készleteket. Ehelyett a felügyelt példány olyan példány-adatbázisok gyűjteménye, amelyek megosztják a felügyelt példányok erőforrásait.

### <a name="blend-single-databases-with-pooled-databases"></a>Önálló adatbázisok beolvasztása a rugalmas készletbe helyezett adatbázisokba

Az önálló adatbázisokat rugalmas készletekkel vegyítheti, és módosíthatja az önálló adatbázisok és a rugalmas készletek szolgáltatási rétegeit, hogy azok alkalmazkodni tudjanak a helyzethez. A SQL Database használatával más Azure-szolgáltatásokat is összekeverheti, és megtekintheti az alkalmazás egyedi kialakítási igényeit, a meghajtó költségeit és erőforrás-hatékonyságát, és új üzleti lehetőségeket nyithat meg.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Széles körű figyelési és riasztási funkciók

A Azure SQL Database fejlett monitorozási és hibaelhárítási funkciókat biztosít, amelyekkel mélyebb elemzéseket kaphat a munkaterhelés jellemzőiről. Ezek a funkciók és eszközök a következők:
 - A SQL Server adatbázismotor legújabb verziójában elérhető beépített figyelési képességek. Lehetővé teszik a valós idejű teljesítménnyel kapcsolatos megállapításokat. 
 - Az Azure által biztosított, a nagy számú adatbázis-példány figyelésére és hibakeresésére képes,

A [query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store)egy beépített SQL Server figyelési funkciója, amely valós időben rögzíti a lekérdezések teljesítményét, és lehetővé teszi a lehetséges teljesítménnyel kapcsolatos problémák és a legfontosabb erőforrás-felhasználók azonosítását. Az automatikus hangolás és a javaslatok a romlott teljesítményével és hiányzó vagy duplikált indexekkel kapcsolatos tanácsokat nyújtanak. A SQL Database automatikus hangolásával manuálisan alkalmazhatja a hibákat kijavító szkripteket, vagy engedélyezheti SQL Database a javítás alkalmazását. A SQL Database tesztelheti és ellenőrizheti, hogy a javítás bizonyos előnyöket biztosít-e, és az eredménytől függően megőrzi vagy visszaállíthatja a változást. A lekérdezési tároló és az Automatikus hangolási funkciók mellett a standard [DMV és a XEvent](sql-database-monitoring-with-dmvs.md) is használható a munkaterhelés teljesítményének figyelésére.

Az Azure [beépített teljesítmény-figyelési](sql-database-performance.md) és [riasztási](sql-database-insights-alerts-portal.md) eszközöket kínál a teljesítmény-minősítéssel együtt, amely lehetővé teszi több ezer adatbázis állapotának figyelését. Ezeknek az eszközöknek a használatával gyorsan elemezheti a fel-vagy leskálázás hatásait az aktuális vagy tervezett teljesítménybeli igények alapján. Az SQL Database emellett [metrikák és diagnosztikai naplók kibocsátásával](sql-database-metrics-diag-logging.md) is képes megkönnyíteni a felügyeletet. Az SQL Database beállítható az erőforrás-használatra, feldolgozókra és munkamenetekre, valamint kapcsolatokra vonatkozó adatok tárolására a következő Azure-erőforrások valamelyikén:

- **Azure Storage**: Nagy tömegű telemetriai adat alacsony költségű archiválására.
- **Azure Event Hubs**: SQL Database telemetria integrálása az egyéni figyelési megoldással vagy a gyors folyamatokkal.
- **Azure monitor naplók**: beépített figyelési megoldás jelentéskészítési, riasztási és kockázatcsökkentő képességekkel.

![Az Azure monitoring architektúra ábrája](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Rendelkezésre állás

Hagyományos SQL Server-környezetben általában legalább két géppel kell helyileg beállítani. Ezek a gépek pontosan, szinkronban karbantartva, az adataik másolatával védik az egyes gépek vagy összetevők meghibásodását. Ez a környezet magas rendelkezésre állást biztosít, de nem nyújt védelmet az adatközpontot megsemmisítő természeti katasztrófák ellen.

A vész-helyreállítás azt feltételezi, hogy egy katasztrofális esemény földrajzilag honosítva van ahhoz, hogy egy másik géppel vagy géppel rendelkezzen az adatai egy példányával. A SQL Server az aszinkron módban futó always on rendelkezésre állási csoportokat használhatja a funkció beszerzéséhez. A felhasználók gyakran nem szeretnének várni, amíg a replikáció el nem kerül a tranzakció véglegesítése előtt, így lehetséges, hogy adatvesztés történik, ha nem tervezett feladatátvételt végez.

A prémium és az üzleti szempontból kritikus szolgáltatási rétegek adatbázisai már [nagyon hasonlítanak](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) a rendelkezésre állási csoportok szinkronizálásához. Az alacsonyabb szolgáltatási szinten lévő adatbázisok egy [másik, de egyenértékű mechanizmus](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)használatával biztosítják a redundanciát a tárterületen. A beépített logika egyetlen gép meghibásodása elleni védelmet nyújt. Az aktív geo-replikációs szolgáltatás lehetővé teszi, hogy védelmet nyújtson a katasztrófák ellen, amikor az egész régió megsemmisül.

A Azure Availability Zones egy adott régión belül egy adatközpont-kialakítás KIMARADÁSÁVAL szembeni védelmet próbál meg védeni. Segít megvédeni az energia vagy a hálózat elvesztését egy épületben. SQL Database a különböző replikákat a különböző rendelkezésre állási zónákba helyezi (a különböző épületek és a hatékony).

Valójában a Microsoft által felügyelt adatközpontok globális hálózata által működtetett Azure szolgáltatói szerződése [(SLA)](https://azure.microsoft.com/support/legal/sla/) segít megőrizni az alkalmazást a 24/7-es verzióban. Az Azure platform teljes mértékben felügyeli az összes adatbázist, és nem garantálja az adatvesztést és az adatmennyiség magas százalékos arányát. Az Azure automatikusan kezeli a javításokat, a biztonsági mentéseket, a replikálást, a hibák észlelését, az alapul szolgáló hardver-, szoftver-és hálózati hibákat, hibajavítások, feladatátvételek, adatbázis-frissítések és egyéb karbantartási feladatok telepítését. A standard szintű rendelkezésre állás a számítási és tárolási rétegek elkülönítését foglalja magában. A prémium szintű rendelkezésre állást úgy érheti el, hogy a számítási és tárolási kapacitást egyetlen csomóponton integrálja a teljesítményre, majd az Always On rendelkezésre állási csoportokhoz hasonló technológiákat implementál. A Azure SQL Database magas rendelkezésre állási képességeinek teljes körű megvitatását lásd: [SQL Database rendelkezésre állás](sql-database-high-availability.md). 

Emellett a SQL Database beépített [üzletmenet-folytonossági és globális skálázhatósági](sql-database-business-continuity.md) funkciókat biztosít. Ezek a következők:

- [Automatikus biztonsági mentések](sql-database-automated-backups.md):

  A SQL Database automatikusan végrehajtja az SQL-adatbázisok teljes, különbözeti és tranzakciós naplójának biztonsági másolatát, hogy bármely időpontra vissza lehessen állítani. Az önálló adatbázisok és a készletezett adatbázisok esetében beállíthatja, hogy SQL Database az adatbázis teljes biztonsági mentését az Azure Storage-ba tárolja a hosszú távú biztonsági mentés megőrzése érdekében. Felügyelt példányok esetén a biztonsági másolatok hosszú távú megőrzéséhez is elvégezheti a csak másolással készített biztonsági mentést.

- [Időponthoz tartozó visszaállítások](sql-database-recovery-using-backups.md):

  Az összes SQL Database üzembe helyezési lehetőség az SQL-adatbázisok automatikus biztonsági mentésének megőrzési időszakán belül bármely időpontra támogatja a helyreállítást.
- [Aktív földrajzi replikálás](sql-database-active-geo-replication.md):

  Az önálló adatbázis és a készletezett adatbázisok beállításai lehetővé teszik legfeljebb négy olvasható másodlagos adatbázis konfigurálását akár azonos, akár globálisan elosztott Azure-adatközpontokban. Ha például egy SaaS-alkalmazás olyan katalógus-adatbázissal rendelkezik, amely nagy mennyiségű egyidejű írásvédett tranzakcióval rendelkezik, akkor az aktív földrajzi replikálással engedélyezheti a globális olvasási léptéket. Ezzel eltávolítja az elsődlegesen az olvasási munkaterhelések miatti szűk keresztmetszeteket. Felügyelt példányok esetén használja az automatikus feladatátvételi csoportokat.
- [Automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md):

  Az összes SQL Database üzembe helyezési lehetőség lehetővé teszi a feladatátvételi csoportok használatát a magas rendelkezésre állás és a terheléselosztás globális méretekben történő engedélyezéséhez. Ez magába foglalja a nagy méretű adatbázisok, rugalmas készletek és felügyelt példányok transzparens földrajzi replikálását és feladatátvételét. A feladatátvételi csoportok lehetővé teszik a globálisan elosztott SaaS-alkalmazások létrehozását, minimális adminisztrációs terheléssel. Ezzel a művelettel az összes összetett figyelési, útválasztási és feladatátvételi folyamat SQL Database.
- [Zóna – redundáns adatbázisok](sql-database-high-availability.md):

  SQL Database lehetővé teszi prémium vagy üzleti szempontból kritikus adatbázisok vagy rugalmas készletek kiépítését több rendelkezésre állási zónában. Mivel ezek az adatbázisok és rugalmas készletek több redundáns replikával rendelkeznek a magas rendelkezésre állás érdekében, a replikák több rendelkezésre állási zónába való elhelyezése nagyobb rugalmasságot biztosít. Ez magában foglalja az adatközpont skálázási hibáiból az adatvesztés nélkül történő automatikus helyreállítás lehetőségét.

## <a name="built-in-intelligence"></a>Beépített intelligencia

A SQL Database használatával olyan beépített intelligenciát érhet el, amely jelentősen csökkentheti az adatbázisok futtatásának és kezelésének költségeit, és maximalizálja az alkalmazás teljesítményét és biztonságát. Akár több millió ügyfél-számítási feladatot is futtat az óra körül, SQL Database nagy mennyiségű telemetria-adatot gyűjt és dolgoz fel, ugyanakkor teljes mértékben tiszteletben tartja az ügyfelek adatvédelmét. A különböző algoritmusok folyamatosan értékelik a telemetria adatait, így a szolgáltatás megtanulhatja és módosíthatja az alkalmazását.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatikus teljesítményfigyelés és finomhangolás

Az SQL Database részletes betekintést nyújt a figyelést kívánó lekérdezésekbe. SQL Database megismerheti az adatbázis-mintákat, és lehetővé teszi az adatbázis-séma a számítási feladatokhoz való hozzáigazítását. Az SQL Database [javaslatokat tesz a teljesítmény finomhangolására](sql-database-advisor.md), és Ön eldöntheti, hogy alkalmazza-e a javasolt finomhangolási műveleteket.

Az adatbázisok folyamatos figyelése azonban nehéz és unalmas feladat, különösen sok adatbázis kezelésekor. [Intelligent Insights](sql-database-intelligent-insights.md) ezt a feladatot a SQL Database teljesítményének automatikus figyelésével végzi el. Tájékoztatja a teljesítmény romlásával kapcsolatos problémákról, azonosítja az egyes problémák kiváltó okát, és lehetővé teszi a teljesítmény javítására vonatkozó ajánlásokat.

Előfordulhat, hogy a nagy számú adatbázis kezelése nem lehetséges hatékonyan, még a SQL Database és az Azure által biztosított összes elérhető eszközzel és jelentéssel. Az adatbázis manuális figyelése és finomhangolása helyett érdemes lehet a figyelési és hangolási műveletek némelyikének delegálását SQL Database az [automatikus hangolás](sql-database-automatic-tuning.md)használatával. A SQL Database automatikusan alkalmazza a javaslatokat, a teszteket és ellenőrzi az egyes hangolási műveleteket, így biztosítva a teljesítmény javítását. Így SQL Database automatikusan alkalmazkodik a számítási feladatokhoz egy ellenőrzött és biztonságos módon. Az automatikus Finomhangolás azt jelenti, hogy az adatbázis teljesítményét minden hangolási művelet előtt és után alaposan figyelni és összehasonlítani kell. Ha a teljesítmény nem javul, a hangolási művelet visszaállt.

Számos partnerünk, akik [SaaS több-bérlős alkalmazásokat](sql-database-design-patterns-multi-tenancy-saas-applications.md) futtatnak SQL Databaseon, az automatikus teljesítmény-finomhangolásra támaszkodva gondoskodnak arról, hogy alkalmazásaik mindig stabil és kiszámítható teljesítményt nyújtsanak. Ez a funkció nagy mértékben csökkenti számukra egy éjszaka közepén fellépő teljesítmény-visszaesés kockázatát. Továbbá, mivel az Ügyfélkörük egy része a SQL Server is használja, a SQL Database által biztosított indexelési ajánlásokat is használják a SQL Server ügyfeleinek segítésére.

SQL Database két automatikus finomhangolási szempont [érhető el](sql-database-automatic-tuning.md):

- **Automatikus indexkezelés**: Azonosítja az adatbázishoz hozzáadandó és az abból eltávolítandó indexeket.
- **Automatikus terv javítása**: azonosítja a problémás terveket, és javítja az SQL-terv teljesítményével kapcsolatos problémákat.

### <a name="adaptive-query-processing"></a>Adaptív lekérdezés-feldolgozás

Használhatja az [adaptív lekérdezések feldolgozását](/sql/relational-databases/performance/intelligent-query-processing), többek között a többutasításos táblázat értékű függvények többértékű futtatását, a kötegelt üzemmód memóriájának visszajelzését és a kötegelt módú adaptív illesztéseket. Ezen adaptív lekérdezés-feldolgozási funkciók mindegyike hasonló "Learn and alkalmazkodás" technikákat alkalmaz, így segítve a korábban bevonható lekérdezés-optimalizálási problémákkal kapcsolatos teljesítménnyel kapcsolatos problémák megoldását.

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

Az SQl Database számos [beépített biztonsági és megfelelőségi szolgáltatást](sql-database-security-overview.md) kínálva járul hozzá, hogy az Ön alkalmazása eleget tegyen a különféle biztonsági és megfelelőségi elvárásoknak.

> [!IMPORTANT]
> A Microsoft minősített Azure SQL Database (az összes üzembe helyezési lehetőség) számos megfelelőségi szabvány alapján. További információkért tekintse meg a [Microsoft Azure adatvédelmi központot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), ahol megtalálhatja a SQL Database megfelelőségi minősítések legfrissebb listáját.

### <a name="advance-threat-protection"></a>Veszélyforrások elleni védelem

A speciális adatbiztonság a speciális SQL-alapú biztonsági képességek egységes csomagja. Ez a funkció magában foglalja a bizalmas adatok felderítését és besorolását, az adatbázis-sebezhetőségek kezelését, valamint az adatbázis fenyegetését jelző rendellenes tevékenységek észlelését. Egyetlen helyet biztosít a képességek engedélyezéséhez és kezeléséhez.

- [Adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md):

  Ez a funkció Azure SQL Database beépített képességeket biztosít az adatbázisaiban található bizalmas adatok felfedezéséhez, besorolásához, címkézéséhez és védelméhez. Betekintést nyújt az adatbázis-besorolási állapotba, és nyomon követi a bizalmas adatokhoz való hozzáférést az adatbázison belül és a határain kívül is.
- [Sebezhetőségi felmérés](sql-vulnerability-assessment.md):

  A szolgáltatás felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. Áttekinthetővé teszi az adatbázisok biztonsági állapotát, és végrehajtható lépéseket kínál a biztonsági problémák megoldására, valamint az adatbázisok védelmének fejlesztésére.
- [Veszélyforrások észlelése](sql-database-threat-detection.md):

  Ez a funkció olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázis eléréséhez vagy kiaknázásához. A szolgáltatás folyamatosan figyeli az adatbázisokat, és azonnal értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A veszélyforrások észlelésével kapcsolatos riasztások a gyanús tevékenység részleteit tartalmazzák, és a fenyegetések kivizsgálásával és enyhítésével kapcsolatos teendőket javasolnak.

### <a name="auditing-for-compliance-and-security"></a>Naplózás a megfelelőség és biztonság szolgálatában

A [naplózás](sql-database-auditing.md) nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókban lévő naplóba írja azokat. A naplózás segíthet a jogszabályi megfelelőség fenntartásában, az adatbázis-tevékenység megértésében, valamint az üzleti problémákat vagy a biztonsági szabálysértések gyanúját jelző eltérések és rendellenességek megismerésében.

### <a name="data-encryption"></a>Adattitkosítás

A SQL Database titkosítás biztosításával segít az adatai védelmében. A mozgásban lévő adatforgalom a [Transport Layer Security](https://support.microsoft.com/kb/3135244)szolgáltatást használja. A nyugalmi állapotban lévő adatok esetében [transzparens adattitkosítást](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használ. A használatban lévő adatforgalomhoz [mindig titkosítva](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)van.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

Az SQL Database az [Azure Active Directory-integráció](sql-database-aad-authentication.md) által lehetővé teszi adatbázis-felhasználók és más Microsoft-szolgáltatások identitásainak központi kezelését. Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. A Azure Active Directory támogatja a [többtényezős hitelesítést](sql-database-ssms-mfa-authentication.md) az adatkezelés és az alkalmazások biztonságának növeléséhez, miközben támogatja az egyszeri bejelentkezést.

## <a name="easy-to-use-tools"></a>Egyszerűen használható eszközök

Az SQL Database egyszerűbbé és hatékonyabbá teszi az alkalmazások létrehozását és karbantartását. Az SQL Database lehetővé teszi, hogy arra koncentrálhasson, amit legjobban tud: kiváló alkalmazások készítésére. SQL Database a már meglévő eszközök és szaktudás használatával kezelheti és fejlesztheti.

- [A Azure Portal](https://portal.azure.com/):

  Webalapú alkalmazás az összes Azure-szolgáltatás kezeléséhez.
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  Ingyenes, letölthető ügyfélalkalmazás bármely SQL-infrastruktúra kezelésére, SQL Serverról SQL Databasere.
- [SQL Server Data Tools a Visual Studióban](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt):

  Ingyenes, letölthető ügyfélalkalmazás SQL Server-alapú kapcsolatok, SQL-adatbázisok, integrációs szolgáltatások csomagjai, Analysis Services adatmodellek és Reporting Services-jelentések fejlesztéséhez.
- [Visual Studio Code](https://code.visualstudio.com/docs):

  Ingyenes, letölthető, nyílt forráskódú Kódszerkesztő Windows, macOS és Linux rendszerekhez. Támogatja a bővítményeket, beleértve az [MSSQL bővítményt](https://aka.ms/mssql-marketplace) Microsoft SQL Server, Azure SQL Database és Azure SQL Data Warehouse lekérdezéséhez.

A SQL Database támogatja a Python, a Java, a Node. js, a PHP, a Ruby és a .NET alkalmazások létrehozását macOS, Linux és Windows rendszeren. Az SQL Database az SQL Serverrel azonos [adatkapcsolattárakat](sql-database-libraries.md) támogat.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL Database gyakori kérdések

### <a name="what-is-the-current-version-of-sql-database"></a>Mi a SQL Database aktuális verziója?

SQL Database jelenlegi verziója a V12-es verzió. A v11 verziója ki lett vonva.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Szabályozható a javítási állásidő?

Nem. A javítások hatása általában nem észlelhető, ha az alkalmazásban [újrapróbálkozási logikát alkalmaz](sql-database-develop-overview.md#resiliency) . További információ: az [Azure karbantartási eseményeinek tervezése Azure SQL Databaseban](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid Benefit kérdések

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Van-e kettős használati jogosultsággal a SQL Server Azure Hybrid Benefit?

A licencek 180 napos kettős használati jogosultsággal rendelkeznek, így biztosítható, hogy a Migrálás zökkenőmentesen fusson. Az 180 napos időszak elteltével a felhőben csak SQL Database használhatja a SQL Server-licencet. Már nem rendelkezik kettős használati jogokkal a helyszínen és a felhőben.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Miben különbözik a SQL Server Azure Hybrid Benefit a licencek mobilitásával?

A licencek mobilitási előnyeit a frissítési garanciával rendelkező ügyfeleink SQL Server biztosítjuk. Ez lehetővé teszi a licencek újbóli hozzárendelését egy partner megosztott kiszolgálóin. Ezt az előnyt az Azure IaaS és az AWS EC2 is használhatja.

A SQL Server Azure Hybrid Benefit két kulcsfontosságú területen különbözik a licenc mobilitástól:

- Gazdasági előnyöket biztosít a nagyvirtualizált munkaterhelések Azure-ba való áthelyezéséhez. A SQL Server Enterprise Edition rendszerű ügyfelek négy maggal rendelkezhetnek az Azure-ban az általános célú SKU-ban minden olyan mag számára, amely a helyszínen a nagyvállalati szintű virtualizált alkalmazások számára készült. A licencek mobilitása nem teszi lehetővé a virtualizált számítási feladatok felhőbe való áthelyezésének speciális költségeit.
- A szolgáltatás az Azure-ban (SQL Database felügyelt példányon) található, nagyvállalati SQL Server a helyszíni környezettel kompatibilis.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Milyen jogokkal rendelkezik a SQL Server Azure Hybrid Benefit?

SQL Database az ügyfelek a következő jogokkal rendelkeznek a SQL Server Azure Hybrid Benefithoz:

|Licenc-lábnyom|Mire Azure Hybrid Benefit a SQL Server?|
|---|---|
|SQL Server Enterprise Edition Core-ügyfelek a SA-val|<li>általános célú vagy üzletileg kritikus SKU-ra is fizethet alapdíj</li><br><li>1 mag helyszíni = 4 mag általános célú SKU-ban</li><br><li>1 mag a helyszínen = 1 mag üzletileg kritikus SKU-ban</li>|
|SQL Server Standard Edition Core-ügyfelek a SA-val|<li>Csak általános célú SKU-ra fizethet alapdíj</li><br><li>1 mag a helyszínen = 1 mag általános célú SKU-ban</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Kapcsolatfelvétel az SQL Server műszaki csoportjával

- [DBA stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): kérdezze meg az adatbázis adminisztrációs kérdéseit.
- [Stack overflow](https://stackoverflow.com/questions/tagged/sql-server): forduljon a fejlesztési kérdésekhez.
- [MSDN-fórumok](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): technikai kérdéseket tehet fel.
- [Visszajelzés](https://aka.ms/sqlfeedback): hibajelentések és kérések funkció.
- [Reddit](https://www.reddit.com/r/SQLServer/): beszéljen SQL Server.

## <a name="next-steps"></a>További lépések

- Tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/sql-database/) , amely az önálló adatbázisokra és a rugalmas készletekre vonatkozó költség-összehasonlításokat és számológépeket tartalmazza.
- Az első lépésekhez tekintse meg ezeket a rövid útmutatókat:

  - [SQL Database létrehozása az Azure Portalon](sql-database-single-database-get-started.md)  
  - [SQL Database létrehozása az Azure CLI-vel](sql-database-get-started-cli.md)
  - [SQL Database létrehozása PowerShell használatával](sql-database-get-started-powershell.md)

- Több Azure CLI és PowerShell-mintát talál itt:
  - [Azure CLI-minták az SQL Database-hez](sql-database-cli-samples.md)
  - [Azure PowerShell-minták az SQL Database-hez](sql-database-powershell-samples.md)

- További információ a bejelentett új képességekről: [SQL Database Azure-útiterve](https://azure.microsoft.com/roadmap/?category=databases).
- Tekintse meg a [Azure SQL Database blogot](https://azure.microsoft.com/blog/topics/database), ahol SQL Server termék csapat tagjainak blogja a SQL Database híreket és szolgáltatásokat tartalmazza.

