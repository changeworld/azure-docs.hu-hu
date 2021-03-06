---
title: Azure Cosmos DB Gremlin-válasz fejlécei
description: A további hibaelhárítást lehetővé tevő kiszolgálói válasz metaadatainak dokumentációja
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755082"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB Gremlin-kiszolgáló válaszának fejlécei
Ez a cikk azokat a fejléceket ismerteti, amelyeket Cosmos DB Gremlin-kiszolgáló visszaküld a hívónak a kérelem végrehajtásakor. Ezek a fejlécek hasznosak a kérelmek teljesítményének hibaelhárításához, a Cosmos DB szolgáltatással natív módon integrálható alkalmazások létrehozásához és az ügyfélszolgálat egyszerűsítéséhez.

Ne feledje, hogy ezen fejlécek függőségének figyelembevételével korlátozza az alkalmazás hordozhatóságát más Gremlin-megvalósításokra. Cserébe egyre szorosabb integráció Cosmos DB Gremlin. Ezek a fejlécek nem TinkerPop standardok.

## <a name="headers"></a>Fejlécek

| Fejléc | Type (Típus) | Minta értéke | Ha foglalt | Magyarázat |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11,3243 | Sikeres és sikertelen | A [kérelem egységében (ru/s vagy RUs)](request-units.md) felhasznált gyűjtemények vagy adatbázis-átviteli sebességek mennyisége részleges válaszüzenet esetén. Ez a fejléc a több adattömbökkel rendelkező kérelmek minden folytatásában megtalálható. Egy adott válasz adathalmazának díját tükrözi. Csak olyan kérelmek esetében, amelyek egyetlen válaszból állnak, ez a fejléc a bejárás teljes költsége. Azonban az összetett bejárásokat többsége esetében ez az érték részleges költségeket jelent. |
| **x-ms-total-request-charge** | double | 423,987 | Sikeres és sikertelen | A kérések egységében felhasznált gyűjtési vagy adatbázis-átviteli sebesség [(ru/s vagy RUs)](request-units.md) a teljes kérelemhez. Ez a fejléc a több adattömbökkel rendelkező kérelmek minden folytatásában megtalálható. A kérelem kezdete óta halmozott díjat jelez. Ennek a fejlécnek az értéke az utolsó adathalmazban a kérelem teljes díját jelzi. |
| **x-MS-Server-Time-MS** | double | 13,75 | Sikeres és sikertelen | Ez a fejléc a késéssel kapcsolatos hibaelhárítási célokhoz tartozik. Megadja azt az időtartamot ezredmásodpercben, ameddig Cosmos DB Gremlin-kiszolgálónak végre kellett hajtania a részleges válaszüzenetet. A fejléc értékének használata és a kérelmek összesített késése alkalmazásokkal való összehasonlítása kiszámíthatja a hálózati késések terhelését. |
| **x-MS-Total-Server-Time-MS** | double | 130,512 | Sikeres és sikertelen | Az Cosmos DB Gremlin-kiszolgáló teljes bejárási ideje összesen ezredmásodpercben. Ez a fejléc minden részleges választ tartalmaz. A kérelem kezdete óta halmozott végrehajtási időt jelképezi. Az utolsó válasz a végrehajtás teljes idejét jelzi. Ez a fejléc hasznos lehet az ügyfél és a kiszolgáló közötti különbségtételhez a késés forrásaként. Összehasonlíthatja a bejárás végrehajtási idejét az ügyfélen a fejléc értékével. |
| **x-ms-status-code** | hosszú | 200 | Sikeres és sikertelen | A fejléc a kérelem befejezésének vagy megszüntetésének belső okát jelzi. Az alkalmazásnak javasoljuk, hogy tekintse meg a fejléc értékét, és végezze el a megfelelő lépéseket. |
| **x-MS-alállapot-kód** | hosszú | 1003 | Csak hiba | Az Cosmos DB egy többmodelles adatbázis, amely egységes tárolási rétegre épül. Ez a fejléc további információkat tartalmaz a hibák okairól, ha a magas rendelkezésre állású verem alsóbb rétegeiben hiba történik. Az alkalmazás a fejléc tárolásához és a Cosmos DB ügyfélszolgálathoz való kapcsolódáskor javasolt. A fejléc értéke hasznos Cosmos DB mérnök számára a gyors hibaelhárításhoz. |
| **x-ms-retry-after-ms** | karakterlánc (TimeSpan) | "00:00:03.9500000" | Csak hiba | Ez a fejléc egy .NET- [TimeSpan](https://docs.microsoft.com/dotnet/api/system.timespan) típusának karakterláncos ábrázolása. Ez az érték csak olyan kérésekben szerepel, amelyeken a kiépített átviteli sebesség kimerítése miatt sikertelen volt. Az alkalmazásnak újra el kell küldenie a bejárást a megadott időtartam elteltével. |
| **x-ms-activity-id** | karakterlánc (GUID) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Sikeres és sikertelen | A fejléc a kérelmek egyedi, kiszolgálóoldali azonosítóját tartalmazza. Minden kéréshez a kiszolgáló egyedi azonosítót rendel hozzá követési célokra. Az alkalmazásoknak a kiszolgáló által visszaadott tevékenység-azonosítókat kell naplóznia azon kérések esetén, amelyekre az ügyfélnek szüksége lehet az ügyfélszolgálattal kapcsolatos kapcsolatfelvételhez. Cosmos DB támogatási személyzet a Cosmos DB Service telemetria ezen azonosítói alapján adott kérelmeket talál. |

## <a name="status-codes"></a>Állapotkódok

A kiszolgáló által visszaadott leggyakoribb állapotkódok alább láthatók.

| Állapot | Magyarázat |
| --- | --- |
| **401** | @No__t_0 hibaüzenet jelenik meg, ha a hitelesítési jelszó nem egyezik meg Cosmos DB fiók kulcsával. Navigáljon a Azure Portal Cosmos DB Gremlin-fiókjához, és győződjön meg arról, hogy a kulcs helyes.|
| **404** | Egyidejű műveletek, amelyek egyazon Edge vagy csúcspont törlését és frissítését kísérli meg egyszerre. Az `"Owner resource does not exist"` (Tulajdonos-erőforrás nem létezik) hibaüzenet azt jelzi, hogy a kapcsolati paraméterekben `/dbs/<database name>/colls/<collection or graph name>` formátumban megadott adatbázis vagy gyűjtemény helytelen.|
| **408** | `"Server timeout"` azt jelzi, hogy a bejárás **30 másodpercnél több időt** vett igénybe, és a kiszolgáló megszakította. Optimalizálja a bejárásokat, hogy gyorsan fusson a csúcspontok vagy élek a bejárási ugrások között, hogy szűkítse a keresési hatókört.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` ez általában akkor fordul elő, ha a gráfban már van ilyen nevű csúcspont vagy egy Edge.| 
| **412** | Az állapotkód a következő hibaüzenettel egészül ki: `"PreconditionFailedException": One of the specified pre-condition is not met`. Ez a hiba azt jelzi, hogy egy optimista egyidejűségi szabályozás sérti az Edge vagy a csúcspont beolvasása és az áruházba való visszaírása között a módosítás után. A hiba előfordulásának leggyakoribb esetei a tulajdonságok módosítása, például `g.V('identifier').property('name','value')`. A Gremlin motor beolvassa a csúcspontot, módosítja, és visszaírja azt. Ha egy másik, párhuzamosan futó bejárási kísérlet ugyanazt a csúcspontot vagy szegélyt próbálja meg írni, akkor az egyik ilyen hibaüzenetet kap. Az alkalmazásnak újra be kell küldenie a kiszolgálóra a bejárást.| 
| **429** | A kérelem szabályozása megtörtént, és a rendszer az **x-MS-újrapróbálkozás** után újrapróbálkozik a-MS értékkel| 
| **500** | @No__t_0 tartalmazó hibaüzenet azt jelzi, hogy egy adatbázis és/vagy gyűjtemény újra lett létrehozva ugyanazzal a névvel. Ez a hiba 5 percen belül eltűnik, mivel a változás propagálja és érvényteleníti a különböző Cosmos DB-összetevők gyorsítótárait. A hiba elkerüléséhez minden alkalommal egyedi adatbázis- és gyűjteményneveket használjon.| 
| **1000** | A rendszer ezt az állapotkódot adja vissza, ha a kiszolgáló sikeresen elemezte az üzenetet, de nem sikerült végrehajtani. Általában a lekérdezéssel kapcsolatos problémát jelez.| 
| **1001** | Ezt a kódot akkor adja vissza a rendszer, ha a kiszolgáló elvégzi a bejárási végrehajtást, de a válasz nem szerializálható vissza az ügyfélre. Ez a hiba akkor fordulhat elő, ha a bejárás összetett eredményt hoz létre, amely túl nagy, vagy nem felel meg a TinkerPop protokoll specifikációjának. Az alkalmazásnak le kell egyszerűsíteni a bejárást, amikor a hibát tapasztalja. | 
| **1003** | a rendszer akkor adja vissza a `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`, ha a bejárás meghaladja az engedélyezett memória korlátját. A memória korlátja **2 GB** /bejárási érték.| 
| **1004** | Ez az állapotkód helytelenül formázott gráf-kérelmet jelez. A kérelem helytelenül formázható, ha a deszerializálás sikertelen, a nem érték típusú típus deszerializálása érték típusú vagy nem támogatott Gremlin művelet. Az alkalmazás nem próbálkozhat újra a kéréssel, mert nem lesz sikeres. | 
| **1007** | Ezt az állapotkódot általában a `"Could not process request. Underlying connection has been closed."` hibaüzenet adja vissza. Ez a helyzet akkor fordulhat elő, ha az ügyfél illesztőprogramja a kiszolgáló által lezárt kapcsolatok használatát kísérli meg. Az alkalmazásnak újra kell próbálkoznia a bejárással egy másik kapcsolatban.
| **1008** | Cosmos DB Gremlin-kiszolgáló megszakíthatja a kapcsolatokat a fürt forgalmának újraelosztása érdekében. Az ügyfél-illesztőprogramoknak ezt a helyzetet kell használniuk, és csak élő kapcsolatok használatával küldhetnek kérelmeket a kiszolgálónak. Esetenként előfordulhat, hogy az ügyfél-illesztőprogramok nem észlelik, hogy a kapcsolatok bezárultak. Ha az alkalmazás hibát észlel, `"Connection is too busy. Please retry after sometime or open more connections."` egy másik kapcsolatban újra kell próbálkoznia a bejárással.

## <a name="samples"></a>Minták

Egy Gremlin.Net alapuló minta ügyfélalkalmazás, amely egy status attribútumot olvas:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Egy példa, amely bemutatja, hogyan olvashatja el az állapot attribútumot a Gremlin Java-ügyfélből:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Következő lépések
* [Azure Cosmos DB HTTP-állapotkódok](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Gyakori Azure Cosmos DB REST-válaszok fejlécei](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [A TinkerPop Graph-illesztőprogram szolgáltatójának követelményei]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
