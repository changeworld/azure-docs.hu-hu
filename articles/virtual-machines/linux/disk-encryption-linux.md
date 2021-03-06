---
title: Azure Disk Encryption forgatókönyvek Linux rendszerű virtuális gépeken
description: Ez a cikk a Linux rendszerű virtuális gépek Microsoft Azure lemezes titkosításának engedélyezéséhez nyújt útmutatást különböző forgatókönyvek esetén
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 19dcfb96f29939fd92f49ba288ddb6d9264e0f9a
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970597"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption forgatókönyvek Linux rendszerű virtuális gépeken

Azure Disk Encryption a Linux DM-Crypt szolgáltatásával biztosítja a kötetek titkosítását az Azure Virtual Machines (VM) operációsrendszer-és adatlemezei számára, és integrálva van Azure Key Vault a lemezes titkosítási kulcsok és titkos kódok felügyeletéhez és kezeléséhez. A szolgáltatás áttekintését lásd: [Azure Disk Encryption Linux rendszerű virtuális gépekhez](disk-encryption-overview.md).

Sok lemezes titkosítási forgatókönyv létezik, és a lépések a forgatókönyvtől függően eltérőek lehetnek. A következő részek a Linux rendszerű virtuális gépekkel kapcsolatos részletesebb forgatókönyveket ismertetik.

A lemezes titkosítást csak a [támogatott virtuálisgép-méretek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)virtuális gépei esetében lehet alkalmazni. A következő előfeltételeknek is teljesülniük kell:

- [A virtuális gépekre vonatkozó további követelmények](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózati követelmények](disk-encryption-overview.md#networking-requirements)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview.md#encryption-key-storage-requirements)

Minden esetben készítsen [pillanatképet](snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági másolatot a lemezek titkosítása előtt. Biztonsági mentések ellenőrizze, hogy a helyreállítási beállítások titkosítás során váratlan hiba esetén lehetséges. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági szükséges, a titkosítás előtt. A biztonsági mentés után a [set-AzVMDiskEncryptionExtension parancsmag](/powershell/module/az.compute/set-azvmdiskencryptionextension) használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. További információ a titkosított virtuális gépek biztonsági mentéséről és visszaállításáról: [Azure Backup](../../backup/backup-azure-vms-encryption.md) . 

>[!WARNING]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Részletekért lásd: [Azure Disk Encryption az Azure ad-vel (előző kiadás)](disk-encryption-overview-aad.md) . 
>
> - A Linux operációs rendszer köteteinek titkosításakor a virtuális gépet nem szabad megfontolni. Határozottan javasoljuk, hogy az SSH-bejelentkezések elkerülése érdekében a titkosítási folyamat során ne akadályozza meg az összes olyan megnyitott fájl blokkolását, amelyet a titkosítási folyamat során el kell érnie. A folyamat ellenőrzéséhez használja a [Get-AzVMDiskEncryptionStatus PowerShell-](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot vagy a [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI-parancsot. Ez a folyamat várhatóan néhány órát is igénybe vehet egy 30 GB-OS kötetre, és további időt az adatkötetek titkosítására. Az adatmennyiség-titkosítási idő arányos lesz az adatkötetek méretétől és mennyiségétől, kivéve, ha az összes titkosítás formátuma beállítást használja. 
> - Linux rendszerű virtuális gépek titkosításának letiltása csak az adatkötetek esetében támogatott. Nem támogatott a adatok vagy operációsrendszer-kötettel, ha az operációsrendszer-kötet titkosított.  

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és az Azure-hoz való kapcsolódás

Azure Disk Encryption engedélyezhető és felügyelhető az [Azure CLI](/cli/azure) -n és [Azure PowerShellon](/powershell/azure/new-azureps-module-az)keresztül. Ehhez telepítenie kell az eszközöket helyileg, és csatlakoznia kell az Azure-előfizetéséhez.

### <a name="azure-cli"></a>Azure CLI

Az [Azure CLI 2,0](/cli/azure) egy parancssori eszköz az Azure-erőforrások kezeléséhez. A parancssori felület célja rugalmasan kérdezhet le adatokat, nem blokkoló hosszú ideig futó műveleteket támogatja, és ellenőrizze, hogy megkönnyítse a parancsprogramok használatát. Az [Azure CLI telepítése](/cli/azure/install-azure-cli?view=azure-cli-latest)című témakör lépéseit követve helyileg is telepítheti.

 

Ha be [szeretné jelentkezni az Azure-fiókjába az Azure CLI-vel](/cli/azure/authenticate-azure-cli), használja az az [login](/cli/azure/reference-index?view=azure-cli-latest#az-login) parancsot.

```azurecli
az login
```

Ha szeretne, válassza ki a bejelentkezéshez, használja a bérlő:
    
```azurecli
az login --tenant <tenant>
```

Ha több előfizetéssel rendelkezik, és meg szeretne adni egy adott ilyet, szerezze be az előfizetések listáját az [az Account List](/cli/azure/account#az-account-list) paranccsal, és adja meg az az [Account set](/cli/azure/account#az-account-set)paranccsal.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

További információ: Ismerkedés [Az Azure CLI 2,0](/cli/azure/get-started-with-azure-cli)-mel. 

### <a name="azure-powershell"></a>Azure PowerShell
Az [Azure PowerShell az modul](/powershell/azure/new-azureps-module-az) olyan parancsmagokat biztosít, amelyek a [Azure Resource Manager](../../azure-resource-manager/management/overview.md) modellt használják az Azure-erőforrások kezeléséhez. A böngészőben a [Azure Cloud Shell](../../cloud-shell/overview.md)használatával is használhatja, vagy telepítheti a helyi gépre a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című részben leírtak szerint. 

Ha már nincs telepítve a helyi számítógépen, győződjön meg arról, hogy a legújabb Azure PowerShell SDK-verzió használatával konfigurálja az Azure Disk Encryption. Töltse le [Azure PowerShell kiadás](https://github.com/Azure/azure-powershell/releases)legújabb verzióját.

Ha [Azure PowerShell használatával szeretne bejelentkezni az Azure-fiókjába](/powershell/azure/authenticate-azureps?view=azps-2.5.0), használja a [AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) parancsmagot.

```powershell
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, és meg szeretne adni egyet, a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) parancsmaggal listázhatja őket, majd a [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) parancsmagot:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

A [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) parancsmag futtatásával ellenőrizheti, hogy a megfelelő előfizetés van-e kiválasztva.

A Azure Disk Encryption-parancsmagok telepítésének megerősítéséhez használja a [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) parancsmagot:
     
```powershell
Get-command *diskencryption*
```
További információ: [Bevezetés a Azure PowerShell](/powershell/azure/get-started-azureps)használatába. 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen
Ebben a forgatókönyvben a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával engedélyezheti a titkosítást. Ha a virtuálisgép-bővítményre vonatkozó séma-információra van szüksége, tekintse meg a [Linux-bővítmények Azure Disk Encryptionét](../extensions/azure-disk-enc-linux.md) ismertető cikket.

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképe a portálról vagy [Azure Backupon](../../backup/backup-azure-vms-encryption.md)keresztül végezhető el. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. A biztonsági mentést követően a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. A set-AzVMDiskEncryptionExtension parancs a felügyelt lemezes virtuális gépektől a biztonsági mentésig meghiúsul, és ez a paraméter meg lett adva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen az Azure CLI használatával 

Az [Azure CLI](/cli/azure/?view=azure-cli-latest) parancssori eszköz telepítésével és használatával engedélyezheti a lemez titkosítását a titkosított virtuális merevlemezen. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben. Az Azure-ban meglévő vagy futó Linux rendszerű virtuális gépek titkosításának engedélyezéséhez használja a következő CLI-parancsokat:

Az az [VM encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) parancs használatával engedélyezheti a titkosítást egy futó virtuális gépen az Azure-ban.

- **Futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Futó virtuális gép titkosítása a KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy virtuális gép titkosítási állapotának megtekintéséhez használja az az [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen a PowerShell használatával
A [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó virtuális gépen az Azure-ban. Készítsen [pillanatképet](snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági másolatot a virtuális gépről [Azure Backup](../../backup/backup-azure-vms-encryption.md) a lemezek titkosítása előtt. A-skipVmBackup paraméter már meg van adva a PowerShell-parancsfájlokban egy futó Linux rendszerű virtuális gép titkosításához.

-  **Futó virtuális gép titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a Key Vault előfeltételként lett létrehozva. Cserélje le az MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket az értékekre. Módosítsa a-VolumeType paramétert annak megadásához, hogy mely lemezek vannak titkosítva.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Futó virtuális gép titkosítása a KEK használatával:** Előfordulhat, hogy a-VolumeType paramétert kell hozzáadnia, ha adatlemezeket titkosít, és nem az operációsrendszer-lemezt. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** A virtuális gépek titkosítási állapotának vizsgálatához használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemez titkosításának letiltása:** A titkosítás letiltásához használja a [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen sablon alapján

Az Azure-ban a [Resource Manager-sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)engedélyezheti a lemez titkosítását egy meglévő vagy futó Linux rendszerű virtuális gépen.

1. Az Azure rövid útmutató sablonjában kattintson az **üzembe helyezés az Azure-** ba lehetőségre.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, paraméterek, jogi feltételek és szerződés. A **Létrehozás** gombra kattintva engedélyezheti a titkosítást a meglévő vagy futó virtuális gépen.

Az alábbi táblázat a meglévő vagy a virtuális gépek Resource Manager sablon paraméterei:

| Paraméter | Leírás |
| --- | --- |
| vmName | A titkosítási művelet végrehajtásához a virtuális gép nevét. |
| keyVaultName | Annak a kulcstárolónak a neve, amelyre a titkosítási kulcsot fel kell tölteni. Ezt a parancsmag `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` vagy az Azure CLI-parancs `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`használatával kérheti le.|
| keyVaultResourceGroup | A kulcstárolót tartalmazó erőforráscsoport neve. |
|  keyEncryptionKeyURL | A titkosítási kulcs titkosításához használt kulcs titkosítási kulcsának URL-címe. Ez a paraméter nem kötelező, ha a UseExistingKek legördülő listában a **nokek** lehetőséget választja. Ha a UseExistingKek legördülő listában a **KEK** elemet választja, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| VolumeType | A titkosítási műveletet végzi el a kötet típusa. Az érvényes értékek az _operációs rendszer_, _az adatok_és _az összes_. 
| forceUpdateTag | Minden alkalommal, amikor a művelet van szüksége lehet kényszerítéséhez futtassa adja át egy egyedi értéket, például egy GUID Azonosítót. |
| resizeOSDisk | Az operációs rendszer partíció a teljes rendszer virtuális Merevlemeze foglalnak rendszerkötet felosztása előtt lehet átméretezni. |
| location | Minden erőforrás helye. |


## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>A EncryptFormatAll funkció használata a Linux rendszerű virtuális gépeken található adatlemezekhez

A **EncryptFormatAll** paraméter csökkenti a Linux-adatlemezek titkosításának idejét. A bizonyos feltételeknek megfelelő partíciók formátuma (a jelenlegi fájlrendszerrel együtt), majd a parancs végrehajtása előtt újra kell csatlakoztatni. Ha kizár egy olyan adatok lemezt, amely megfelel a feltételeknek, válassza le azt a parancs futtatása előtt.

 A parancs futtatása után a korábban csatlakoztatott meghajtók formázva lesznek, és a titkosítási réteg a most üres meghajtón lesz elindítva. Ha ezt a lehetőséget választja, a rövid élettartamú erőforrás lemezt a virtuális Géphez csatolt is titkosítva lesznek. A rövid élettartamú meghajtó alaphelyzetbe áll, ha újraformázta és újra titkosítja a virtuális gép az Azure Disk Encryption megoldás a következő alkalommal. Az erőforrás-lemez titkosítása után a [Microsoft Azure Linux-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) nem fogja tudni kezelni az erőforrás lemezét, és engedélyezi a lapozófájlt, de manuálisan is konfigurálhatja a lapozófájlt.

>[!WARNING]
> EncryptFormatAll nem használható, ha egy virtuális Gépet az adatkötetek szükséges adatokat. Lemezek kizárhatják titkosítás, ha shellről őket. Meg kell megismerheti a szolgáltatás paraméter és a utalás előtt próbálja ki az éles virtuális gép a, próbálja ki az először a tesztelési virtuális gép EncryptFormatAll először. A EncryptFormatAll beállítás formázza az adatlemezt, és a rajta lévő adatok elvesznek. A folytatás előtt győződjön meg arról, hogy ki szeretne lemezek megfelelően le-e. </br></br>
 >Ez a paraméter beállítás folyamatban van a titkosítási beállítások frissítése közben, ha azt a számítógép újraindítása előtt a tényleges titkosítási vezethet. Ebben az esetben is érdemes távolítsa el a lemezt nem szeretné, hogy formázva az fstab fájl. Hasonlóképpen adja hozzá azt a partíciót az fstab fájl titkosítása-formátumú, mielőtt elindítaná a titkosítási műveletet. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll feltételek
A (z) paraméter minden partíciót megnyit, és titkosítja őket, ha az alábbi feltételek **mindegyike** teljesül: 
- Nem legfelső szintű vagy az operációs rendszer és rendszerindító partíció
- Már nem titkosított
- Nem rendelkeznek BEk-KEL kötet
- Nem RAID-kötet
- Az LVM-kötet nem
- Csatlakoztatva van

A RAID vagy LVM kötetet, hanem a RAID vagy LVM kötetet alkotó lemezek titkosítása.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>A EncryptFormatAll paraméter használata az Azure CLI-vel
Az az [VM encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) parancs használatával engedélyezheti a titkosítást egy futó virtuális gépen az Azure-ban.

-  **Futó virtuális gép titkosítása EncryptFormatAll használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>A EncryptFormatAll paraméter használata PowerShell-parancsmaggal
Használja a [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmagot a EncryptFormatAll paraméterrel. 

**Futó virtuális gép titkosítása EncryptFormatAll használatával:** Az alábbi szkript például inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot a EncryptFormatAll paraméterrel. Az erőforráscsoport, a virtuális gép és a Key Vault előfeltételként lett létrehozva. Cserélje le az MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket az értékekre.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>A EncryptFormatAll paraméter használata a logikai kötet-kezelővel (LVM) 
Azt javasoljuk, hogy az LVM-a-crypt beállítása. A következő példák az összes cserélje le az eszköz-elérési útját, és akkor csatlakozási bármilyen megfelelő-e a használati eset. A telepítő a következő elvégezhető:

- Adja hozzá az adatlemezeket a virtuális Gépet alkotó lesz.
- Formázhatja, csatlakoztathatja és ezeket a lemezeket ad hozzá az fstab fájlt.

    1. Formázza az újonnan hozzáadott lemezt. Itt az Azure létrehoz symlinks használjuk. Symlinks használatával elkerülheti a problémákat, az eszköz nevének módosítása. További információt az [eszközök neveivel kapcsolatos problémák elhárítása](troubleshoot-device-names-problems.md) című cikkben talál.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    1. Csatlakoztassa a lemezeket.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    1. Adja hozzá az fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    1. Futtassa a set-AzVMDiskEncryptionExtension PowerShell-parancsmagot a-EncryptFormatAll használatával a lemezek titkosításához.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Állítsa be LVM felett ezeket a lemezeket. Vegye figyelembe a titkosított meghajtó zárolását a virtuális gép befejezése után indul el. Az LVM csatlakoztatási így is megkapják ezt követően kell-e késleltetni.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Az ügyfél által titkosított VHD-és titkosítási kulcsokból létrehozott új virtuális gépek
Ebben a forgatókönyvben engedélyezheti a PowerShell-parancsmagokkal vagy a CLI-parancsok használatával titkosítja. 

Használja az Azure Disk Encryption szolgáltatásban megjelenő utasításokat az Azure-ban használható előre titkosított rendszerképek előkészítéséhez. A lemezkép létrehozását követően használhatja a lépéseket a következő szakaszban titkosított Azure virtuális gép létrehozásához.

* [Előre titkosított linuxos virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemezről pillanatképet készíthet a portálról, vagy [Azure Backup](../../backup/backup-azure-vms-encryption.md) használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. A biztonsági mentést követően a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. A set-AzVMDiskEncryptionExtension parancs a felügyelt lemezes virtuális gépektől a biztonsági mentésig meghiúsul, és ez a paraméter meg lett adva. 
>
> Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Azure PowerShell használata a virtuális gépek titkosításához előre titkosított VHD-k használatával 
A titkosított virtuális merevlemez titkosítását a [set-AzVMOSDisk PowerShell-](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples)parancsmag használatával engedélyezheti. Az alábbi példa néhány gyakori paramétereket biztosít. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott adatlemez

Új adatlemezt az [az VM Disk Attach](add-disk.md)vagy [a Azure Portal](attach-disk-portal.md)használatával adhat hozzá. Mielőtt titkosíthatók, először csatlakoztassa az újonnan csatolt adatlemezre kell. Az adatmeghajtó titkosításához kell kérnie, mert a meghajtó használhatatlan lesz, bár a titkosítás folyamatban van. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure CLI-vel

 Ha a virtuális gép korábban "all" titkosítással lett titkosítva, akkor a--Volume-Type paraméternek "all" értékűnek kell maradnia. Mind az operációs rendszer és az adatlemezek összes tartalmazza. Ha a virtuális gépet korábban "operációs rendszer" típusú kötettel titkosítták, akkor a--Volume-type paramétert az "all" értékre kell módosítani, hogy az operációs rendszer és az új adatlemez is szerepelni fog. Ha a virtuális gép titkosított "Adatok" csak a kötet típusát, majd azt is marad, "Data" ahogyan alább is látható. Hozzáadásával és a egy új adatlemez csatolása a virtuális gép nem elegendő előkészítése a titkosításhoz. Az újonnan csatolt lemez is kell formázva, és megfelelően csatlakoztatva a virtuális gép titkosítás engedélyezése előtt. Linux rendszeren a lemezt egy [állandó blokk-eszköz nevével](troubleshoot-device-names-problems.md)kell csatlakoztatni az/etc/fstab-ben.  

Powershell-szintaxis szakembereket a CLI nem igényel egy egyedi verziója szolgáltatni a titkosítás engedélyezése a felhasználó. A CLI automatikusan hoz létre, és használja a saját egyedi feladatütemezési version értéke.

-  **Egy futó virtuális gép adatköteteinek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Futó virtuális gép adatköteteinek titkosítása a KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure PowerShell használatával
 Új lemez titkosítása Linux rendszeren a Powershell segítségével, amikor egy új verziója kell adni. A feladatütemezési verziójában található egyedinek kell lennie. Az alábbi parancsfájlt előállít egy GUID Azonosítót a verziója. Készítsen [pillanatképet](snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági másolatot a virtuális gépről [Azure Backup](../../backup/backup-azure-vms-encryption.md) a lemezek titkosítása előtt. A-skipVmBackup paraméter már meg van adva a PowerShell-parancsfájlokban egy újonnan hozzáadott adatlemez titkosításához.
 

-  **Egy futó virtuális gép adatköteteinek titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a key vault kell rendelkezik már létre van hozva előfeltételeket. Cserélje le az MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket az értékekre. A - VolumeType paraméter elfogadható értékek: All, az operációs rendszer és az adatok. Ha a virtuális gépet korábban "OS" vagy "all" típusú kötettel titkosították, akkor a-VolumeType paramétert az "all" értékre kell módosítani, hogy az operációs rendszer és az új adatlemez is szerepelni fog.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Futó virtuális gép adatköteteinek titkosítása a KEK használatával:** A-VolumeType paraméter számára elfogadható értékek az összes, az operációs rendszer és az adatok. Ha a virtuális gép korábban titkosított "OS" vagy "All" típusú kötet, majd a - VolumeType paraméter kell módosítani az összes, hogy az operációs rendszer és az új adatlemezt is tartalmazza.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[KVresource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Linux rendszerű virtuális gépek titkosításának letiltása
Letilthatja a titkosítás az Azure PowerShell, az Azure CLI használatával vagy a Resource Manager-sablonnal. 

>[!IMPORTANT]
>Letiltja a titkosítást a Linuxos virtuális gépeken az Azure Disk Encryption csak a támogatott adatköteteket. Nem támogatott a adatok vagy operációsrendszer-kötettel, ha az operációsrendszer-kötet titkosított.  

- **Lemez titkosításának letiltása a Azure PowerShell:** A titkosítás letiltásához használja a [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Titkosítás letiltása az Azure CLI-vel:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **A titkosítás letiltása Resource Manager-sablonnal:** A titkosítás letiltásához használja a [Linux rendszerű virtuális gép titkosításának letiltására](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) szolgáló sablont.
     1. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
     2. Válassza ki az előfizetés, erőforráscsoport, hely, virtuális gép, jogi feltételeket és szerződés.

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A Azure Disk Encryption a következő Linux-forgatókönyvek, funkciók és technológiák esetében nem működik:

- A klasszikus virtuálisgép-létrehozási módszerrel létrehozott alapszintű virtuális gép vagy virtuális gépek titkosítása.
- Egy Linux rendszerű virtuális gép operációsrendszer-meghajtóján vagy adatmeghajtóján lévő titkosítás letiltása az operációs rendszer meghajtójának titkosítása esetén.
- Operációs rendszer meghajtójának titkosítása linuxos virtuálisgép-méretezési csoportokhoz.
- Egyéni lemezképek titkosítása Linux rendszerű virtuális gépeken.
- Integráció egy helyszíni kulcskezelő rendszerrel.
- Az Azure Files (megosztott fájlrendszert).
- Hálózati fájlrendszer (NFS).
- A dinamikus köteteket.
- Ideiglenes operációsrendszer-lemezek.
- Megosztott/elosztott fájlrendszerek titkosítása, például (de nem kizárólag): DFS, GFS, DRDB és CephFS.
- Kernel-összeomlási memóriakép (kdump).

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption áttekintése](disk-encryption-overview.md)
- [Azure Disk Encryption – mintaszkriptek](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)
