---
title: Nyilvános előzetes verziójú szolgáltatás korlátai – Azure digitális Twins | Microsoft Docs
description: Ismerje meg a nyilvános előzetes verziójú szolgáltatást, az előfizetést, a példányt és a díjszabási korlátokat az Azure digitális Twins szolgáltatáshoz.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 5e323d8faa19ceb0712aa6183df17740ce2a0a1d
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370379"
---
# <a name="public-preview-service-limits"></a>A szolgáltatás nyilvános előzetes verziójának korlátozásai

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

A nyilvános előzetes verzióban az Azure Digital Twins az alábbi ideiglenes előfizetést, példányt és díjszabási korlátozásokat tartalmaz a meglévő ügyfelek számára. Ezek a megkötések megkönnyítik az új szolgáltatás és számos funkció megismerését, és az általánosan elérhetővé tétel (GA) növelésével vagy eltávolításával.

## <a name="per-subscription-limits"></a>Előfizetési korlátok

A nyilvános előzetes verzióban minden egyes Azure-előfizetés egyszerre csak egy Azure digitális Twins-példányt tud létrehozni vagy futtatni. Ha törli a példányt, létrehozhat egy újat.

## <a name="per-instance-limits"></a>Felhasználónkénti korlátok

Az egyes Azure-beli digitális Twins-példányok pedig a következőket tehetik:

- Pontosan egy beágyazott **IoTHub** -erőforrás, amelyet a rendszer automatikusan hozott létre a szolgáltatás kiépítés során.
- Pontosan egy **EventHub** -végpont az esemény típusának **DeviceMessage**.
- Legfeljebb három **EventHub**, **ServiceBus**vagy **EventGrid** végpont, **SensorChange**, **SpaceChange**, **TopologyOperation**vagy **UdfCustom**eseménytípus.

> [!NOTE]
> A nyilvános előzetes verzióban általában a fenti Azure IoT-entitások létrehozásakor meghatározott paraméterek nem szükségesek.
> - A legfrissebb API-specifikációkat a [hencegő dokumentációban találja](./how-to-use-swagger.md) .

## <a name="azure-digital-twins-management-api-limits"></a>Azure digitális Twins felügyeleti API-korlátok

Az Azure Digital Twins felügyeleti API-ra vonatkozó kérelmek díjszabása a következő:

- 100 másodpercenkénti kérelmek az Azure digitális Twins felügyeleti API-hoz.
- Akár 1 000 objektum, amelyet egyetlen Azure digitális ikrek felügyeleti API-lekérdezése adott vissza.

> [!IMPORTANT]
> Ha túllépi az 1 000-Object korlátot, hibaüzenetet kap, és egyszerűsíteni kell a lekérdezést.

## <a name="user-defined-functions-rate-limits"></a>Felhasználó által definiált függvények díjszabási korlátai

A következő korlátok határozzák meg az Azure Digital Twins-példányon végrehajtott összes felhasználó által megadott függvényhívás teljes számát:

- 400 ügyféloldali függvénytár-hívások másodpercenként
- 100 **SendNotification** -hívások másodpercenként

> [!NOTE]
> A következő műveletek a további díjszabási korlátokat is okozhatják ideiglenesen:
> - A topológiai objektum metaadatainak módosításai
> - A felhasználó által definiált függvény definíciójának frissítései
> - Azok az eszközök, amelyek első alkalommal küldenek telemetria

## <a name="device-telemetry-limits"></a>Eszközök telemetria korlátai

Az alábbi korlátok az eszközök által az Azure Digital Twins-példányba küldött összes üzenet teljes száma.

- 100 üzenet másodpercenként az összes eszközön
-    25 üzenet/másodperc/eszköz

## <a name="next-steps"></a>További lépések

- Egy Azure-beli digitális Twins-minta kipróbálásához lépjen a gyors üzembe helyezési [lehetőségre, és keresse meg az elérhető szobákat](./quickstart-view-occupancy-dotnet.md).
