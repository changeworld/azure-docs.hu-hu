---
title: '& Jelentés archiválása Azure Monitor-Azure AD-jogosultságok kezelése'
description: Megtudhatja, hogyan archiválhatja a naplókat, és hogyan hozhat létre jelentéseket Azure Monitor használatával Azure Active Directory jogosultságok kezelésében.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/27/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f89af42e32783de479c4302b19c0a7ddc1289bb8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202181"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Archiválási naplók és jelentéskészítés az Azure AD-jogosultságok kezelésében Azure Monitor

Az Azure AD a naplóban legfeljebb 30 napig naplózza a naplózási eseményeket. A naplózási adatok azonban továbbra is megtarthatók az alapértelmezett megőrzési időtartamnál, amely az [Azure ad áruház jelentéskészítési adatai?](../reports-monitoring/reference-reports-data-retention.md), az Azure Storage-fiókba vagy a Azure monitor használatával történő átirányításával. Ezután a munkafüzeteket és az egyéni lekérdezéseket és jelentéseket is használhatja ezen az adatforráson.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Az Azure AD konfigurálása Azure Monitor használatára
A Azure Monitor munkafüzetek használata előtt konfigurálnia kell az Azure AD-t, hogy elküldje a naplófájlok másolatát Azure Monitor.

Az Azure AD-naplók archiválásához a Azure Monitor Azure-előfizetésben kell lennie. További információt az Azure AD-beli Azure Monitor használatának előfeltételeiről és becsült költségeiről [Azure monitorban](../reports-monitoring/concept-activity-logs-azure-monitor.md)olvashat.

**Előfeltételként szükséges szerepkör**: globális rendszergazda

1. Jelentkezzen be a Azure Portalba globális rendszergazdai jogosultsággal rendelkező felhasználóként. Győződjön meg arról, hogy van hozzáférése az Azure Monitor munkaterületet tartalmazó erőforráscsoporthoz.
 
1. Válassza ki **Azure Active Directory** majd a bal oldali navigációs menü figyelés területén kattintson a **diagnosztikai beállítások** elemre. Ellenőrizze, hogy van-e már egy beállítás, amely elküldi a naplókat az adott munkaterületnek.

1. Ha még nincs beállítva beállítás, kattintson a **diagnosztikai beállítás hozzáadása**elemre. Az Azure ad- [naplók Azure monitor naplókba való integrálásához](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) kövesse az Azure ad-naplófájlok integrálása az Azure monitor munkaterületre című cikk utasításait.

    ![Diagnosztikai beállítások ablaktábla](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Miután a rendszer elküldje a naplót Azure Monitorre, válassza a **log Analytics munkaterületek**lehetőséget, majd válassza ki az Azure ad-naplókat tartalmazó munkaterületet.

1. Válassza a **használat és becsült költségek** lehetőséget, majd kattintson **az adatmegőrzés**elemre. Módosítsa a csúszkát arra a napra, ameddig meg szeretné őrizni az adatait, hogy megfeleljenek a naplózási követelményeknek.

    ![Log Analytics munkaterületek panel](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Egyéni Azure Monitor-lekérdezések létrehozása a Azure Portal használatával
Létrehozhat saját lekérdezéseket az Azure AD-naplózási eseményeken, beleértve a jogosultsági felügyeleti eseményeket is.  

1. A Azure Portal Azure Active Directory kattintson a bal oldali navigációs menü figyelés szakaszában található **naplók** elemre egy új lekérdezési oldal létrehozásához.

1. A munkaterület a lekérdezési oldal bal felső részén jelenik meg. Ha több Azure Monitor munkaterülettel rendelkezik, és az Azure AD-naplózási események tárolására használt munkaterület nem látható, kattintson a **hatókör kiválasztása**elemre. Ezután válassza ki a megfelelő előfizetést és munkaterületet.

1. Ezután a lekérdezés szövege területen törölje a "keresés *" karakterláncot, és cserélje le a következő lekérdezésre:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Ezután kattintson a **Futtatás**gombra. 

    ![A lekérdezés indításához kattintson a Futtatás gombra.](./media/entitlement-management-logs-and-reporting/run-query.png)

A tábla alapértelmezés szerint megjeleníti a jogosultsági felügyelet naplózási naplójának eseményeit az elmúlt órában. A régi események megtekintéséhez módosíthatja az "időtartomány" beállítást. Azonban a beállítás módosítása csak azokat az eseményeket jeleníti meg, amelyek az Azure AD konfigurálását követően történtek az események Azure Monitor való küldésére.

Ha szeretné megismerni a Azure Monitorban tárolt legrégebbi és legújabb naplózási eseményeket, használja a következő lekérdezést:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

A Azure Monitor naplózási eseményeihez tárolt oszlopokkal kapcsolatos további információkért lásd: [Az Azure ad-naplók sémájának értelmezése Azure monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Egyéni Azure Monitor-lekérdezések létrehozása a Azure PowerShell használatával

A naplók a PowerShellen keresztül érhetők el, miután konfigurálta az Azure AD-t, hogy naplókat küldjön Azure Monitor. Ezután a parancsfájlokból vagy a PowerShell parancssorból küldhet lekérdezéseket anélkül, hogy globális rendszergazdának kellene lennie a bérlőben. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Győződjön meg arról, hogy a felhasználó vagy a szolgáltatásnév megfelelő szerepkör-hozzárendeléssel rendelkezik

Győződjön meg arról, hogy az Azure AD-ben hitelesíteni kívánt felhasználó vagy szolgáltatásnév a megfelelő Azure-szerepkörben található a Log Analytics munkaterületen. A szerepkör-beállítások Log Analytics olvasók vagy a Log Analytics közreműködők. Ha már szerepel valamelyik szerepkörben, ugorjon az [log Analytics-azonosító lekérése egyetlen Azure-előfizetéssel](#retrieve-log-analytics-id-with-one-azure-subscription)lehetőségre.

A szerepkör-hozzárendelés beállításához és a lekérdezés létrehozásához hajtsa végre a következő lépéseket:
1. A Azure Portal keresse meg a [log Analytics munkaterületet](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Válassza a **Access Control (iam)** lehetőséget.

1. A szerepkör-hozzárendelés hozzáadásához kattintson a **Hozzáadás** gombra.

    ![Szerepkör-hozzárendelés hozzáadása](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Azure PowerShell modul telepítése

Ha rendelkezik a megfelelő szerepkör-hozzárendeléssel, indítsa el a PowerShellt, és [telepítse a Azure PowerShell modult](/powershell/azure/install-az-ps?view=azps-3.3.0) (ha még nem tette meg), írja be a következőt:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Most már készen áll az Azure AD-hitelesítésre, és beolvasni a lekérdezni kívánt Log Analytics munkaterület azonosítóját.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Log Analytics-azonosító beolvasása egy Azure-előfizetéssel
Ha csak egyetlen Azure-előfizetéssel rendelkezik, és egyetlen Log Analytics munkaterülettel rendelkezik, írja be a következőt az Azure AD-hez való hitelesítéshez, az előfizetéshez való kapcsolódáshoz és a munkaterület lekéréséhez:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Log Analytics-azonosító lekérése több Azure-előfizetéssel

 A [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) egyszerre egy előfizetésben működik. Ha tehát több Azure-előfizetéssel rendelkezik, akkor győződjön meg arról, hogy csatlakozik az Azure AD-naplókkal rendelkező Log Analytics munkaterülettel rendelkezőhöz. 
 
 A következő parancsmagok megjelenítik az előfizetések listáját, és megkeresik annak az előfizetésnek az azonosítóját, amely a Log Analytics munkaterülettel rendelkezik:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
A PowerShell-munkamenetet újra hitelesítheti és hozzárendelheti ehhez az előfizetéshez egy parancs, például `Connect-AzAccount –Subscription $subs[0].id`használatával. Ha többet szeretne megtudni arról, hogyan végezheti el a hitelesítést az Azure-ból a PowerShellből, például nem interaktív módon, tekintse meg a [bejelentkezés Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)használatával című témakört.

Ha az előfizetésben több Log Analytics munkaterülettel rendelkezik, a [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) parancsmag a munkaterületek listáját adja vissza. Ezután megtalálhatja az Azure AD-naplókkal rendelkezőt. A parancsmag által visszaadott `CustomerId` mező ugyanaz, mint a (z) Log Analytics munkaterület áttekintésében Azure Portalban megjelenő "munkaterület-azonosító" értéke.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>A lekérdezés elküldése a Log Analytics munkaterületre
Végül, ha már azonosított egy munkaterületet, akkor az [AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) használatával küldhet Kusto-lekérdezést az adott munkaterületre. Ezek a lekérdezések [Kusto lekérdezési nyelven](https://docs.microsoft.com/azure/kusto/query/)íródnak.
 
Például lekérheti a naplózási események rekordjainak időtartományát a Log Analytics munkaterületről, a PowerShell-parancsmagokkal pedig egy lekérdezés küldéséhez, például:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

A jogosultsági felügyeleti eseményeket a következő lekérdezéssel kérheti le:

```azurepowershell
$bQuery = = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Következő lépések:
- [Interaktív jelentések létrehozása Azure Monitor munkafüzetekkel](../../azure-monitor/app/usage-workbooks.md) 

