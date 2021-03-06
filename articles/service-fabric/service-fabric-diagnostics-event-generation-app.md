---
title: Azure Service Fabric alkalmazások szintjének figyelése
description: Ismerje meg az Azure Service Fabric-fürtök figyelésére és diagnosztizálására szolgáló alkalmazás-és szolgáltatási szintű eseményeket és naplókat.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 97c3be391dfbee7301ea47bf7234a9549d373370
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464720"
---
# <a name="application-logging"></a>Alkalmazásnaplózás

A kód kialakítása nem csupán a felhasználókra vonatkozó elemzések megszerzésének módja, de az egyetlen lehetőség, hogy megtudja, van-e probléma az alkalmazásban, és hogy mit kell megjavítani. Habár technikailag lehetséges, hogy a hibakeresőt egy éles szolgáltatáshoz is összekapcsolhatják, nem általános gyakorlat. Ezért fontos a részletes rendszerállapot-információk használata.

Egyes termékek automatikusan kialakítják a kódot. Habár ezek a megoldások jól működhetnek, a manuális rendszerállapotot szinte mindig az üzleti logikára kell megadni. A végén elegendő információval kell rendelkeznie az alkalmazás kriminalisztikai hibakereséséhez. Service Fabric alkalmazások bármilyen naplózási keretrendszerrel rendelkezhetnek. Ez a dokumentum néhány különböző megközelítést ismertet a kód kivezetéséhez, és ha egy másik megközelítést választ ki. 

A javaslatok használatáról a következő témakörben talál példákat: [naplózás hozzáadása a Service Fabric alkalmazáshoz](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Application Insights SDK

A Application Insights gazdag integrációval rendelkezik Service Fabric a dobozból. A felhasználók hozzáadhatják az AI Service Fabric nuget-csomagokat, és megkapják a Azure Portalban megtekinthető és gyűjtött adatokat és naplókat. Emellett a felhasználóknak javasoljuk saját telemetria hozzáadását az alkalmazások diagnosztizálásához és hibakereséséhez, és nyomon követni, hogy az alkalmazás mely szolgáltatásokat és részeit használja. Az SDK [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) osztálya számos módszert kínál az alkalmazások telemetria nyomon követésére. Tekintse meg, hogyan helyezhet el és adhat hozzá Application bepillantást az alkalmazásához az oktatóanyagban [egy .NET-alkalmazás monitorozásához és diagnosztizálásához](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource

Amikor létrehoz egy Service Fabric megoldást egy sablonból a Visual Studióban, létrejön egy **EventSource**származtatott osztály (**ServiceEventSource** vagy **ActorEventSource**). Létrejön egy sablon, amelyben eseményeket adhat hozzá az alkalmazáshoz vagy szolgáltatáshoz. A **EventSource** nevének **egyedinek kell** lennie, és átnevezni kell az alapértelmezett sablon sztring sajátvállalat-&lt;megoldás&gt;-&lt;Project&gt;. Ha több olyan **EventSource** -definíció is van, amelyek ugyanazt a nevet használják, a probléma futás közben is hibát okoz. Minden meghatározott eseménynek egyedi azonosítóval kell rendelkeznie. Ha egy azonosító nem egyedi, futásidejű hiba történik. Egyes szervezetek a különböző fejlesztői csapatok közötti ütközések elkerülése érdekében előrendelik az azonosítók tartományait. További információ: a [Vance blogja](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) vagy az [MSDN dokumentációja](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>ASP.NET Core naplózás

Fontos, hogy alaposan megtervezze, hogyan fogja kiépíteni a kódot. A megfelelő kialakítási terv segít elkerülni a kód alapjainak esetleges destabilizálása, majd a kód visszavonása szükséges. A kockázat csökkentése érdekében olyan rendszerállapot-függvénytárat választhat, mint például a Microsoft [. Extensions. Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), amely a Microsoft ASP.net Core része. ASP.NET Core rendelkezik egy [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) -felülettel, amely a választott szolgáltatóval használható, és minimalizálja a meglévő kódra gyakorolt hatást. A kódot a Windows-és Linux-ASP.NET Coreon, a teljes .NET-keretrendszerben is használhatja, így a rendszerállapot-kódok szabványosítva vannak.

## <a name="next-steps"></a>Következő lépések

Miután kiválasztotta a naplózási szolgáltatót az alkalmazások és szolgáltatások kiválasztásához, a naplókat és az eseményeket összesíteni kell, mielőtt bármilyen Analysis platformba el lehetne őket juttatni. A Azure Monitor ajánlott lehetőségek megismeréséhez olvassa el a [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) és a [EventFlow segítségével](service-fabric-diagnostics-event-aggregation-eventflow.md) című témakört.
