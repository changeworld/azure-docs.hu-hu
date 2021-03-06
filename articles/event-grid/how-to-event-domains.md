---
title: Események közzététele esemény-tartományokkal Azure Event Grid
description: Bemutatja, hogyan kezelheti a témakörök nagy csoportjait a Azure Event Gridban, és hogyan teheti közzé az eseményeket az esemény-tartományok használatával.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 1d07227249806b7d54523af66817a170c19354ee
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786553"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Témakörök kezelése és események közzététele az Event Domain használatával

Ez a cikk a következőket mutatja be:

* Event Grid tartomány létrehozása
* Előfizetés az Event Grid-témakörökre
* Kulcsok listázása
* Események közzététele tartományba

Az események tartományával kapcsolatos további tudnivalókért lásd: [az események tartományának megismerése Event Grid témakörök kezeléséhez](event-domains.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Az előzetes verzió funkciójának telepítése

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Esemény tartományának létrehozása

A nagyméretű témakörök kezeléséhez hozzon létre egy Event tartományt.

# <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

A sikeres létrehozás a következő értékeket adja vissza:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Jegyezze fel a `endpoint` és `id`, ahogy a tartomány kezeléséhez és az események közzétételéhez szükségesek.

## <a name="manage-access-to-topics"></a>Témakörökhöz való hozzáférés kezelése

A témakörökhöz való hozzáférés kezelése [szerepkör-hozzárendelés](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)használatával történik. A szerepkör-hozzárendelés szerepköralapú hozzáférés-vezérléssel korlátozza az Azure-erőforrások műveleteit egy bizonyos hatókörön belüli jogosult felhasználók számára.

A Event Grid két beépített szerepkörrel rendelkezik, amelyek segítségével adott felhasználókhoz rendelhet hozzá különböző témaköröket a tartományon belül. Ezek a szerepkörök `EventGrid EventSubscription Contributor (Preview)`, amelyek lehetővé teszik az előfizetések létrehozását és törlését, valamint `EventGrid EventSubscription Reader (Preview)`ét, amely csak az esemény-előfizetések listázását teszi lehetővé.

# <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)
A következő Azure CLI-parancs korlátozza `alice@contoso.com` az esemény-előfizetések létrehozásához és törléséhez csak a témakör `demotopic1`:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
A következő PowerShell-parancs korlátozza `alice@contoso.com` az esemény-előfizetések létrehozásához és törléséhez csak a témakör `demotopic1`:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

A Event Grid műveletekhez való hozzáférés kezelésével kapcsolatos további információkért lásd: [Event Grid biztonság és hitelesítés](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Témakörök és előfizetések létrehozása

A Event Grid szolgáltatás automatikusan hozza létre és kezeli a megfelelő témakört egy tartományban a tartományhoz tartozó esemény-előfizetés létrehozási hívása alapján. Nincs külön lépés egy témakör létrehozásához egy tartományban. Hasonlóképpen, amikor egy témakör utolsó esemény-előfizetését törlik, a témakör is törlődik.

Egy tartományban lévő témakörre való feliratkozás megegyeznek a többi Azure-erőforrásra való feliratkozással. A forrás erőforrás-AZONOSÍTÓnál adja meg a tartomány korábbi létrehozásakor visszaadott esemény-tartományi azonosítót. Az előfizetni kívánt témakör megadásához adja hozzá `/topics/<my-topic>` a forrás erőforrás-azonosító végéhez. Ha olyan tartományi hatókörbeli esemény-előfizetést szeretne létrehozni, amely a tartományban lévő összes eseményt fogadja, a témakörök meghatározása nélkül adhatja meg az esemény tartomány-AZONOSÍTÓját.

Az előző szakaszban megadott hozzáférési jogosultsággal rendelkező felhasználó általában létrehozza az előfizetést. A cikk egyszerűsítése érdekében hozza létre az előfizetést. 

# <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Ha tesztelési végpontra van szüksége az események előfizetéséhez, bármikor üzembe helyezhet egy [előre elkészített webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) , amely megjeleníti a bejövő eseményeket. Az eseményeket `https://<your-site-name>.azurewebsites.net/api/updates`címen küldheti el a teszt webhelyére.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

A témakörhöz beállított engedélyek Azure Active Directory tárolódnak, és explicit módon törölni kell őket. Egy esemény-előfizetés törlése nem vonja vissza a felhasználók hozzáférését az esemény-előfizetések létrehozásához, ha a témakörben írási hozzáféréssel rendelkeznek.


## <a name="publish-events-to-an-event-grid-domain"></a>Események közzététele egy Event Grid tartományban

Az események tartományba való közzététele ugyanaz, mint a [Közzététel egy egyéni témakörben](./post-to-custom-topic.md). Az egyéni témakörre való közzététel helyett azonban az összes eseményt közzé kell tenni a tartományi végponton. A JSON-események adatkészletében meg kell adnia azt a témakört, amelyre az eseményeket el szeretné jutni. A következő események eredményeképpen az esemény `"id": "1111"` a témakör `demotopic1`, miközben a `"id": "2222"`-eseményt a témakör `demotopic2`fogja elküldeni:

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)
A tartomány végpontjának Azure CLI-vel való beszerzéséhez használja a következőt

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Egy tartomány kulcsainak beszerzéséhez használja a következőt:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
A tartomány végpontjának PowerShell-lel való beszerzéséhez használja a következőt

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Egy tartomány kulcsainak beszerzéséhez használja a következőt:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

Ezt követően pedig kedvenc metódusával teheti közzé az eseményeket a Event Grid tartományában.

## <a name="next-steps"></a>Következő lépések

* További információ az Event-tartományok magas szintű fogalmakról és azok hasznos okairól: az esemény- [tartományok fogalmi áttekintése](event-domains.md).
