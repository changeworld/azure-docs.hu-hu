---
title: Az LVM konfigurálása Linux rendszerű virtuális gépen
description: Ismerje meg, hogyan konfigurálhatja az LVMt Linux rendszeren az Azure-ban.
author: mimckitt
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/27/2018
ms.author: mimckitt
ms.subservice: disks
ms.openlocfilehash: 781ff1e6bda655ebd60d86e19375dcb6da051039
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969728"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Az LVM konfigurálása Linux rendszerű virtuális gépen az Azure-ban
Ebből a dokumentumból megtudhatja, hogyan konfigurálhatja a Logical Volume Managert (LVM) az Azure-beli virtuális gépen. Az LVM az operációsrendszer-lemezen vagy az Azure-beli virtuális gépeken lévő adatlemezeken is használható, azonban a legtöbb Felhőbeli rendszerkép alapértelmezés szerint nem lesz beállítva az operációsrendszer-lemezen. Az alábbi lépések az LVM konfigurálására koncentrálnak az adatlemezek esetében.

## <a name="linear-vs-striped-logical-volumes"></a>Lineáris és csíkozott logikai kötetek
Az LVM használatával számos fizikai lemez egyesíthető egyetlen tárolási kötetre. Alapértelmezés szerint az LVM általában lineáris logikai köteteket hoz létre, ami azt jelenti, hogy a fizikai tárterület össze van fűzve. Ebben az esetben a rendszer általában csak egyetlen lemezre küldi az olvasási/írási műveleteket. Ezzel szemben olyan csíkozott logikai köteteket is létrehozhat, amelyekben az olvasási és írási műveletek a mennyiségi csoportban található több lemezre vannak elosztva (a RAID0-hez hasonlóan). A teljesítmény szempontjából valószínű, hogy a logikai kötetek csíkozását szeretné használni, így az olvasás és az írás az összes csatlakoztatott adatlemezt felhasználja.

Ebből a dokumentumból megtudhatja, hogyan egyesítheti több adatlemezt egyetlen kötet csoportba, majd hogyan hozhat létre csíkozott logikai kötetet. Az alábbi lépések általánosítva működnek a legtöbb disztribúcióval. A legtöbb esetben az LVM Azure-beli kezeléséhez szükséges segédprogramok és munkafolyamatok nem különböznek alapvetően a többi környezettől. A szokásos módon tekintse meg a linuxos gyártótól származó dokumentációt és ajánlott eljárásokat az LVM az adott disztribúcióval való használatához.

## <a name="attaching-data-disks"></a>Adatlemezek csatolása
Az egyik általában két vagy több üres adatlemezt szeretne kezdeni az LVM használatakor. Az i/o-igények alapján a standard szintű tárolóban tárolt lemezeket csatlakoztathatja, és akár 500 IO/PS-t, akár lemezes, akár 5000 IO/PS-alapú Premium Storage-t is használhat. Ez a cikk nem részletezi az adatlemezek Linux rendszerű virtuális géphez való kiépítésével és csatlakoztatásával kapcsolatos információkat. Tekintse meg a Microsoft Azure a [lemez csatlakoztatása](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) című cikket, amely részletesen ismerteti, hogyan lehet üres adatlemezt csatlakoztatni egy linuxos virtuális géphez az Azure-ban.

## <a name="install-the-lvm-utilities"></a>Az LVM segédprogramok telepítése
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS & Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 és openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    A SLES11-on a `/etc/sysconfig/lvm` szerkesztése és a `LVM_ACTIVATED_ON_DISCOVERED` beállítása az "engedélyezés" beállításra is vonatkozik:

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>LVM konfigurálása
Ebben az útmutatóban feltételezzük, hogy három adatlemezt csatoltunk, amelyeket `/dev/sdc`, `/dev/sdd` és `/dev/sde`re fogunk hivatkozni. Előfordulhat, hogy ezek az elérési utak nem egyeznek a virtuális gép lemezének elérési útjaival. A "`sudo fdisk -l`" vagy hasonló parancs futtatásával listázhatja az elérhető lemezeket.

1. A fizikai kötetek előkészítése:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Hozzon létre egy kötet csoportot. Ebben a példában a `data-vg01`kötetet hívjuk:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Hozza létre a logikai kötet (eke) t. Az alábbi parancs egy `data-lv01` nevű logikai kötetet hoz létre, amely a teljes kötet csoportra terjed ki, azonban vegye figyelembe, hogy a kötet csoportban több logikai kötet is létrehozható.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. A logikai kötet formázása

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > A SLES11 használata `-t ext3` az ext4 helyett. A SLES11 csak olvasási hozzáférést támogat az ext4 fájlrendszerekhez.

## <a name="add-the-new-file-system-to-etcfstab"></a>Az új fájlrendszer hozzáadása az/etc/fstab modulhoz
> [!IMPORTANT]
> A `/etc/fstab` fájl nem megfelelő szerkesztése nem indítható rendszert eredményezhet. Ha nem biztos, tekintse meg a terjesztés dokumentációját a fájl megfelelő szerkesztésével kapcsolatos információkért. Azt is javasoljuk, hogy a Szerkesztés előtt hozza létre a `/etc/fstab` fájl biztonsági másolatát.

1. Hozza létre az új fájlrendszer kívánt csatlakoztatási pontját, például:

    ```bash  
    sudo mkdir /data
    ```

2. A logikai kötet elérési útjának megkeresése

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Nyisson meg `/etc/fstab` egy szövegszerkesztőben, és adjon hozzá egy bejegyzést az új fájlrendszerhez, például:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Ezután mentse és zárjunk be `/etc/fstab`.

4. Ellenőrizze, hogy helyesek-e a `/etc/fstab` bejegyzés:

    ```bash    
    sudo mount -a
    ```

    Ha ez a parancs hibaüzenetet eredményez, ellenőrizze a szintaxist a `/etc/fstab` fájlban.
   
    Ezután futtassa a `mount` parancsot a fájlrendszer csatlakoztatásának biztosításához:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. Választható Failsafe rendszerindítási paraméterek `/etc/fstab`
   
    Számos eloszlás magában foglalja az `/etc/fstab`-fájlhoz adható `nobootwait` vagy `nofail` csatlakoztatási paramétereket. Ezek a paraméterek lehetővé teszik a meghibásodást egy adott fájlrendszer csatlakoztatásakor, és lehetővé teszik a Linux rendszernek a rendszerindítás folytatását akkor is, ha nem tudja megfelelően csatlakoztatni a RAID fájlrendszert. A paraméterekkel kapcsolatos további információkért tekintse meg a terjesztés dokumentációját.
   
    Példa (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>TRIM/LEKÉPEZÉSÉNEK megszüntetése-támogatás
Egyes linuxos kernelek támogatják a TRIM/LEKÉPEZÉSÉNEK megszüntetése műveleteket a lemezen lévő nem használt blokkok elvetéséhez. Ezek a műveletek elsődlegesen a standard szintű tárolásban hasznosak, hogy tájékoztassák az Azure-t arról, hogy a törölt lapok már nem érvényesek, és el lehet őket dobni. Ha nagyméretű fájlokat hoz létre, és törli őket, a lapok elvetése is megtakaríthatja a költségeket.

A Linux rendszerű virtuális gépen kétféleképpen engedélyezhető a TRIM-támogatás. A szokásos módon tekintse meg az ajánlott módszert az eloszlásban:

- Használja a `/etc/fstab``discard` csatlakoztatási lehetőségét, például:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- Bizonyos esetekben a `discard`i beállítás teljesítménybeli következményekkel járhat. Azt is megteheti, hogy manuálisan futtatja a `fstrim` parancsot a parancssorból, vagy hozzáadja azt a crontabhoz, hogy rendszeresen fusson:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL, CentOS & Oracle Linux**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
