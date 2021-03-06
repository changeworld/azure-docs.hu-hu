---
title: Korlátok, kvóták és küszöbértékek az Azure Schedulerben
description: Az Azure Scheduler korlátainak, kvótáinak, alapértelmezett értékeinek és szabályozási küszöbértékének megismerése
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898527"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Korlátok, kvóták és szabályozási küszöbértékek az Azure Schedulerben

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) az Azure Scheduler cseréje [folyamatban](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)van. Ha továbbra is szeretne dolgozni a Feladatütemezőben beállított feladatokkal, akkor a lehető leghamarabb [telepítse át Azure Logic apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Az ütemező már nem érhető el a Azure Portalban, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagjai](scheduler-powershell-reference.md) jelenleg is elérhetők maradnak, így a feladatok és a feladatok gyűjteményei kezelhetők.

## <a name="limits-quotas-and-thresholds"></a>Korlátok, kvóták és küszöbértékek

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-MS-Request-ID fejléc

Minden, az ütemező szolgáltatással kapcsolatban benyújtott kérelem egy **x-MS-Request-ID**nevű válasz fejlécet ad vissza. Ez a fejléc egy átlátszatlan értéket tartalmaz, amely egyedileg azonosítja a kérést. Ha tehát a kérést a rendszer következetesen meghibásodik, és Ön megerősítette, hogy a kérés megfelelően formázott, a hibát a Microsoftnak az **x-MS-Request-ID** válasz fejlécének megadásával, valamint a következő részletekkel is jelezheti: 

* Az **x-MS-Request-ID** érték
* A kérelem teljesítésének becsült időpontja 
* Az Azure-előfizetéshez, a feladathoz és a feladathoz tartozó azonosítók 
* A kérelem által megkísérelt művelet típusa

## <a name="next-steps"></a>További lépések

* [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)
* [Csomagok és számlázás az Azure Schedulerben](scheduler-plans-billing.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)
* [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)