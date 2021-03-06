---
title: Azure Policy üzembe helyezése a delegált előfizetések számára nagy léptékben
description: Ismerje meg, hogy az Azure-beli delegált erőforrás-kezelés lehetővé teszi a szabályzat-definíciók és a házirendek hozzárendelésének telepítését több bérlőn keresztül.
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 9e061995b728e2864d1bd33a32d530634ab794d8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456839"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Azure Policy üzembe helyezése a delegált előfizetések számára nagy léptékben

Szolgáltatóként több ügyfél-bérlőt is felkészített az Azure-beli delegált erőforrás-kezeléshez. Az [Azure Lighthouse](../overview.md) lehetővé teszi a szolgáltatók számára, hogy egyszerre több bérlőn is elvégezzék a műveleteket, így hatékonyabbá téve a felügyeleti feladatokat.

Ebből a témakörből megtudhatja, hogyan használhatók a [Azure Policy](../../governance/policy/index.yml) egy házirend-definíció és egy házirend-hozzárendelés több bérlőn való üzembe helyezésére a PowerShell-parancsok használatával. Ebben a példában a házirend-definíció biztosítja, hogy a rendszer csak HTTPS-forgalmat engedélyezzen a Storage-fiókok biztonságossá tételéhez.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Az Azure Resource Graph használata az ügyfél-bérlők lekérdezéséhez

Az [Azure Resource Graph](../../governance/resource-graph/index.yml) segítségével lekérdezheti a kezelt ügyfél-bérlő összes előfizetését. Ebben a példában olyan Storage-fiókokat fogunk azonosítani ezekben az előfizetésekben, amelyek jelenleg nem igényelnek HTTPS-forgalmat.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Házirend üzembe helyezése több ügyfél bérlője között

Az alábbi példa azt mutatja be, hogyan használható egy [Azure Resource Manager sablon](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json) egy házirend-definíció és egy házirend-hozzárendelés központi telepítéséhez több ügyfél-bérlőben lévő delegált előfizetések között. Ennek a házirend-definíciónak a használatához minden Storage-fióknak HTTPS-forgalmat kell használnia, megakadályozva az új Storage-fiókok létrehozását, amelyek nem felelnek meg a meglévő Storage-fiókoknak, és nem megfelelő beállítás nélkül.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Szabályzat központi telepítésének ellenőrzése

Miután telepítette a Azure Resource Manager sablont, megerősítheti, hogy a házirend-definíció alkalmazása sikeresen megtörtént, ha a **EnableHttpsTrafficOnly** beállítással **Hamis értéket** ad meg a delegált előfizetések egyikében. A házirend-hozzárendelés miatt nem lehet létrehozni ezt a Storage-fiókot.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, távolítsa el az üzemelő példány által létrehozott házirend-definíciót és hozzárendelést.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Következő lépések

- A [Azure Policy](../../governance/policy/index.yml)megismerése.
- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).
