---
title: Mi az Azure Event Hubs? – Big adatfeldolgozási szolgáltatás | Microsoft Docs
description: További tudnivalók az Azure Event Hubsról, amely egy másodpercenként több millió eseményt feldolgozó Big Data streamszolgáltatás.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: overview
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e8a91647d7532a9904901c247bb0b4096ffa0a1c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161698"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs – A big data streaming platform és az esemény-betöltési szolgáltatás
Az Azure Event Hubs egy big data streaming platform és esemény-betöltési szolgáltatás. Másodpercenként akár több millió eseményt is képes fogadni és feldolgozni. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók.

A következő forgatókönyvek néhány olyan forgatókönyvet érintenek, amelyekben a Event Hubs használható:

- Rendellenességek észlelése (csalások/kiugró értékek)
- Alkalmazásnaplózás
- Elemzési folyamatok, például kattintássorozatoké
- Élő irányítópultok
- Adatok archiválása
- Tranzakciófeldolgozás
- Felhasználói telemetria feldolgozása
- Eszköztelemetria-stream

<iframe width="560" height="315" src="https://www.youtube.com/embed/45wgY-VSk9I" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## <a name="why-use-event-hubs"></a>Miért érdemes az Event Hubs platformot használni?

Az adatok csak akkor képviselnek értéket, ha könnyen feldolgozhatók, és időszerű elemzések állíthatók össze az adatforrásokból. A Event Hubs az Azure-on belüli és azon kívüli adatelemzési és a zökkenőmentes integrációt biztosító elosztott adatfolyam-feldolgozó platformot biztosít a teljes big data folyamat létrehozásához.

Az Event Hubs az eseményfolyamatok „bejárati ajtaja”, amelyet gyakran neveznek *eseménybetöltőnek* a megoldásarchitektúrákban. Az eseménybetöltő egy olyan összetevő vagy szolgáltatás, amely az esemény-közzétevők és az eseményfelhasználók közé ékelődve elkülöníti az eseménystream létrehozását az események felhasználásától. A Event Hubs egy egységes adatfolyam-platformot biztosít az időmegőrzési pufferrel, az események fogyasztóitól való leválasztást.

A következő szakaszok az Azure Event Hubs szolgáltatás legfontosabb jellemzőit ismertetik:

## <a name="fully-managed-paas"></a>Teljes mértékben felügyelt PaaS

A Event Hubs egy teljes körűen felügyelt platform-szolgáltatás (a Pásti), amely kis konfigurációval vagy felügyeleti terheléssel rendelkezik, így Ön az üzleti megoldásokra koncentrálhat. [Az Apache Kafkához készült Event Hubs-ökoszisztéma](event-hubs-for-kafka-ecosystem-overview.md) anélkül biztosítja a PaaS Kafka előnyeit, hogy saját fürtöket kellene kezelnie, konfigurálnia vagy futtatnia.

## <a name="support-for-real-time-and-batch-processing"></a>Valós idejű kötegelt feldolgozás támogatása

Valós időben töltheti be, pufferelheti, tárolhatja és dolgozhatja fel a streameket, hogy a gyakorlatban is használható elemzésekhez jusson. A Event Hubs [particionált fogyasztói modellt](event-hubs-scalability.md#partitions)használ, amely lehetővé teszi, hogy több alkalmazás egyszerre dolgozza fel az adatfolyamot, és lehetővé tegye a feldolgozás sebességének szabályozását.

Az adatok közel valós időben [rögzíthetők](event-hubs-capture-overview.md) [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) -ban vagy [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) a hosszú távú adatmegőrzés vagy a mikro-batch feldolgozás számára. Ezt a viselkedést a valós idejű elemzések elvégzéséhez használt adatfolyamon érheti el. Az események rögzítésének beállítása gyors. Nincs felügyeleti költség a futtatásához, és a rendszer automatikusan méretezi Event Hubs [átviteli egységekkel](event-hubs-scalability.md#throughput-units). A Event Hubs lehetővé teszi az adatfeldolgozásra koncentrálva az adatrögzítés helyett.

Az Azure Event Hubs az [Azure Functions](/azure/azure-functions/) szolgáltatással is integrálható egy kiszolgáló nélküli architektúra keretein belül.

## <a name="scalable"></a>Méretezhető

Az Event Hubs szolgáltatással megabájtokban kezdheti az adatstreameket, és onnan továbbléphet a giga- és terabájtokra. Az [automatikus feltöltési](event-hubs-auto-inflate.md) funkció az egyik lehetőség az átviteli egységek számának a használati igényeinek megfelelő módosítására.

## <a name="rich-ecosystem"></a>Gazdag ökoszisztéma

[A Apache Kafka ökoszisztémák Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) [Apache Kafka (1,0 és újabb)](https://kafka.apache.org/) ügyfelek és alkalmazások számára lehetővé teszi, hogy Event Hubsekkel beszéljenek. Nem kell beállítania, konfigurálnia és felügyelni saját Kafka-fürtöket.

A különböző [nyelveken (.net, Java, Python, go, JavaScript)](https://github.com/Azure/azure-event-hubs)elérhető széles körű ökoszisztémával könnyedén elindíthatja a streamek feldolgozását Event Hubsról. Az összes támogatott ügyfélnyelv alacsony szintű integrációt biztosít. Az ökoszisztéma olyan zökkenőmentes integrációt is biztosít az Azure-szolgáltatásokkal, mint például a Azure Stream Analytics és a Azure Functions, így kiszolgáló nélküli architektúrákat hozhat létre.

## <a name="key-architecture-components"></a>Az architektúra legfontosabb összetevői
Az Event Hubs az alábbi [fő összetevőket](event-hubs-features.md) foglalja magába:

- **Esemény-előállítók**: Minden entitás, amely adatokat küld egy eseményközpontnak. Az esemény-közzétevők a HTTPS, az AMQP 1.0 vagy az Apache Kafka (1.0 és újabb) használatával tehetik közzé az eseményeket.
- **Partíciók**: Mindegyik felhasználó az üzenetstreamnek csak egy adott részét vagy partícióját olvassa.
- **Felhasználói csoportok**: A teljes eseményközpont egyik nézete (állapot, pozíció vagy eltolás). A fogyasztói csoportok lehetővé teszik, hogy az alkalmazások mindegyike külön nézetben jelenítse meg az esemény-adatfolyamot. A streamet egymástól függetlenül, saját tempójában, saját eltolással olvassák.
- **Átviteli egységek**: Előre megvásárolt kapacitásegységek, amelyek az eseményközpontok átviteli kapacitását vezérlik.
- **Eseményérzékelő**: Minden entitás, amely eseményadatokat olvas egy eseményközpontból. Minden Event Hubs fogyasztó a AMQP 1,0-munkameneten keresztül kapcsolódhat. A Event Hubs szolgáltatás egy munkameneten keresztül nyújt eseményeket, amint azok elérhetővé válnak. Minden Kafka-fogyasztó a Kafka-protokoll 1.0-s vagy újabb verziójával csatlakozik.

A következő ábrán az Event Hubs streamfeldolgozási architektúrája látható:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.png)


## <a name="next-steps"></a>Következő lépések

Az Event Hubs használatának megkezdéséhez tekintse meg az **események küldése és fogadása** oktatóanyagokat:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (csak küldés)](event-hubs-c-getstarted-send.md)
- [Apache Storm (csak fogadás)](event-hubs-storm-getstarted-receive.md)


Az Event Hubsszal kapcsolatos további információkért olvassa el a következő cikkeket:

- [Az Event Hubs-szolgáltatások áttekintése](event-hubs-features.md)
- [Gyakori kérdések](event-hubs-faq.md)


