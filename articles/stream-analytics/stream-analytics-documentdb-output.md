---
title: Az Azure Cosmos DB Azure Stream Analytics-kimenet
description: Ez a cikk ismerteti az Azure Stream Analytics használatával az Azure Cosmos DB JSON kimeneti adatok archiválása és kis késleltetésű lekérdezéseket a strukturálatlan JSON-adatok mentése.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254442"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Az Azure Cosmos DB Azure Stream Analytics-kimenet  
A Azure Stream Analytics a JSON-kimenethez [Azure Cosmos db](https://azure.microsoft.com/services/documentdb/) , így lehetővé teszi az adatarchiválást és az alacsony késésű lekérdezéseket a strukturálatlan JSON-adatokat illetően. Ez a dokumentum áttekint néhány ajánlott eljárást a konfiguráció implementálása a.

Ha nem ismeri a Azure Cosmos DBt, az első lépésekhez tekintse meg a [Azure Cosmos db dokumentációját](https://docs.microsoft.com/azure/cosmos-db/) . 

> [!Note]
> A Stream Analytics jelenleg csak az *SQL API*-n keresztül támogatja a Azure Cosmos db való kapcsolódást.
> Más Azure Cosmos DB API-k még nem támogatott. Ha a Stream Analytics Azure Cosmos DB más API-kkal létrehozott fiókokra mutat, előfordulhat, hogy az adathalmazok nem lesznek megfelelően tárolva. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>A Azure Cosmos DB alapjai a kimeneti célként
A Stream Analytics Azure Cosmos DB kimenete lehetővé teszi, hogy a stream feldolgozásának eredményét JSON-kimenetként írja be a Azure Cosmos DB tárolóba. 

Stream Analytics nem hoz létre tárolókat az adatbázisban. Ehelyett létre kell hoznia őket elölről. Ezután szabályozhatja Azure Cosmos DB tárolók számlázási költségeit. A tárolók teljesítményét, következetességét és kapacitását közvetlenül a [Azure Cosmos db API](https://msdn.microsoft.com/library/azure/dn781481.aspx)-k használatával is beállíthatja.

> [!Note]
> Az engedélyezett IP-címek listájához hozzá kell adni a 0.0.0.0 elemet az Azure Cosmos DB tűzfalon.

A következő részekben részletesen ismertetjük a Azure Cosmos DBhoz tartozó tárolók beállításait.

## <a name="tuning-consistency-availability-and-latency"></a>A konzisztencia, a rendelkezésre állás és a késés finomhangolása
Az alkalmazás követelményeinek való megfelelés érdekében Azure Cosmos DB lehetővé teszi az adatbázis és a tárolók finomhangolását, valamint a konzisztencia, a rendelkezésre állás, a késés és az átviteli sebesség közötti kompromisszumok biztosítását. 

Attól függően, hogy milyen szintű olvasás következetes ellen a forgatókönyv igényeinek olvassa el, és írási késés, választhatja a konzisztenciaszint a fiókot a. Az átviteli sebességet a tárolóban lévő kérelmek egységének (RUs) skálázásával növelheti. 

Alapértelmezés szerint a Azure Cosmos DB lehetővé teszi az egyes szifilisz-műveletek szinkron indexelését a tárolón. Ez egy másik hasznos lehetőség a Azure Cosmos DB írási/olvasási teljesítményének vezérléséhez. 

További információkért tekintse át az [adatbázis módosítása és a lekérdezési konzisztencia szintjei](../cosmos-db/consistency-levels.md) című cikket.

## <a name="upserts-from-stream-analytics"></a>A Stream Analytics Upserts
Stream Analytics integrációja Azure Cosmos DB lehetővé teszi a tárolóban lévő rekordok beszúrását vagy frissítését egy adott **dokumentum-azonosító** oszlop alapján. Ezt *upsert*is nevezik.

A Stream Analytics optimista upsert megközelítést használ. A frissítések csak akkor történnek, ha egy Beszúrás meghiúsul egy dokumentum-AZONOSÍTÓval. 

A 1,0-es kompatibilitási szinttel Stream Analytics a frissítést javítási műveletként hajtja végre, így részlegesen frissíti a dokumentumot. Stream Analytics új tulajdonságokat vesz fel, vagy egy meglévő tulajdonságot Növekményesen cserél le. A JSON-dokumentum tömb tulajdonságaiban szereplő értékek változásai azonban a teljes tömb felülírását eredményezik. Vagyis a tömb nincs egyesítve. 

A 1,2-es upsert-viselkedés a dokumentum beszúrására vagy cseréjére van módosítva. A kompatibilitási szint 1,2-as verziójának további szakasza ezt a viselkedést ismerteti.

Ha a bejövő JSON-dokumentum rendelkezik egy meglévő azonosító mezővel, a rendszer automatikusan a Azure Cosmos DB **dokumentum-azonosító** oszlopként használja ezt a mezőt. A további írásokat a rendszer a következő esetekben kezeli:

- Az egyedi azonosítók a beszúráshoz vezethetnek.
- A duplikált azonosítók és a **dokumentumkészlet** **azonosítójának** értéke upsert.
- Az első dokumentum után a duplikált azonosítók és a **dokumentum-azonosító** nem állítható be hiba miatt.

Ha menteni kívánja az *összes* dokumentumot, beleértve az ismétlődő azonosítóval rendelkezőket is, nevezze át a lekérdezés azonosító mezőjét (a **as** kulcsszó használatával). Azure Cosmos DB hozza létre az azonosító mezőt, vagy cserélje le az azonosítót egy másik oszlop értékére (a **as** kulcsszó vagy a dokumentum- **azonosító** beállítás használatával).

## <a name="data-partitioning-in-azure-cosmos-db"></a>Adatparticionálás Azure Cosmos DB
A Azure Cosmos DB a számítási feladatok alapján automatikusan méretezi a partíciókat. Ezért javasoljuk, hogy [korlátlan](../cosmos-db/partition-data.md) méretű tárolók legyenek az adatai particionálásának módja. Ha a Stream Analytics korlátlan számú tárolóba ír, az előző lekérdezési lépésként vagy a bemeneti particionálási sémaként annyi párhuzamos írókat használ.

> [!NOTE]
> A Azure Stream Analytics csak a legfelső szintű partíciós kulcsokkal rendelkező korlátlan tárolókat támogatja. A `/region` például támogatott. A beágyazott partíciós kulcsok (például `/region/name`) nem támogatottak. 

A kiválasztható partíciós kulcstól függően a következő _Figyelmeztetés_jelenhet meg:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Fontos, hogy olyan partíciós kulcs-tulajdonságot válasszon, amely számos különböző értékkel rendelkezik, és így egyenletesen terjesztheti a számítási feladatokat ezen értékek között. A particionálás természetes összetevője, hogy az azonos partíciós kulcsra vonatkozó kérelmeket egy adott partíció maximális átviteli sebessége korlátozza. 

Az ugyanahhoz a partíciós kulcshoz tartozó dokumentumok tárolási mérete 10 GB-ra van korlátozva. Az ideális partíciós kulcs olyan, amely gyakran a lekérdezésekben található szűrőként jelenik meg, és a megoldás elégséges mértékben megfelel annak biztosításához, hogy a megoldás skálázható legyen.

A partíciós kulcs az Azure Cosmos DB tárolt eljárásaiban és eseményindítójában lévő tranzakciók határa is. A partíciós kulcsot úgy kell kiválasztani, hogy a tranzakciókban együtt előforduló dokumentumok ugyanazt a partíciós kulcs értékét használják. A particionálás a [Azure Cosmos DBban](../cosmos-db/partitioning-overview.md) című cikk részletesen ismerteti a partíciós kulcs kiválasztását.

A rögzített Azure Cosmos DB tárolók esetében a Stream Analytics a megtelt után nem lehet vertikális felskálázást végezni. Az átviteli sebesség felső határa 10 GB és 10 000 RU/s. Ha egy rögzített tárolóból egy korlátlan tárolóba kívánja áttelepíteni az adatait (például egy legalább 1 000 RU/s-t és egy partíciós kulcsot), használja az [adatáttelepítési eszközt](../cosmos-db/import-data.md) vagy a [módosítási hírcsatorna függvénytárát](../cosmos-db/change-feed.md).

A több rögzített tárolóba való írás lehetősége elavult. A Stream Analytics feladatainak méretezése nem ajánlott.

## <a name="improved-throughput-with-compatibility-level-12"></a>Javított átviteli sebesség az 1,2-os kompatibilitási szinttel
A 1,2-es kompatibilitási szinttel Stream Analytics támogatja a natív integrációt a Azure Cosmos DBba való tömeges íráshoz. Ez az integráció lehetővé teszi a hatékony írást Azure Cosmos DB az átviteli sebesség maximalizálása és a szabályozási kérelmek hatékony kezelése érdekében. 

A továbbfejlesztett írási mechanizmus új kompatibilitási szinten érhető el, mivel a upsert viselkedése eltér. A 1,2 előtti szinteken a upsert viselkedése a dokumentum beillesztése vagy egyesítése. A 1,2-es upsert-viselkedés a dokumentum beszúrására vagy cseréjére van módosítva.

A 1,2 előtti szinteken a Stream Analytics egy egyéni tárolt eljárást használ a dokumentumok upsert tömeges kiszámításához a Azure Cosmos DBba. Ott a köteg tranzakcióként van megírva. Még akkor is, ha egy rekord átmeneti hibát (szabályozást) észlel, a teljes köteget újra kell próbálkozni. Ez a forgatókönyvek esetében is viszonylag lassú szabályozást tesz lehetővé.

Az alábbi példa két azonos Stream Analytics feladatot mutat be ugyanabból az Azure Event Hubs-bemenetből. Mindkét Stream Analytics feladat [teljes mértékben particionálva](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) van egy átadó lekérdezéssel, és azonos Azure Cosmos db tárolóba ír. A bal oldali metrikák a 1,0 kompatibilitási szinttel konfigurált feladatból származnak. A jobb oldalon a metrikák a 1,2-vel vannak konfigurálva. Az Azure Cosmos DB tároló partíciós kulcsa egy egyedi GUID, amely a bemeneti eseményből származik.

![Stream Analytics mérőszámok összehasonlítása](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

A bejövő események aránya Event Hubsban két alkalommal magasabb, mint Azure Cosmos DB tárolók (20 000 RUs), ezért a rendszer a következőre állítja a szabályozást: Azure Cosmos DB. Azonban a 1,2-es feladatoknak következetesen kell megírniuk egy magasabb átviteli sebességgel (percenkénti kimeneti esemény), és alacsonyabb átlagos SU%-os kihasználtsággal. A környezetében ez a különbség néhány tényezőtől függ. Ezek a tényezők például az esemény formátuma, a bemeneti esemény/üzenet mérete, a partíciós kulcsok és a lekérdezés.

![Azure Cosmos DB mérőszámok összehasonlítása](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

A 1,2-as verzióban a Stream Analytics sokkal intelligensebb a rendelkezésre álló átviteli sebesség 100 százalékának kihasználásával Azure Cosmos DB és a szabályozástól és a díjszabástól való korlátozástól függően nagyon kevés újraküldéssel. Ez jobb felhasználói élményt nyújt más számítási feladatokhoz, például a tárolón futó lekérdezésekhez. Ha szeretné megtekinteni, hogy a Stream Analytics hogyan méretezi a Azure Cosmos DB-et a 1 000-10 000 üzenetek másodpercenkénti fogadójának, próbálja ki [ezt az Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)-beli projektet.

A Azure Cosmos DB kimenetének teljesítménye megegyezik a 1,0-es és a 1,1-es értékkel. Azt *javasoljuk* , hogy a Stream Analytics kompatibilitási 1,2 szintjének használatát Azure Cosmos db.

## <a name="azure-cosmos-db-settings-for-json-output"></a>A JSON-kimenet Azure Cosmos DB beállításai

A Azure Cosmos DB Stream Analytics kimenetként való használata a következő információt hozza létre.

![Azure Cosmos DB kimeneti adatfolyamhoz tartozó információs mezők](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Mező           | Leírás|
|-------------   | -------------|
|Kimeneti alias    | Egy alias, amely a Stream Analytics lekérdezésében erre a kimenetre hivatkozik.|
|Előfizetést    | Az Azure-előfizetés.|
|Fiókazonosító      | A Azure Cosmos DB fiók neve vagy végpontjának URI azonosítója.|
|Fiókkulcs     | A Azure Cosmos DB-fiókhoz tartozó megosztott elérési kulcs.|
|Adatbázis        | A Azure Cosmos DB adatbázis neve.|
|Tárolónév | A tároló neve, például `MyContainer`. Egy `MyContainer` nevű tárolónak léteznie kell.  |
|Dokumentumazonosító     | Választható. A kimeneti eseményekben a melyik beszúrási vagy frissítési műveletek kell alapozni egyedi kulcsaként használt oszlopok neve. Ha üresen hagyja, a rendszer az összes eseményt beszúrja, és nem rendelkezik frissítési lehetőséggel.|

Miután konfigurálta a Azure Cosmos DB kimenetét, használhatja azt a lekérdezésben a [into utasítás](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)céljaként. Ha Azure Cosmos DB kimenetet használ, [a partíciós kulcsot explicit módon be kell állítani](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). 

A kimeneti rekordnak tartalmaznia kell egy kis-és nagybetűket megkülönböztető oszlopot, amely a Azure Cosmos DB partíciós kulcsának neve után van. Ha nagyobb párhuzamos szeretne elérni, az utasításhoz szükség lehet egy olyan [Partition by záradékra](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) , amely ugyanazt az oszlopot használja.

Példa egy lekérdezésre:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Hibakezelés és újrapróbálkozások

Ha egy átmeneti hiba, a szolgáltatás nem érhető el, vagy a szabályozás nem történik meg, miközben Stream Analytics események küldése Azure Cosmos DBra, Stream Analytics a művelet sikeres befejezéséhez a rendszer határozatlan ideig próbálkozik. A következő hibák miatt azonban nem próbálkozik újra:

- Nem engedélyezett (HTTP-hibakód: 401)
- NotFound (HTTP-hibakód: 404)
- Tiltott (403-es HTTP-hibakód)
- BadRequest (HTTP-hibakód: 400)
