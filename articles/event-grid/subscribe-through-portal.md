---
title: Előfizetések Azure Event Grid portálon keresztül
description: Ez a cikk azt ismerteti, hogyan hozhatók létre Event Grid-előfizetések a támogatott forrásokhoz, például az Azure Blob Storagehoz a Azure Portal használatával.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 3172c92ecae094ab5d978803d2ccac7e6404a5e1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721506"
---
# <a name="subscribe-to-events-through-portal"></a>Előfizetés az eseményekre a portálon keresztül

Ez a cikk azt ismerteti, hogyan hozhatók létre Event Grid-előfizetések a portálon keresztül.

## <a name="create-event-subscriptions"></a>Esemény-előfizetések létrehozása

Ha Event Grid-előfizetést szeretne létrehozni bármelyik támogatott [eseményforrás](event-sources.md)esetében, kövesse az alábbi lépéseket. Ez a cikk bemutatja, hogyan hozhat létre Event Grid-előfizetést egy Azure-előfizetéshez.

1. Válassza az **Összes szolgáltatás** elemet.

   ![Válassza ki az összes szolgáltatás](./media/subscribe-through-portal/select-all-services.png)

1. Keressen **Event Grid előfizetéseket** , és válassza ki az elérhető lehetőségek közül.

   ![Keresés](./media/subscribe-through-portal/search.png)

1. Válassza a **+ Esemény-előfizetés** lehetőséget.

   ![Előfizetés hozzáadása](./media/subscribe-through-portal/add-subscription.png)

1. Válassza ki a létrehozni kívánt előfizetés típusát. Ha például az Azure-előfizetéséhez tartozó eseményekre szeretne előfizetni, válassza az **Azure-előfizetések** és a cél előfizetés lehetőséget.

   ![Azure-előfizetés kiválasztása](./media/subscribe-through-portal/azure-subscription.png)

1. Az eseményforrás összes eseménytípus előfizetéséhez tartsa be az **előfizetést az összes eseménytípus** beállításnál. Egyéb esetben válassza ki az előfizetéshez tartozó eseménytípus típusát.

   ![Eseménytípus kiválasztása](./media/subscribe-through-portal/select-event-types.png)

1. Adja meg az esemény-előfizetés további részleteit, például az események kezelésére szolgáló végpontot és az előfizetés nevét.

   ![Előfizetés részleteinek megadása](./media/subscribe-through-portal/provide-subscription-details.png)

1. A kézbesítetlen üzenetek engedélyezéséhez és az újrapróbálkozási szabályzatok testreszabásához válassza a **További szolgáltatások**lehetőséget.

   ![További funkciók kiválasztása](./media/subscribe-through-portal/select-additional-features.png)

1. Válasszon ki egy tárolót, amelyet nem kézbesítő események tárolására kíván használni, és állítsa be az újrapróbálkozások küldésének módját.

   ![A kézbesítetlen levelek engedélyezése és újrapróbálkozás](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Ha elkészült, válassza a **Létrehozás** lehetőséget.

## <a name="create-subscription-on-resource"></a>Előfizetés létrehozása erőforráson

Néhány eseményforrás támogatja az esemény-előfizetések létrehozását az adott erőforráshoz tartozó portál felületén keresztül. Válassza ki az eseményforrás, és keresse meg az **eseményeket** a bal oldali ablaktáblán.

![Előfizetés részleteinek megadása](./media/subscribe-through-portal/resource-events.png)

A portálon megtekintheti az adott forráshoz kapcsolódó esemény-előfizetések létrehozásának lehetőségeit.

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítéséről és újrapróbálkozásáról, [Event Grid az üzenetek kézbesítéséről, és próbálkozzon újra](delivery-and-retry.md).
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
