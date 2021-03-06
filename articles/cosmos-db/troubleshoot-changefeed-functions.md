---
title: Hibák elhárítása Azure Functions trigger használatakor Cosmos DB
description: Gyakori problémák, megkerülő megoldások és diagnosztikai lépések a Azure Functions trigger használatakor Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365508"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Problémák diagnosztizálása és hibaelhárítása Azure Functions trigger használatakor Cosmos DB

Ez a cikk a gyakori problémákról, a megkerülő megoldásokról és a diagnosztikai lépésekről nyújt útmutatást, ha a [Cosmos DB Azure functions triggert](change-feed-functions.md)használja.

## <a name="dependencies"></a>Függőségek

A Cosmos DB Azure Functions-trigger és-kötések a Azure Functions futtatókörnyezeten alapuló kiterjesztési csomagoktól függenek. Mindig tartsa naprakészen ezeket a csomagokat, mert olyan javításokat és új funkciókat is tartalmazhatnak, amelyek az esetlegesen felmerülő esetleges problémák megoldására képesek:

* Azure Functions v2 esetén lásd: [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* A Azure Functions v1 esetében lásd: [Microsoft. Azure. webjobs. Extensions. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Ez a cikk mindig Azure Functions v2-re hivatkozik, ha a futtatókörnyezetet megemlítik, kivéve, ha explicit módon meg van adva.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Az Azure Cosmos DB SDK egymástól független felhasználása

A kiterjesztési csomag legfontosabb funkciója, hogy támogatást nyújtson a Azure Functions-triggerhez és a Cosmos DB-kötésekhez. Emellett tartalmazza a [Azure Cosmos db .net SDK](sql-api-sdk-dotnet-core.md)-t is, amely akkor hasznos, ha az trigger és a kötések használata nélkül szeretné interaktívan használni a Azure Cosmos db.

Ha az Azure Cosmos DB SDK-t szeretné használni, győződjön meg arról, hogy nem adja hozzá a projekthez egy másik NuGet-csomag hivatkozását. Ehelyett az **SDK-referenciát a Azure functions "kiterjesztési csomagján keresztül oldja**fel. Az Azure Cosmos DB SDK használata az triggertől és a kötéstől függetlenül

Emellett, ha manuálisan hozza létre az [Azure Cosmos db SDK-ügyfél](./sql-api-sdk-dotnet-core.md)saját példányát, kövesse az ügyfél csak egy példányának [használatát egy egyedi minta megközelítéssel](../azure-functions/manage-connections.md#documentclient-code-example-c). Ez a folyamat elkerüli a lehetséges szoftvercsatorna-problémákat a műveletekben.

## <a name="common-scenarios-and-workarounds"></a>Gyakori forgatókönyvek és megkerülő megoldások

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Az Azure-függvény meghiúsul, és a hibaüzenetek gyűjtése nem létezik

Az Azure-függvény a következő hibaüzenettel meghiúsul: "vagy" a forrás gyűjteménye "(adatbázis neve"), vagy a (z) "collection2-Name" címbérleti gyűjtemény (adatbázis: "Adatbázis2") nem létezik. Mindkét gyűjteménynek léteznie kell a figyelő elindítása előtt. A címbérleti gyűjtemény automatikus létrehozásához állítsa a "CreateLeaseCollectionIfNotExists" értéket "true" értékre.

Ez azt jelenti, hogy a trigger működéséhez szükséges Azure Cosmos-tárolók egyike vagy mindkettő nem létezik, vagy nem érhető el az Azure-függvény számára. **Maga a hiba azt fogja megállapítani, hogy melyik Azure Cosmos-adatbázis és-tároló az** a konfiguráció alapján keresett trigger.

1. Ellenőrizze a `ConnectionStringSetting` attribútumot, és hogy az **Azure-függvényalkalmazás található beállításokra hivatkozik-** e. Az attribútum értéke nem lehet maga a kapcsolatok karakterlánca, hanem a konfigurációs beállítás neve.
2. Győződjön meg arról, hogy a `databaseName` és a `collectionName` létezik az Azure Cosmos-fiókjában. Ha automatikus érték-helyettesítést használ (`%settingName%` mintázatok használatával), győződjön meg arról, hogy a beállítás neve létezik az Azure-függvényalkalmazásban.
3. Ha nem ad meg `LeaseCollectionName/leaseCollectionName`, az alapértelmezett érték a "bérletek". Ellenőrizze, hogy létezik-e ilyen tároló. Igény szerint beállíthatja a trigger `CreateLeaseCollectionIfNotExists` attribútumát, hogy `true` automatikusan létrehozza azt.
4. Ellenőrizze az [Azure Cosmos-fiókja tűzfal-konfigurációját](how-to-configure-firewall.md) , és győződjön meg róla, hogy nem blokkolja az Azure-függvényt.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Az Azure-függvény nem indul el "a megosztott átviteli sebességű gyűjteménynek rendelkeznie kell partíciós kulccsal"

A Azure Cosmos DB bővítmény korábbi verziói nem támogatják a [megosztott átviteli sebességű adatbázisban](./set-throughput.md#set-throughput-on-a-database)létrehozott bérletek tároló használatát. A probléma megoldásához frissítse a [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) kiterjesztést a legújabb verzió beszerzéséhez.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Az Azure-függvény nem kezdődhet a következővel: "PartitionKey kell megadni ehhez a művelethez."

Ez a hiba azt jelenti, hogy jelenleg egy particionált címbérleti gyűjteményt használ, amely egy régi [bővítmény-függőséggel](#dependencies)rendelkezik. Frissítsen a legújabb elérhető verzióra. Ha jelenleg Azure Functions v1 rendszeren fut, frissítenie kell Azure Functions v2-re.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Az Azure-függvény nem indul el a következővel: "a bérlet gyűjteménye, ha particionálva van, az azonosítóval megegyező partíciós kulcsnak kell lennie."

Ez a hiba azt jelenti, hogy az aktuális címbérleti tároló particionálva van, de a partíciós kulcs elérési útja nem `/id`. A probléma megoldásához újra létre kell hoznia a bérletek tárolót a `/id` partíciós kulcsként.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>"Az érték nem lehet null. Paraméter neve: o "a Azure Functions naplókban, amikor megpróbálja futtatni az triggert

Ez a probléma akkor jelenik meg, ha a Azure Portal használja, és az triggert használó Azure-függvény vizsgálatakor megpróbálja kiválasztani a **Futtatás** gombot a képernyőn. Az trigger nem igényli, hogy válassza a Futtatás az indításhoz lehetőséget, a rendszer automatikusan elindul az Azure-függvény telepítésekor. Ha szeretné ellenőrizni az Azure-függvény naplózási streamjét a Azure Portalon, csak nyissa meg a figyelt tárolót, és szúrjon be néhány új elemet, automatikusan megjelenik az indítást végrehajtó művelet.

### <a name="my-changes-take-too-long-to-be-received"></a>A módosítások túl sokáig tartanak

Ennek a forgatókönyvnek több oka is lehet, és mindegyiket ellenőrizni kell:

1. Azure-függvénye ugyanabban a régióban található, mint az Azure Cosmos-fiókja? Az optimális késéshez az Azure-függvénynek és az Azure Cosmos-fióknak ugyanabban az Azure-régióban kell lennie.
2. A változások folyamatosan vagy szórványosan történnek az Azure Cosmos-tárolójában?
Ha ez utóbbi, akkor késés fordulhat elő a módosítások tárolása és azok az Azure-függvény általi felvétele között. Ez azért van így, mert belsőleg, amikor az eseményindító ellenőrzi az Azure Cosmos-tároló változásait, és nem talál függőben lévő elemet, egy személyre szabható ideig (alapértelmezés szerint 5 másodpercig) alvó módban lesz a következő ellenőrzésig (a magas RU-használat elkerülése végett). Ezt a szüneteltetési időt az eseményindító `FeedPollDelay/feedPollDelay`konfigurációjának[](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) beállításában adhatja meg (ezredmásodpercben).
3. Előfordulhat, hogy az Azure Cosmos [-tárolók száma korlátozott](./request-units.md).
4. A trigger `PreferredLocations` attribútumával megadhatja az Azure-régiók vesszővel tagolt listáját az egyéni előnyben részesített kapcsolódási sorrend meghatározásához.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Néhány módosítás ismétlődik az triggerben

A "módosítás" fogalma egy dokumentumon végzett művelet. A leggyakoribb forgatókönyvek, ahol az azonos dokumentum eseményeinek fogadása a következő:
* A fiók végleges konzisztenciát használ. Miközben a változási csatornát egy végleges konzisztencia-szinten fogyasztja, előfordulhat, hogy a későbbi módosítási hírcsatorna olvasási műveletei között ismétlődő események szerepelnek (az egyik olvasási művelet utolsó eseménye a következőként jelenik meg).
* A dokumentum frissítése folyamatban van. A módosítási hírcsatorna több műveletet is tartalmazhat ugyanahhoz a dokumentumokhoz, ha a dokumentum frissítéseket fogad, több eseményt is felvehet (egyet az egyes frissítések esetében). Egy egyszerű módja annak, hogy megkülönböztethető legyen egy dokumentum különböző műveletei között, hogy nyomon követhesse az [egyes módosítások `_lsn` tulajdonságát](change-feed.md#change-feed-and-_etag-_lsn-or-_ts). Ha nem egyeznek meg, ezek a dokumentumok eltérő módosításai.
* Ha csak `id`alapján azonosítja a dokumentumokat, ne feledje, hogy a dokumentum egyedi azonosítója a `id` és a hozzá tartozó partíciós kulcs (lehet, hogy két olyan dokumentum létezik, amely azonos `id` de eltérő partíciós kulccsal rendelkezik).

### <a name="some-changes-are-missing-in-my-trigger"></a>Néhány módosítás hiányzik a saját triggerben

Ha azt tapasztalja, hogy az Azure Cosmos-tárolóban történt módosítások közül néhányat nem az Azure-függvény vette fel, akkor a kezdeti vizsgálati lépésnek kell megtörténnie.

Amikor az Azure-függvény megkapja a módosításokat, gyakran dolgozza fel őket, és igény szerint elküldheti az eredményt egy másik célhelyre. Ha a hiányzó módosításokat vizsgálja, győződjön meg arról, hogy a **betöltési ponton milyen változások érkeznek** (az Azure-függvény indításakor), nem a célhelyen.

Ha néhány módosítás hiányzik a célhelyről, ez azt jelentheti, hogy hiba történt az Azure-függvény végrehajtása során a módosítások fogadása után.

Ebben az esetben a legjobb megoldás az, ha `try/catch` blokkokat ad hozzá a kódban, és azon a hurkon belül, amely a módosításokat feldolgozhatja, hogy észlelje az elemek adott részhalmazának hibáit, és ennek megfelelően kezelje őket (további elemzés vagy újrapróbálkozás céljából küldje el őket egy másik tárolóba). 

> [!NOTE]
> A Cosmos DB Azure Functions-triggere alapértelmezés szerint nem próbálkozik újra egy köteg változásával, ha nem kezelt kivétel történt a kód végrehajtása során. Ez azt jelenti, hogy a módosítások nem érkeznek meg a célhelyre, mert nem kell feldolgoznia azokat.

Ha úgy találja, hogy a trigger nem fogadta el a módosításokat, a leggyakoribb forgatókönyv az, hogy **egy másik Azure-függvény fut**. Lehet, hogy egy másik Azure-függvény üzembe helyezése az Azure-ban vagy egy olyan Azure-függvény, amely egy olyan fejlesztői gépen fut, amely **pontosan ugyanazokkal a konfigurációval** rendelkezik (ugyanazokkal a figyelt és bérlet tárolókkal), és ez az Azure-függvény ellopja az Azure-függvény feldolgozására várható változások egy részhalmazát.

Emellett a forgatókönyv érvényesíthető is, ha tudja, hány Azure függvényalkalmazás példány fut. Ha megvizsgálja a bérletek tárolóját, és megszámolja a címbérletek számát a n belül, a bennük lévő `Owner` tulajdonság különböző értékeinek meg kell egyezniük a függvényalkalmazás példányainak számával. Ha több tulajdonos van, mint az ismert Azure függvényalkalmazás-példányok, az azt jelenti, hogy ezek a további tulajdonosok a módosítások "ellopása".

Ennek a helyzetnek az egyik egyszerű módja, ha egy új/eltérő értékkel `LeaseCollectionPrefix/leaseCollectionPrefix`t alkalmaz a függvényre, vagy egy új bérletek tárolóval teszteli.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>A tárolóban lévő összes elem újraindítása és újrafeldolgozásának kezdete 
Egy tároló összes elemének újrafeldolgozása a kezdetektől fogva:
1. Ha éppen fut, állítsa le az Azure-függvényt. 
1. Törölje a címbérleti gyűjtemény dokumentumait (vagy törölje, majd hozza létre újra a címbérleti gyűjteményt, hogy az üres legyen)
1. Állítsa a függvény [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger attribútumát True (igaz) értékre. 
1. Indítsa újra az Azure-függvényt. Most már elolvashatja és feldolgozhatja az összes módosítást a kezdetektől fogva. 

A [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) True értékre állításával megtudhatja, hogy az Azure-függvény az aktuális idő helyett a gyűjtemény előzményeinek elejéről olvassa be a módosításokat. Ez csak akkor működik, ha nincsenek már létrehozott bérletek (azaz a bérletek gyűjtemény dokumentumai). Ha a tulajdonságot igaz értékre állítja, akkor a már létrehozott bérletek nem lépnek érvénybe. Ebben az esetben, ha egy függvény leáll és újraindul, a a bérletek gyűjteményében meghatározottak szerint megkezdi az utolsó ellenőrzőpont olvasását. Az elejétől az újrafeldolgozáshoz kövesse a 1-4-es lépéseket.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>A kötés csak a IReadOnlyList\<Document > vagy a JArray használatával végezhető el.

Ez a hiba akkor fordul elő, ha a Azure Functions projekt (vagy bármely hivatkozott projekt) manuális NuGet hivatkozást tartalmaz az Azure Cosmos DB SDK-ra, amely az [Azure Functions Cosmos db bővítmény](./troubleshoot-changefeed-functions.md#dependencies)által megadott verziótól eltérő verziójú.

A probléma megkerüléséhez távolítsa el a hozzáadott manuális NuGet-hivatkozást, és hagyja meg a Azure Cosmos DB SDK-referenciát a Azure Functions Cosmos DB bővítmény-csomagon keresztül.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Az Azure-függvény lekérdezési intervallumának módosítása a változások észleléséhez

Ahogy azt fentebb említettük, hogy a [módosítások túl sokáig tartanak](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received), az Azure Function (alapértelmezés szerint 5 másodperc) az új módosítások ellenőrzése előtt alvó állapotba lép (a magas ru-használat elkerüléséhez). Ezt a szüneteltetési időt az eseményindító `FeedPollDelay/feedPollDelay`konfigurációjának[](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) beállításában adhatja meg (ezredmásodpercben).

## <a name="next-steps"></a>Következő lépések

* [A Azure Functions figyelésének engedélyezése](../azure-functions/functions-monitoring.md)
* [A .NET SDK Azure Cosmos DB hibaelhárítása](./troubleshoot-dot-net-sdk.md)
