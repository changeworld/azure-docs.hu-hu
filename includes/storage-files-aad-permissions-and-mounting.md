---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 23550c83e76631e44d5036e0a038f01b61a79f1b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208235"
---
## <a name="assign-access-permissions-to-an-identity"></a>Hozzáférési engedélyek kiosztása identitáshoz

A Azure Files-erőforrások identitás-alapú hitelesítéssel való eléréséhez az identitásnak (egy felhasználónak, csoportnak vagy egyszerű szolgáltatásnak) rendelkeznie kell a szükséges engedélyekkel a megosztás szintjén. Ez a folyamat hasonló a Windows-megosztási engedélyek megadásához, ahol megadhatja, hogy egy adott felhasználó milyen típusú hozzáférést osszon meg a fájlmegosztás számára. Az ebben a szakaszban található útmutatás azt mutatja be, hogyan lehet olvasási, írási vagy törlési engedélyeket rendelni egy fájlmegosztás identitásához.

Három Azure-beli beépített szerepkört vezettünk be a felhasználók számára a megosztási szintű engedélyek megadásához:

- **Storage file-adat SMB-megosztás olvasója** az Azure Storage-fájlmegosztás SMB-kapcsolaton keresztüli olvasási hozzáférését teszi lehetővé.
- A **Storage file-adat SMB-megosztási közreműködői** lehetővé teszik az olvasási, írási és törlési hozzáférést az Azure Storage-fájlmegosztás SMB-en keresztül.
- A **Storage file-ADATsmb-megosztás emelt szintű közreműködője** lehetővé teszi az Azure Storage-FÁJLMEGOSZTÁS az SMB protokollon keresztül történő olvasását, írását, törlését és módosítását.

> [!IMPORTANT]
> Egy fájlmegosztás teljes körű felügyeleti ellenőrzése, beleértve a fájl tulajdonjogának átvételének lehetőségét, a Storage-fiók kulcsát kell használnia. Az Azure AD hitelesítő adatai nem támogatják a felügyeleti felügyeletet.

A Azure Portal, a PowerShell vagy az Azure CLI használatával hozzárendelheti a beépített szerepköröket egy felhasználó Azure AD-identitásához a megosztási szintű engedélyek megadásához.

> [!NOTE]
> Ne feledje, hogy az ad hitelesítő adatait az Azure AD-be szinkronizálja, ha azt tervezi, hogy az AD-t hitelesítéshez használja. Az AD-ből az Azure AD-be való jelszó-kivonatolási szinkronizálás nem kötelező. Az AD-ből szinkronizált Azure AD-identitásnak meg kell adni a megosztási szintű engedélyt.

#### <a name="azure-portal"></a>Azure Portal
Ha RBAC-szerepkört szeretne hozzárendelni egy Azure AD-identitáshoz a [Azure Portal](https://portal.azure.com)használatával, kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg a fájlmegosztást, vagy [hozzon létre egy fájlmegosztást](../articles/storage/files/storage-how-to-create-file-share.md).
2. Válassza a **Access Control (iam)** lehetőséget.
3. Válassza **a szerepkör-hozzárendelés hozzáadása** elemet.
4. A **szerepkör-hozzárendelés hozzáadása** panelen válassza ki a megfelelő beépített szerepkört (tárolási fájl adatsmb-megosztási olvasó, tárolási fájl adat SMB-megosztás közreműködője) a **szerepkör** listából. Az alapértelmezett beállításhoz ne **rendeljen hozzá hozzáférést** : **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév**. Válassza ki a cél Azure AD-identitást név vagy e-mail-cím alapján.
5. A szerepkör-hozzárendelési művelet befejezéséhez válassza a **Mentés** lehetőséget.

#### <a name="powershell"></a>PowerShell

A következő PowerShell-minta bemutatja, hogyan rendelhet hozzá egy RBAC-szerepkört egy Azure AD-identitáshoz a bejelentkezési név alapján. A RBAC-szerepkörök PowerShell-lel való hozzárendelésével kapcsolatos további információkért lásd: [a hozzáférés kezelése a RBAC és a Azure PowerShell használatával](../articles/role-based-access-control/role-assignments-powershell.md).

A következő minta parancsfájl futtatása előtt ne felejtse el helyettesíteni a helyőrző értékeket, beleértve a zárójeleket is a saját értékeivel.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>parancssori felület
  
A következő CLI 2,0-parancs bemutatja, hogyan rendelhet hozzá egy RBAC-szerepkört egy Azure AD-identitáshoz a bejelentkezési név alapján. A RBAC szerepköreinek Azure CLI-vel való hozzárendelésével kapcsolatos további információkért lásd: [a hozzáférés kezelése a RBAC és az Azure CLI használatával](../articles/role-based-access-control/role-assignments-cli.md). 

A következő minta parancsfájl futtatása előtt ne felejtse el helyettesíteni a helyőrző értékeket, beleértve a zárójeleket is a saját értékeivel.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>NTFS-engedélyek konfigurálása SMB-kapcsolaton keresztül 
A megosztási szintű engedélyek RBAC való hozzárendelését követően megfelelő NTFS-engedélyeket kell rendelnie a gyökér, a könyvtár vagy a fájl szintjén. Gondoljon arra, hogy a megosztási szintű engedélyek magas szintű forgalomirányító, amely meghatározza, hogy a felhasználó hozzáférhet-e a megosztáshoz. Míg az NTFS-engedélyek részletesebben határozzák meg, hogy a felhasználó milyen műveleteket végezhet el a címtárban vagy a fájl szintjén.

Azure Files a teljes NTFS alapszintű és speciális engedélyeket támogatja. Az Azure-fájlmegosztás könyvtárain és fájljain NTFS-engedélyeket tekinthet meg és konfigurálhat, ha csatlakoztatja a megosztást, majd a Windows fájlkezelővel vagy a Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) vagy a [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) parancsot futtatja. 

Az NTFS rendszergazdai jogosultságokkal történő konfigurálásához a megosztást a tartományhoz csatlakoztatott virtuális gépről a Storage-fiók kulcsa használatával kell csatlakoztatnia. Kövesse a következő szakaszban található utasításokat egy Azure-fájlmegosztás parancssorból való csatlakoztatásához és ennek megfelelően az NTFS-engedélyek konfigurálásához.

A fájlmegosztás gyökérkönyvtárában a következő engedélyek támogatottak:

- Builtin\rendszergazda: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (IO) (GR, GE)
- NT AUTHORITY\Authenticated-felhasználók: (OI) (CI) (M)
- NT AUTHORITY\SYSTEM: (F)
- LÉTREHOZÓ TULAJDONOS: (OI) (CI) (IO) (F)

### <a name="configure-ntfs-permissions-with-icacls"></a>NTFS-engedélyek konfigurálása icacls-val
A következő Windows-paranccsal teljes körű engedélyeket adhat meg a fájlmegosztás alatt lévő összes könyvtárhoz és fájlhoz, beleértve a gyökérkönyvtárat is. Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Ha többet szeretne megtudni arról, hogyan használható a icacls az NTFS-engedélyek megadásához és a különböző típusú támogatott engedélyekhez, tekintse meg [az icacls parancssori útmutatóját](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="mount-a-file-share-from-the-command-prompt"></a>Fájlmegosztás csatlakoztatása a parancssorból

Az Azure-fájlmegosztás csatlakoztatásához használja a Windows **net use** parancsot. Ne felejtse el lecserélni az alábbi példában szereplő helyőrző értékeket a saját értékeire. A fájlmegosztás csatlakoztatásával kapcsolatos további információkért lásd: [Azure-fájlmegosztás használata a Windowsban](../articles/storage/files/storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>NTFS-engedélyek konfigurálása a Windows fájlkezelővel
A Windows fájlkezelővel teljes hozzáférést biztosíthat a fájlmegosztás alatt lévő összes könyvtárhoz és fájlhoz, beleértve a gyökérkönyvtárat is.

1. Nyissa meg a Windows fájlkezelő alkalmazást, és kattintson a jobb gombbal a fájlra/könyvtárra, és válassza a **Tulajdonságok** lehetőséget
2. Kattintson a **Biztonság** fülre.
3. Kattintson a **Szerkesztés..** . elemre. az engedélyek módosításához szükséges gomb
4. Módosíthatja a meglévő felhasználók engedélyeit, vagy kattintson a **Hozzáadás...** gombra az új felhasználók engedélyeinek megadásához.
5. Az új felhasználók felvételének megadására szolgáló ablakban adja meg azt a célként megadott felhasználónevet, amelyet az **adja meg a kijelölendő objektumok nevét** mezőbe, majd kattintson **a Névellenőrzés elemre, hogy** megkeresse a CÉLKÉNT megadott felhasználó teljes UPN-nevét.
7.  Kattintson **az OK** gombra
8.  A biztonság lapon válassza ki az összes olyan engedélyt, amelyet az újonnan felvett felhasználónak szeretne megadni
9.  Kattintson az **alkalmaz** gombra

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Fájlmegosztás csatlakoztatása tartományhoz csatlakoztatott virtuális gépről

A következő folyamat ellenőrzi, hogy a fájlmegosztás és a hozzáférési engedélyek megfelelően lettek-e beállítva, és hogy elérhető-e egy Azure-fájlmegosztás egy tartományhoz csatlakoztatott virtuális gépről:

Jelentkezzen be a virtuális gépre azzal az Azure AD-identitással, amelyhez engedélyeket adott meg, ahogy az az alábbi képen is látható. Ha engedélyezte az AD-hitelesítést a Azure Fileshoz, használja az AD-hitelesítő adatokat. Az Azure AD DS-hitelesítéshez jelentkezzen be az Azure AD hitelesítő adataival.

![Az Azure AD bejelentkezési képernyőjét bemutató képernyőkép a felhasználói hitelesítéshez](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Az Azure-fájlmegosztás csatlakoztatásához használja az alábbi parancsot. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. Mivel a hitelesítése megtörtént, nem kell megadnia a Storage-fiók kulcsát, az AD-hitelesítő adatokat vagy az Azure AD-beli hitelesítő adatokat. Az egyszeri bejelentkezés az AD-vel vagy az Azure AD DS-vel történő hitelesítésre is használható.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
