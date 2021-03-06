---
title: Fürt eseményeinek lekérdezése a EventStore API-k használatával
description: Ismerje meg, hogyan használhatja az Azure Service Fabric EventStore API-kat a platform eseményeinek lekérdezéséhez
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 48350caef6bdaafda9aff7ac776d67b314aeaf8c
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614400"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>EventStore API-k lekérdezése a fürt eseményeihez

Ez a cikk azt ismerteti, hogyan lehet lekérdezni a EventStore API-kat, amelyek a 6,2-es és újabb verziókban érhetők Service Fabric el – Ha többet szeretne megtudni a EventStore szolgáltatásról, tekintse meg a [EventStore szolgáltatás áttekintését](service-fabric-diagnostics-eventstore.md). A EventStore szolgáltatás jelenleg csak az elmúlt 7 napban fér hozzá az adatokhoz (ez a fürt diagnosztikai adatmegőrzési szabályzata alapján történik).

>[!NOTE]
>A EventStore API-k az Azure-on futó Windows-fürtökön a GA Service Fabric 6,4-es verziójának megfelelően működnek.

A EventStore API-k közvetlenül egy REST-végponton keresztül, vagy programozott módon érhetők el. A lekérdezéstől függően számos paramétert kell megadnia a megfelelő adatok összegyűjtéséhez. Ezek a paraméterek jellemzően a következőket tartalmazzák:
* `api-version`: az Ön által használt EventStore API-k verziója
* `StartTimeUtc`: a megtekinteni kívánt időszak kezdetét határozza meg
* `EndTimeUtc`: az időszak vége

Ezen paraméterek mellett választható paraméterek is elérhetők, például:
* `timeout`: felülbírálja az alapértelmezett 60 második időtúllépést a kérelem műveletének végrehajtásakor.
* `eventstypesfilter`: az adott eseménytípus szűrésére szolgáló lehetőséget biztosít.
* `ExcludeAnalysisEvents`: nem ad vissza "Analysis" eseményeket. Alapértelmezés szerint a EventStore-lekérdezések az "elemzés" eseményekkel lesznek visszaadva, ahol lehetséges. Az elemzési események olyan gazdagabb működési csatorna-események, amelyek további kontextust vagy információt tartalmaznak a rendszeres Service Fabric eseményeken túl, és részletesebben biztosítanak.
* `SkipCorrelationLookup`: ne keressen a lehetséges korrelált eseményeket a fürtben. Alapértelmezés szerint a EventStore megkísérli összekapcsolni az eseményeket egy fürtön keresztül, és ha lehetséges, össze kell kapcsolni az eseményeket. 

A fürt minden entitása lehet lekérdezés az eseményekhez. Az eseményeket a típus összes entitása esetében is lekérdezheti. Például lekérdezheti egy adott csomópont vagy a fürt összes csomópontja eseményeit. Azon entitások aktuális készlete, amelyekhez eseményeket lehet lekérdezni (a lekérdezés szerkezetének módjával):
* Fürt: `/EventsStore/Cluster/Events`
* Csomópontok: `/EventsStore/Nodes/Events`
* Csomópont: `/EventsStore/Nodes/<NodeName>/$/Events`
* Alkalmazások: `/EventsStore/Applications/Events`
* Alkalmazás: `/EventsStore/Applications/<AppName>/$/Events`
* Szolgáltatások: `/EventsStore/Services/Events`
* Szolgáltatás: `/EventsStore/Services/<ServiceName>/$/Events`
* Partíciók: `/EventsStore/Partitions/Events`
* Partíció: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Replikák: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Replika: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Egy alkalmazás vagy szolgáltatás nevének hivatkozásakor a lekérdezésnek nem kell tartalmaznia a "Fabric:/"-t előtag. Emellett, ha az alkalmazás vagy a szolgáltatás neve "/" szerepel bennük, váltson a "~" értékre a lekérdezés működésének megtartásához. Ha például az alkalmazás "Fabric:/App1/FrontendApp"-ként jelenik meg, az alkalmazás-specifikus lekérdezések `/EventsStore/Applications/App1~FrontendApp/$/Events`ként lesznek felépítve.
>Emellett a szolgáltatásokhoz tartozó állapotfigyelő jelentések a megfelelő alkalmazás alatt jelennek meg, így a megfelelő alkalmazás-entitáshoz tartozó `DeployedServiceHealthReportCreated` eseményeket kell lekérdezni. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>A EventStore lekérdezése REST API végpontokon keresztül

A EventStore közvetlenül egy REST-végponton keresztül kérdezheti le, ha `GET` kérelmeket a következőre kéri: `<your cluster address>/EventsStore/<entity>/Events/`.

Ha például az `2018-04-03T18:00:00Z` és `2018-04-04T18:00:00Z`közötti összes fürtcsomópont lekérdezését szeretné lekérdezni, a kérelem a következőképpen fog kinézni:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Ez nem adhat vissza eseményeket vagy a JSON-ban visszaadott események listáját:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

Itt láthatjuk, hogy a `2018-04-03T18:00:00Z` és `2018-04-04T18:00:00Z`között ez a fürt sikeresen befejezte első frissítését, amikor az első felállt, a `"CurrentClusterVersion": "0.0.0.0:"` a `"TargetClusterVersion": "6.2:1.0"``"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>EventStore programozott lekérdezése

A EventStore programozott módon is lekérdezheti az [Service Fabric ügyféloldali kódtár](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)használatával.

Miután beállította a Service Fabric-ügyfelet, az alábbihoz hasonló EventStore érheti el az eseményeket: `sfhttpClient.EventStore.<request>`

Íme egy példa a `2018-04-03T18:00:00Z` és `2018-04-04T18:00:00Z`közötti összes fürtcsomópont-kérelemre az `GetClusterEventListAsync` függvény használatával.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Íme egy másik példa, amely lekérdezi a fürt állapotát és az összes Node eseményt a 2018 szeptemberében, és kinyomtatja őket.

```csharp
  const int timeoutSecs = 60;
  var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
  var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

  var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
  Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());

  
  Console.WriteLine("Querying for node events...");
  var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
      "2018-09-01T00:00:00Z",
      "2018-09-30T23:59:59Z",
      timeoutSecs,
      "NodeDown,NodeUp")
      .GetAwaiter()
      .GetResult()
      .ToList();
  Console.WriteLine("Result Count: {0}", nodesEvents.Count());

  foreach (var nodeEvent in nodesEvents)
  {
      Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
      if (nodeEvent is NodeDownEvent)
      {
          var nodeDownEvent = nodeEvent as NodeDownEvent;
          Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
      }
      else if (nodeEvent is NodeUpEvent)
      {
          var nodeUpEvent = nodeEvent as NodeUpEvent;
          Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
      }
  }
```

## <a name="sample-scenarios-and-queries"></a>Példák és lekérdezések

Íme néhány példa arra, hogyan hívhatja meg az Event Store REST API-kat a fürt állapotának megismerésére.

*Fürt frissítései:*

Ha szeretné megtekinteni, hogy a fürt Mikor volt sikeres, vagy mikor próbálta frissíteni a múlt héten, lekérdezheti az API-kat a fürthöz legutóbb befejezett frissítésekhez, ha lekérdezi a "ClusterUpgradeCompleted" eseményeit a EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Fürt frissítésével kapcsolatos problémák:*

Hasonlóképpen, ha problémák léptek fel a fürt legutóbbi frissítésekor, a fürt entitás összes eseményét lekérdezheti. Számos eseményt láthat, beleértve a frissítések kezdeményezését és az egyes UD-ket, amelyeken a frissítés sikeresen áthaladt. Azt is megtekintheti, hogy az adott pontnál hol találhatók a visszaállítás megkezdésének és a megfelelő állapot eseményeinek eseményei. Itt látható a következő lekérdezés: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Csomópont állapotának változása:*

Ha szeretné megtekinteni a csomópontok állapotának változását az elmúlt néhány napban – amikor a csomópontok fel-vagy leálltak, vagy inaktiváltak vagy inaktiváltak (akár a platform, a Chaos szolgáltatás vagy a felhasználói adatbevitel használatával) – használja a következő lekérdezést: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Alkalmazás eseményei:*

Nyomon követheti a legutóbbi alkalmazások központi telepítését és frissítéseit is. A következő lekérdezéssel tekintheti meg a fürtben lévő összes alkalmazási eseményt: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Alkalmazás korábbi állapota:*

Az alkalmazás-életciklus eseményeinek megjelenítésén kívül előfordulhat, hogy egy adott alkalmazás állapotára vonatkozó korábbi adatokra is kíváncsi. Ehhez meg kell adnia annak az alkalmazásnak a nevét, amelyhez össze kívánja gyűjteni az adatokat. Ezzel a lekérdezéssel kérheti le az összes alkalmazás-állapotra vonatkozó eseményt: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`. Ha olyan állapot-eseményeket szeretne szerepeltetni, amelyek érvényessége lejárt (az élettartam (TTL) miatt), vegyen fel `,ApplicationHealthReportExpired`t a lekérdezés végére, és két típusú eseményre szűrje.

*A "SajátPr" összes szolgáltatásának korábbi állapota:*

Jelenleg a szolgáltatások állapotjelentés-eseményei `DeployedServicePackageNewHealthReport` eseményként jelennek meg a megfelelő alkalmazás entitásban. Ha szeretné megtekinteni, hogy a szolgáltatásai hogyan lettek a "App1", használja a következő lekérdezést: `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Partíció újrakonfigurálása:*

Ha szeretné megtekinteni a fürtben történt összes partíciós mozdulatot, akkor a `PartitionReconfigured` eseményt kérdezze le. Ez segít kideríteni, hogy mely munkaterhelések futnak a megadott időpontokban a fürtben felmerülő problémák diagnosztizálásakor. Íme egy példa a következő lekérdezésre: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Chaos szolgáltatás:*

A Chaos szolgáltatás indításakor vagy leállításakor esemény történik, amely a fürt szintjén van kitéve. A Chaos szolgáltatás legutóbbi használatának megtekintéséhez használja az alábbi lekérdezést: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

