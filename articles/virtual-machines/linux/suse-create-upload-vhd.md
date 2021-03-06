---
title: SUSE Linux rendszerű virtuális merevlemez létrehozása és feltöltése az Azure-ban
description: Megtudhatja, hogyan hozhat létre és tölthet fel egy SUSE Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: mimckitt
ms.openlocfilehash: d64fc55159ddc3ce88397879958a63bf30808ad9
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251570"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>SLES-vagy openSUSE-beli virtuális gép előkészítése az Azure-hoz


Ez a cikk azt feltételezi, hogy már telepített egy SUSE vagy openSUSE Linux operációs rendszert egy virtuális merevlemezre. Több eszköz létezik a. vhd fájlok létrehozásához, például egy virtualizációs megoldáshoz, például a Hyper-V-hez. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és a virtuális gép konfigurálása](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="sles--opensuse-installation-notes"></a>SLES/openSUSE telepítési megjegyzések
* A Linux for Azure előkészítésével kapcsolatos további tippeket a [Linux általános telepítési megjegyzései](create-upload-generic.md#general-linux-installation-notes) című témakörben talál.
* A VHDX formátuma nem támogatott az Azure-ban, csak a **rögzített VHD**.  A lemezt VHD formátumba konvertálhatja a Hyper-V kezelőjével vagy a convert-VHD parancsmag használatával.
* A Linux rendszer telepítésekor azt javasoljuk, hogy az LVM helyett standard partíciót használjon (ez általában számos telepítés esetében). Ezzel elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen akkor, ha egy operációsrendszer-lemezt egy másik virtuális géphez kell csatolni a hibaelhárításhoz. Az [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy a [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezeken is használható, ha az előnyben részesített.
* Ne állítson be swap-partíciót az operációsrendszer-lemezen. A Linux-ügynök úgy konfigurálható, hogy lapozófájlt hozzon létre az ideiglenes erőforrás lemezén.  Erről további információt az alábbi lépésekben találhat.
* Az Azure-ban az összes virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor gondoskodnia kell arról, hogy a nyers lemez mérete a konverzió előtt egy 1MB többszöröse legyen. További információért lásd a [Linux telepítési megjegyzéseit](create-upload-generic.md#general-linux-installation-notes) .

## <a name="use-suse-studio"></a>A SUSE Studio használata
A [SUSE Studio](http://www.susestudio.com) egyszerűen létrehozhatja és kezelheti az Azure-hoz és a Hyper-V-hez készült SLES és openSUSE-lemezképeket. Ez az ajánlott módszer a saját SLES és az openSUSE-lemezképek testre szabására.

A saját virtuális merevlemez kiépítésének alternatívájaként a SUSE a BYOS (saját előfizetés) lemezképeket is közzéteszi a SLES címen a [vmdepottal](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf)címen.

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>SUSE Linux Enterprise Server 11 SP4 előkészítése
1. A Hyper-V kezelőjének középső ablaktábláján válassza ki a virtuális gépet.
2. Kattintson a **Kapcsolódás** gombra a virtuális gép ablakának megnyitásához.
3. Regisztrálja a SUSE Linux Enterprise rendszerét, hogy lehetővé tegye a frissítések letöltését és a csomagok telepítését.
4. A System frissítése a legújabb javításokkal:
   
        # sudo zypper update
5. Telepítse az Azure Linux-ügynököt a SLES adattárból:
   
        # sudo zypper install python-azure-agent
6. Ellenőrizze, hogy a waagent "on" értékre van-e állítva a Chkconfig, és ha nem, engedélyezze az automatikus indításhoz:
   
        # sudo chkconfig waagent on
7. Ellenőrizze, hogy fut-e a waagent szolgáltatás, és ha nem, indítsa el a következőket: 
   
        # sudo service waagent start
8. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez nyissa meg a "/boot/grub/menu.lst" szöveget egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel a következő paramétereket tartalmazza:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Ezzel biztosítható, hogy az összes konzol üzenetei az első soros porton legyenek elküldve, amely a hibakeresési problémákkal segíti az Azure-támogatást.
9. Győződjön meg arról, hogy a/boot/grub/menu.lst és az/etc/fstab is hivatkozik a lemezre a lemez AZONOSÍTÓjának (by-id) helyett az UUID (by-UUID) használatával. 
   
    Lemez UUID beolvasása
   
        # ls /dev/disk/by-uuid/
   
    Ha/dev/disk/by-id/használ, frissítse a/boot/grub/menu.lst és az/etc/fstab-et a megfelelő by-UUID értékkel
   
    Módosítás előtt
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Módosítás után
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Módosítsa a udev-szabályokat úgy, hogy ne generáljon statikus szabályokat az Ethernet-adapter (ek) számára. Ezek a szabályok problémákat okozhatnak a Microsoft Azure vagy Hyper-V-ben lévő virtuális gépek klónozásakor:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Javasoljuk, hogy szerkessze a "/etc/sysconfig/network/DHCP" fájlt, és módosítsa a `DHCLIENT_SET_HOSTNAME` paramétert a következőre:
    
     DHCLIENT_SET_HOSTNAME="no"
12. A "/etc/sudoers" elemnél írja ki vagy távolítsa el a következő sorokat, ha vannak ilyenek:
    
    ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```
13. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik.  Ez általában az alapértelmezett.
14. Ne hozzon létre lapozófájlt az operációsrendszer-lemezen.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatja a lapozófájlt a virtuális géphez az Azure-ban való üzembe helyezést követően csatlakozó helyi erőforrás lemez használatával. Vegye figyelembe, hogy a helyi erőforrás lemeze egy *ideiglenes* lemez, és a virtuális gép kiépítésekor kiürítésre kerülhet. Az Azure Linux-ügynök telepítése után (lásd az előző lépést) a/etc/waagent.conf megfelelően módosítsa a következő paramétereket:
    
     ResourceDisk. Format = y ResourceDisk. filesystem = ext4 ResourceDisk. csatlakoztatási pont =/mnt/Resource ResourceDisk. EnableSwap = y ResourceDisk. SwapSizeMB = 2048 # # Megjegyzés: állítsa be a következőt, amire szüksége van.
15. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Kattintson a **művelet – > leállítás** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

---
## <a name="prepare-opensuse-131"></a>Az openSUSE 13.1 + előkészítése
1. A Hyper-V kezelőjének középső ablaktábláján válassza ki a virtuális gépet.
2. Kattintson a **Kapcsolódás** gombra a virtuális gép ablakának megnyitásához.
3. A rendszerhéjon futtassa a következő parancsot: "`zypper lr`". Ha a parancs a következőhöz hasonló kimenetet ad vissza, akkor a Tárházak a várt módon lesznek konfigurálva – nincs szükség módosításra (vegye figyelembe, hogy a verziószámok száma változhat):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Ha a parancs visszaadja a "nincsenek adattárak definiálva..." értéket. Ezután az alábbi parancsokkal adhatja hozzá ezeket a repókat:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Ezután ellenőrizheti, hogy a Tárházak hozzá lettek-e adva a (z) "`zypper lr`" parancs újbóli futtatásával. Ha az egyik releváns frissítési tárház nincs engedélyezve, engedélyezze a következő paranccsal:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. A rendszermag frissítése a legújabb elérhető verzióra:
   
        # sudo zypper up kernel-default
   
    Vagy frissítse a rendszeren a legújabb javításokat:
   
        # sudo zypper update
5. Telepítse az Azure Linux-ügynököt.
   
        # sudo zypper install WALinuxAgent
6. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez nyissa meg a "/boot/grub/menu.lst" kifejezést egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel a következő paramétereket tartalmazza:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Ezzel biztosítható, hogy az összes konzol üzenetei az első soros porton legyenek elküldve, amely a hibakeresési problémákkal segíti az Azure-támogatást. Továbbá távolítsa el a következő paramétereket a kernel rendszerindítási sorából, ha vannak ilyenek:
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. Javasoljuk, hogy szerkessze a "/etc/sysconfig/network/DHCP" fájlt, és módosítsa a `DHCLIENT_SET_HOSTNAME` paramétert a következőre:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Fontos:** A "/etc/sudoers" elemnél írja ki vagy távolítsa el a következő sorokat, ha vannak ilyenek:
     
     ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```

9. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik.  Ez általában az alapértelmezett.
10. Ne hozzon létre lapozófájlt az operációsrendszer-lemezen.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatja a lapozófájlt a virtuális géphez az Azure-ban való üzembe helyezést követően csatlakozó helyi erőforrás lemez használatával. Vegye figyelembe, hogy a helyi erőforrás lemeze egy *ideiglenes* lemez, és a virtuális gép kiépítésekor kiürítésre kerülhet. Az Azure Linux-ügynök telepítése után (lásd az előző lépést) a/etc/waagent.conf megfelelően módosítsa a következő paramétereket:
    
     ResourceDisk. Format = y ResourceDisk. filesystem = ext4 ResourceDisk. csatlakoztatási pont =/mnt/Resource ResourceDisk. EnableSwap = y ResourceDisk. SwapSizeMB = 2048 # # Megjegyzés: állítsa be a következőt, amire szüksége van.
11. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Ellenőrizze, hogy az Azure Linux-ügynök fut-e indításkor:
    
        # sudo systemctl enable waagent.service
13. Kattintson a **művelet – > leállítás** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="next-steps"></a>Következő lépések
Most már készen áll a SUSE Linux rendszerű virtuális merevlemez használatára, hogy új virtuális gépeket hozzon létre az Azure-ban. Ha első alkalommal tölti fel a. vhd-fájlt az Azure-ba, tekintse meg a Linux rendszerű [virtuális gép létrehozása egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd)című témakört.
