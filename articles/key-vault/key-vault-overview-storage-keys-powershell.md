---
title: Azure Key Vault felügyelt Storage-fiók – PowerShell-verzió
description: A felügyelt tár fiók funkciója zökkenőmentes integrációt biztosít Azure Key Vault és egy Azure Storage-fiók között.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 833f78d89a1a9033e62c10c3b16c5adfc65e1da4
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195124"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>A Storage-fiók kulcsainak kezelése Key Vault és Azure PowerShell

Az Azure Storage-fiók a fiók nevét és kulcsát tartalmazó hitelesítő adatokat használ. A kulcs automatikusan létrejön, és jelszóként szolgál, nem pedig titkosítási kulcsként. A Key Vault a Storage-fiókok kulcsait úgy kezeli, hogy [Key Vault titokként](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)tárolja őket. 

A Key Vault felügyelt Storage-fiók kulcsa funkció használatával listázhatja (szinkronizálhatja) a kulcsokat egy Azure Storage-fiókkal, és rendszeresen újragenerálhatja (elforgathatja) a kulcsokat. A kulcsokat a Storage-fiókok és a klasszikus Storage-fiókok esetében is kezelheti.

A felügyelt Storage-fiók kulcsa funkció használata esetén vegye figyelembe a következő szempontokat:

- A rendszer soha nem adja vissza a kulcs értékeit a hívónak adott válaszként.
- Csak Key Vault kell kezelnie a Storage-fiók kulcsait. Ne kezelje a kulcsokat, és ne zavarja a Key Vault folyamatokat.
- Csak egyetlen Key Vault objektumnak kell kezelnie a Storage-fiók kulcsait. Ne engedélyezze a kulcskezelő szolgáltatás több objektumból való felügyeletét.
- Key Vault kérheti, hogy kezelje a Storage-fiókját egy egyszerű felhasználóval, de nem egy egyszerű szolgáltatásnév használatával.
- Kulcsok újragenerálása csak Key Vault használatával. Ne végezze el manuálisan a Storage-fiók kulcsainak újragenerálása.

Javasoljuk, hogy az Azure Storage-integrációt Azure Active Directory (Azure AD), a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatásával használja. Az Azure AD-integráció az [Azure-blobok és-várólisták](../storage/common/storage-auth-aad.md)számára érhető el, és OAuth2 token-alapú hozzáférést biztosít az Azure Storage-hoz (akárcsak Azure Key Vault).

Az Azure AD lehetővé teszi az ügyfélalkalmazás hitelesítését alkalmazás vagy felhasználói identitás használatával a Storage-fiók hitelesítő adatai helyett. Azure AD-beli [felügyelt identitást](/azure/active-directory/managed-identities-azure-resources/) használhat az Azure-ban való futtatáskor. A felügyelt identitások nem szükségesek az ügyfél-hitelesítéshez és a hitelesítő adatok tárolásához a vagy az alkalmazásban.

Az Azure AD szerepköralapú hozzáférés-vezérlést (RBAC) használ az engedélyezés kezelésére, amelyet a Key Vault is támogat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Egyszerű szolgáltatásnév alkalmazásának azonosítója

Az Azure AD-bérlő minden regisztrált alkalmazást biztosít egy [egyszerű szolgáltatással](/azure/active-directory/develop/developer-glossary#service-principal-object). Az egyszerű szolgáltatásnév a RBAC-on keresztül más Azure-erőforrásokhoz való hozzáférés engedélyezési beállítása során használt alkalmazás-AZONOSÍTÓként szolgál.

A Key Vault egy olyan Microsoft-alkalmazás, amely az összes Azure AD-bérlőben előre regisztrálva van. A Key Vault minden Azure-felhőben ugyanazzal az alkalmazás-AZONOSÍTÓval van regisztrálva.

| Bérlők | Felhő | Alkalmazásazonosító |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Nyilvános Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Egyéb  | Bármely | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Előfeltételek

Az útmutató elvégzéséhez először a következőket kell tennie:

- [Telepítse a Azure PowerShell modult](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Kulcstartó létrehozása](quick-create-powershell.md)
- [Hozzon létre egy Azure-tárfiókot](../storage/common/storage-account-create.md?tabs=azure-powershell). A Storage-fiók nevének csak kisbetűket és számokat kell használnia. A név hosszának 3 és 24 karakter közöttinek kell lennie.
      

## <a name="manage-storage-account-keys"></a>A Storage-fiók kulcsainak kezelése

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Hitelesítse a PowerShell-munkamenetet a Connection [-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) parancsmag használatával. 

```azurepowershell-interactive
Connect-AzAccount
```
Ha több Azure-előfizetéssel rendelkezik, a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) parancsmag használatával is listázhatja őket, és megadhatja a [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) parancsmaggal használni kívánt előfizetést. 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Változók beállítása

Először állítsa be az alábbi lépésekben a PowerShell-parancsmagok által használandó változókat. Ügyeljen arra, hogy frissítse a <YourResourceGroupName>, <YourStorageAccountName>és <YourKeyVaultName> helyőrzőket, és állítsa $keyVaultSpAppId `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`re (a fenti [szolgáltatásnév alkalmazás-azonosítójában](#service-principal-application-id)megadott módon).

A Get [-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) és a [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) parancsmagokkal is Azure PowerShell a felhasználói azonosítót és az Azure Storage-fiók környezetét fogjuk használni.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault hozzáférés biztosítása a Storage-fiókhoz

Mielőtt Key Vault a Storage-fiók kulcsainak elérését és kezelését, engedélyeznie kell a Storage-fiókjához való hozzáférését. A Key Vault alkalmazásnak engedélyekkel kell rendelkeznie a Storage-fiók kulcsainak *listázásához* és *újbóli létrehozásához* . Ezek az engedélyek engedélyezve vannak a beépített RBAC szerepkör- [kezelő szolgáltatás](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)szerepkörrel. 

Rendelje hozzá ezt a szerepkört az Key Vault egyszerű szolgáltatáshoz, és korlátozza a hatókört a Storage-fiókra a Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) parancsmag használatával.

```azurepowershell-interactive
# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Sikeres szerepkör-hozzárendelés esetén a következő példához hasonló kimenetnek kell megjelennie:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Ha Key Vault már hozzá lett adva a szerepkörhöz a Storage-fiókjában, akkor a *"szerepkör-hozzárendelés már létezik* " hibaüzenet jelenik meg. hiba. A szerepkör-hozzárendelést is ellenőrizheti, ha a Azure Portal a Storage-fiók "hozzáférés-vezérlés (IAM)" lapját használja.  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Felhasználói fiók engedélyezése a felügyelt Storage-fiókok számára

A Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) parancsmaggal frissítse a Key Vault hozzáférési szabályzatot, és adja meg a Storage-fiók engedélyeit a felhasználói fiókjához.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Vegye figyelembe, hogy a Storage-fiókokra vonatkozó engedélyek nem érhetők el a Azure Portal "hozzáférési szabályzatok" lapján.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Felügyelt Storage-fiók hozzáadása a Key Vault-példányhoz

A Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) parancsmaggal hozzon létre egy felügyelt Storage-fiókot a Key Vault-példányban. A `-DisableAutoRegenerateKey` kapcsoló azt adja meg, hogy ne generálja újra a Storage-fiók kulcsait.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Ha a Storage-fiókot a kulcs újragenerálása nélkül is sikeresen felhasználta, az alábbi példához hasonló kimenetnek kell megjelennie:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Kulcs újragenerálásának engedélyezése

Ha azt Key Vault szeretné, hogy a rendszer rendszeres időközönként újragenerálja a Storage-fiók kulcsait, akkor a Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) parancsmaggal állíthatja be a regenerációs időszakot. Ebben a példában a három napos újragenerálási időszakot állítjuk be. Három nap elteltével Key Vault újragenerálta a "key2", és az aktív kulcsot a "key2" értékről "key1"-re cseréli.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

A Storage-fiók a kulcs újragenerálásával való sikeres hozzáadását követően az alábbi példához hasonló kimenetnek kell megjelennie:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="shared-access-signature-tokens"></a>Közös hozzáférésű aláírási jogkivonatok

Azt is megteheti, Key Vault hogy közös hozzáférésű aláírási jogkivonatokat állítson elő. Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít. A fiók kulcsainak megosztása nélkül megadhatja az ügyfeleknek a Storage-fiók erőforrásaihoz való hozzáférést. A közös hozzáférésű aláírás biztonságos módot biztosít a tárolási erőforrások megosztására a fiók kulcsainak veszélyeztetése nélkül.

Az ebben a szakaszban szereplő parancsok a következő műveleteket hajtják végre:

- Fiók közös hozzáférésű aláírás-definíciójának beállítása. 
- Hozzon létre egy fiók közös hozzáférési aláírási tokent a blob-, fájl-, tábla-és üzenetsor-szolgáltatásokhoz. A jogkivonat az erőforrástípusok szolgáltatás, a tároló és az objektum számára lett létrehozva. A jogkivonat minden engedélyekkel, HTTPS-kapcsolattal és a megadott kezdési és befejezési dátumokkal jön létre.
- Key Vault felügyelt tároló közös hozzáférésű aláírás-definíciójának beállítása a tárban. A definíció a megosztott hozzáférés-aláírási jogkivonat sablonjának URI-JÁT hozza létre. A definícióban a közös hozzáférésű aláírás típusa `account`, és N napig érvényes.
- Ellenőrizze, hogy a közös hozzáférésű aláírás mentve lett-e a Key vaultban titkos kulcsként.
- 
### <a name="set-variables"></a>Változók beállítása

Először állítsa be az alábbi lépésekben a PowerShell-parancsmagok által használandó változókat. Ügyeljen arra, hogy frissítse a <YourStorageAccountName> és <YourKeyVaultName> helyőrzőket.

Az Azure Storage-fiók kontextusának beszerzéséhez az Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) parancsmagokat is használjuk.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Közös hozzáférésű aláírási jogkivonat létrehozása

Hozzon létre egy közös hozzáférési aláírás definícióját a Azure PowerShell [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) parancsmagok használatával.
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
A $sasToken értéke ehhez hasonlóan fog kinézni.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Közös hozzáférésű aláírás definíciójának létrehozása

Közös hozzáférési aláírás definíciójának létrehozásához használja a Azure PowerShell [set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) parancsmagot.  Megadhatja a választott nevet a `-Name` paraméternek.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>A közös hozzáférésű aláírás definíciójának ellenőrzése

A Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) parancsmag használatával ellenőrizheti, hogy a közös hozzáférésű aláírás definíciója a kulcstartóban van-e tárolva.

Először keresse meg a közös hozzáférési aláírás definícióját a Key vaultban.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Az SAS-definíciónak megfelelő titok a következő tulajdonságokkal rendelkezik:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Mostantól a [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) parancsmagot és a Secret `Name` tulajdonságot is használhatja a titkos kód tartalmának megtekintéséhez.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

A parancs kimenete az SAS-definíciós karakterláncot jeleníti meg.


## <a name="next-steps"></a>További lépések

- [Felügyelt Storage-fiók kulcsainak mintái](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
- [PowerShell-útmutató Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
