---
title: Virtuális gépek jelszavának beállítása Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be és állíthatja alaphelyzetbe a virtuális gépek (VM) jelszavát a Azure Lab Services osztályterem Labs szolgáltatásban.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 0ff464936025a20cb6925adc7ef6eb44c2fe1f0e
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933811"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Virtuális gépek készleteinek létrehozása és kezelése 
Ez a cikk bemutatja, hogyan végezheti el a következő feladatokat:

- A virtuális gépek (VM-EK) számának növeléséhez a tesztkörnyezetben
- Minden virtuális gép vagy kiválasztott virtuális gép indítása 
- Virtuális gépek alaphelyzetbe állítása

## <a name="update-the-lab-capacity"></a>A tesztkörnyezet kapacitásának frissítése
A labor kapacitásának növeléséhez vagy csökkentéséhez (a laborban található virtuális gépek száma) hajtsa végre a következő lépéseket:

1. A **virtuálisgép-készlet** lapon válassza a **labor kapacitása: &lt;szám&gt; Machines**elemet.
2. Adja meg a laborban használni kívánt **virtuális gépek új számát** . Ennek a számnak nagyobbnak vagy egyenlőnek kell lennie a laborban regisztrált felhasználók számával. 
3. Ezt követően válassza a **Mentés** lehetőséget. 

    ![Összes elindítása gomb](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Ha növelte a kapacitást, megtekintheti a létrehozott virtuális gépet vagy virtuális gépeket. Ha nem látja az új virtuális gépet a listában, frissítse a lapot. 

    ![Létrehozott virtuális gép](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Virtuális gépek indítása

### <a name="start-ot-stop-all-vms"></a>Az összes virtuális gép leállításának elindítása
1. Váltson a **virtuálisgép-készlet** lapra. 
2. Válassza az **összes elindítása** lehetőséget az eszköztáron. 

    ![Összes elindítása gomb](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Az összes virtuális gép elindítása után leállíthatja az összes virtuális gépet az eszköztáron az **összes leállítása** gomb kiválasztásával. 

    ![Összes leállítása gomb](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Kijelölt virtuális gépek indítása
A kiválasztott virtuális gépek kétféleképpen indíthatók el (egy vagy több). Első lépésként válassza ki a virtuális gépet vagy virtuális gépeket a listából, majd válassza az **Indítás** lehetőséget az eszköztáron. 

A második módszer egy vagy több virtuális gép kijelölése a listában, majd az **állapot** oszlopban lévő gomb bekapcsolása. 

![Kijelölt virtuális gépek indítása](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Hasonlóképpen leállíthat egy vagy több virtuális gépet is, ha az **állapot** oszlopban lévő gombra kattint, vagy kiválasztja az eszköztár **Leállítás** elemét. 

> [!NOTE]
> Ha egy oktató bekapcsol egy tanulói virtuális gépet, az nem érinti a tanulóra vonatkozó kvótát. A felhasználóhoz tartozó kvóta meghatározza, hogy a felhasználó számára hány labor óra legyen elérhető az ütemezett osztály időpontján kívül. A kvótákkal kapcsolatos további információkért lásd: [kvóták beállítása a felhasználók](how-to-configure-student-usage.md?#set-quotas-for-users)számára.

## <a name="reset-vms"></a>Virtuális gépek alaphelyzetbe állítása
Egy vagy több virtuális gép alaphelyzetbe állításához jelölje ki azokat a listában, majd válassza az **Alaphelyzetbe állítás** lehetőséget az eszköztáron. 

![Kijelölt virtuális gépek alaphelyzetbe állítása](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

A **virtuális gép (ek) alaphelyzetbe állítása** párbeszédpanelen válassza az **Alaphelyzetbe állítás**lehetőséget. 

![Virtuális gép alaphelyzetbe állítása párbeszédpanel](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Virtuális gépek jelszavának beállítása
A labor tulajdonosa (tanár) beállíthatja/alaphelyzetbe állíthatja a virtuális gépek jelszavát a labor létrehozásakor (labor létrehozása varázsló) vagy a labor létrehozása után a **sablon** lapon. 

### <a name="set-password-at-the-time-of-lab-creation"></a>Jelszó beállítása a labor létrehozásakor
A labor tulajdonosa (tanár) a labor létrehozása varázsló **virtuális gép hitelesítő adatai** lapján állíthatja be a virtuális gépek jelszavát a laborban.

![Új tesztkörnyezet ablak](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Ha engedélyezi vagy letiltja az **azonos jelszó használata az összes virtuális gép számára** beállítást ezen az oldalon, a tanár dönthet úgy, hogy ugyanazt a jelszót használja a tesztkörnyezetben lévő összes virtuális géphez, vagy lehetővé teszi a tanulók számára a virtuális gépek jelszavának beállítását. Alapértelmezés szerint ez a beállítás az Ubuntu kivételével minden Windows-és Linux operációsrendszer-lemezkép esetében engedélyezve van. Ha ez a beállítás le van tiltva, a diákoknak meg kell adnia egy jelszót, amikor első alkalommal próbálnak csatlakozni a virtuális géphez. 

### <a name="reset-password-later"></a>Jelszó alaphelyzetbe állítása később

1. A labor **sablon** lapján válassza a **jelszó alaphelyzetbe állítása** lehetőséget az eszköztáron. 
1. A **jelszó alaphelyzetbe állítása** párbeszédpanelen adja meg a jelszót, és válassza a **jelszó alaphelyzetbe állítása**lehetőséget.
    
    ![Jelszó beállítása párbeszédpanel](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Kapcsolódás tanulói virtuális gépekhez
A labor Creator (oktató/professzor) a következő feltételek teljesülése esetén tud csatlakozni a tanuló virtuális géphez: 

- A tesztkörnyezet létrehozásakor a **virtuális gépekhez ugyanazt a jelszót kell használni**
- A virtuális gép fut 

 A tanuló virtuális géphez való kapcsolódáshoz vigye az egeret a listában a virtuális gépre, és válassza a számítógép gombot.  

![Kapcsolódás tanulói virtuális géphez gomb](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Ha a professzor elindítja a virtuális gépet, és csatlakozik hozzá, a tanulói kvóta nincs hatással. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Virtuális gépek listájának exportálása CSV-fájlba

1. Váltson a **virtuálisgép-készlet** lapra.
2. Válassza a **...** (három pont) lehetőséget az eszköztáron, majd válassza a **CSV exportálása**lehetőséget. 

    ![Virtuális gépek listájának exportálása](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>További lépések
Ha szeretne többet megtudni a tanulók által beállítható egyéb használati lehetőségekről, tekintse meg a következő cikket: a [tanulói használat konfigurálása](how-to-configure-student-usage.md).

A virtuális gépek jelszavának alaphelyzetbe állításáról a következő témakörben olvashat: [Virtual Machines-jelszó beállítása vagy alaphelyzetbe állítása a tanterem Labs-ban (diákok)](how-to-set-virtual-machine-passwords-student.md).