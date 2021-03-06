---
title: Betölteni az adatokat a Kafkából az Azure Data Explorer
description: Ebből a cikkből megismerheti, hogyan (betöltés) adatok betöltését az Azure Data Explorer kafka.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 03b46ff50683149a22c71ccb155480a0f08455bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497280"
---
# <a name="ingest-data-from-kafka-into-azure-data-explorer"></a>Betölteni az adatokat a Kafkából az Azure Data Explorer
 
Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer Kafka Adatbetöltési (az adatok betöltése) kínál. A Kafka egy elosztott streamelési platform streamadatfolyamatok, amely megbízhatóan adatáthelyezést közötti rendszerek vagy alkalmazások kiépítését lehetővé teszi, hogy.
 
## <a name="prerequisites"></a>Előfeltételek
 
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 
 
* [Egy teszt fürt és az adatbázis](create-cluster-database-portal.md).
 
* [Egy mintaalkalmazás](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/kafka) , amely adatokat állít elő, és elküldi azokat a Kafka.

* [A Visual Studio 2019](https://visualstudio.microsoft.com/vs/) , futtassa a mintaalkalmazást.
 
## <a name="kafka-connector-setup"></a>A Kafka-összekötő telepítése

A Kafka csatlakozzon egy olyan eszköz, skálázható és megbízható folyamatos átviteli adatok az Apache Kafka és más rendszerek közötti. Lehetővé teszi, hogy gyorsan meghatározásához, hogy az adatok nagy gyűjteményeknek Kafka-összekötők egyszerű. A Kafka fogadó ADX Kafka a összekötőként szolgál.
 
### <a name="bundle"></a>Csomag

A Kafka betöltheti egy `.jar` , egy beépülő modult, amely egy egyéni összekötőt fog működni. Előállításához, például egy `.jar`, klónozza a kód a helyi rendszer és hozhat létre a Maven használatával. 

#### <a name="clone"></a>Klónozás

```bash
git clone git://github.com:Azure/kafka-sink-azure-kusto.git
cd ./kafka-sink-azure-kusto/kafka/
```

#### <a name="build"></a>Felépítés

Helyileg készítése a Mavennel előállításához egy `.jar` függőségekkel befejeződött.

* JDK > = 1.8-as [letöltése](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Maven [letöltése](https://maven.apache.org/install.html)
 

A legfelső szintű könyvtárán belül található *kafka-fogadó-azure-kusto*futtassa:

```bash
mvn clean compile assembly:single
```

### <a name="deploy"></a>Üzembe helyezés 

Töltse be a Kafka beépülő modult. A docker használatával központi telepítési példában talál [kafka-fogadó-azure-kusto](https://github.com/Azure/kafka-sink-azure-kusto#deploy)
 

Részletes dokumentációt a Kafka-összekötők, és hogyan kell őket telepíteni található [Kafka csatlakoztatása](https://kafka.apache.org/documentation/#connect) 

### <a name="example-configuration"></a>Konfigurációs példa 
 
```config
name=KustoSinkConnector 
connector.class=com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector 
kusto.sink.flush_interval_ms=300000 
key.converter=org.apache.kafka.connect.storage.StringConverter 
value.converter=org.apache.kafka.connect.storage.StringConverter 
tasks.max=1 
topics=testing1 
kusto.tables.topics_mapping=[{'topic': 'testing1','db': 'daniel', 'table': 'TestTable','format': 'json', 'mapping':'TestMapping'}] 
kusto.auth.authority=XXX 
kusto.url=https://ingest-{mycluster}.kusto.windows.net/ 
kusto.auth.appid=XXX 
kusto.auth.appkey=XXX 
kusto.sink.tempdir=/var/tmp/ 
kusto.sink.flush_size=1000
```
 
## <a name="create-a-target-table-in-adx"></a>ADX a céloldali tábla létrehozása
 
Hozzon létre egy táblát ADX, amelyhez a Kafka adatokat küldhet. A tábla létrehozása a fürt és az adatbázis kiépítése a **Előfeltételek**.
 
1. Az Azure Portalon keresse meg a fürt és a select **lekérdezés**.
 
    ![Alkalmazáshivatkozás lekérdezése](media/ingest-data-event-hub/query-explorer-link.png)
 
1. Másolja be a következő parancsot az ablakba, és válassza a **Futtatás** lehetőséget.
 
    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```
 
    ![Létrehozási lekérdezés futtatása](media/ingest-data-event-hub/run-create-query.png)
 
1. Másolja be a következő parancsot az ablakba, és válassza a **Futtatás** lehetőséget.
 
    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

    A parancs a bejövő JSON-adatokat leképezi a táblában (TestTable) szereplő oszlopnevekre és adattípusokra.


## <a name="generate-sample-data"></a>Mintaadatok létrehozása

Most, hogy a Kafka-fürt ADX csatlakoztatva van, használja a [mintaalkalmazás](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) letöltött adatok létrehozására.

### <a name="clone"></a>Klónozás

Klónozza a mintaalkalmazást helyileg:

```cmd
git clone git://github.com:Azure/azure-kusto-samples-dotnet.git
cd ./azure-kusto-samples-dotnet/kafka/
```

### <a name="run-the-app"></a>Az alkalmazás futtatása

1. Nyissa meg a mintaalkalmazást a Visual Studióban.

1. Az a `Program.cs` fájlt, frissítse a `connectionString` konstans, a Kafka-kapcsolati karakterláncot.

    ```csharp    
    const string connectionString = @"<YourConnectionString>";
    ```

1. Hozza létre és futtassa az alkalmazást. Az alkalmazás üzeneteket küld a Kafka-fürt, és jelenít meg, az állapota 10 másodpercenként.

1. Miután az alkalmazás néhány üzenetet küldött, folytassa a következő lépéssel.
 
## <a name="query-and-review-the-data"></a>Lekérdezés, és tekintse át az adatokat

1. Győződjön meg arról, hogy nem történt hiba Adatbetöltési során:

    ```Kusto
    .show ingestion failures
    ```

1. Az újonnan betöltött adatok megjelenítése:

    ```Kusto
    TestTable 
    | count
    ```

1. Láthatja az üzenetek:
 
    ```Kusto
    TestTable
    ```
 
    Az eredményhalmaz a következőhöz hasonlóan kell kinéznie:
 
    ![Üzenetek eredményhalmaza](media/ingest-data-event-hub/message-result-set.png)
 
## <a name="next-steps"></a>További lépések
 
* [Az Azure Data Explorer adatok lekérdezése](web-query-data.md)
