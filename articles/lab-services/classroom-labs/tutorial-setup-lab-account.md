---
title: Tesztkörnyezetfiók beállítása az Azure Lab Services szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan állíthat be labor-fiókot Azure Lab Serviceskal, hozzáadhat egy labor-létrehozót, és megadhatja a Labs által a labor-fiókban használandó Piactéri lemezképeket.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: dba6a4c07691f3d7ec88d8b889e68d6ac7116f07
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239447"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Oktatóanyag: Tesztkörnyezetfiók beállítása az Azure Lab Services szolgáltatással
Az Azure Lab Services szolgáltatásban a tesztkörnyezetfiók központi fiókként szolgál a vállalat összes tesztkörnyezetének felügyeletéhez. A tesztkörnyezetfiókban engedélyeket adhat másoknak a tesztkörnyezetek létrehozására, és szabályzatokat állíthat be, amelyek a tesztkörnyezetfiók alá tartozó összes tesztkörnyezetben érvényesek. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre labor-fiókot. 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Tesztkörnyezetfiók létrehozása
> * Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-lab-account"></a>Tesztkörnyezetfiók létrehozása
A következő lépések bemutatják, hogyan használhatja az Azure Portalt tesztkörnyezetfiók létrehozására az Azure Lab Services szolgáltatásban. 

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. Válassza ki a **DevOps** **kategóriából**. Ezután válassza a **labor Services**elemet. Ha a **Lab szolgáltatások**mellett a csillag (`*`) lehetőséget választja, a rendszer hozzáadja a bal oldali menü **Kedvencek** szakaszához. A következő időponttól kezdve válassza a **Lab szolgáltatások** lehetőséget a **Kedvencek**alatt.

    ![Minden szolgáltatás – > labor Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. A **labor Services** lapon kattintson a **Hozzáadás** elemre az eszköztáron, vagy válassza a **labor-fiók létrehozása** gombot az oldalon. 

    ![Válassza a Hozzáadás lehetőséget a labor-fiókok oldalon](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. A **labor-fiók létrehozása** lap **alapok** lapján hajtsa végre a következő műveleteket: 
    1. Írjon be egy nevet a **Tesztkörnyezetfiók neve** mezőbe. 
    2. Válassza ki azt az **Azure-előfizetést**, amelyben a tesztkörnyezetfiókot létre szeretné hozni.
    3. Az **erőforráscsoport**területen válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új létrehozása**lehetőséget, és adja meg az erőforráscsoport nevét.
    4. A **hely**mezőben válasszon ki egy helyet vagy régiót, amelyben létre kívánja hozni a labor-fiókot. 

        ![Lab-fiók – alapismeretek lap](../media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Válassza az **Áttekintés + létrehozás** lehetőséget.
    6. Tekintse át az összegzést, és válassza a **Létrehozás**lehetőséget. 

        ![Felülvizsgálat + létrehozás – > Létrehozás](../media/tutorial-setup-lab-account/create-button.png)    
5. Ha a telepítés befejeződött, bontsa ki a **következő lépések**csomópontot, és válassza az **Ugrás az erőforráshoz**lehetőséget. 

    ![Ugrás a labor-fiók oldalra](../media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Ellenőrizze, hogy megjelenik-e a **labor-fiók** lap. 

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
A felhasználónak **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. Ha engedélyt kíván adni az oktatóknak, hogy létrehozzák a tesztkörnyezeteket a tanóráikhoz, adja hozzá őket a **Tesztkörnyezet-létrehozó** szerepkörhöz:

> [!NOTE]
> A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha azt tervezi, hogy ugyanazt a felhasználói fiókot használja egy osztályterem labor létrehozásához ebben az oktatóanyagban, hagyja ki ezt a lépést. 

1. A **labor-fiók** lapon válassza a **hozzáférés-vezérlés (iam)** lehetőséget, válassza a **+ Hozzáadás** lehetőséget az eszköztáron, majd válassza a **+ szerepkör-hozzárendelés hozzáadása** lehetőséget az eszköztáron. 

    ![Access Control – > szerepkör-hozzárendelés hozzáadása gomb](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. A **szerepkör-hozzárendelés hozzáadása** lapon válassza a **tesztkörnyezet létrehozója** **szerepkört**, válassza ki azt a felhasználót, amelyet hozzá szeretne adni a labor létrehozói szerepkörhöz, majd válassza a **Mentés**lehetőséget. 

    ![Tesztkörnyezet létrehozójának hozzáadása](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy tesztkörnyezetfiókot. Ha szeretne többet megtudni arról, hogyan hozhat létre egy tantermi labort professzorként, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Osztályterem-tesztkörnyezet beállítása](tutorial-setup-classroom-lab.md)

