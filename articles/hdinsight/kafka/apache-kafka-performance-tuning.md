---
title: Apache Kafka HDInsight-fürtök teljesítményének optimalizálása
description: Áttekintést nyújt Apache Kafka számítási feladatok Azure HDInsight való optimalizálásához szükséges technikákról.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/26/2019
ms.locfileid: "75494922"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Apache Kafka HDInsight-fürtök teljesítményének optimalizálása

Ez a cikk néhány javaslatot tartalmaz a Apache Kafka számítási feladatok teljesítményének optimalizálására a HDInsight-ben. A hangsúly a gyártó és a közvetítő konfigurációjának beállításán alapul. A teljesítmény mérése különböző módokon történik, és az alkalmazott optimalizálások az üzleti igényektől függenek.

## <a name="architecture-overview"></a>Az architektúra áttekintése

A Kafka-témakörök a rekordok rendszerezésére szolgálnak. A rekordokat a gyártók állítják elő, és a fogyasztók használják. A gyártók rekordokat küldenek a Kafka-közvetítőknek, amelyek ezután tárolják az adatokat. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Kafka-közvetítő.

A témakörök particionálják a rekordokat a közvetítők között. A rekordok felhasználásakor partíciónként legfeljebb egy fogyasztó használható, az adatok párhuzamos feldolgozása érdekében.

A replikáció a csomópontok közötti duplikált partíciók esetében használatos. Ez védelmet biztosít a csomópontok (brókerek) leállása ellen. A replikák csoportja között egyetlen partíció van kijelölve partíciós vezetőként. Az előállítói forgalmat a csomópontok vezetőjéhez irányítja a rendszer a ZooKeeper által kezelt állapot segítségével.

## <a name="identify-your-scenario"></a>A szituáció azonosítása

Apache Kafka teljesítménynek két fő aspektusa van – az átviteli sebesség és a késés. Az átviteli sebesség a maximális sebesség, amellyel az adatok feldolgozhatók. A nagyobb átviteli sebesség általában jobb. A késés az adattároláshoz vagy a lekéréshez szükséges idő. Az alacsonyabb késés általában jobb. Kihívást jelenthet az átviteli sebesség, a késés és az alkalmazási infrastruktúra díja közötti megfelelő egyensúly megtalálása. A teljesítményre vonatkozó követelmények valószínűleg a következő három gyakori helyzet valamelyikével fognak megfelelni, attól függően, hogy magas átviteli sebességet, kis késést vagy mindkettőt igényel:

* Nagy teljesítményű, kis késleltetésű. Ehhez a forgatókönyvhöz nagy átviteli sebesség és kis késleltetés szükséges (~ 100 ezredmásodperc). Ilyen típusú alkalmazás például a szolgáltatás rendelkezésre állásának figyelése.
* Nagy teljesítményű, nagy késésű. Ehhez a forgatókönyvhöz nagy átviteli sebesség szükséges (~ 1,5 GB/s), de nagyobb késést (< 250 MS) is el tud viselni. Ilyen típusú alkalmazás például telemetria az adatfeldolgozás a közel valós idejű folyamatok, például a biztonsági és a behatolás-felismerő alkalmazások számára.
* Alacsony átviteli sebesség, kis késleltetés. Ehhez a forgatókönyvhöz kis késés (< 10 MS) szükséges a valós idejű feldolgozáshoz, de az alacsonyabb átviteli sebességet is képes elviselni. Ilyen típusú alkalmazás például az online helyesírás-és nyelvhelyesség-ellenőrzés.

## <a name="producer-configurations"></a>Termelői konfigurációk

A következő részekben a Kafka-gyártók teljesítményének optimalizálása érdekében a legfontosabb konfigurációs tulajdonságokat fogjuk kiemelni. Az összes konfigurációs tulajdonság részletes ismertetését lásd: [Apache Kafka dokumentáció a termelői konfigurációkról](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Köteg mérete

Apache Kafka-termelők olyan üzeneteket (batchs-csoportokat) állítanak össze, amelyeket egyetlen tárolási partícióban tárolt egységként kell elküldeni. A köteg mérete azt jelenti, hogy hány bájtnak kell szerepelnie a csoport továbbítása előtt. A `batch.size` paraméter növelése növelheti az átviteli sebességet, mert csökkenti a hálózati és az IO-kérelmek feldolgozási terhelését. A könnyű betöltés alatt a köteg nagyobb mérete növelheti a Kafka küldési késleltetését, mivel a termelő megvárja, amíg a köteg készen áll. A nagy terhelés alatt javasolt a Batch méretének növelése az átviteli sebesség és a késés javítása érdekében.

### <a name="producer-required-acknowledgments"></a>Producer szükséges nyugták

A gyártó által igényelt `acks` konfiguráció meghatározza, hogy a partíció vezetője milyen nyugtákat igényel, mielőtt az írási kérelem befejeződik. Ez a beállítás hatással van az adatok megbízhatóságára, és a `0`, `1`vagy `-1`értékeit veszi figyelembe. `-1` értéke azt jelenti, hogy az írás befejezése előtt az összes replikáról nyugtát kell kapnia. A `acks = -1` beállítása nagyobb mértékű garanciát biztosít az adatvesztés ellen, de magasabb késést és alacsonyabb átviteli sebességet is eredményez. Ha az alkalmazásra vonatkozó követelmények nagyobb átviteli sebességet igényelnek, próbálkozzon `acks = 0` vagy `acks = 1`beállításával. Ne feledje, hogy az összes replikát nem ismerheti fel az adatmegbízhatóság csökkentése érdekében.

### <a name="compression"></a>Tömörítés

A Kafka-gyártó konfigurálható úgy, hogy tömörítse az üzeneteket, mielőtt elküldené őket a brókereknek. A `compression.type` beállítás a használni kívánt tömörítési kodeket határozza meg. A támogatott tömörítési kodekek a következők: "gzip", "Snappy" és "lz4". A tömörítés hasznos lehet, és figyelembe kell venni a lemez kapacitásának korlátozását.

A két leggyakrabban használt tömörítési kodek, `gzip` és `snappy`esetében a `gzip` nagyobb tömörítési aránnyal rendelkezik, ami nagyobb CPU-terhelést eredményez. A `snappy` kodek kevesebb tömörítést biztosít kevesebb CPU-terheléssel. Eldöntheti, hogy melyik kodeket szeretné használni a Broker Disk vagy a producer CPU korlátozásai alapján. a `gzip` a `snappy`nál ötször nagyobb mértékben tömörítheti az adatok sebességét.

Az adattömörítés használatával növelheti a lemezen tárolható rekordok számát. Emellett növelheti a CPU-terhelést azokban az esetekben, amikor a gyártó és a közvetítő által használt tömörítési formátumok között eltérés tapasztalható. mivel a feldolgozás előtt az adatoknak tömörítettnek kell lenniük a küldés előtt, majd ki kell tömöríteni őket.

## <a name="broker-settings"></a>A bróker beállításai

A következő részekben a Kafka-közvetítők teljesítményének optimalizálása érdekében a legfontosabb beállítások jelennek meg. A brókeri beállítások részletes ismertetését lásd: [Apache Kafka dokumentáció a termelői konfigurációkról](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="number-of-disks"></a>Lemezek száma

A tároló lemezek száma korlátozott IOPS (bemeneti/kimeneti műveletek másodpercenként) és olvasási/írási bájtok másodpercenként. Új partíciók létrehozásakor a Kafka a lemezen lévő összes új partíciót a lehető legkevesebb partícióval tárolja a rendelkezésre álló lemezek elosztása érdekében. A tárolási stratégia ellenére, amikor az egyes lemezeken több száz partíciós replikát dolgoz fel, a Kafka könnyedén leterhelheti a rendelkezésre álló lemez átviteli sebességét. A kompromisszum az átviteli sebesség és a Cost között történik. Ha az alkalmazásnak nagyobb átviteli sebességre van szüksége, hozzon létre egy több felügyelt lemezzel rendelkező fürtöt. A HDInsight jelenleg nem támogatja a felügyelt lemezek hozzáadását egy futó fürthöz. A felügyelt lemezek számának konfigurálásával kapcsolatos további információkért lásd: a [HDInsight Apache Kafka tárolásának és méretezhetőségének konfigurálása](apache-kafka-scalability.md). Ismerje meg a fürt csomópontjaihoz tartozó tárolóhelyek növelésének költségeit.

### <a name="number-of-topics-and-partitions"></a>Témakörök és partíciók száma

A Kafka-producerek a témakörökbe írnak. A Kafka-fogyasztók olvasni a témákat. A témakörök egy naplóhoz vannak társítva, amely a lemez adatstruktúrája. A Kafka egy gyártó (k) rekordjait egy témakör-napló végére fűzi. A témakör naplója számos olyan partíciót tartalmaz, amelyek több fájlon oszlanak el. Ezek a fájlok viszont több Kafka-fürt csomópontjain oszlanak el. A felhasználók a saját lépésszám alapján olvassák el a Kafka-témaköröket, és a témakör naplójában kiválaszthatják a helyüket (eltolás).

Mindegyik Kafka-partíció egy naplófájl a rendszeren, és a termelői szálak egyszerre több naplóba is írhatnak. Hasonlóképpen, mivel minden fogyasztói szál az egyik partícióról olvas üzeneteket, a több partícióból származó adatokat is párhuzamosan kezeli.

A partíciók sűrűségének növelése (a partíciók száma a brókerek számára) a metaadat-műveletekhez kapcsolódó terhelést, valamint a partíciós vezető és a követői által küldött partíciós kérelmeket és válaszokat is növeli. Még a-on keresztüli adatforgalom hiányában is a partíciós replikák továbbra is lekérik az adatokról a vezetőktől, ami további feldolgozást eredményez a küldési és fogadási kérelmek számára a hálózaton keresztül.

Apache Kafka a 1,1-es és a HDInsight-nál újabb fürtök esetében azt javasoljuk, hogy brókerként legfeljebb 1000 partíciót, beleértve a replikákat is. A partíciók számának növelése a brókerek számára csökkenti az átviteli sebességet, és a téma hiányát is okozhatja. A Kafka-partíciók támogatásával kapcsolatos további információkért tekintse meg a következőt: [hivatalos Apache Kafka blogbejegyzés a támogatott partíciók számának növekedését a 1.1.0 verzióban](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). A témakörök módosításáról további részleteket a [Apache Kafka: témakörök módosítása](https://kafka.apache.org/documentation/#basic_ops_modify_topic)című témakörben talál.

### <a name="number-of-replicas"></a>Replikák száma

A magasabb replikációs tényező további kérelmeket eredményez a partíciós vezető és a követői között. Ennek következtében egy magasabb replikációs tényező több lemezt és CPU-t használ a további kérések kezeléséhez, az írási késés növeléséhez és a csökkenő átviteli sebességhez.

Javasoljuk, hogy legalább 3x-os replikálást használjon a Kafka számára az Azure HDInsight-ben. A legtöbb Azure-régió három tartalék tartománnyal rendelkezik, de a csak két tartalék tartománnyal rendelkező régiókban a felhasználóknak a 4x-es replikációt kell használniuk.

A replikációval kapcsolatos további információkért lásd [: Apache Kafka: replikáció](https://kafka.apache.org/documentation/#replication) és [Apache Kafka: a replikációs tényező növelése](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Következő lépések

* [Napi több trillió esemény feldolgozása az Apache Kafka on Azure használatával](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Mi a HDInsight Apache Kafka?](apache-kafka-introduction.md)
