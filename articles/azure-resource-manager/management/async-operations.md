---
title: Aszinkron műveletek állapota
description: Útmutatás az aszinkron műveletek nyomon követéséhez az Azure-ban. Megjeleníti a hosszan futó művelet állapotának lekéréséhez használt értékeket.
ms.topic: conceptual
ms.date: 12/09/2018
ms.custom: seodec18
ms.openlocfilehash: 1cf8898e5fd63e35447f6580e13347ba6d7fc413
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485441"
---
# <a name="track-asynchronous-azure-operations"></a>Aszinkron Azure-műveletek nyomon követése
Bizonyos Azure REST-műveletek aszinkron módon futnak, mert a művelet nem hajtható végre gyorsan. Ez a cikk bemutatja, hogyan követheti nyomon az aszinkron műveletek állapotát a válaszban visszaadott értékek alapján.  

## <a name="status-codes-for-asynchronous-operations"></a>Az aszinkron műveletekhez tartozó állapotkódok
Egy aszinkron művelet először a HTTP-állapotkódot adja vissza a következők közül:

* 201 (létrehozva)
* 202 (elfogadva) 

A művelet sikeres befejezését követően a a következők egyikét adja vissza:

* 200 (OK)
* 204 (nincs tartalom) 

Tekintse meg az [REST API dokumentációját](/rest/api/) , hogy láthassa a végrehajtandó műveletre adott válaszokat.

## <a name="monitor-status-of-operation"></a>A művelet állapotának figyelése
Az aszinkron REST-műveletek adja vissza a fejléc értékeit, amelyek segítségével meghatározhatja a művelet állapotát. A megvizsgálható három fejléc-érték is lehetséges:

* `Azure-AsyncOperation` – URL a művelet folyamatos állapotának ellenőrzéséhez. Ha a művelet visszaadja ezt az értéket, mindig használja (hely helyett) a művelet állapotának nyomon követéséhez.
* `Location` – a művelet befejezését meghatározó URL-cím. Ezt az értéket csak akkor használja, ha az Azure-AsyncOperation nem ad vissza.
* `Retry-After` – az aszinkron művelet állapotának ellenőrzése előtt megvárni kívánt másodpercek száma.

Azonban nem minden aszinkron művelet adja vissza ezeket az értékeket. Előfordulhat például, hogy ki kell értékelnie az Azure-AsyncOperation fejléc értékét egy művelethez, és egy másik műveletnél a Location fejléc értékét. 

A fejléc értékeinek lekérése a kérelem fejlécének bármely értékének lekérése esetén. A (z) C#esetében például lekéri a fejléc értékét egy `response` nevű `HttpWebResponse` objektumból a következő kóddal:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation kérelem és válasz

Az aszinkron művelet állapotának lekéréséhez küldjön egy GET kérelmet az URL-címre az Azure-AsyncOperation header Value értékben.

A művelettől kapott válasz törzse információkat tartalmaz a műveletről. A következő példa a művelet által visszaadott lehetséges értékeket mutatja:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

Az összes válaszhoz csak `status` lesz visszaadva. A rendszer a hiba objektumot adja vissza, ha az állapot meghiúsul vagy meg lett szakítva. Az összes többi érték nem kötelező; Ezért a kapott válasz a példától eltérő lehet.

## <a name="provisioningstate-values"></a>provisioningState-értékek

Az erőforrások létrehozására, frissítésére vagy törlésére (PUT, javítás, törlés) vonatkozó műveletek általában `provisioningState` értéket adnak vissza. Egy művelet befejezését követően a következő három érték egyikét adja vissza: 

* Sikeres
* Meghiúsult
* Törölve

Az összes többi érték azt jelzi, hogy a művelet még fut. Az erőforrás-szolgáltató egy testreszabott értéket adhat vissza, amely jelzi az állapotát. Előfordulhat például, hogy **fogadja el** a kérés fogadása és futtatása esetén elfogadott értesítést.

## <a name="example-requests-and-responses"></a>Példa kérelmekre és válaszokra

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Virtuális gép indítása (202 Azure-AsyncOperation)
Ebből a példából megtudhatja, hogyan határozhatja meg a virtuális gépek **indítási** műveletének állapotát. A kezdeti kérelem formátuma a következő:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

A 202 állapotkódot adja vissza. A fejléc értékei között a következő jelenik meg:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Az aszinkron művelet állapotának megtekintéséhez küldjön egy másik kérelmet erre az URL-címre.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

A válasz törzse a művelet állapotát tartalmazza:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Erőforrások üzembe helyezése (201 Azure-AsyncOperation)

Ebből a példából megtudhatja, hogyan határozhatja meg a **központi telepítések** állapotát az erőforrások Azure-ba történő telepítéséhez. A kezdeti kérelem formátuma a következő:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

A 201 állapotkódot adja vissza. A válasz törzse a következőket tartalmazza:

```json
"provisioningState":"Accepted",
```

A fejléc értékei között a következő jelenik meg:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Az aszinkron művelet állapotának megtekintéséhez küldjön egy másik kérelmet erre az URL-címre.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

A válasz törzse a művelet állapotát tartalmazza:

```json
{"status":"Running"}
```

Ha a telepítés befejeződött, a válasz a következőket tartalmazza:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Storage-fiók létrehozása (202, hely és újrapróbálkozás után)

Ebből a példából megtudhatja, hogyan határozhatja meg a **létrehozási** művelet állapotát a Storage-fiókok esetében. A kezdeti kérelem formátuma a következő:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

A kérés törzse a Storage-fiók tulajdonságait tartalmazza:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

A 202 állapotkódot adja vissza. A fejléc értékei között a következő két érték látható:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Az újrapróbálkozást követően megadott másodpercek számának várakozása után az aszinkron művelet állapotának ellenőrzéséhez küldjön egy másik kérelmet erre az URL-címre.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Ha a kérelem még fut, a 202 állapotkódot kapja meg. Ha a kérelem befejeződött, a kapott állapotkód 200, a válasz törzse pedig a létrehozott Storage-fiók tulajdonságait tartalmazza.

## <a name="next-steps"></a>Következő lépések

* Az egyes REST-műveletekkel kapcsolatos dokumentációért lásd: [REST API dokumentáció](/rest/api/).
* A sablonok Resource Manager-REST API használatával történő telepítésével kapcsolatos információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és Resource Manager-Rest APIokkal](../templates/deploy-rest.md).