---
title: Adatlemez csatolása vagy leválasztása virtuális géphez Azure DevTest Labs
description: Megtudhatja, hogyan csatolhat vagy leválaszthat egy adatlemezt egy virtuális géphez Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2020
ms.author: spelluru
ms.openlocfilehash: 3f18425408e6526904db85eae1c3a4db41d11a58
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198802"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Adatlemez csatolása vagy leválasztása virtuális géphez Azure DevTest Labs
Az [Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) kezeli a virtuális gép adatlemezei által társított Storage-fiókokat. A felhasználó egy új adatlemezt csatlakoztat egy virtuális géphez, megadja a szükséges lemez típusát és méretét, az Azure pedig automatikusan létrehozza és kezeli a lemezt. Ezután leválaszthatja az adatlemezt a virtuális gépről, vagy később ismét csatlakoztathatja ugyanahhoz a virtuális géphez, vagy az ugyanahhoz a felhasználóhoz tartozó másik virtuális géphez csatolva van.

Ez a funkció hasznos lehet az egyes virtuális gépeken kívüli tárolók és szoftverek kezeléséhez. Ha a tároló vagy a szoftver már létezik egy adatlemezen belül, egyszerűen csatolható, leválasztható és újra csatolható bármely olyan virtuális géphez, amely az adatlemez tulajdonosa.

## <a name="attach-a-data-disk"></a>Adatlemez csatolása
Az adatlemez virtuális géphez való csatolása előtt tekintse át a következő tippeket:

- A virtuális gép mérete határozza meg, hogy hány adatlemezt tud csatlakoztatni. Részletekért lásd: [virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- A-t futtató virtuális gépekhez csak adatlemezt lehet csatolni. Az adatlemez csatlakoztatása előtt győződjön meg arról, hogy a virtuális gép fut.

### <a name="attach-a-new-disk"></a>Új lemez csatolása
Az alábbi lépéseket követve hozzon létre és csatoljon új felügyelt adatlemezt egy Azure DevTest Labs-beli virtuális géphez.

1. Jelentkezzen be az [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
1. A laborok listájából válassza ki a kívánt labort. 
1. A **virtuális gépek**listájából válassza ki a futó virtuális gépet.
1. A bal oldali menüben válassza a **lemezek**lehetőséget.
1. Válassza az **új csatolás** lehetőséget az új adatlemez létrehozásához és a virtuális géphez csatolásához.

    ![Új adatlemez csatlakoztatása virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Töltse ki az **új lemez csatolása** panelt az adatlemez nevének, típusának és méretének megadásával.

    ![Fejezze be az "új lemez csatolása" űrlapot](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Kattintson az **OK** gombra.

Néhány pillanat elteltével létrejön az új adatlemez a virtuális géphez, és megjelenik az adott virtuális géphez tartozó **adatlemezek** listájában.

### <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása
Kövesse az alábbi lépéseket egy meglévő elérhető adatlemez egy futó virtuális géphez való újracsatolásához. 

1. Válassza ki azt a futó virtuális gépet, amelyhez adatlemezt kíván csatlakoztatni.
1. A bal oldali menüben válassza a **lemezek**lehetőséget.
1. Válassza a **meglévő csatolása** lehetőséget, hogy egy rendelkezésre álló adatlemezt csatoljon a virtuális géphez.

    ![Meglévő adatlemez csatolása egy virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. A **meglévő lemez csatolása** panelen kattintson az OK gombra.

    ![Meglévő adatlemez csatolása egy virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Néhány pillanat elteltével az adatlemez csatlakoztatva van a virtuális géphez, és megjelenik az adott virtuális gép **adatlemezei** listájában.

## <a name="detach-a-data-disk"></a>Adatlemez leválasztása
Ha már nincs szüksége egy virtuális géphez csatolt adatlemezre, könnyedén leválaszthatja. A leválasztás eltávolítja a lemezt a virtuális gépről, de később tárolja a tárolóban.

Ha újra szeretné használni a lemezen lévő meglévő adatlemezeket, akkor azt újra csatlakoztathatja ugyanahhoz a virtuális géphez vagy egy másikhoz.

### <a name="detach-from-the-vms-management-pane"></a>Leválasztás a virtuális gép felügyeleti paneljéről
1. A virtuális gépek listájából válassza ki azt a virtuális GÉPET, amelyhez adatlemez van csatolva.
1. A bal oldali menüben válassza a **lemezek**lehetőséget.
1. Az **adatlemezek**listájából válassza ki a leválasztani kívánt adatlemezt.

    ![Adatlemezek kiválasztása virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. Válassza a **Leválasztás** lehetőséget a lemez részletek paneljének tetején.

    ![Adatlemez leválasztása](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Az **Igen** gombra kattintva erősítse meg, hogy le kívánja választani az adatlemezt.

A lemez le van választva, és elérhető egy másik virtuális géphez való csatoláshoz. 
### <a name="detach-from-the-labs-main-pane"></a>Leválasztás a labor fő paneljéről
1. A labor fő ablaktábláján válassza **a saját adatlemezek**lehetőséget.
1. Kattintson a jobb gombbal a leválasztani kívánt adatlemezre – vagy válassza ki a három pontot ( **...** ), és válassza a **Leválasztás**lehetőséget.

    ![Adatlemez leválasztása](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Az **Igen** gombra kattintva erősítse meg, hogy le kívánja választani.

   > [!NOTE]
   > Ha egy adatlemez már le van választva, a **Törlés**lehetőség kiválasztásával eltávolíthatja azt az elérhető adatlemezek listájáról.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Nem felügyelt adatlemez frissítése
Ha olyan meglévő virtuális géppel rendelkezik, amely nem felügyelt adatlemezeket használ, egyszerűen átalakíthatja a virtuális gépet a felügyelt lemezek használatára. Ez a folyamat az operációsrendszer-lemezt és a csatlakoztatott adatlemezeket is átalakítja.

A nem felügyelt adatlemezek frissítéséhez kövesse az ebben a cikkben ismertetett lépéseket az adatlemez nem felügyelt virtuális gépről való [leválasztásához](#detach-a-data-disk) . Ezután [csatlakoztassa újra a lemezt](#attach-an-existing-disk) egy felügyelt virtuális géphez, hogy automatikusan frissítse az adatlemezt a felügyelet nélküliről a felügyelt értékre.

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan kezelheti az adatlemezeket a [igényelhető virtuális gépekhez](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

