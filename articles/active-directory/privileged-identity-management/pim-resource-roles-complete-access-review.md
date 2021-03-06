---
title: Az Azure-erőforrás szerepköreinek hozzáférési felülvizsgálatának befejezése a PIM-ben – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan végezheti el Azure Active Directory Privileged Identity Management Azure Resource szerepköreinek hozzáférési felülvizsgálatát.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021985"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>A Privileged Identity Management Azure Resource szerepköreinek hozzáférési felülvizsgálatának befejezése

A Kiemelt szerepkörű rendszergazdák [a hozzáférési felülvizsgálat megkezdése](pim-resource-roles-start-access-review.md)után ellenőrizhetik a Kiemelt hozzáférési jogosultságokat. A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) automatikusan elküld egy e-mailt, amely arra kéri a felhasználókat, hogy tekintsék át a hozzáférésüket. Ha a felhasználó nem kap e-mailt, elküldheti a [hozzáférési felülvizsgálat végrehajtásának](pim-resource-roles-perform-access-review.md)utasításait.

Ha a hozzáférési felülvizsgálati időszak véget ért, vagy miután az összes felhasználó befejezte az önellenőrzést, kövesse a jelen cikkben ismertetett lépéseket a felülvizsgálat kezeléséhez és az eredmények megtekintéséhez.

## <a name="manage-access-reviews"></a>Hozzáférési felülvizsgálatok kezelése

1. Nyissa meg az [Azure Portal](https://portal.azure.com/). Az irányítópulton válassza ki az **Azure Resources** szolgáltatást.

2. Válassza ki az erőforrást.

3. Válassza ki az irányítópult **hozzáférési felülvizsgálatok** szakaszát.

    ![Azure-erőforrások – hozzáférési felülvizsgálatok listája, amely a szerepkört, a tulajdonost, a kezdő dátumot, a záró dátumot és az állapotot mutatja](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Válassza ki a felügyelni kívánt hozzáférési felülvizsgálatot.

A hozzáférési felülvizsgálat részletes lapján számos lehetőség van a felülvizsgálat kezelésére. A lehetőségek a következők:

![A felülvizsgálatok kezelésének lehetőségei – Leállítás, alaphelyzetbe állítás, alkalmazás, törlés](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Leállítás

Minden hozzáférési felülvizsgálat befejező dátummal rendelkezik. Kattintson a **Leállítás** gombra a korai befejezéshez. Minden olyan felhasználó, aki még nem fejezte be a felülvizsgálatot, nem tudja befejezni azt a felülvizsgálat leállítása után. A felülvizsgálat leállítását követően nem indítható újra.

### <a name="reset"></a>Alaphelyzetbe állítás

A hozzáférési felülvizsgálat alaphelyzetbe állítható, hogy eltávolítsa a rajta végrehajtott összes döntést. A hozzáférési felülvizsgálat alaphelyzetbe állítása után a rendszer minden felhasználót megjelöl, és nem tekinti át újra.

### <a name="apply"></a>Alkalmaz

A hozzáférési felülvizsgálat befejezése után válassza az **alkalmaz** lehetőséget a felülvizsgálat eredményének megvalósításához. Ha egy felhasználó hozzáférése megtagadva a felülvizsgálatban, ez a lépés eltávolítja a szerepkör-hozzárendelést.  

### <a name="delete"></a>Törlés

Ha még nem érdekli a felülvizsgálat, törölje. Válassza a **Törlés** gombra, majd távolítsa el a felülvizsgálatot a Privileged Identity Management szolgáltatásból.

## <a name="results"></a>Results (Eredmények)

Az **eredmények** lapon megtekintheti és letöltheti a felülvizsgálati eredmények listáját.

![A felhasználók, az eredmény, a Reason, a reviewed by, a által alkalmazott eredmények és az eredmény alkalmazása](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Látogató

Felülvizsgálók megtekintése és hozzáadása a meglévő hozzáférési felülvizsgálathoz. Visszajelzések elvégzéséhez emlékeztesse a véleményezőket.

![Véleményezők lap listázási neve és egyszerű felhasználóneve](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Következő lépések

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának elindítása Privileged Identity Management](pim-resource-roles-start-access-review.md)
- [A Privileged Identity Management Azure-beli erőforrás-szerepköreinek hozzáférési felülvizsgálatának elvégzése](pim-resource-roles-perform-access-review.md)
