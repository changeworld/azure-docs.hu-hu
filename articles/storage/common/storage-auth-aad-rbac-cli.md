---
title: Az Azure CLI használata RBAC-szerepkör hozzárendeléséhez adathozzáféréshez
titleSuffix: Azure Storage
description: Megtudhatja, hogyan rendelhet hozzá engedélyeket egy Azure Active Directory rendszerbiztonsági tag számára az Azure CLI használatával szerepköralapú hozzáférés-vezérléssel (RBAC). Az Azure Storage támogatja a beépített és az egyéni RBAC-szerepköröket az Azure AD-n keresztül történő hitelesítéshez.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: abe35f3193e2d7ff9a949ca7cd330cb58da2b78c
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891968"
---
# <a name="use-azure-cli-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Az Azure CLI használata RBAC-szerepkör hozzárendeléséhez a blob-és üzenetsor-adathoz való hozzáféréshez

Azure Active Directory (Azure AD) a [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz. Az Azure Storage olyan beépített RBAC-szerepköröket határoz meg, amelyek a blob-vagy üzenetsor-adateléréshez használt engedélyek közös készleteit foglalják magukban.

Ha egy Azure AD-rendszerbiztonsági tag egy RBAC-szerepkört rendel hozzá, az Azure hozzáférést biztosít ezen rendszerbiztonsági tag erőforrásaihoz. A hozzáférés hatóköre az előfizetés, az erőforráscsoport, a Storage-fiók vagy egy adott tároló vagy várólista szintjére is kiterjed. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md).

Ez a cikk bemutatja, hogyan használhatja az Azure CLI-t a beépített RBAC-szerepkörök listázásához és a felhasználókhoz való hozzárendeléséhez. További információ az Azure CLI használatáról: [Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure).

## <a name="rbac-roles-for-blobs-and-queues"></a>Blobok és várólisták RBAC szerepkörei

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Erőforrás hatókörének meghatározása

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Elérhető RBAC-szerepkörök listázása

Az elérhető beépített RBAC-szerepkörök az Azure CLI-vel való listázásához használja az az [role definition List](/cli/azure/role/definition#az-role-definition-list) parancsot:

```azurecli-interactive
az role definition list --out table
```

Megjelenik az Azure Storage beépített adatszerepkörei, valamint az Azure egyéb beépített szerepkörei:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>RBAC-szerepkör társítása egy rendszerbiztonsági tag számára

Ha RBAC szerepkört szeretne hozzárendelni egy rendszerbiztonsági tag számára, használja az az [szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create) parancsot. A parancs formátuma eltérő lehet a hozzárendelés hatóköre alapján. Az alábbi példák bemutatják, hogyan rendeljen hozzá egy szerepkört egy felhasználóhoz különböző hatókörökben, de ugyanazt a parancsot használhatja egy szerepkör hozzárendelésére bármely rendszerbiztonsági tag számára.

### <a name="container-scope"></a>Tároló hatóköre

Egy tárolóhoz hatókörrel rendelkező szerepkör hozzárendeléséhez adjon meg egy karakterláncot, amely tartalmazza a `--scope` paraméter tárolójának hatókörét. A tároló hatóköre a következő formában van:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

A következő példa hozzárendeli a **Storage blob-adatközreműködői** szerepkört egy felhasználóhoz, hatókörét a tároló szintjére. Ügyeljen rá, hogy a minták értékeit és a zárójelben lévő helyőrző értékeket a saját értékeire cserélje le:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Várólista hatóköre

Ha egy szerepkör hatókörét szeretné hozzárendelni egy várólistához, adjon meg egy karakterláncot, amely a `--scope` paraméter várólistájának hatókörét tartalmazza. Az üzenetsor hatóköre a következő formában van:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

Az alábbi példa a **Storage üzenetsor-adatközreműködői** szerepkört rendeli hozzá egy felhasználóhoz, hatókörét pedig a várólista szintjére. Ügyeljen rá, hogy a minták értékeit és a zárójelben lévő helyőrző értékeket a saját értékeire cserélje le:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Storage-fiók hatóköre

A Storage-fiókhoz tartozó szerepkör hozzárendeléséhez adja meg a Storage-fiók erőforrásának hatókörét a `--scope` paraméterhez. A Storage-fiók hatóköre a következő formában van:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Az alábbi példa bemutatja, hogyan rendelhető hozzá a **Storage blob-Adatolvasói** szerepkör a felhasználóhoz a Storage-fiók szintjén. Győződjön meg arról, hogy a minták értékeit a saját értékeire cseréli: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Erőforráscsoport hatóköre

Az erőforráscsoport hatókörének hozzárendeléséhez adja meg az erőforráscsoport nevét vagy AZONOSÍTÓját a `--resource-group` paraméterhez. Az alábbi példa a **Storage üzenetsor-Adatolvasói** szerepkört rendeli hozzá egy felhasználóhoz az erőforráscsoport szintjén. Ügyeljen rá, hogy a minták értékeit és a helyőrző értékeket zárójelben adja meg a saját értékeivel:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Előfizetés hatóköre

Ahhoz, hogy az előfizetéshez hatókört rendeljen, adja meg az előfizetés hatókörét a `--scope` paraméterhez. Az előfizetés hatóköre a következő formában van:

```
/subscriptions/<subscription>
```

Az alábbi példa bemutatja, hogyan rendelhető hozzá a **Storage blob-Adatolvasói** szerepkör a felhasználóhoz a Storage-fiók szintjén. Győződjön meg arról, hogy a minták értékeit a saját értékeire cseréli: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Következő lépések

- [Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)
- [Hozzáférés biztosítása az Azure Blob-és üzenetsor-adataihoz a RBAC használatával Azure PowerShell](storage-auth-aad-rbac-powershell.md)
- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával az Azure Portalon](storage-auth-aad-rbac-portal.md)
