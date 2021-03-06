---
title: Azure Application Insights adatmodell – nyomkövetési telemetria
description: Application Insights adatmodell nyomkövetési telemetria
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671953"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Nyomkövetési telemetria: Application Insights adatmodell

A nyomkövetési telemetria ( [Application Insights](../../azure-monitor/app/app-insights-overview.md)) a szöveg által keresett, `printf` stílusú nyomkövetési utasításokat jelöli. a `Log4Net`, `NLog`és más szöveg alapú naplófájl-bejegyzések ilyen típusú példányokra vannak lefordítva. A nyomkövetés nem rendelkezik bővíthetőségi mértékkel.

## <a name="message"></a>Üzenet

Nyomkövetési üzenet.

Maximális hossz: 32768 karakter

## <a name="severity-level"></a>Súlyossági szint

Nyomkövetés súlyossági szintje Az érték lehet `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Következő lépések

- [Ismerkedjen meg a .net-nyomkövetési naplók Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Ismerkedjen meg a Java-nyomkövetési naplók Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- [Egyéni nyomkövetési telemetria írása](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Tekintse meg Application Insights által támogatott [platformokat](../../azure-monitor/app/platforms.md) .
