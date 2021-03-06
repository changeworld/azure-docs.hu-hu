---
title: Azure IoT Hub skálázás | Microsoft Docs
description: Az IoT hub méretezése a várt üzenet-átviteli sebesség és a kívánt funkciók támogatásához. Összefoglalja az egyes szintek támogatott átviteli sebességét és a horizontális skálázási lehetőségeket.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.openlocfilehash: d1de29124825a7f398b9722bb2455d1105e9c9f7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284420"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Válassza ki a megoldás megfelelő IoT Hubi szintjét

Minden IoT-megoldás eltérő, így az Azure IoT Hub számos lehetőséget kínál a díjszabás és a skála alapján. A cikk célja, hogy segítséget nyújtson a IoT Hub igényeinek kiértékelésében. A IoT Hub-csomagokra vonatkozó díjszabással kapcsolatban lásd: [IoT hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub).

Ha szeretné eldönteni, hogy melyik IoT Hub szint a legmegfelelőbb a megoldásához, kérdezzen két kérdést:

**Milyen funkciókat kívánok használni?**

Az Azure IoT Hub két, alapszintű és standard szintű csomagot kínál, amelyek az általuk támogatott szolgáltatások számától eltérőek. Ha a IoT-megoldás az adatok az eszközökről való gyűjtésén és központilag történő elemzésén alapul, az alapszintű csomag valószínűleg az Ön számára megfelelő. Ha további speciális konfigurációkat szeretne használni a IoT-eszközök távoli vezérléséhez vagy az egyes számítási feladatok saját eszközökre való terjesztéséhez, akkor érdemes megfontolnia a standard szintet. Az egyes szintekbe tartozó funkciók részletes részletezése az alapszintű [és a standard csomagra](#basic-and-standard-tiers)is vonatkozik.

**Mekkora mennyiségű adatáthelyezést tervezek naponta?**

Az egyes IoT Hub szintek három méretben érhetők el, attól függően, hogy mekkora mennyiségű adatátvitelt képesek kezelni egy adott napon belül. Ezeket a méreteket a rendszer numerikusan 1, 2 és 3 értékként azonosítja. Például az 1. szintű IoT hub minden egysége naponta 400 000 üzenetet képes kezelni, míg a 3. szint egység képes a 300 000 000 kezelésére. Az adatirányelvekkel kapcsolatos további információkért folytassa az [üzenet átviteli sebességét](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Alapszintű és standard csomag

A IoT Hub standard szintje lehetővé teszi az összes funkció használatát, és minden olyan IoT-megoldáshoz szükséges, amely a kétirányú kommunikációs képességeket kívánja kihasználni. Az alapszintű csomag lehetővé teszi a szolgáltatások egy részhalmazát, és olyan IoT-megoldások számára készült, amelyek csak az eszközökről a felhőbe irányuló, UNI-irányú kommunikációt igényelnek. Mindkét szinten ugyanazokat a biztonsági és hitelesítési funkciókat kínálja.

Egy adott rétegen belül csak egy [kiadást](https://azure.microsoft.com/pricing/details/iot-hub/) lehet kiválasztani IoT hub. Létrehozhat például egy, az S1 több egységgel rendelkező IoT Hub, de különböző kiadásokból (például S1 és S2) álló egységeket is.

| Képesség | Alapszintű csomag | Ingyenes/standard szint |
| ---------- | ---------- | ------------- |
| [Eszközről a felhőbe irányuló telemetria](iot-hub-devguide-messaging.md) | Igen | Igen |
| [Eszközönkénti azonosító](iot-hub-devguide-identity-registry.md) | Igen | Igen |
| [Üzenetek útválasztása](iot-hub-devguide-messages-read-custom.md), [üzenetek gazdagítása](iot-hub-message-enrichments-overview.md)és [Event Grid integráció](iot-hub-event-grid.md) | Igen | Igen |
| [HTTP-, AMQP-és MQTT-protokollok](iot-hub-devguide-protocols.md) | Igen | Igen |
| [Eszköz kiépítési szolgáltatása](../iot-dps/about-iot-dps.md) | Igen | Igen |
| [Monitorozás és diagnosztika](iot-hub-monitor-resource-health.md) | Igen | Igen |
| [A felhőből az eszközre irányuló üzenetküldés](iot-hub-devguide-c2d-guidance.md) |   | Igen |
| [Eszközök](iot-hub-devguide-device-twins.md), ikrek, [modulok](iot-hub-devguide-module-twins.md) [és eszközkezelés](iot-hub-device-management-overview.md) |   | Igen |
| [Eszköz streamek (előzetes verzió)](iot-hub-device-streams-overview.md) |   | Igen |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Igen |
| [IoT Plug and Play előzetes verzió](../iot-pnp/overview-iot-plug-and-play.md) |   | Igen |

A IoT Hub ingyenes szintet is biztosít teszteléshez és értékeléshez. Rendelkezik a standard csomag összes képességével, de korlátozott üzenetküldési támogatással. Az ingyenes szintről nem lehet alapszintű vagy standard szintűre frissíteni.

## <a name="partitions"></a>Partíciók

Az Azure IoT-hubok az [azure Event Hubs](../event-hubs/event-hubs-features.md)számos alapvető összetevőjét tartalmazzák, beleértve a [partíciókat](../event-hubs/event-hubs-features.md#partitions)is. A IoT hubok esetében általában a különböző IoT-eszközök által jelentett bejövő telemetria adatokkal vannak feltöltve. Az esemény-adatfolyam particionálásával csökkenthető az esemény-adatfolyamok egyidejű olvasásakor és írásakor felmerülő események.

A partíciós korlát akkor van kiválasztva, ha IoT Hub jön létre, és nem módosítható. Az alapszintű csomag IoT Hub és a standard szintű IoT Hub maximális partíciós korlátja 32. A legtöbb IoT-hubhoz csak 4 partíció szükséges. A partíciók meghatározásával kapcsolatos további információkért tekintse meg a Event Hubs GYIK, [hogy hány partícióra van szükségem?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Rétegek frissítése

Az IoT hub létrehozása után az alapszintű csomagról a standard szintre válthat a meglévő műveletek megszakítása nélkül. További információ: az [IoT hub frissítése](iot-hub-upgrade.md).

A partíció konfigurációja változatlan marad az alapszintről a standard szintű csomagra való Migrálás során.

> [!NOTE]
> Az ingyenes szint nem támogatja az alapszintű vagy a standard verzióra való frissítést.

## <a name="iot-hub-rest-apis"></a>IoT Hub REST API-k

Az alapszintű és a standard szintű IoT Hub által támogatott képességek különbsége azt jelenti, hogy egyes API-hívások nem működnek az alapszintű csomópontokkal. A következő táblázat bemutatja, hogy mely API-k érhetők el:

| API | Alapszintű csomag | Ingyenes/standard szint |
| --- | ---------- | ------------- |
| [Eszköz törlése](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | Igen | Igen |
| [Eszköz beolvasása](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | Igen | Igen |
| [Modul törlése](https://docs.microsoft.com/rest/api/iothub/service/deletemodule) | Igen | Igen |
| [Modul beolvasása](https://docs.microsoft.com/rest/api/iothub/service/getmodule) | Igen | Igen |
| [Regisztrációs statisztika beolvasása](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | Igen | Igen |
| [Szolgáltatások statisztikáinak beolvasása](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | Igen | Igen |
| [Eszköz létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | Igen | Igen |
| [Modul létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/iothub/service/createorupdatemodule) | Igen | Igen |
| [Lekérdezés IoT Hub](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | Igen | Igen |
| [Fájlfeltöltés SAS URI-ja létrehozása](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Igen | Igen |
| [Eszköz kötött értesítésének fogadása](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Igen | Igen |
| [Eszköz eseményének küldése](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Igen | Igen |
| Modul küldése esemény | Csak AMQP és MQTT | Csak AMQP és MQTT |
| [Fájlfeltöltés állapotának frissítése](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Igen | Igen |
| [Tömeges eszköz művelete](https://docs.microsoft.com/rest/api/iothub/service/bulkcreateorupdatedevices) | Igen, a IoT Edge képességek kivételével | Igen |
| [Importálási exportálási feladat megszakítása](https://docs.microsoft.com/rest/api/iothub/service/cancelimportexportjob) | Igen | Igen |
| [Importálási exportálási feladatok létrehozása](https://docs.microsoft.com/rest/api/iothub/service/createimportexportjob) | Igen | Igen |
| [Importálási exportálási feladatok beolvasása](https://docs.microsoft.com/rest/api/iothub/service/getimportexportjob) | Igen | Igen |
| [Importálási exportálási feladatok beolvasása](https://docs.microsoft.com/rest/api/iothub/service/getimportexportjobs) | Igen | Igen |
| [Parancssori várólista kiürítése](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | Igen |
| [Eszköz dupla beolvasása](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | Igen |
| [Dupla modul beolvasása](https://docs.microsoft.com/rest/api/iothub/service/getmoduletwin) |   | Igen |
| [Eszköz metódusának meghívása](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | Igen |
| [Eszköz dupla frissítése](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | Igen |
| [Modul frissítése – Twin](https://docs.microsoft.com/rest/api/iothub/service/updatemoduletwin) |   | Igen |
| [Eszköz kötött értesítésének elhagyása](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Igen |
| [Eszköz kötött értesítésének befejezése](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Igen |
| [Feladat megszakítása](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | Igen |
| [Feladatok létrehozása](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | Igen |
| [Feladatok beolvasása](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | Igen |
| [Lekérdezési feladatok](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | Igen |

## <a name="message-throughput"></a>Üzenet átviteli sebessége

IoT Hub megoldás méretezésének legjobb módja a forgalom kiértékelése egységnyi alapon. Különösen vegye figyelembe a következő típusú műveletek szükséges maximális átviteli sebességét:

* Az eszközről a felhőbe irányuló üzenetek
* Felhőből az eszközre irányuló üzenetek
* Identitásjegyzék műveletei

Az IoT hub forgalmát egységenként kell mérni. Az IoT hub létrehozásakor kiválaszthatja annak szintjét és kiadását, és beállíthatja a rendelkezésre álló egységek számát. Akár 200 egységet is megvásárolhat a B1, a B2, az S1 vagy az S2 kiadáshoz, vagy akár 10 egységet a B3 vagy az S3 kiadáshoz. Az IoT hub létrehozása után megváltoztathatja a kiadáson belül elérhető egységek számát, a szintet (B1 – B2) belüli kiadások közötti frissítést vagy visszalépést, vagy az alapszintről a standard szintre (B1 – S1) történő frissítést anélkül, hogy megszakítja a meglévő műveleteket. További információ: az [IoT hub frissítése](iot-hub-upgrade.md).  

Az egyes szintek forgalmának, az eszközről a felhőbe irányuló üzenetek esetében például a következő fenntartható átviteli sebességre vonatkozó irányelvek láthatók:

| Szintű kiadás | Tartós átviteli sebesség | Tartós küldési arány |
| --- | --- | --- |
| B1, S1 |Akár 1111 KB/perc egységenként<br/>(1,5 GB/nap/egység) |Átlagosan 278 üzenet/perc egységenként<br/>(400 000 üzenet/nap egységenként) |
| B2, S2 |Akár 16 MB/perc egységenként<br/>(22,8 GB/nap/egység) |Átlagosan 4 167 üzenet/perc egységenként<br/>(6 000 000 üzenet/nap egységenként) |
| B3, S3 |Akár 814 MB/perc egységenként<br/>(1144,4 GB/nap/egység) |Átlagosan 208 333 üzenet/perc egységenként<br/>(300 000 000 üzenet/nap egységenként) |

Az eszközről a felhőbe irányuló átviteli sebesség csak az egyik mérőszám, amelyet figyelembe kell vennie egy IoT-megoldás tervezésekor. További információk: [IoT hub kvóták és szabályozások](iot-hub-devguide-quotas-throttling.md).

### <a name="identity-registry-operation-throughput"></a>Azonosító beállításjegyzékbeli művelet átviteli sebessége

Az IoT Hub Identity registry-műveletek nem feltételezetten futásidejű műveletek, mivel ezek többnyire az eszközök kiépítés kapcsán állnak kapcsolatban.

Adott burst-teljesítményi számok esetében lásd: [IoT hub kvóták és szabályozások](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Automatikus méretezés

Ha közeledik az engedélyezett IoT hub-korláthoz, a következő [lépésekkel automatikusan méretezheti](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) az IoT hub egységet ugyanazon a IoT hub szinten.

## <a name="next-steps"></a>További lépések

* További információ a IoT Hub képességekről és a teljesítmény részleteiről: [IoT hub díjszabási](https://azure.microsoft.com/pricing/details/iot-hub) vagy [IoT hub kvóták és szabályozások](iot-hub-devguide-quotas-throttling.md).

* Ha módosítani szeretné a IoT Hub szintet, kövesse az [IoT hub frissítésének](iot-hub-upgrade.md)lépéseit.
