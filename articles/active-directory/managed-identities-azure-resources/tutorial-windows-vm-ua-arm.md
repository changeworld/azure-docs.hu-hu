---
title: Oktatóanyag`:` felügyelt identitás használata a Azure Resource Manager eléréséhez – Windows-Azure AD
description: Oktatóanyag, amely végigvezeti az Azure Resource Manager Windows VM-beli, felhasználó által hozzárendelt felügyelt identitással való elérésének folyamatán.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec9956f0c5d834633646938da19f03e5467a9f6d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977841"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Oktatóanyag: felhasználó által hozzárendelt felügyelt identitás használata Windows rendszerű virtuális gépen a Azure Resource Manager eléréséhez

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ez az oktatóanyag ismerteti, hogyan hozhat létre felhasználó által hozzárendelt identitást, hogyan csatolhatja azt Windows rendszerű virtuális géphez, majd hogyan használhatja ezt az identitást az Azure Resource Manager API eléréséhez. A felügyeltszolgáltatás-identitások kezelését az Azure automatikusan végzi. Ezek segítségével anélkül végezhet hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban, hogy be kellene ágyaznia a hitelesítő adatokat a kódba. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt felügyelt identitás létrehozása
> * Felhasználó által hozzárendelt identitás hozzárendelése Windows rendszerű virtuális géphez
> * Hozzáférés engedélyezése a felhasználó által hozzárendelt identitás számára az Azure Resource Manager erőforráscsoportjához 
> * Hozzáférési jogkivonat lekérése a felhasználó által hozzárendelt identitás használatával, majd az Azure Resource Manager meghívása a jogkivonat használatával 
> * Erőforráscsoport tulajdonságainak olvasása

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Bejelentkezés az Azure Portalra](https://portal.azure.com)

- [Windows rendszerű virtuális gép létrehozása](/azure/virtual-machines/windows/quick-create-portal)

- A jelen oktatóanyag elvégzéséhez szükséges erőforrás-létrehozási és szerepkör-felügyeleti lépések végrehajtásához a fiókjának „Tulajdonos” jogosultságokkal kell rendelkeznie a megfelelő hatókörben (az előfizetésben vagy az erőforráscsoportban). Ha segítségre van szüksége a szerepkör-hozzárendeléssel kapcsolatban, tekintse meg [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel](/azure/role-based-access-control/role-assignments-portal) részben leírtakat.
- [Telepítse a Azure PowerShell modul legújabb verzióját](/powershell/azure/install-az-ps). 
- Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.
- Telepítse a [PowerShellGet legújabb verzióját](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
- Futtassa a következőt: `Install-Module -Name PowerShellGet -AllowPrerelease` a `PowerShellGet` modul kiadás előtti verziójának eléréséhez (előfordulhat, hogy a parancs futtatása után ki kell lépnie (`Exit`) az aktuális PowerShell-munkamenetből, hogy telepíteni tudja az `Az.ManagedServiceIdentity` modult).
- Futtassa az `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` parancsot az `Az.ManagedServiceIdentity` modul kiadás előtti verziójának telepítéséhez, hogy elvégezhesse a cikkben szereplő, felhasználó által hozzárendelt identitással kapcsolatos műveleteket.


## <a name="enable"></a>Engedélyezés

A felhasználó által hozzárendelt identitáson alapuló forgatókönyv esetén a következő lépéseket kell elvégeznie:

- Identitás létrehozása
 
- Az újonnan létrehozott identitás kiosztása

### <a name="create-identity"></a>Identitás létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre felhasználó által hozzárendelt identitást. A felhasználó által hozzárendelt identitás különálló Azure-erőforrásként jön létre. A [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity)használatával az Azure létrehoz egy identitást az Azure ad-bérlőben, amely egy vagy több Azure-szolgáltatási példányhoz rendelhető hozzá.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

A válasz tartalmazza az imént létrehozott, felhasználó által hozzárendelt identitás részleteit, az alábbi példához hasonlóan. Jegyezze fel a felhasználó által hozzárendelt identitás `Id` és `ClientId` értékét, mivel ezeket a következő lépésekben használni fogja:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

### <a name="assign-identity"></a>Identitás kiosztása

Ez a szakasz bemutatja, hogyan rendelhető hozzá a felhasználó által hozzárendelt identitás egy Windows rendszerű virtuális géphez. A felhasználó által hozzárendelt identitást az ügyfelek több Azure-erőforrás esetében is használhatják. Az alábbi parancsokkal rendelhet felhasználó által hozzárendelt identitást egyetlen virtuális géphez. Ehhez használja az előző lépésben az `-IdentityID` paraméter esetében visszaadott `Id` tulajdonságot.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>Hozzáférés biztosítása 

Ez a szakasz azt mutatja be, hogyan adható meg a felhasználó által hozzárendelt identitás hozzáférése egy erőforráscsoporthoz a Azure Resource Manager. Az Azure-erőforrások felügyelt identitásai segítségével a kód hozzáférési jogkivonatokat tud lekérni az olyan erőforrás API-k hitelesítéséhez, amelyek támogatják az Azure AD-hitelesítést. Ebben az oktatóanyagban a kódot az Azure Resource Manager API-jának eléréséhez használjuk. 

Ehhez azonban még engedélyeznie kell az identitás számára a hozzáférést az Azure Resource Manager erőforrásaihoz. Ebben az esetben arról az erőforráscsoportról van szó, amelyben a virtuális gép megtalálható. A környezetnek megfelelően frissítse a `<SUBSCRIPTION ID>` értékét.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

A válasz tartalmazza az imént létrehozott szerepkör-hozzárendelés részleteit, az alábbi példához hasonlóan:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="access-data"></a>Adatok elérése

### <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése 

Az oktatóanyag további részében a korábban létrehozott virtuális gépről dolgozunk.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen

2. A portálon lépjen a **Virtuális gépek** szakaszra, lépjen a Windows rendszerű virtuális géphez, és az **Áttekintés** területen kattintson a **Csatlakozás** elemre.

3. Írja be a Windows rendszerű virtuális gép létrehozásakor használt **felhasználónevet** és **jelszót**.

4. Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a **PowerShellt** a távoli munkamenetben.

5. A Powershell `Invoke-WebRequest` parancsával küldjön kérést az Azure-erőforrások helyi felügyeltidentitási végpontjára, hogy lekérjen egy hozzáférési jogkivonatot az Azure Resource Managerhez.  A `client_id` érték a felhasználó által hozzárendelt felügyelt identitás létrehozásakor visszaadott érték.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>Tulajdonságok olvasása

Az Azure Resource Managert az előző lépésben lekért hozzáférési jogkivonattal érheti el, illetve azzal olvashatja annak az erőforráscsoportnak a tartalmát, amelyhez a felhasználó által hozzárendelt identitás számára hozzáférést adott. Cserélje le a(z) `<SUBSCRIPTION ID>` kifejezést a környezet előfizetés-azonosítójára.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
A válasz tartalmazza az adott erőforráscsoport adatait, az alábbi példához hasonlóan:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan hozhat létre felhasználó által hozzárendelt identitást, és hogyan csatlakoztathatja azt egy Azure-beli virtuális géphez a Azure Resource Manager API eléréséhez.  További információ az Azure Resource Managerről:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
