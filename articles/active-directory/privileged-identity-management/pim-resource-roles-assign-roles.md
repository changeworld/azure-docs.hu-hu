---
title: Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan rendelhet hozzá Azure-beli erőforrás-szerepköröket Azure AD Privileged Identity Management (PIM) szolgáltatáshoz.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266558"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) felügyelheti a beépített Azure-erőforrás-szerepköröket, valamint az egyéni szerepköröket, beleértve a (de nem kizárólag):

- Tulajdonos
- Felhasználói hozzáférés rendszergazdája
- Közreműködő
- Biztonsági rendszergazda
- Security Manager

> [!NOTE]
> A tulajdonos vagy a felhasználói hozzáférés rendszergazdai előfizetési szerepköreihez rendelt felhasználók vagy csoportok tagjai, valamint az Azure ad-előfizetések felügyeletét engedélyező globális rendszergazdák alapértelmezés szerint erőforrás-rendszergazdai jogosultságokkal rendelkeznek. Ezek a rendszergazdák szerepköröket oszthatnak ki, konfigurálják a szerepkör beállításait, és áttekinthetik a hozzáférést az Azure-erőforrások Privileged Identity Management használatával. A felhasználók nem kezelhetnek erőforrás-rendszergazdai jogosultságokkal nem rendelkező erőforrások Privileged Identity Management. Megtekintheti az [Azure-erőforrások beépített szerepköreinek](../../role-based-access-control/built-in-roles.md)listáját.

## <a name="assign-a-role"></a>Szerepkör kiosztása

Kövesse az alábbi lépéseket, hogy a felhasználók jogosultak legyenek az Azure-erőforrások szerepkörre.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörnek.

    További információ a Privileged Identity Management kezeléséhez szükséges további rendszergazdai hozzáférésről: [hozzáférés biztosítása más rendszergazdák számára a Privileged Identity Management kezeléséhez](pim-how-to-give-access-to-pim.md).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza ki az **Azure-erőforrásokat**.

1. Az **Erőforrás-szűrő** használatával szűrheti a felügyelt erőforrások listáját.

    ![A felügyelni kívánt Azure-erőforrások listája](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Válassza ki a kezelni kívánt erőforrást, például egy előfizetést vagy egy felügyeleti csoportot.

1. A kezelés területen válassza ki a **szerepkörök** elemet az Azure-erőforrások szerepköreinek megtekintéséhez.

    ![Azure-erőforrások szerepkörei](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Válassza a **tag hozzáadása** lehetőséget az új hozzárendelés ablaktábla megnyitásához.

1. Válassza a **szerepkör kiválasztása** lehetőséget a szerepkör kiválasztása ablaktábla megnyitásához.

    ![Új hozzárendelés ablaktábla](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Válassza ki a hozzárendelni kívánt szerepkört, majd kattintson a **kiválasztás**gombra.

    Megnyílik a tag vagy csoport kiválasztása panel.

1. Válassza ki a szerepkörhöz hozzárendelni kívánt tagot vagy csoportot, majd kattintson a **kiválasztás**elemre.

    ![Tag vagy csoport ablaktábla kiválasztása](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Megnyílik a tagsági beállítások panel.

1. A **hozzárendelés típusa** listában válassza a **jogosult** vagy az **aktív**lehetőséget.

    ![Tagságok beállításai panel](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Az Azure-erőforrások Privileged Identity Management két különböző hozzárendelési típust biztosít:

    - A **jogosult** hozzárendelésekhez a szerepkör tagjának kell lennie a szerepkör használatára vonatkozó művelet végrehajtásához. A műveletek tartalmazhatják a többtényezős hitelesítés (MFA) ellenőrzését, üzleti indoklást biztosítanak, vagy a kijelölt jóváhagyók jóváhagyását kérik.

    - Az **aktív** hozzárendelésekhez nem szükséges, hogy a tag bármilyen műveletet hajtson végre a szerepkör használatához. Az aktívként hozzárendelt tagok rendelkeznek a szerepkörhöz tartozó jogosultságokkal.

1. Ha a hozzárendelésnek állandónak kell lennie (tartósan jogosult vagy véglegesen kiosztott), jelölje be a **véglegesen** jelölőnégyzetet.

    A szerepkör beállításaitól függően előfordulhat, hogy a jelölőnégyzet nem jelenik meg, vagy lehet, hogy nem módosítható.

1. Egy adott hozzárendelés időtartamának megadásához törölje a jelölőnégyzet jelölését, és módosítsa a kezdő és/vagy befejező dátum és idő mezőket.

    ![Tagsági beállítások – dátum és idő](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Ha elkészült, válassza a **kész**gombot.

    ![Új hozzárendelés – Hozzáadás](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Az új szerepkör-hozzárendelés létrehozásához válassza a **Hozzáadás**lehetőséget. Megjelenik az állapot értesítése.

    ![Új hozzárendelés – értesítés](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Meglévő szerepkör-hozzárendelés frissítése vagy eltávolítása

A meglévő szerepkör-hozzárendelések frissítéséhez vagy eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza ki az **Azure-erőforrásokat**.

1. Válassza ki a kezelni kívánt erőforrást, például egy előfizetést vagy egy felügyeleti csoportot.

1. A kezelés területen válassza ki a **szerepkörök** elemet az Azure-erőforrások szerepköreinek megtekintéséhez.

    ![Azure-erőforrás szerepkörei – szerepkör kiválasztása](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Válassza ki a frissíteni vagy eltávolítani kívánt szerepkört.

1. Keresse meg a szerepkör-hozzárendelést a **jogosult szerepkörök** vagy az **aktív szerepkörök** lapon.

    ![Szerepkör-hozzárendelés frissítése vagy eltávolítása](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. A szerepkör-hozzárendelés frissítéséhez vagy eltávolításához válassza a **frissítés** vagy az **Eltávolítás** lehetőséget.

    További információ a szerepkör-hozzárendelés kibővítéséről: [Azure-beli erőforrás-szerepkörök kiterjesztése vagy megújítása Privileged Identity Managementban](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>További lépések

- [Azure-beli erőforrás-szerepkörök kiterjesztése vagy megújítása Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Az Azure erőforrás-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
