---
title: Riasztási lekérdezések naplózása a Azure Monitorban | Microsoft Docs
description: Ajánlásokat nyújt a Azure Monitor-frissítések naplójának hatékony lekérdezéséhez, valamint a meglévő lekérdezések átalakításának folyamatához.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667788"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Riasztási lekérdezések naplózása Azure Monitor
[A Azure monitor naplókon alapuló riasztási szabályok](alerts-unified-log.md) rendszeres időközönként futnak, ezért meg kell győződnie arról, hogy a terhelést és a késést a lehető legkisebbre kell írni. Ez a cikk a naplózási riasztások hatékony lekérdezéseit és a meglévő lekérdezések átalakításának folyamatát ismerteti. 

## <a name="types-of-log-queries"></a>A napló típusú lekérdezések típusai
[Azure monitor a lekérdezéseket](../log-query/log-query-overview.md) egy táblával vagy egy [keresési](/azure/kusto/query/searchoperator) vagy [Union](/azure/kusto/query/unionoperator) operátorral kezdheti meg.

Például a következő lekérdezés hatóköre a _SecurityEvent_ táblára vonatkozik, és megkeresi az adott esemény azonosítóját. Ez az egyetlen tábla, amelyet a lekérdezésnek fel kell dolgoznia.

``` Kusto
SecurityEvent | where EventID == 4624 
```

`search` vagy `union` kezdetű lekérdezések lehetővé teszik egy tábla több oszlopának vagy akár több tábla keresését. Az alábbi példák több módszert is mutatnak a feltételek _memóriabeli_kereséséhez:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Bár a `search` és `union` az adatfeltárás során hasznosak, a kifejezés a teljes adatmodellre való kereséskor kevésbé hatékony, mint a tábla használata, mivel több táblában kell megvizsgálni. Mivel a riasztási szabályok lekérdezéseit rendszeres időközönként futtatják, ez túlzott terhelést eredményezhet a riasztáshoz képest. Ezen terhelés miatt az Azure-beli naplózási riasztási szabályok lekérdezései mindig táblázattal kezdődnek, hogy egyértelmű hatókört határozzanak meg, ami javítja a lekérdezési teljesítményt és az eredmények relevanciáját.

## <a name="unsupported-queries"></a>Nem támogatott lekérdezések
A 2019. január 11-én kezdődően a `search`-t használó naplózási riasztási szabályok létrehozása vagy módosítása, vagy `union` operátorok nem támogatják a Azure Portal. Ha ezeket a kezelőket riasztási szabályban használja, hibaüzenetet ad vissza. Ez a változás nem érinti a meglévő riasztási szabályokat és a Log Analytics API-val szerkesztett riasztási szabályokat. Érdemes megfontolnia az ilyen típusú lekérdezéseket használó riasztási szabályok módosítását is, de így hatékonyabbá teheti a hatékonyságát.  

Ez a változás nem érinti a riasztási szabályokat a [több erőforrást használó lekérdezések](../log-query/cross-workspace-query.md) használatával, mivel az erőforrások közötti lekérdezések `union`használnak, ami korlátozza a lekérdezés hatókörét adott erőforrásokra. Ez nem felel meg a nem használható `union *`nak.  A következő példa egy naplózási riasztási szabályban érvényes:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>Az új [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)támogatja a naplózási riasztásokban lévő [erőforrás-lekérdezések közötti lekérdezést](../log-query/cross-workspace-query.md) . Alapértelmezés szerint a Azure Monitor az [örökölt log Analytics riasztási API](api-alerts.md) -t használja az új naplózási riasztási szabályok létrehozásához Azure Portalból, kivéve, ha az [örökölt naplózási riasztások API](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api)-ból vált. A kapcsoló után az új API lesz az új riasztási szabályok alapértelmezett értéke Azure Portalban, és lehetővé teszi az erőforrások közötti lekérdezési napló riasztási szabályainak létrehozását. A [SCHEDULEDQUERYRULES API ARM-sablonjának](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) használata nélkül hozhat létre [erőforrás-lekérdezési](../log-query/cross-workspace-query.md) napló-riasztási szabályokat, de ez a riasztási szabály kezelhető, bár a [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) nem Azure Portal.

## <a name="examples"></a>Példák
Az alábbi példák olyan naplózási lekérdezéseket tartalmaznak, amelyek `search` és `union` használnak, és megadják azokat a lépéseket, amelyek segítségével módosíthatja ezeket a lekérdezéseket a riasztási szabályokkal való használatra.

### <a name="example-1"></a>1\. példa
A következő lekérdezéssel szeretne létrehozni egy naplózási riasztási szabályt, amely a `search`használatával kérdezi le a teljesítményadatokat: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

A lekérdezés módosításához Kezdje a következő lekérdezéssel annak a táblának az azonosításához, amelyhez a tulajdonságok tartoznak:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

A lekérdezés eredménye azt mutatja, hogy a _CounterName_ tulajdonság a _perf_ táblából származik. 

Ezt az eredményt használhatja a következő lekérdezés létrehozásához, amelyet a riasztási szabályhoz szeretne használni:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>2\. példa
A következő lekérdezéssel szeretne létrehozni egy naplózási riasztási szabályt, amely a `search`használatával kérdezi le a teljesítményadatokat: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

A lekérdezés módosításához Kezdje a következő lekérdezéssel annak a táblának az azonosításához, amelyhez a tulajdonságok tartoznak:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

A lekérdezés eredménye azt mutatja, hogy a _ObjectName_ és a _CounterName_ tulajdonság a _perf_ táblából származik. 

Ezt az eredményt használhatja a következő lekérdezés létrehozásához, amelyet a riasztási szabályhoz szeretne használni:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>3\. példa

A következő lekérdezéssel szeretne létrehozni egy naplózási riasztási szabályt, amely a `search` és a `union` is használja a teljesítményadatok lekéréséhez: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

A lekérdezés módosításához Kezdje a következő lekérdezéssel annak a táblának az azonosításához, amely a lekérdezés első részében szereplő tulajdonságok tartoznak: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

A lekérdezés eredménye azt mutatja, hogy ezek a tulajdonságok a _perf_ táblából származnak. 

Most a `union` és `withsource` paranccsal azonosíthatja, hogy melyik forrástábla járult hozzá az egyes sorokhoz.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

A lekérdezés eredménye azt mutatja, hogy ezek a tulajdonságok a _perf_ táblából is származnak. 

Ezeket az eredményeket a riasztási szabályhoz használni kívánt következő lekérdezés létrehozásához használhatja: 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>4\. példa
A következő lekérdezéssel szeretne létrehozni egy naplózási riasztási szabályt, amely két `search` lekérdezés eredményét egyesíti:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```
 

A lekérdezés módosításához Kezdje a következő lekérdezéssel a csatlakozás bal oldalán található tulajdonságokat tartalmazó tábla azonosításához: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Az eredmény azt jelzi, hogy az illesztés bal oldalán lévő tulajdonságok a _SecurityEvent_ táblához tartoznak. 

Most a következő lekérdezéssel azonosítsa az illesztés jobb oldalán található tulajdonságokat tartalmazó táblát: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Az eredmény azt jelzi, hogy az illesztés jobb oldalán lévő tulajdonságok a szívverési táblához tartoznak. 

Ezeket az eredményeket a riasztási szabályhoz használni kívánt következő lekérdezés létrehozásához használhatja: 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>Következő lépések
- További információ a Azure Monitor [naplózási értesítéseiről](alerts-log.md) .
- További információ a [naplók lekérdezéséről](../log-query/log-query-overview.md).

