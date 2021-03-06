---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 0aa62a76727f6f913c277100d8c5b36ed1b00110
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618493"
---
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

*Ha már rendelkezik erőforráscsoporthoz, ugorjon a [kulcstartó létrehozása](#create-a-key-vault)lehetőségre.*

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

Hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI paranccsal, a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell paranccsal vagy a [Azure Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

*Ha már rendelkezik kulcstartóval, ugorjon a [Key Vault speciális hozzáférési házirendjeinek beállítása](#set-key-vault-advanced-access-policies)lehetőségre.*

Hozzon létre egy Key vaultot az az kulcstartó [create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI parancs, a [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure PowerShell-parancs, a [Azure Portal](https://portal.azure.com)vagy egy [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)használatával.

>[!WARNING]
> Annak biztosítása érdekében, hogy a titkosítási titkok ne haladják meg a regionális határokat, Azure Disk Encryption megköveteli, hogy a Key Vault és a virtuális gépek ugyanabban a régióban legyenek. Hozzon létre és használjon olyan Key Vault, amely ugyanabban a régióban található, mint a titkosítani kívánt virtuális gépek. 

Minden Key Vault egyedi névvel kell rendelkeznie. A következő példákban cserélje le a < az egyedi-kulcstartó-Name > a Key Vault nevét.

### <a name="azure-cli"></a>Azure CLI

Amikor kulcstartót hoz létre az Azure CLI használatával, adja hozzá a "--enabled-for-Disk-Encryption" jelzőt.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Ha Azure PowerShell használatával hoz létre kulcstartót, adja hozzá a "-EnabledForDiskEncryption" jelzőt.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager-sablon

A Key vaultot a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)használatával is létrehozhatja.

1. Az Azure Gyorsindítás sablonon kattintson az **üzembe helyezés az Azure**-ban lehetőségre.
2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, Key Vault nevét, az objektumazonosító, a jogi feltételek és a szerződés elemet, majd kattintson a **vásárlás**elemre. 


##  <a name="set-key-vault-advanced-access-policies"></a>A Key Vault speciális hozzáférési házirendjeinek beállítása

Az Azure platform a titkosítási kulcsok vagy titkos kódok, hogy elérhetők legyenek a rendszerindítást, és visszafejti a köteteket a virtuális géphez a key vaultban lévő hozzá kell férnie. 

Ha a Key vaultot nem engedélyezte a lemez titkosításához, üzembe helyezéséhez vagy a sablon üzembe helyezéséhez a létrehozáskor (ahogy az az előző lépésben látható), frissítenie kell a speciális hozzáférési szabályzatokat.  

### <a name="azure-cli"></a>Azure CLI

A Key Vault lemezes titkosításának engedélyezéséhez használja az [az kulcstartó frissítést](/cli/azure/keyvault#az-keyvault-update) . 

 - **Key Vault engedélyezése a lemezes titkosításhoz:** Engedélyezve van a-Disk-Encryption szükséges. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Key Vault telepítésének engedélyezése, ha szükséges:** Engedélyezi a Microsoft. számítási erőforrás-szolgáltató számára, hogy a kulcstartóból beolvassa a titkos kulcsokat, amikor ez a kulcstartó az erőforrás-létrehozásban hivatkozik, például virtuális gép létrehozásakor.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Szükség esetén Key Vault engedélyezése a sablonok telepítéséhez:** Annak engedélyezése, hogy a Resource Manager beolvassa a titkos kulcsokat a tárból.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 A Key Vault PowerShell-parancsmagjának [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) használatával engedélyezheti a lemez titkosítását.

  - **Key Vault engedélyezése a lemezes titkosításhoz:** Az Azure Disk Encryption EnabledForDiskEncryption szükséges.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Key Vault telepítésének engedélyezése, ha szükséges:** Engedélyezi a Microsoft. számítási erőforrás-szolgáltató számára, hogy a kulcstartóból beolvassa a titkos kulcsokat, amikor ez a kulcstartó az erőforrás-létrehozásban hivatkozik, például virtuális gép létrehozásakor.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Szükség esetén Key Vault engedélyezése a sablonok telepítéséhez:** Lehetővé teszi a Azure Resource Manager számára, hogy a kulcstartóból beolvassa a titkos kulcsokat, ha a kulcstároló a sablon központi telepítésében van hivatkozva.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Válassza ki a kulcstartót, lépjen a **hozzáférési házirendek**elemre, és **kattintson ide a speciális hozzáférési szabályzatok megjelenítéséhez**.
2. Jelölje be a **Azure Disk Encryptionhoz való hozzáférés engedélyezése a kötetek titkosításához**jelölőnégyzetet.
3. Ha szükséges, jelölje be az **Azure Virtual Machines való hozzáférés engedélyezése az üzembe helyezéshez** és/vagy az **Azure Resource Manager hozzáférésének engedélyezése a sablonok telepítéséhez**lehetőséget. 
4. Kattintson a **Save** (Mentés) gombra.

    ![Az Azure key vault speciális hozzáférési szabályzatok](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Kulcs titkosítási kulcs (KEK) beállítása

Egy további titkosítási kulcsok biztonsági szintet szeretne kulcstitkosítási kulcs-(KEK) használatára, ha egy KEK hozzáadása a key vaultban. Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt.

Létrehozhat egy új KEK-t az Azure CLI az [kulcstartó kulcs létrehozása](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) parancs, a Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmag vagy a [Azure Portal](https://portal.azure.com/)használatával. RSA-kulcs típusát kell előállítania; Azure Disk Encryption még nem támogatja az elliptikus görbe kulcsait.

Ehelyett egy KEK-t is importálhat a helyszíni kulcskezelő HSM-ből. További információ: [Key Vault dokumentáció](/azure/key-vault/key-vault-hsm-protected-keys).

A Key Vault KEK URL-címeinek verziószámozással kell rendelkezniük. Az Azure ezt a korlátozást, versioning, érvénybe lépteti. Érvényes titkos kulcsot, és KEK URL-címeket tekintse meg az alábbi példák:

* Érvényes titkos URL-cím – példa: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Érvényes KEK URL-cím – példa: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Az Azure Disk Encryption részeként a key vault titkos kódok és KEK URL-címek megadásához használt portszámok nem támogatja. Nem támogatott, és támogatott a key vault URL-címek példákért lásd az alábbi példákat:

  * Elfogadható Key Vault URL-cím: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Nem elfogadható Key Vault URL-cím: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI az kulcstartó [kulcs létrehozása](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) paranccsal létrehozhat egy új KEK-t, és tárolhatja azt a kulcstartóban.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

Ehelyett egy titkos kulcsot is importálhat az Azure CLI az [kulcstartó kulcs importálása](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) paranccsal:

Mindkét esetben meg kell adnia a KEK nevét az Azure CLI-hez az [VM encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --Key-encryption-Key paraméterrel. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Egy új KEK létrehozásához használja a Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) parancsmagot, és tárolja azt a Key vaultban.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Ehelyett egy titkos kulcsot is importálhat az Azure PowerShell az [kulcstartó kulcs importálása](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) paranccsal.

Mindkét esetben meg kell adnia a KEK Key Vault AZONOSÍTÓját és a KEK URL-címét a Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId és-KeyEncryptionKeyUrl paraméterekben. Vegye figyelembe, hogy ez a példa feltételezi, hogy ugyanazt a kulcstartót használja, mint a lemez-titkosítási kulcs és a KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
