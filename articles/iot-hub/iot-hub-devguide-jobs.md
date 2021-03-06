---
title: Azure IoT Hub-feladatok ismertetése |} A Microsoft Docs
description: Az IoT hub fejlesztői útmutató – több eszközön futtatandó feladatok ütemezése csatlakoztatva. Feladatok frissítheti a címkéket és kívánt tulajdonságok és több eszközön közvetlen metódusok meghívása.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 147dd0f454bd85673bcba5cd6148c5da9716c580
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409065"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Feladatok ütemezése több eszközön

Az Azure IoT Hub lehetővé teszi, hogy a építőelemek, például számos [eszköz-ikertulajdonságok és címkék](iot-hub-devguide-device-twins.md) és [közvetlen metódusok](iot-hub-devguide-direct-methods.md). Általában a háttér-alkalmazások engedélyezése eszközadminisztrátorok és üzemeltetői, módosíthatja és használhatja az IoT-eszközök tömeges és a egy megadott időpontban. Feladatok ütemezett időpontban ikereszköz-frissítések és az eszközök ellen közvetlen metódusok végrehajtása. Az operátor például egy háttér-alkalmazást, amely kezdeményezi és követi nyomon egy feladatot, amely az eszközök, amelyek nem lenne zavaró a az épület a műveletek egyszerre 43 és emelet 3 épületben újraindítás használja.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Vegye figyelembe, hogy bármelyikével feladatok ütemezését és nyomon követheti a szolgálatot a következő tevékenységeket meg az eszközök:

* Eszköz kívánt tulajdonságainak frissítése
* A címkék frissítése
* Közvetlen metódusok meghívása

## <a name="job-lifecycle"></a>Feladat életciklusa

Feladatok a megoldás háttérrendszere által kezdeményezett, és az IoT Hub által karbantartott. Egy feladat keresztül a szolgáltatás által használt URI-t is kezdeményezhető (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`), és folyamatban van egy végrehajtó feladaton keresztül a szolgáltatás által használt URI lekérdezése (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`). Feladat lekérdezés futtatása a futó feladatok, ha egy feladat állapotának frissítéséhez.

> [!NOTE]
> Ha olyan feladatot kezdeményez, nevét és értékeit tartalmazhat US-ASCII nyomtatható alfanumerikus, kivéve az alábbi: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Közvetlen metódusok végrehajtása feladatok

Az alábbi kódrészlet bemutatja a HTTPS 1.1 a kérelem részletes adatainak végre egy [közvetlen metódus](iot-hub-devguide-direct-methods.md) meg az eszközök egy feladat használatával:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

A lekérdezési feltétel is lehet egyetlen Eszközazonosítót vagy eszköz a következő példákban szemléltetett módon azonosítók listáját:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md) IoT Hub lekérdezési nyelv további részletesen ismerteti.

A következő kódrészlet azt mutatja be, a kérés- és egy közvetlen metódus meghívása a contoso-hub-1, minden eszközön testMethod nevű ütemezett feladat:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>Eszköz-ikertulajdonságok frissíteni feladatok

A következő kódrészlet azt mutatja be, HTTPS 1.1 kérés részleteinek frissítése egy feladat használatával eszköz-ikertulajdonságok:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> A *updateTwin* tulajdonság érvényes etag egyezést igényel; például `etag="*"`.

A következő kódrészlet azt mutatja be, a kérés- és frissíteni a teszt-device-contoso-hub-1 eszköz-ikertulajdonságok ütemezett feladat:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>A folyamatban lévő feladatok lekérdezése

Az alábbi kódrészlet bemutatja a feladatok lekérdezése HTTPS 1.1 kérelem részletei:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

A válaszból biztosítja a continuationtoken argumentumot használja.

A feladat végrehajtási állapotát minden egyes eszköz segítségével lekérdezhető a [az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Feladat tulajdonságai

Az alábbi lista a tulajdonságait, és a vonatkozó leírásokat, amely használható lekérdezésekor feladatok vagy a feladat eredményeinek tartalmazza.

| Tulajdonság | Leírás |
| --- | --- |
| **jobId** |Alkalmazás azonosítója a feladatra vonatkozóan megadott. |
| **startTime** |Alkalmazás a megadott kezdési idő (ISO-8601) a feladathoz. |
| **endTime** |Az IoT Hub dátuma (ISO-8601) amikor a feladat befejeződött-e megadva. Csak azt követően a feladat eléri a "kész" állapot érvényes. |
| **type** |Feladatok típusai: |
| | **scheduleUpdateTwin**: Egy feladat, kívánt tulajdonságok vagy címkék frissítése. |
| | **scheduleDeviceMethod**: Ikereszközök különböző eszköz metódus meghívásához használt feladat. |
| **status** |A feladat jelenlegi állapota. Lehetséges értékek a állapota: |
| | **Függőben lévő**: Ütemezett és váró észlelnie kell a feladatokat végző szolgáltatás. |
| | **Ütemezett**: Ütemezi későbbi időpontra. |
| | **Futó**: A jelenleg aktív feladat. |
| | **Meg lett szakítva**: Feladat meg lett szakítva. |
| | **Nem sikerült**: Nem sikerült végrehajtani a feladatot. |
| | **Befejezett**: Feladat befejeződött. |
| **deviceJobStatistics** |A feladat-végrehajtási statisztikája. |
| | **deviceJobStatistics** tulajdonságai: |
| | **deviceJobStatistics.deviceCount**: A feladat eszközök száma. |
| | **deviceJobStatistics.failedCount**: Ha a feladat nem sikerült eszközök száma. |
| | **deviceJobStatistics.succeededCount**: Ha a feladat sikeres eszközök száma. |
| | **deviceJobStatistics.runningCount**: A feladat jelenleg futó eszközök száma. |
| | **deviceJobStatistics.pendingCount**: A feladat futtatása függőben lévő eszközök száma. |

### <a name="additional-reference-material"></a>További – referenciaanyag

Az IoT Hub fejlesztői útmutató más referencia témakörei a következők:

* [IoT Hub-végpontok](iot-hub-devguide-endpoints.md) ismerteti a különféle végpontok, amely minden IoT-központ közzéteszi a futásidejű és felügyeleti műveletekhez.

* [Sávszélesség-szabályozási és kvóták](iot-hub-devguide-quotas-throttling.md) ismerteti a kvótákat, az IoT Hub szolgáltatás és a szabályozás működésék, számítson, ha a szolgáltatás használatához.

* [Az Azure IoT eszköz- és szolgáltatásspecifikus SDK-k](iot-hub-devguide-sdks.md) felsorolja a különböző nyelvű SDK-ban is használhatja az IoT Hub szolgáltatással kommunikáló eszközt és szolgáltatást is alkalmazások fejlesztése során.

* [Az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása](iot-hub-devguide-query-language.md) ismerteti az IoT Hub lekérdezési nyelv. A lekérdező nyelv használata az IoT Hub az ikereszközökhöz és feladatokhoz kapcsolatos információk lekérése.

* [IoT Hub MQTT-támogatás](iot-hub-mqtt-support.md) további információ az IoT Hub-támogatásról nyújt az MQTT protokoll.

## <a name="next-steps"></a>További lépések

Próbálja ki a jelen cikkben ismertetett fogalmakat, tekintse meg a következő IoT Hub-oktatóanyag:

* [Feladatok ütemezése és szórása](iot-hub-node-node-schedule-jobs.md)