---
title: Engedélyek megadása az alkalmazásoknak az Azure Key Vault eléréséhez – Azure Key Vault | Microsoft Docs
description: Megtudhatja, hogyan biztosíthat engedélyt számos alkalmazás számára egy kulcstartó eléréséhez
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d5086377b0bb7f3ca2ece643f82a4e45156f1955
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184859"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Hozzáférés-vezérlési házirenddel Key Vault hitelesítés megadása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A felhőalapú alkalmazások Key Vaulthoz való hitelesítésének legegyszerűbb módja felügyelt identitás; a részletekért tekintse meg a [app Service felügyelt identitás használata a Azure Key Vault eléréséhez](managed-identity.md) című témakört.  Ha helyszíni alkalmazást hoz létre, helyi fejlesztést végez, vagy más módon nem tud felügyelt identitást használni, ehelyett manuálisan is regisztrálhat egy egyszerű szolgáltatásnevet, és hozzáférést biztosíthat a kulcstartóhoz egy hozzáférés-vezérlési házirend használatával.  

A Key Vault legfeljebb 1024 hozzáférési szabályzatot támogat, és minden bejegyzést, amely külön engedélyeket biztosít egy "rendszerbiztonsági tag" számára, például azt, hogy a Azure Key Vault a .NET-hez készült [ügyféloldali kódtáran](quick-create-net.md) lévő konzol alkalmazás hogyan fér hozzá a kulcstartóhoz.

A Key Vault hozzáférés-vezérléssel kapcsolatos részletes információkért lásd [: Azure Key Vault biztonság: identitás-és hozzáférés-kezelés](overview-security.md#identity-and-access-management). A [kulcsok, titkok és tanúsítványok](about-keys-secrets-and-certificates.md) hozzáférés-vezérlésének részletes ismertetését lásd: 

- [Kulcsok hozzáférés-vezérlése](about-keys-secrets-and-certificates.md#key-access-control)
- [Titkok hozzáférés-vezérlése](about-keys-secrets-and-certificates.md#secret-access-control)
- [Tanúsítványok hozzáférés-vezérlése](about-keys-secrets-and-certificates.md#certificate-access-control)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

- Key Vault. Egy meglévő kulcstartót is használhat, vagy létrehozhat egy újat a következő rövid útmutatók lépéseinek végrehajtásával:
   - [Kulcstartó létrehozása az Azure CLI-vel](quick-create-cli.md)
   - [Key Vault létrehozása Azure PowerShell](quick-create-powershell.md)
   - [Hozzon létre egy Key vaultot a Azure Portal](quick-create-portal.md).
- Az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/overview). Azt is megteheti, hogy a [Azure Portal](https://portal.azure.com)is használja.

## <a name="grant-access-to-your-key-vault"></a>Hozzáférés biztosítása a kulcstartóhoz

A Key Vault hozzáférési szabályzatának bejegyzései külön engedélyeket biztosítanak a rendszerbiztonsági tag számára:

- **Egy alkalmazás** Ha az alkalmazás felhőalapú, ehelyett [felügyelt identitás használatával férhet hozzá Azure Key Vaulthoz](managed-identity.md), ha lehetséges
- **Azure ad-csoport** Bár a Key Vault csak a 1024 hozzáférési szabályzatot támogatja, több alkalmazást és felhasználót is hozzáadhat egyetlen Azure AD-csoporthoz, és ezt a csoportot egyetlen bejegyzésként hozzáadhatja a hozzáférés-vezérlési házirendhez.
- **Egy felhasználó** A felhasználók közvetlen hozzáférésének biztosítása a kulcstartóhoz nem **ajánlott**. Ideális esetben a felhasználókat hozzá kell adni egy Azure AD-csoporthoz, amely a Key vaulthoz való hozzáférést is lehetővé tette. Lásd: [Azure Key Vault biztonság: identitás-és hozzáférés-kezelés](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>A objectID beolvasása

Ahhoz, hogy egy alkalmazást, Azure AD-csoportot vagy felhasználói hozzáférést adjon a kulcstartóhoz, először be kell szereznie a objectId.

#### <a name="applications"></a>Alkalmazások

Az alkalmazások objectId a társított egyszerű szolgáltatásnak felel meg. Az egyszerű szolgáltatásokkal kapcsolatos részletes információkat. Lásd: [alkalmazás-és szolgáltatásnév-objektumok Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md). 

Az alkalmazások objectId kétféleképpen szerezhetők be.  Az első az, hogy regisztrálja az alkalmazást Azure Active Directory. Ehhez kövesse az [alkalmazás regisztrálása a Microsoft Identity platformon](../active-directory/develop/quickstart-register-app.md)című útmutató lépéseit. A regisztráció befejezésekor a objectID "Application (ügyfél-) AZONOSÍTÓként" jelenik meg.

A második egy egyszerű szolgáltatásnév létrehozása egy terminál-ablakban. Az Azure CLI-vel használja az az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot.

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

A objectId a kimenetben `clientID`jelenik meg.

A Azure PowerShell használatával használja a [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0) parancsmagot.


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

A objectId a kimenetben `Id` (nem `ApplicationId`) jelenik meg.

#### <a name="azure-ad-groups"></a>Azure AD-csoportok

Hozzáadhat több alkalmazást és felhasználót egy Azure AD-csoporthoz, majd a csoport számára hozzáférést adhat a kulcstartóhoz.  További részletekért tekintse meg a [tagok létrehozása és hozzáadása egy Azure ad-csoporthoz](#creating-and-adding-members-to-an-azure-ad-group) szakaszt alább.

Az Azure parancssori felülettel rendelkező Azure AD-csoportok objectId az az [ad Group List](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) parancs használatával keresheti meg. A szervezetben esetlegesen használt csoportok nagy száma miatt a `--display-name` paraméterhez is meg kell adnia egy keresési karakterláncot.

```azurecli-interactive
az ad group list --display-name <search-string>
```
A objectId a rendszer a JSON-ban adja vissza:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Ha Azure PowerShell használatával szeretné megkeresni egy Azure AD-csoport objectId, használja a [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) parancsmagot. A szervezetben esetlegesen használt csoportok nagy száma miatt valószínűleg keresési karakterláncot is meg kíván adni a `-SearchString` paraméterhez.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

A kimenetben a objectId `Id`jelenik meg:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Felhasználók

Egyéni felhasználót is hozzáadhat a Key Vault hozzáférés-vezérlési házirendjéhez. **Ezt nem javasoljuk.** Ehelyett javasoljuk, hogy felhasználókat adjon hozzá egy Azure AD-csoporthoz, és adja hozzá a csoportot a szabályzatokhoz.

Ha azonban az Azure CLI-vel szeretne felhasználókat megkeresni, használja az az [ad User show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) parancsot, és adja át a felhasználók e-mail-címét a `--id` paraméternek.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

A rendszer visszaadja a felhasználó objectId a kimenetben:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Azure PowerShellt használó felhasználó kereséséhez használja a [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) parancsmagot, és adja át a felhasználók e-mail-címét a `-UserPrincipalName` paraméternek.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

A rendszer a kimenetben `Id`ként adja vissza a felhasználó objectId.

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>A Key vaulthoz való hozzáférés biztosítása

Most, hogy már rendelkezik egy objectID, létrehozhat egy hozzáférési szabályzatot a kulcstartóhoz, amely lehetővé teszi, hogy a kulcsokhoz és titkos kulcsokhoz, valamint az esetlegesen szükséges további engedélyekhez engedélyeket kapjon.

Az Azure CLI-vel ezt úgy teheti meg, hogy átadja a objectId az az Key [Vault set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancsnak.

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

A Azure PowerShell segítségével ezt a objectId a [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) parancsmagra való átadásával teheti meg. 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ApplicationId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Tagok létrehozása és hozzáadása egy Azure AD-csoporthoz

Létrehozhat egy Azure AD-csoportot, alkalmazásokat és felhasználókat vehet fel a csoportba, és hozzáférést biztosíthat a csoportnak a kulcstartóhoz.  Ez lehetővé teszi, hogy több alkalmazást adjon hozzá egy kulcstartóhoz egyetlen hozzáférési házirend-bejegyzésként, és megakadályozza, hogy a felhasználóknak közvetlenül hozzáférjenek a kulcstartóhoz (amit nem lehet lemondani). További részletekért lásd: [alkalmazás-és erőforrás-hozzáférés kezelése Azure Active Directory csoportok használatával](../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>További előfeltételek

A [fenti előfeltételeken](#prerequisites)kívül a csoportok létrehozásához és szerkesztéséhez is engedélyre van szüksége a Azure Active Directory-bérlőben. Ha nem rendelkezik megfelelő engedélyekkel, előfordulhat, hogy kapcsolatba kell lépnie Azure Active Directory rendszergazdájával.

Ha a PowerShellt szeretné használni, az [Azure ad PowerShell-modulra](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50) is szüksége lesz.

### <a name="create-an-azure-active-directory-group"></a>Azure Active Directory csoport létrehozása

Hozzon létre egy új Azure Active Directory csoportot az Azure CLI az [ad Group Create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) paranccsal, vagy a Azure PowerShell [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) parancsmag használatával.


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

Mindkét esetben jegyezze fel az újonnan létrehozott csoportok GroupId, ahogy az alábbi lépésekhez szüksége lesz rá.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Az alkalmazások és a felhasználók objectIds megkeresése

Az Azure CLI-vel az az [ad SP List](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) paranccsal keresheti meg az alkalmazások objectIds az `--show-mine` paraméterrel.

```azurecli-interactive
az ad sp list --show-mine
```

A [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) parancsmaggal megkeresheti az Azure PowerShell alkalmazások objectIds, és átadva a keresési karakterláncot a `-SearchString` paraméternek.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

A felhasználók objectIds megkereséséhez kövesse a fenti [felhasználók](#users) szakasz lépéseit.

### <a name="add-your-applications-and-users-to-the-group"></a>Alkalmazások és felhasználók hozzáadása a csoporthoz

Most adja hozzá a objectIds az újonnan létrehozott Azure AD-csoporthoz.

Az Azure CLI-vel használja az az [ad Group tag Add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add), a objectId átadása a `--member-id` paraméternek.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Azure PowerShell használja az [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) parancsmagot, és adja át a objectId a `-MemberObjectId` paraméternek.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Adja meg az AD-csoport hozzáférését a kulcstartóhoz

Végül adja meg az AD-csoport engedélyeit a kulcstartónak az Azure CLI az Key [Vault set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancs vagy a Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) parancsmag használatával. Példaként tekintse meg az [alkalmazás, az Azure ad-csoport vagy a felhasználó hozzáférése a Key vaulthoz](#give-the-principal-access-to-your-key-vault) című szakaszt.

Az alkalmazásnak szüksége van legalább egy, a Key vaulthoz hozzárendelt identitás-és hozzáférés-kezelési (IAM) szerepkörre is. Ellenkező esetben nem fog tudni bejelentkezni, és nem lesz megfelelő jogosultsága az előfizetéshez való hozzáféréshez.

## <a name="next-steps"></a>További lépések

- [Azure Key Vault biztonság: identitás-és hozzáférés-kezelés](overview-security.md#identity-and-access-management)
- [App Service felügyelt identitással rendelkező Key Vault hitelesítés megadása](managed-identity.md)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
- [A kulcstartó védelme](key-vault-secure-your-key-vault.md).
- [Azure Key Vault fejlesztői útmutató](key-vault-developers-guide.md)
- [Azure Key Vault ajánlott eljárások](key-vault-best-practices.md) áttekintése
