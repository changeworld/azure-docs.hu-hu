---
title: Adatok átvitele az Azure Data Box Gatewayjel | Microsoft Docs
description: Megismerheti, hogyan adhat hozzá megosztásokat a Data Box Gateway eszközön, és hogyan csatlakozhat azokhoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: alkohli
ms.openlocfilehash: 32466cc0a1ab9b86fc2fb8eb791c232ae13f1c01
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79213572"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Oktatóanyag: adatok átvitele Azure Data Box Gateway


## <a name="introduction"></a>Introduction (Bevezetés)

Ez a cikk azt ismerteti, hogyan lehet hozzáadni és csatlakozni a Data Box Gateway-megosztásokhoz. A megosztások hozzáadása után Data Box Gateway eszköz adatátvitelt hajthat végre az Azure-ba.

A folyamat elvégzése körülbelül 10 percet vesz igénybe.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megosztásokat ad a Data Box Gatewayhez, ellenőrizze az alábbiakat:

- Kiépített egy virtuális eszközt, és azt részletesen csatlakoztatta a [Hyper-V Data Box Gateway](data-box-gateway-deploy-provision-hyperv.md) üzembe helyezése vagy [a VMware-Data Box Gateway kiépítése](data-box-gateway-deploy-provision-vmware.md)során.

- Aktiválta a [Azure Data Box Gateway csatlakoztatása és aktiválása](data-box-gateway-deploy-connect-setup-activate.md)című témakörben leírt virtuális eszközt.

- Az eszköz készen áll a megosztások létrehozására és az adatátvitelre.

## <a name="add-a-share"></a>Megosztás hozzáadása

A megosztás létrehozásához hajtsa végre a következő eljárást:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Data Box Gateway erőforrást, majd kattintson az **Áttekintés**gombra. Az eszköznek online állapotban kell lennie. Válassza a **+ megosztás hozzáadása** lehetőséget az eszköz parancssáv-sávján.
   
   ![Megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. A **megosztás hozzáadása**területen hajtsa végre a következő eljárást:

    1. Adjon egy egyedi nevet a megosztásnak. A megosztások nevei csak kisbetűket, számokat és kötőjeleket tartalmazhatnak. A megosztási névnek 3 – 63 karakter hosszúnak kell lennie, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.
    
    2. Válassza ki a megosztás **típusát**. A típus SMB vagy NFS lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek.

    3. Adja meg azt a Storage-fiókot, amelyben a megosztás található. Ha egy tároló még nem létezik, a rendszer létrehozza a Storage-fiókban az újonnan létrehozott megosztási névvel. Ha a tároló már létezik, a rendszer a tárolót használja.
       > [!IMPORTANT]
       > Győződjön meg arról, hogy a használt Azure Storage-fiók nem rendelkezik módosíthatatlansági-házirendekkel, ha Azure Stack peremhálózati vagy Data Box Gateway eszközzel használja. További információ: [módosíthatatlansági-szabályzatok beállítása és kezelése a blob Storage-hoz](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).
    
    4. Válassza ki a **tárolási szolgáltatást** a blokkblobok, lapblobok vagy fájlok közül. A kiválasztott szolgáltatástípustól függ, hogy az Azure milyen formátumban tárolja az adatokat. Ebben az esetben például azt szeretnénk, hogy az adatok blokkblobokban legyenek tárolva az Azure-ban, ezért a Blokkblob lehetőséget választjuk. Ha a Lapblob lehetőséget választja, biztosítania kell az adatok 512 bájtos igazítását. A VHDX például mindig 512 bájtos igazítású.
   
    5. A következő lépés attól függ, hogy SMB- vagy NFS-megosztást hozunk-e létre.
     
    - **SMB-megosztás** – **az összes jogosultság helyi felhasználó**területen válassza az **új létrehozása** vagy a **meglévő használata**lehetőséget. Ha új helyi felhasználót hoz létre, adjon meg egy **felhasználónevet** és egy **jelszót**, majd **erősítse meg a jelszót**. Ez a művelet hozzárendeli az engedélyeket a helyi felhasználóhoz. A megosztási szintű engedélyek módosítása jelenleg nem támogatott.
    
        ![SMB-megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Ha bejelöli a **csak olvasási műveletek engedélyezése** jelölőnégyzetet a megosztási adatokhoz, megadhat csak olvasási jogosultsággal rendelkező felhasználókat.
        
    - **NFS-megosztás** – adja meg azon engedélyezett ügyfelek IP-címeit, akik hozzáférhetnek a megosztáshoz.

        ![NFS-megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. A megosztás létrehozásához válassza a **Létrehozás** lehetőséget.
    
    Értesítést kap arról, hogy a megosztás létrehozása folyamatban van. Miután létrehozta a megosztást a megadott beállításokkal, a **megosztások** csempe frissül, hogy tükrözze az új megosztást.
    
    ![Frissített megosztások csempe](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Csatlakozás a megosztáshoz

Most már csatlakozhat egy vagy több, az utolsó lépésben létrehozott megosztáshoz. Attól függően, hogy van-e SMB vagy NFS-megosztás, a lépések változhatnak.

### <a name="connect-to-an-smb-share"></a>Csatlakozás SMB-megosztáshoz

A Data Box Gatewayhoz csatlakoztatott Windows Server-ügyfélen a következő parancsok megadásával csatlakozhat egy SMB-megosztáshoz:


1. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Ha a rendszer kéri, adja meg a megosztás jelszavát. Az alábbiakban látható egy példa a parancs kimenetére.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. A billentyűzeten válassza a Windows + R lehetőséget. 
3. A **Futtatás** ablakban adja meg a `\\<device IP address>`, majd kattintson az **OK gombra**. Megnyílik a fájlkezelő. Ekkor meg kell tudnia tekinteni a mappákként létrehozott megosztásokat. A Fájlkezelőben kattintson duplán egy megosztásra (mappára) a tartalom megtekintéséhez.
 
    ![Csatlakozás SMB-megosztáshoz](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Az adatokat azok előállításakor a rendszer a megosztásba írja, majd az eszköz továbbítja az adatokat a felhőbe.

### <a name="connect-to-an-nfs-share"></a>Csatlakozás NFS-megosztáshoz

A Data Box Edge eszközhöz csatlakoztatott Linux-ügyfélen hajtsa végre a következő eljárást:

1. Győződjön meg arról, hogy az ügyfélen telepítve van a Nfsv4 névleképezője-ügyfél. Ha nincs, az NFS-ügyfél telepítéséhez használja az alábbi parancsot:

   `sudo apt-get install nfs-common`

    További információkat az [NFSv4-ügyfél telepítését ismertető](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) cikkben találhat.

2. Az NFS-ügyfél telepítését követően az alábbi parancs használatával csatlakoztathatja a Data Box Gateway eszközön létrehozott NFS-fájlmegosztást:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    A csatlakoztatás előtt ellenőrizze, hogy a könyvtárak, amelyek helyi számítógépen csatlakozási pontként fognak szolgálni, már létrejöttek-e, illetve hogy nem tartalmaznak-e fájlokat vagy almappákat.

    Az alábbi példa bemutatja az NFS-en keresztüli csatlakozást egy Gateway eszközön található megosztáshoz. A virtuális eszköz IP-címe `10.10.10.60`, a `mylinuxshare2` megosztás az ubuntuVM virtuális géphez csatlakozik, a csatlakozási pont pedig a `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> A következő kikötések alkalmazhatók erre a kiadásra:
> - Miután létrehozott egy fájlt a megosztásokban, a fájl átnevezése nem támogatott.
> - A fájlok megosztásból való törlése nem törli a bejegyzéseket a tárfiókból.
> - Ha az adatmásoláshoz `rsync` használ, a `rsync -a` lehetőség nem támogatott.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a Data Box Gatewayjel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz


A következő oktatóanyag azt mutatja be, hogyan felügyelhető a Data Box Gateway.

> [!div class="nextstepaction"]
> [A Data Box Gateway felügyelete a helyi webes felhasználói felületen](https://aka.ms/dbg-docs)


