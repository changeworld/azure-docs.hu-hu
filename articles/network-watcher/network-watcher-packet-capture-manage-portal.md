---
title: A csomagok rögzítésének kezelése – Azure Portal
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan kezelheti Network Watcher Packet Capture funkcióját a Azure Portal használatával.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: damendo
ms.openlocfilehash: 6fc4a25e39fb8f27151b2e3bec1959d74a619233
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840827"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>A csomagok rögzítésének kezelése az Azure Network Watcher a portál használatával

Network Watcher a csomagok rögzítése lehetővé teszi, hogy rögzítési munkameneteket hozzon létre a virtuális gépekre irányuló és onnan érkező forgalom nyomon követéséhez. A rögzítési munkamenethez szűrőket biztosít, hogy csak a kívánt forgalmat rögzítsen. A csomagok rögzítése segít az újraaktív és proaktív hálózati rendellenességek diagnosztizálásában. Más felhasználások közé tartozik a hálózati statisztikák összegyűjtése, a hálózati behatolásokkal kapcsolatos információk beszerzése, az ügyfél-kiszolgáló kommunikáció hibakeresése és sok más. A csomagok rögzítésének távoli elindítása lehetővé teszi, hogy a csomagok rögzítése manuálisan fusson a kívánt virtuális gépen, ami értékes időt takarít meg.

Ebben a cikkben megismerheti a csomagok rögzítésének elindítását, leállítását, letöltését és törlését. 

## <a name="before-you-begin"></a>Előzetes teendők

A csomagok rögzítéséhez a következő kapcsolat szükséges:
* Kimenő kapcsolat egy Storage-fiókkal a 443-as porton keresztül.
* Bejövő és kimenő kapcsolat a 169.254.169.254
* Bejövő és kimenő kapcsolat a 168.63.129.16

Ha hálózati biztonsági csoport van társítva a hálózati adapterhez vagy az alhálózathoz, amelyhez a hálózati adapter tartozik, győződjön meg arról, hogy léteznek olyan szabályok, amelyek engedélyezik a korábbi portokat. Hasonlóképpen, ha a felhasználó által megadott forgalmi útvonalakat a hálózathoz adja, előfordulhat, hogy a fenti IP-címek és portok kapcsolata megszakad. Ellenőrizze, hogy elérhetők-e. 

## <a name="start-a-packet-capture"></a>Csomag rögzítésének elindítása

1. A böngészőben navigáljon a [Azure Portal](https://portal.azure.com) és válassza a **minden szolgáltatás**lehetőséget, majd válassza a **Network Watcher** lehetőséget a **hálózatkezelés szakaszban**.
2. Válassza a **hálózati diagnosztikai eszközök**területen a **csomagok rögzítése** lehetőséget. A meglévő csomagok rögzítései az állapotuktól függetlenül jelennek meg.
3. Válassza a **Hozzáadás** lehetőséget a csomagok rögzítésének létrehozásához. A következő tulajdonságok értékeit adhatja meg:
   - **Előfizetés**: az a virtuális gép, amelyhez a csomagot létre kívánja hozni.
   - **Erőforráscsoport**: a virtuális gép erőforráscsoport.
   - **Cél virtuális gép**: az a virtuális gép, amelyhez létre kívánja hozni a csomag rögzítését.
   - **Csomag-rögzítés neve**: a csomag rögzítésének neve.
   - **Storage-fiók vagy-fájl**: válassza ki a **Storage-fiókot**, a **fájlt**vagy mindkettőt. Ha a **fájl**lehetőséget választja, a rögzítés a virtuális gépen belüli elérési útra íródik.
   - **Helyi fájl elérési útja**: annak a virtuális gépnek a helyi elérési útja, ahol a csomag rögzítése el lesz mentve (csak a *fájl* kiválasztásakor érvényes). Az elérési útnak érvényes elérési útnak kell lennie. Ha Linux rendszerű virtuális gépet használ, az elérési útnak */var/Captures*kell kezdődnie.
   - **Storage-fiókok**: válasszon ki egy meglévő Storage-fiókot, ha a *Storage-fiókot*választotta. Ez a beállítás csak akkor érhető el, ha a **tárolót**választotta.
   
     > [!NOTE]
     > A prémium szintű Storage-fiókok jelenleg nem támogatottak a csomagok rögzítésének tárolásához.

   - **Maximális bájt/csomag**: az egyes rögzített csomagokból származó bájtok száma. Ha üresen hagyja, a rendszer az összes bájtot rögzíti.
   - **Maximális bájt/munkamenet**: a rögzített bájtok teljes száma. Ha elérte az értéket, a csomagok rögzítése leáll.
   - Időkorlát **(másodperc)** : a csomag rögzítésének leállítása előtti időkorlát. Az alapértelmezett érték 18 000 másodperc.
   - Szűrés (nem kötelező). Válassza a **+ szűrő hozzáadása** lehetőséget
     - **Protokoll**: a csomag rögzítésére szolgáló protokoll. A rendelkezésre álló értékek: TCP, UDP és any.
     - **Helyi IP-cím**: azokat a csomagokat szűri, amelyekben a helyi IP-cím megfelel ennek az értéknek.
     - **Helyi port**: azokat a csomagokat szűri, amelyekben a helyi port megfelel ennek az értéknek.
     - **Távoli IP-cím**: azokat a csomagokat szűri, amelyekben a távoli IP-cím megfelel ennek az értéknek.
     - **Távoli port**: azokat a csomagokat szűri, amelyekben a távoli port megfelel ennek az értéknek.
    
     > [!NOTE]
     > A portok és IP-címek értéke lehet egyetlen érték, egy értéktartomány vagy egy tartomány, például a 80-1024, a porthoz. Tetszőleges számú szűrőt meghatározhat.

4. Kattintson az **OK** gombra.

Miután a csomag rögzítésére beállított időkorlát lejárt, a csomagok rögzítése leáll, és áttekinthető. Manuálisan is leállíthatja a csomagok rögzítési munkamenetét.

> [!NOTE]
> A portál automatikusan:
>  * Létrehoz egy hálózati figyelőt abban a régióban, ahol a kiválasztott virtuális gép szerepel a régióban, ha a régió még nem rendelkezik hálózati figyelővel.
>  * Hozzáadja a *AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) vagy [Windows rendszerű](../virtual-machines/windows/extensions-nwa.md) virtuálisgép-bővítményt a virtuális géphez, ha még nincs telepítve.

## <a name="delete-a-packet-capture"></a>Csomagok rögzítésének törlése

1. A csomagok rögzítése nézetben válassza a **...** lehetőséget a csomag rögzítésének jobb oldalán, vagy kattintson a jobb gombbal egy meglévő csomag-rögzítésre, és válassza a **Törlés**lehetőséget.
2. A rendszer megkéri, hogy erősítse meg, hogy törölni kívánja a csomagok rögzítését. Válassza az **Igen** lehetőséget.

> [!NOTE]
> A csomagok rögzítésének törlése nem törli a rögzítési fájlt a Storage-fiókban vagy a virtuális gépen.

## <a name="stop-a-packet-capture"></a>Csomagok rögzítésének leállítása

A csomagok rögzítése nézetben válassza a **...** lehetőséget a csomag rögzítésének jobb oldalán, vagy kattintson a jobb gombbal egy meglévő csomag-rögzítésre, és válassza a **Leállítás**lehetőséget.

## <a name="download-a-packet-capture"></a>Csomagok rögzítésének letöltése

A csomag-rögzítési munkamenet befejezése után a rendszer feltölti a rögzítési fájlt a blob Storage-ba vagy egy helyi fájlba a virtuális gépen. A csomag rögzítésének tárolási helye a csomag rögzítésének létrehozásakor van meghatározva. A Storage-fiókba mentett rögzítési fájlok elérésének kényelmes eszköze Microsoft Azure Storage Explorer, amelyet [letöltheti](https://storageexplorer.com/).

Ha meg van adva egy Storage-fiók, a rendszer a csomag rögzítési fájljait a következő helyen menti a Storage-fiókba:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Ha a rögzítés létrehozásakor a **fájl** lehetőséget választotta, megtekintheti vagy letöltheti a fájlt a virtuális gépen konfigurált elérési útról.

## <a name="next-steps"></a>Következő lépések

- Ha szeretné megtudni, hogyan automatizálható a csomagok rögzítése a virtuális gépek riasztásai között, tekintse meg a [riasztások által aktivált csomagok rögzítésének létrehozása](network-watcher-alert-triggered-packet-capture.md)című témakört.
- Annak megállapításához, hogy egy adott forgalom engedélyezett-e a virtuális gépeken, illetve ki-vagy befelé, tekintse meg [a virtuális gép hálózati forgalmának szűrésével kapcsolatos problémát](diagnose-vm-network-traffic-filtering-problem.md).
