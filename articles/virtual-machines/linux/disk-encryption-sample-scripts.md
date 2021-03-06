---
title: Azure Disk Encryption minta parancsfájlok
description: Ez a cikk a Linux rendszerű virtuális gépek Microsoft Azure lemezes titkosításának függeléke.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: c98da4b41da183f56d80fad1e8c01706d1cfcf23
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970510"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption minta parancsfájlok 

Ez a cikk példákat tartalmaz az előre titkosított virtuális merevlemezek és egyéb feladatok előkészítéséhez.

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Az Azure Disk Encryption minta PowerShell-parancsfájlok 

- **Az előfizetésben található összes titkosított virtuális gép listázása**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **A Key vaultban lévő virtuális gépek titkosításához használt összes lemez-titkosítási titok listázása** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Az Azure Disk Encryption előfeltételek PowerShell-parancsfájl használata
Ha már ismeri a Azure Disk Encryption előfeltételeit, használhatja az [Azure Disk Encryption előfeltételek PowerShell-szkriptet](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). A PowerShell-szkriptek használatára példát a [virtuális gépek titkosítása](disk-encryption-powershell-quickstart.md)– gyors útmutató című témakörben talál. Eltávolíthatja a megjegyzéseket a parancsfájl egy szakaszában sor 211, az összes lemez titkosítása egy meglévő erőforráscsoportot a meglévő virtuális gépek indítása. 

Az alábbi táblázat mutatja, hogy mely paraméterek is használható a PowerShell-parancsfájlt: 


|Paraméter|Leírás|Kötelező?|
|------|------|------|
|$resourceGroupName| Az erőforrás nevét, amelyhez a KeyVault tartozik.  Ezen a néven egy új erőforráscsoport létrejön, ha egy nem létezik.| True (Igaz)|
|$keyVaultName|A KeyVault a melyik titkosítási kulcsai elhelyezni kívánt nevét. Ezen a néven egy új tároló létrejön, ha egy nem létezik.| True (Igaz)|
|$location|A KeyVault helye. Győződjön meg arról a KeyVault és a virtuális gépek titkosítását ugyanazon a helyen. A helyek listáját a következővel érheti el: `Get-AzLocation`.|True (Igaz)|
|$subscriptionId|Használható az Azure-előfizetés azonosítója.  Az előfizetés-azonosítóját a következővel érheti el: `Get-AzSubscription`.|True (Igaz)|
|$aadAppName|Neve az Azure AD-alkalmazást, amely a KeyVault titkos kódok írása történik. Ha a megadott néven még nem létezik alkalmazás, a rendszer létrehoz egyet a beírt néven. Ha az alkalmazás már létezik, aadClientSecret a paramétert átadhatja a parancsfájlt.|False (Hamis)|
|$aadClientSecret|A korábban létrehozott Azure AD-alkalmazás titkos ügyfélkódja.|False (Hamis)|
|$keyEncryptionKeyName|A KeyVault választható kulcstitkosítási kulcs neve. Ezen a néven egy új kulcsot létrejön, ha egy nem létezik.|False (Hamis)|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Titkosítása és visszafejtése a virtuális gépek az Azure AD-alkalmazás nélkül

- [A lemez titkosításának engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Titkosítás letiltása futó Linux rendszerű virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Titkosítása és visszafejtése a virtuális gépek az Azure AD-alkalmazás (előző kiadás) 
 
- [A lemez titkosításának engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Titkosítás letiltása futó Linux rendszerű virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez. 


- [Új titkosított felügyelt lemez létrehozása egy előre titkosított VHD/Storage-blobból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Létrehoz egy új titkosított felügyelt lemezt előre titkosított virtuális merevlemez és a megfelelő titkosítási beállítások





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>OPERÁCIÓSRENDSZER-meghajtó titkosítása futó Linux rendszerű virtuális gépen

### <a name="prerequisites-for-os-disk-encryption"></a>Az operációs rendszer lemeztitkosítás előfeltételei

* A virtuális gépnek az operációsrendszer-lemez titkosításával kompatibilis disztribúciót kell használnia a [Azure Disk Encryption támogatott operációs rendszerek](disk-encryption-overview.md#supported-vm-sizes) listájában. 
* A virtuális Gépen a Marketplace-lemezképből az Azure Resource Manager kell létrehozni.
* Az Azure virtuális gép legalább 4 GB RAM (javasolt a mérete, 7 GB).
* (Az RHEL és CentOS) Tiltsa le a SELinux. Tiltsa le a SELinux, lásd: "4.4.2. A SELinux letiltása a [SELinux felhasználói és rendszergazdai útmutatójában](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) a virtuális gépen.
* Miután letiltja az SELinux, legalább egyszer indítsa újra a virtuális gép.

### <a name="steps"></a>Lépések
1. Virtuális gép létrehozása a korábban megadott disztribúció használatával.

   7\.2 CentOS az operációs rendszer lemeztitkosítás támogatott keresztül egy rendszerképet. Ez a rendszerkép használatához adja meg a "7.2n" Termékváltozat, a virtuális gép létrehozásakor:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Konfigurálja a virtuális gép igény szerint. Ha a (operációs rendszer és összes adatok) titkosítása meghajtók, az adatmeghajtók kell lennie a megadott és a csatlakoztatható /etc/fstab fog.

   > [!NOTE]
   > Használja az UUID azonosítója =... megadásához adatmeghajtók az/etc/fstab fájlban a blokk-eszköz neve (például/dev/sdb1) megadása helyett. Titkosítás során meghajtók sorrendje módosítja, a virtuális gépen. Ha a virtuális gép egy adott rendelés blokkeszközöket támaszkodik, azt fogja tudni titkosítás után csatlakoztassa őket.

3. Jelentkezzen ki az SSH-munkamenetet.

4. Az operációs rendszer titkosításához a titkosítás engedélyezésekor az **összes** vagy az **operációs rendszer** volumeType kell megadni.

   > [!NOTE]
   > A `systemd`-szolgáltatásként nem futó összes felhasználói felületi folyamatot le kell ölni egy `SIGKILL`. Indítsa újra a virtuális Gépet. Ha engedélyezi az operációs rendszer lemeztitkosítás egy futó virtuális gépen, tervezze meg a virtuális gépek üzemszünete.

5. A [következő szakaszban](#monitoring-os-encryption-progress)található utasítások alapján rendszeres időközönként figyelje a titkosítás előrehaladását.

6. Miután a Get-AzVmDiskEncryptionStatus megjeleníti a "VMRestartPending" kifejezést, indítsa újra a virtuális gépet a bejelentkezéshez vagy a portál, a PowerShell vagy a CLI használatával.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   A rendszer újraindítása előtt azt javasoljuk, hogy mentse a virtuális gép [rendszerindítási diagnosztikát](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) .

## <a name="monitoring-os-encryption-progress"></a>Az operációs rendszer titkosítási folyamat figyelése
Az operációs rendszer titkosítási folyamat három módon figyelheti:

* Használja az `Get-AzVmDiskEncryptionStatus` parancsmagot, és vizsgálja meg a Feladatnézetben mezőt:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Miután a virtuális gép eléri a "Az operációs rendszer lemezén titkosítás lépései", körülbelül 40 – 50 percet vesz igénybe egy Premium Storage virtuális gépek biztonsági.

  A WALinuxAgent-ban [#388 probléma](https://github.com/Azure/WALinuxAgent/issues/388) miatt a `OsVolumeEncrypted` és a `DataVolumesEncrypted` `Unknown`ként jelennek meg egyes eloszlásokban. A WALinuxAgent verzió 2.1.5, és később, a probléma automatikusan megoldódik. Ha a kimenetben `Unknown` jelenik meg, akkor a Azure Erőforrás-kezelő használatával ellenőrizheti a lemez titkosításának állapotát.

  Nyissa meg a [Azure erőforrás-kezelő](https://resources.azure.com/), majd bontsa ki ezt a hierarchiát a bal oldali kiválasztási panelen:

  ~~~~
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ~~~~                

  A az InstanceView görgessen le a titkosítási állapot meghajtó megtekintéséhez.

  ![Virtuális gép példányait tartalmazó nézet](./media/disk-encryption/vm-instanceview.png)

* Tekintse meg a [rendszerindítási diagnosztikát](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Az ADE-bővítményből származó üzeneteket `[AzureDiskEncryption]`kell előre rögzíteni.

* Jelentkezzen be a virtuális gép SSH-n keresztül, és a bővítmény a napló beolvasása:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Azt javasoljuk, hogy nem jelentkezik be a virtuális gép, amíg folyamatban van az operációs rendszer titkosítási. Másolja a naplókat, csak akkor, ha a két módszer nem sikerült.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Előre titkosított linuxos virtuális merevlemez előkészítése
Előre titkosított virtuális merevlemezek előkészítéséhez a terjesztési függően változhat. Az Ubuntu 16, az openSUSE 13,2 és a CentOS 7 előkészítésének példái érhetők el. 

### <a name="ubuntu-16"></a>Ubuntu 16
A terjesztési telepítése közben titkosítás konfigurálása a következő lépések végrehajtásával:

1. Válassza a **titkosított kötetek konfigurálása** a lemezek particionálásakor lehetőséget.

   ![Ubuntu 16.04 beállítása – titkosított kötetek konfigurálni](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Hozzon létre egy külön rendszerindítási meghajtót, amely nem lesznek titkosítva. A legfelső szintű meghajtójának titkosításához.

   ![Ubuntu 16.04-telepítő – titkosításához, jelölje ki az eszközöket](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Adjon meg egy jelszót. Ez az a jelszót a key vault feltöltött.

   ![Ubuntu 16.04 beállítása – adja meg a jelszót](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Fejezze be a particionálást.

   ![Ubuntu 16.04 beállítása – Befejezés particionálása](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Ha a virtuális gép, és a egy hozzáférési kódot a rendszer kéri, használja a 3. lépésében megadott jelszót.

   ![Ubuntu 16.04 beállítása – adja meg a jelszót a rendszerindító](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Készítse elő a virtuális gépet az Azure-ba való feltöltéshez [ezen utasítások](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/)használatával. Nem futnak (a virtuális gép megszüntetése) az utolsó lépés még.

Adja meg a titkosítás működéséhez az Azure-ral a következő lépések végrehajtásával:

1. Hozzon létre egy fájlt /usr/local/sbin/azure_crypt_key.sh, a következő parancsfájl tartalmát. A KeyFileName figyelmet fordítania, mert az Azure által használt a hozzáférési kódot fájlnév.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Módosítsa a */etc/crypttab*található Crypt-konfigurációt. A listának így kell kinéznie:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Adja hozzá a parancsfájl végrehajtható engedélyek:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. */Etc/initramfs-tools/modules* szerkesztése sorok hozzáfűzésével:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. `update-initramfs -u -k all` futtatásával frissítse a initramfs a `keyscript` életbe léptetéséhez.

7. Most már Ön is a virtuális gép megszüntetéséhez.

   ![Ubuntu 16.04-telepítő – frissítés-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Folytassa a következő lépéssel, és töltse fel a VHD-t az Azure-bA.

### <a name="opensuse-132"></a>openSUSE, 13.2
Konfigurálja a titkosítást a terjesztési telepítése során, tegye a következőket:
1. A lemezek particionálásakor válassza a **kötet csoport titkosítása**lehetőséget, majd adja meg a jelszót. Ez az a jelszó a key vault feltöltött lesz.

   ![openSUSE, 13.2 beállítása – Kötetcsoport titkosítása](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Indítsa el a virtuális gép az Ön jelszavát.

   ![openSUSE, 13.2 beállítása – adja meg a jelszót a rendszerindító](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Készítse elő a virtuális GÉPET az Azure-ba való feltöltéshez a [SLES-vagy openSUSE-alapú virtuális gép Azure-ban történő előkészítésének](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131)utasításait követve. Nem futnak (a virtuális gép megszüntetése) az utolsó lépés még.

A titkosítás működéséhez az Azure-ral konfigurálásához kövesse az alábbi lépéseket:
1. Szerkessze a /etc/dracut.conf, és adja hozzá a következő sort:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Tegye megjegyzésbe ezen a fájl /usr/lib/dracut/modules.d/90crypt/module-setup.sh végén:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Fűzze hozzá a következő sort a fájl /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh elejére:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   És az összes előfordulását módosítsa:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   erre:
   ```bash
    if [ 1 ]; then
   ```
4. Szerkesztés /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh, és fűzze hozzá a "# nyitott LUKS eszköz":

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. `/usr/sbin/dracut -f -v` futtatásával frissítse a initrd.

6. Most a virtuális gép megszüntetése, és töltse fel a VHD-t az Azure-bA.

### <a name="centos-7-and-rhel-81"></a>CentOS 7 és RHEL 8,1

Konfigurálja a titkosítást a terjesztési telepítése során, tegye a következőket:
1. Válassza **a saját adatai titkosítása a** lemezek particionálásakor lehetőséget.

   ![CentOS 7 beállítása – telepítési cél](./media/disk-encryption/centos-encrypt-fig1.png)

2. Győződjön meg arról, hogy a gyökérszintű partícióhoz a **titkosítás** van kiválasztva.

   ![CentOS 7 beállítása – válassza ki a gyökérpartíciót titkosítása](./media/disk-encryption/centos-encrypt-fig2.png)

3. Adjon meg egy jelszót. Ez az a feltöltött lesz a kulcstartó hozzáférési kódot.

   ![CentOS 7 beállítása – adja meg a jelszót](./media/disk-encryption/centos-encrypt-fig3.png)

4. Ha a virtuális gép, és a egy hozzáférési kódot a rendszer kéri, használja a 3. lépésében megadott jelszót.

   ![CentOS 7 beállítása – adja meg a jelszót a rendszerindítási](./media/disk-encryption/centos-encrypt-fig4.png)

5. Készítse elő a virtuális gépet az Azure-ba való feltöltéshez az [Azure-hoz készült CentOS-alapú virtuális gép előkészítése](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70)című témakör "CentOS 7.0 +" utasításával. Nem futnak (a virtuális gép megszüntetése) az utolsó lépés még.

6. Most a virtuális gép megszüntetése, és töltse fel a VHD-t az Azure-bA.

A titkosítás működéséhez az Azure-ral konfigurálásához kövesse az alábbi lépéseket:

1. Szerkessze a /etc/dracut.conf, és adja hozzá a következő sort:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Tegye megjegyzésbe ezen a fájl /usr/lib/dracut/modules.d/90crypt/module-setup.sh végén:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Fűzze hozzá a következő sort a fájl /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh elejére:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   És az összes előfordulását módosítsa:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   erre:
   ```bash
    if [ 1 ]; then
   ```
4. /Usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh szerkesztése, és fűzze hozzá a "# nyitott LUKS eszköz" után a következőket:
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Futtassa a "/ usr/sbin/dracut - f - v" az initrd frissíteni.

    ![CentOS 7 telepítő – /usr/sbin/dracut -f - v rendszert futtató](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Titkosított virtuális merevlemez feltöltése Azure Storage-fiókba
A DM-Crypt titkosítás engedélyezése után a helyi titkosított VHD-t fel kell tölteni a Storage-fiókjába.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Töltse fel az előre titkosított virtuális gép titkos kulcsát a kulcstartóba
Titkosításához az Azure AD-alkalmazás (előző kiadás) használatával, a a korábban beszerzett-lemeztitkosítás titkos kulcsot a kulcstartóban titkos blobnévvel legyen feltöltve. A key vaultban kell rendelkeznie a lemeztitkosítás és az Azure AD-ügyfél engedélyezve.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>A lemez titkosítási titka nem titkosított KEK-sel
A titkos kulcs a Key vaultban történő beállításához használja a [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A jelszó Base64 karakterláncként van kódolva, majd feltöltve a kulcstartóba. Emellett ellenőrizze, hogy a titkos kulcsot a key vaultban történő létrehozásakor a következő címkék vannak-e beállítva.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


A következő lépésben az [operációsrendszer-lemez csatlakoztatásához a KEK használata nélkül](#without-using-a-kek)`$secretUrl`.

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>A lemez titkosítási titka egy KEK-sel titkosítva
Mielőtt feltölti a titkos kulcsot a key vaulthoz, igény szerint titkosíthatók, kulcstitkosítási kulcs használatával. A wrap [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) használatával először Titkosítsa a titkos kulcsot a kulcs titkosítási kulcsával. Ennek a körbefuttatási műveletnek a kimenete Base64 URL-kódolású karakterlánc, amelyet aztán titkosként tölthet fel a [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) parancsmag használatával.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

A következő lépésben használja a `$KeyEncryptionKey` és `$secretUrl` elemet az [operációsrendszer-lemez a KEK használatával történő csatlakoztatásához](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Titkos URL-cím megadása operációsrendszer-lemez csatlakoztatásakor

###  <a name="without-using-a-kek"></a>KEK használata nélkül
Az operációsrendszer-lemez csatlakoztatása közben `$secretUrl`kell átadnia. Az URL-címet az "a-lemeztitkosítás titkos kulcs egy KEK nem titkosított" szakaszban jött létre.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>KEK használata
Az operációsrendszer-lemez csatlakoztatásakor adja át `$KeyEncryptionKey` és `$secretUrl`. Az URL-címet az "Lemeztitkosítás titkos kódja egy KEK titkosított" szakaszban jött létre.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
