---
title: Hozzáférés biztosítása Azure Enterprise-előfizetések létrehozásához
description: Ismerje meg, hogy a felhasználók vagy az egyszerű szolgáltatások hogyan hozhatnak létre programozott módon Azure Enterprise-előfizetéseket.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75478876"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Hozzáférés biztosítása Azure Enterprise-előfizetések létrehozásához (előzetes verzió)

Ha [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)Azure-ügyfelet használ, megadhat egy másik felhasználói vagy egyszerű szolgáltatást a fiókjához tartozó előfizetések létrehozásához. Ebből a cikkből megtudhatja, hogyan használhatja a [szerepköralapú Access Control (RBAC)](../../active-directory/role-based-access-control-configure.md) az előfizetések létrehozására, valamint az előfizetés-létrehozás naplózására. A megosztani kívánt fiók tulajdonosi szerepkörének kell lennie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Hozzáférés biztosítása

A [beléptetési fiókkal rendelkező előfizetések létrehozásához](programmatically-create-subscription.md)a felhasználóknak a [RBAC tulajdonosi szerepkörrel](../../role-based-access-control/built-in-roles.md#owner) kell rendelkezniük az adott fiókon. Az alábbi lépéseket követve adhat meg egy felhasználót vagy felhasználói csoportot a RBAC tulajdonosi szerepkörének beléptetési fiókjához:

1. Annak a beléptetési fióknak az objektumazonosító beszerzése, amelyhez hozzáférést szeretne biztosítani

    Ha a RBAC tulajdonosi szerepkörét szeretné megadni egy regisztrációs fiókon, akkor a fiók tulajdonosának vagy RBAC-tulajdonosának kell lennie.

    # <a name="resttabrest"></a>[REST](#tab/rest)

    Az összes olyan regisztrációs fiók listázására vonatkozó kérelem, amelyhez hozzáféréssel rendelkezik:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Az Azure válaszol az összes beléptetési fiók listájára:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    A `principalName` tulajdonsággal azonosíthatja azt a fiókot, amelyhez RBAC tulajdonosi hozzáférést szeretne biztosítani. Másolja a fiók `name`ét. Ha például RBAC-tulajdonosi hozzáférést szeretne biztosítani az SignUpEngineering@contoso.com beléptetési fiókhoz, akkor másolja ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ez a beléptetési fiók objektumazonosító. Illessze be ezt az értéket valahova, hogy a következő lépésben használja `enrollmentAccountObjectId`ként.

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    A [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) parancsmag használatával listázhatja az összes olyan regisztrációs fiókot, amelyhez hozzáféréssel rendelkezik. Válassza a **kipróbálás** lehetőséget a [Azure Cloud Shell](https://shell.azure.com/)megnyitásához. A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéj ablakára, majd válassza a **Beillesztés**lehetőséget.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Az Azure az Ön számára elérhető regisztrációs fiókok listájával válaszol:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    A `principalName` tulajdonsággal azonosíthatja azt a fiókot, amelyhez RBAC tulajdonosi hozzáférést szeretne biztosítani. Másolja a fiók `ObjectId`ét. Ha például RBAC-tulajdonosi hozzáférést szeretne biztosítani az SignUpEngineering@contoso.com beléptetési fiókhoz, akkor másolja ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Illessze be ezt az objektumazonosítót valahova, hogy a következő lépésben használja a `enrollmentAccountObjectId`.

    # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

    Az az [Billing beléptetés-Account List](https://aka.ms/EASubCreationPublicPreviewCLI) paranccsal listázhatja az összes olyan regisztrációs fiókot, amelyhez hozzáférése van. Válassza a **kipróbálás** lehetőséget a [Azure Cloud Shell](https://shell.azure.com/)megnyitásához. A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéj ablakára, majd válassza a **Beillesztés**lehetőséget.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Az Azure az Ön számára elérhető regisztrációs fiókok listájával válaszol:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    A `principalName` tulajdonsággal azonosíthatja azt a fiókot, amelyhez RBAC tulajdonosi hozzáférést szeretne biztosítani. Másolja a fiók `name`ét. Ha például RBAC-tulajdonosi hozzáférést szeretne biztosítani az SignUpEngineering@contoso.com beléptetési fiókhoz, akkor másolja ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ez a beléptetési fiók objektumazonosító. Illessze be ezt az értéket valahova, hogy a következő lépésben használja `enrollmentAccountObjectId`ként.

1. <a id="userObjectId"></a>Szerezze be azon felhasználó vagy csoport objektumazonosító-AZONOSÍTÓját, amelyhez a RBAC tulajdonosi szerepkört kívánja adni

    1. A Azure Portal keresse meg a **Azure Active Directory**.
    1. Ha felhasználói hozzáférést szeretne biztosítani, kattintson a bal oldali menüben a **felhasználók** lehetőségre. Ha hozzáférést szeretne adni egy csoporthoz, kattintson a **csoportok**elemre.
    1. Válassza ki azt a felhasználót vagy csoportot, amely számára a RBAC tulajdonosi szerepkört kívánja adni.
    1. Ha kiválasztott egy felhasználót, az objektumazonosító a profil oldalon található. Ha kiválasztott egy csoportot, az objektum azonosítója az Áttekintés oldalon jelenik meg. Másolja a **ObjectId** a szövegmezőtől jobbra található ikonra kattintva. Illessze be ezt valahova, hogy a következő lépésben használja `userObjectId`ként.

1. A RBAC tulajdonosi szerepkörének megadása a felhasználónak vagy csoportnak a beléptetési fiókban

    Az első két lépésben összegyűjtött értékek használatával adja meg a felhasználó vagy csoport számára a RBAC tulajdonosi szerepkörét a beléptetési fiókban.

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    Futtassa a következő parancsot, és cserélje le a ```<enrollmentAccountObjectId>```t az első lépésben (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) másolt `name`. Cserélje le a ```<userObjectId>```t a második lépésből másolt objektumazonosító-AZONOSÍTÓra.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Ha a tulajdonosi szerepkör sikeresen hozzá van rendelve a beléptetési fiók hatóköréhez, az Azure a szerepkör-hozzárendelés információit válaszolja:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

    Futtassa a következő [New-AzRoleAssignment](../../active-directory/role-based-access-control-manage-access-powershell.md) parancsot, és cserélje le a ```<enrollmentAccountObjectId>```t az első lépésben összegyűjtött `ObjectId`ra (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Cserélje le a ```<userObjectId>```t a második lépésben összegyűjtött objektum-AZONOSÍTÓra.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[Azure CLI](#tab/azure-cli-2)

    Futtassa a következőt az az [szerepkör-hozzárendelés létrehozása](../../active-directory/role-based-access-control-manage-access-azure-cli.md) paranccsal, és cserélje le az ```<enrollmentAccountObjectId>```t az első lépésben (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) másolt `name`. Cserélje le a ```<userObjectId>```t a második lépésben összegyűjtött objektum-AZONOSÍTÓra.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Miután a felhasználó RBAC válik a beléptetési fiókjához, programozott módon [hozhat létre előfizetéseket](programmatically-create-subscription.md) . A delegált felhasználó által létrehozott előfizetés még mindig rendelkezik az eredeti fiók tulajdonosával a szolgáltatás-rendszergazdaként, de alapértelmezés szerint a meghatalmazott felhasználó RBAC tulajdonosa is.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>A Tevékenységnaplók használatával létrehozott előfizetések naplózása

Az ezen az API-n keresztül létrehozott előfizetések nyomon követéséhez használja a [bérlői tevékenység log API](/rest/api/monitor/tenantactivitylogs)-ját. Az előfizetés létrehozásának nyomon követéséhez jelenleg nem használható a PowerShell, a CLI vagy a Azure Portal.

1. Az Azure AD-bérlő bérlői rendszergazdájaként [emelheti a jogosultságszintet](../../active-directory/role-based-access-control-tenant-admin-access.md), majd Olvasó szerepkört rendelhet a naplózást végző felhasználóhoz a `/providers/microsoft.insights/eventtypes/management` hatókörben.
1. A naplózási felhasználóként hívja meg a [bérlői tevékenység naplójának API](/rest/api/monitor/tenantactivitylogs) -ját az előfizetés-létrehozási tevékenységek megtekintéséhez. Példa:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Az API-t kényelmesen meghívhatja a parancssorból az [ARMClient](https://github.com/projectkudu/ARMClient) segítségével.

## <a name="next-steps"></a>Következő lépések

* Most, hogy a felhasználó vagy az egyszerű szolgáltatás jogosult az előfizetés létrehozására, az identitás használatával [programozott módon hozhat létre Azure Enterprise-előfizetéseket](programmatically-create-subscription.md).
* Az előfizetések .NET-tel történő létrehozásával kapcsolatos példa: [mintakód a githubon](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Ha többet szeretne megtudni a Azure Resource Manager és az API-król, tekintse meg a [Azure Resource Manager áttekintését](overview.md).
* Ha többet szeretne megtudni a nagy számú előfizetés felügyeleti csoportokkal való kezelésével kapcsolatban, tekintse meg [az erőforrások rendszerezése az Azure felügyeleti csoportok](../../governance/management-groups/overview.md) segítségével című témakört.
* Az előfizetési irányítással foglalkozó nagyméretű szervezetek átfogó bevált gyakorlatával kapcsolatos útmutatásért lásd: [Azure Enterprise állvány – előírásos előfizetés szabályozása](/azure/architecture/cloud-adoption-guide/subscription-governance)
