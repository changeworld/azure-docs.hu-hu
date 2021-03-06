---
title: Felhasználó által hozzárendelt felügyelt identitások kezelése a REST-Azure AD használatával
description: Részletes útmutató a felhasználó által hozzárendelt felügyelt identitás létrehozásához, listázásához és törléséhez REST API-hívások kezdeményezéséhez.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e108451e4c19e77e01b5bcc5d8dd21e86ad73a
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547416"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása vagy törlése REST API-hívások használatával

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az Azure-erőforrások felügyelt identitásai lehetővé teszi az Azure-szolgáltatások hitelesítését az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy hitelesítő adatokat kellene megadnia a kódban. 

Ebből a cikkből megtudhatja, hogyan hozhat létre, listázhat és törölhet egy felhasználó által hozzárendelt felügyelt identitást a CURL használatával REST API-hívások kezdeményezéséhez.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ha Windows rendszert használ, telepítse a [Linux Windows alrendszerét](https://msdn.microsoft.com/commandline/wsl/about) , vagy használja a Azure Portal [Azure Cloud Shell](../../cloud-shell/overview.md) .
- Ha a [Windows alrendszert Linux](https://msdn.microsoft.com/commandline/wsl/about) vagy Linux rendszerű [terjesztési operációs rendszeren](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)használja, [telepítse az Azure CLI helyi konzolt](/cli/azure/install-azure-cli).
- Ha az Azure CLI helyi konzolt használja, jelentkezzen be az Azure-ba a `az login` használatával egy olyan fiókkal, amely társítva van egy olyan Azure-előfizetéshez, amely a felhasználó által hozzárendelt felügyelt identitási adatokat szeretné telepíteni vagy beolvasni.
- Egy tulajdonos hozzáférési token lekérése a `az account get-access-token` használatával a következő, felhasználó által hozzárendelt felügyelt identitás-műveletek végrehajtásához.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása 

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésével.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Kérések fejlécei**

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        |

**Kérelem törzse**

|Név  |Leírás  |
|---------|---------|
|location     | Kötelező. Erőforrás helye.        |

## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások listázása

Felhasználó által hozzárendelt felügyelt identitás listázásához/olvasásához a fióknak a [felügyelt identitás-kezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) vagy a [felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésre van szüksége.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

Felhasználó által hozzárendelt felügyelt identitás törléséhez a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésével.

> [!NOTE]
> A felhasználó által hozzárendelt felügyelt identitás törlése nem távolítja el a hivatkozást a hozzá rendelt összes erőforrásból. Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuális gépről a CURL használatával: [felhasználó által hozzárendelt identitás eltávolítása egy Azure-beli virtuális](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm)gépről.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        |

## <a name="next-steps"></a>Következő lépések

A felhasználó által hozzárendelt felügyelt identitás Azure-beli virtuális gépekhez/VMSS való hozzárendelésével kapcsolatos információkért tekintse meg a [felügyelt identitások konfigurálása](qs-configure-rest-vm.md#user-assigned-managed-identity) Azure-beli virtuális gépen REST API hívásokkal és felügyelt identitások konfigurálása az Azure-erőforrásokhoz a [virtuálisgép-méretezési csoportokban REST API hívások használatával](qs-configure-rest-vmss.md#user-assigned-managed-identity)című témakört.
