---
title: Linux hibaelhárítási virtuális gép használata az Azure CLI-vel | Microsoft Docs
description: A Linux rendszerű virtuális gépekkel kapcsolatos hibák elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez az Azure CLI használatával történő csatlakoztatásával
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 1b91a39e1297d8952da67a4f8d3b8568cefe04ce
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620565"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Linux rendszerű virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez az Azure CLI-vel való csatlakoztatásával
Ha a linuxos virtuális gép (VM) rendszerindítási vagy lemezhiba miatti hibát észlel, lehetséges, hogy a virtuális merevlemezen hibaelhárítási lépéseket kell végrehajtania. Egy gyakori példa a `/etc/fstab` érvénytelen bejegyzése lenne, amely megakadályozza, hogy a virtuális gép sikeresen elindítható legyen. Ez a cikk részletesen ismerteti, hogyan lehet az Azure CLI használatával összekapcsolni a virtuális merevlemezt egy másik linuxos virtuális géppel a hibák kijavítása érdekében, majd újból létre kell hoznia az eredeti virtuális gépet. 

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Állítsa le az érintett virtuális gépet.
1. Készítsen pillanatképet a virtuális gép operációsrendszer-lemezéről.
1. Hozzon létre egy lemezt az operációsrendszer-lemez pillanatképből.
1. Az új operációsrendszer-lemez csatlakoztatása és csatlakoztatása egy másik linuxos virtuális géphez hibaelhárítási célból.
1. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Módosítsa a fájlokat, vagy futtasson eszközöket az új operációsrendszer-lemezen lévő problémák elhárításához.
1. Válassza le és válassza le az új operációsrendszer-lemezt a hibaelhárítási virtuális gépről.
1. Módosítsa az érintett virtuális gép operációsrendszer-lemezét.

A hibaelhárítási lépések elvégzéséhez a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t kell telepítenie, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

> [!Important]
> A cikkben szereplő parancsfájlok csak a [felügyelt lemezt](../linux/managed-disks-overview.md)használó virtuális gépekre vonatkoznak. 

Az alábbi példákban cserélje le a paraméterek nevét a saját értékeire, például `myResourceGroup` és `myVM`.

## <a name="determine-boot-issues"></a>Rendszerindítási problémák meghatározása
Ellenőrizze a soros kimenetet annak meghatározásához, hogy a virtuális gép miért nem tud megfelelően elindulni. Gyakori példa a `/etc/fstab`érvénytelen bejegyzése, vagy a mögöttes virtuális merevlemez törölve vagy áthelyezve.

Szerezze be a rendszerindítási naplókat az [az VM boot-Diagnostics Get-boot-log](/cli/azure/vm/boot-diagnostics)paranccsal. A következő példa lekéri a `myVM` nevű virtuális gépről a `myResourceGroup`nevű erőforráscsoport soros kimenetét:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Tekintse át a soros kimenetet annak meghatározásához, hogy miért nem sikerült elindítani a virtuális gépet. Ha a soros kimenet nem ad meg semmilyen jelzést, előfordulhat, hogy a naplófájlokat át kell tekintenie `/var/log` ha a virtuális merevlemez csatlakoztatva van egy hibaelhárítási virtuális géphez.

## <a name="stop-the-vm"></a>A virtuális gép leállítása

A következő példa leállítja a `myVM` nevű virtuális gépet a `myResourceGroup`nevű erőforráscsoporthoz:

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Pillanatkép készítése az érintett virtuális gép operációsrendszer-lemezéről

A pillanatkép egy virtuális merevlemez teljes, írásvédett másolata. Nem csatlakoztatható virtuális géphez. A következő lépésben létrehozunk egy lemezt ebből a pillanatképből. Az alábbi példa egy `mySnapshot` nevű pillanatképet hoz létre a "myVM" nevű virtuális gép operációsrendszer-lemezéről. 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Lemez létrehozása a pillanatképből

Ez a szkript létrehoz egy `myOSDisk` nevű felügyelt lemezt a `mySnapshot`nevű pillanatképből.  

```azurecli
#Provide the name of your resource group
$resourceGroup=myResourceGroup

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot=mySnapshot

#Provide the name of the Managed Disk
$osDisk=myNewOSDisk

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType=Premium_LRS

#Provide the OS type
$osType=linux

#Provide the name of the virtual machine
$virtualMachine=myVM

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query [id] -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Ha az erőforráscsoport és a forrás pillanatkép nem ugyanabban a régióban található, akkor a `az disk create`futtatásakor az "erőforrás nem található" hibaüzenet jelenik meg. Ebben az esetben meg kell adnia `--location <region>`t, hogy a lemezt a forrás pillanatképével megegyező régióba hozza létre.

Most már rendelkezik az eredeti operációsrendszer-lemez másolatával. Az új lemezt hibaelhárítási célból csatlakoztathatja egy másik Windows rendszerű virtuális géphez.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Az új virtuális merevlemez csatolása egy másik virtuális GÉPHEZ
A következő néhány lépésben hibaelhárítási célból egy másik virtuális gépet használ. A lemez tartalmának tallózásához és szerkesztéséhez csatolja a lemezt ehhez a hibaelhárítási virtuális géphez. Ez a folyamat lehetővé teszi a konfigurációs hibák kijavítását, vagy a további alkalmazás-vagy rendszernapló-fájlok áttekintését.

Ez a szkript csatolja a `myNewOSDisk` lemezt a virtuális géphez `MyTroubleshootVM`:

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $diskId --resource-group MyResourceGroup --size-gb 128 --sku Standard_LRS --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>A csatlakoztatott adatlemez csatlakoztatása

> [!NOTE]
> Az alábbi példák egy Ubuntu virtuális gépen szükséges lépéseket részletezik. Ha más Linux-disztribúciót használ, mint például a Red Hat Enterprise Linux vagy a SUSE, a naplófájl helyei és a `mount` parancsok némileg eltérőek lehetnek. Tekintse át az adott disztribúció dokumentációját a parancsok megfelelő módosításaihoz.

1. A megfelelő hitelesítő adatok használatával SSH-t a hibaelhárítási virtuális géphez. Ha ez a lemez a hibaelhárítási virtuális géphez csatolt első adatlemez, a lemez valószínűleg `/dev/sdc`hoz csatlakozik. `dmesg` használata a csatlakoztatott lemezek megtekintéséhez:

    ```bash
    dmesg | grep SCSI
    ```

    A kimenet a következő példához hasonló:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    Az előző példában az operációsrendszer-lemez `/dev/sda`, és az egyes virtuális gépekhez megadott ideiglenes lemez `/dev/sdb`. Ha több adatlemezzel is rendelkezett, `/dev/sdd`, `/dev/sde`és így tovább.

2. Hozzon létre egy könyvtárat a meglévő virtuális merevlemez csatlakoztatásához. A következő példa egy `troubleshootingdisk`nevű könyvtárat hoz létre:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Ha több partícióval rendelkezik a meglévő virtuális merevlemezen, csatlakoztassa a szükséges partíciót. Az alábbi példa az első elsődleges partíciót csatlakoztatja `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Ajánlott eljárás az adatlemezek csatlakoztatása az Azure-beli virtuális gépekhez a virtuális merevlemez univerzálisan egyedi azonosítója (UUID) használatával. Ebben a rövid hibaelhárítási forgatókönyvben nem szükséges a virtuális merevlemezt az UUID használatával csatlakoztatni. A normál használat során azonban a `/etc/fstab` szerkesztésével a virtuális merevlemezeket az UUID helyett az eszköznév használatával csatlakoztathatja, így a virtuális gép nem indítható el.


## <a name="fix-issues-on-the-new-os-disk"></a>Az új operációsrendszer-lemez hibáinak elhárítása
A meglévő virtuális merevlemez csatlakoztatása után a szükséges karbantartási és hibaelhárítási lépéseket is elvégezheti. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.


## <a name="unmount-and-detach-the-new-os-disk"></a>Az új operációsrendszer-lemez leválasztása és leválasztása
A hibák megoldása után leválaszthatja a meglévő virtuális merevlemezt a hibaelhárító virtuális gépről. A virtuális merevlemezt nem használhatja más virtuális géppel, amíg a virtuális merevlemezt a hibaelhárítási virtuális géphez csatlakoztató címbérlet megjelent.

1. Az SSH-munkamenetből a hibaelhárító virtuális GÉPHEZ válassza le a meglévő virtuális merevlemezt. Először a csatlakoztatási ponthoz tartozó szülő könyvtáron változtassa meg a következőt:

    ```bash
    cd /
    ```

    Most válassza le a meglévő virtuális merevlemezt. Az alábbi példa leválasztja az eszközt a `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Most válassza le a virtuális merevlemezt a virtuális gépről. Lépjen ki az SSH-munkamenetből a hibaelhárítási virtuális gépre:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Az érintett virtuális gép operációsrendszer-lemezének módosítása

Az operációs rendszer lemezeit az Azure CLI használatával cserélheti le. Nem kell törölnie és újból létrehoznia a virtuális gépet.

Ez a példa leállítja a `myVM` nevű virtuális gépet, és hozzárendeli a `myNewOSDisk` nevű lemezt az új operációsrendszer-lemezként.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>További lépések
Ha problémába ütközik a virtuális géphez való csatlakozással kapcsolatban, olvassa el az [SSH-kapcsolatok Azure-beli virtuális géphez való hibaelhárítását](troubleshoot-ssh-connection.md)ismertető témakört. A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákért lásd: az [alkalmazások kapcsolódási problémáinak elhárítása Linux rendszerű virtuális gépen](troubleshoot-app-connection.md).

