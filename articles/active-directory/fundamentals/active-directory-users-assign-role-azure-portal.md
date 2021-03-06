---
title: Címtárbeli szerepkörök kiosztása a felhasználók számára – Azure Active Directory | Microsoft Docs
description: Útmutatás a rendszergazdai és nem rendszergazdai szerepkörök hozzárendeléséhez a Azure Active Directoryrel rendelkező felhasználók számára.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df52969ea79e5d1af132aa82c2ec1ceedb92b82
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422893"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Rendszergazdai és nem rendszergazdai szerepkörök kiosztása a felhasználóknak a Azure Active Directory
Ha a szervezet egyik felhasználójának jogosultsággal kell rendelkeznie Azure Active Directory (Azure AD-) erőforrások kezeléséhez, a felhasználónak megfelelő szerepkört kell rendelnie az Azure AD-ben azon műveletek alapján, amelyekre a felhasználónak szüksége van a végrehajtásához.

További információ az elérhető szerepkörökről: [rendszergazdai szerepkörök Kiosztása Azure Active Directory-ben](../users-groups-roles/directory-assign-admin-roles.md). A felhasználók hozzáadásával kapcsolatos további információkért lásd: [új felhasználók hozzáadása Azure Active Directoryhoz](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Szerepkörök hozzárendelése
Az Azure AD-szerepkörök felhasználóhoz való hozzárendelésének általános módja a **címtár-szerepkör** lap egy felhasználó számára.

A szerepköröket Privileged Identity Management (PIM) használatával is hozzárendelheti. További információ a PIM használatáról: [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Szerepkör társítása felhasználóhoz
1. Lépjen a [Azure Portalra](https://portal.azure.com/) , és jelentkezzen be egy globális rendszergazdai fiókkal a címtárhoz. 

2. Keresse meg és válassza ki a **Azure Active Directory**.

      ![Azure Active Directory Azure Portal keresése](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Válassza a **Felhasználók** lehetőséget.

4. Keresse meg és válassza ki a szerepkör-hozzárendelést beolvasó felhasználót. Például _Alain Charon_.

      ![Minden felhasználó lap – válassza ki a felhasználót](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Az **Alain Charon-profil** lapon válassza a **hozzárendelt szerepkörök**elemet.

    Megjelenik az **Alain Charon-Directory szerepkör** lap.

6. Válassza a **hozzárendelés hozzáadása**lehetőséget, válassza ki az Alain-hoz hozzárendelni kívánt szerepkört (például _alkalmazás-rendszergazda_), majd válassza a **kiválasztás**lehetőséget.

    ![Hozzárendelt szerepkörök lap – a kiválasztott szerepkör megjelenítése](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Az alkalmazás-rendszergazdai szerepkör az Alain Charon van hozzárendelve, és megjelenik az **Alain Charon-Directory szerepkör** lapon.

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása
Ha el kell távolítania a szerepkör-hozzárendelést egy felhasználótól, azt az **Alain Charon-Directory szerepkör** oldaláról is elvégezheti.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Szerepkör-hozzárendelés eltávolítása a felhasználótól

1. Válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**lehetőséget, majd keresse meg és válassza ki azt a felhasználót, aki a szerepkör-hozzárendelést eltávolítja. Például _Alain Charon_.

2. Válassza a **hozzárendelt szerepkörök**lehetőséget, válassza az **alkalmazás rendszergazdája**lehetőséget, majd válassza a **hozzárendelés eltávolítása**lehetőséget.

    ![Hozzárendelt szerepkörök lap, amely megjeleníti a kiválasztott szerepkört és az Eltávolítás lehetőséget.](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Az alkalmazás-rendszergazdai szerepkör el lett távolítva az Alain Charon, és már nem jelenik meg az **Alain Charon-Directory szerepkör** lapon.

## <a name="next-steps"></a>Következő lépések
- [Felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md)

- [Profil adatainak hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Vendégfelhasználók hozzáadása másik címtárból](../b2b/what-is-b2b.md)

Más felhasználói felügyeleti feladatokat is elvégezhet, például a delegált hozzárendelését, a szabályzatok használatát és a felhasználói fiókok megosztását. További információ az egyéb elérhető műveletekről: [Azure Active Directory felhasználói kezelés dokumentációja](../users-groups-roles/index.yml).


