---
title: Azure Portal irányítópultok megosztása szerepköralapú Access Control használatával
description: Ez a cikk azt ismerteti, hogyan oszthat meg egy irányítópultot a Azure Portal szerepköralapú Access Control használatával.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: e8d251cef9e67cb8fc0c11df8ce546383f75a679
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248462"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Azure-irányítópultok megosztása szerepköralapú Access Control használatával

Az irányítópult konfigurálását követően közzéteheti és megoszthatja azt a szervezet más felhasználóival. Lehetővé teszi mások számára az irányítópult megtekintését az Azure [szerepköralapú Access Control](../role-based-access-control/role-assignments-portal.md) (RBAC) használatával. Rendeljen hozzá egy felhasználót vagy egy felhasználói csoportot egy szerepkörhöz. Ez a szerepkör határozza meg, hogy a felhasználók megtekinthetik vagy módosíthatják a közzétett irányítópultot.

Minden közzétett irányítópult Azure-erőforrásként van megvalósítva. Az előfizetéshez tartozó felügyelhető elemek, és egy erőforráscsoport tartalmazza őket. Hozzáférés-vezérlési perspektívából az irányítópultok nem különböznek más erőforrásokkal, például egy virtuális géppel vagy egy Storage-fiókkal.

> [!TIP]
> Az irányítópulton lévő csempék a saját hozzáférés-vezérlési követelményeiket a megjelenített erőforrások alapján kényszerítik ki. Az irányítópultok széles körben is megoszthatók az egyes csempék adatvédelme során.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Az irányítópultok hozzáférés-vezérlésének ismertetése

Szerepköralapú Access Control (RBAC) esetén a felhasználók a hatókör három különböző szintjén oszthatók ki szerepkörökhöz:

* előfizetést
* erőforráscsoport
* resource

A hozzárendelt engedélyek az előfizetésből öröklik az erőforrást. A közzétett irányítópult egy erőforrás. Lehetséges, hogy már rendelkezik a közzétett irányítópultra vonatkozó előfizetéshez tartozó szerepkörökhöz hozzárendelt felhasználókkal.

Tegyük fel, hogy rendelkezik Azure-előfizetéssel, és a csapat különböző tagjai az előfizetéshez tartozó *tulajdonos*, *közreműködő*vagy *olvasó* szerepkört kaptak. A tulajdonosok vagy közreműködők az előfizetésben lévő irányítópultokat listázhatja, megtekinthetik, létrehozhatják, módosíthatják vagy törölhetik. Azok a felhasználók, akik az olvasók listázzák és megtekinthetik az irányítópultokat, de nem módosíthatják és nem törölhetik azokat. Az olvasói hozzáféréssel rendelkező felhasználók helyi módosításokat végezhetnek egy közzétett irányítópulton, például egy probléma hibaelhárításakor, de a módosításokat nem tehetik vissza a kiszolgálóra. Saját maguk készíthetik el az irányítópult saját példányát.

Emellett engedélyeket is hozzárendelhet az erőforráscsoporthoz, amely több irányítópultot vagy egy egyéni irányítópultot tartalmaz. Dönthet például úgy, hogy egy adott felhasználói csoportnak korlátozott engedélyekkel kell rendelkeznie az előfizetésben, de nagyobb hozzáférésre van szüksége egy adott irányítópulthoz. Rendelje hozzá ezeket a felhasználókat az irányítópult egyik szerepköréhez.

## <a name="publish-dashboard"></a>Irányítópult közzététele

Tegyük fel, hogy olyan irányítópultot konfigurál, amelyet szeretne megosztani az előfizetésében lévő felhasználói csoporttal. A következő lépések bemutatják, hogyan oszthat meg egy irányítópultot a Storage Managers nevű csoportba. Tetszés szerint megadhatja a csoport nevét. További információ: [csoportok kezelése a Azure Active Directoryban](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

A hozzáférés kiosztása előtt közzé kell tennie az irányítópultot.

1. Az irányítópulton válassza a **megosztás**lehetőséget.

    ![Az irányítópult megosztásának kiválasztása](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. A **megosztás + hozzáférés-vezérlés**területen válassza a **Közzététel**lehetőséget.

    ![Az irányítópult közzététele](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Alapértelmezés szerint a megosztás közzéteszi az irányítópultot az **irányítópultok**nevű erőforráscsoporthoz.

Az irányítópult már közzé van téve. Ha az előfizetésből örökölt engedélyek megfelelőek, semmit nem kell tennie. A szervezet többi felhasználója elérheti és módosíthatja az irányítópultot az előfizetési szint szerepkörük alapján.

## <a name="assign-access-to-a-dashboard"></a>Irányítópulthoz való hozzáférés kiosztása

Az irányítópult egyik szerepköréhez hozzárendelhet egy felhasználói csoportot.

1. Miután közzétette az irányítópultot, a **megosztás + hozzáférés-vezérlés**területen válassza a **felhasználók kezelése**lehetőséget.

    ![irányítópultok felhasználóinak kezelése](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

    Ha egy irányítópulton szeretné elérni a **megosztás + hozzáférés-vezérlést** , válassza a **megosztás** vagy a **megosztás** megszüntetése lehetőséget.

1. Válassza a **szerepkör-hozzárendelések** lehetőséget, hogy megtekintse azokat a meglévő felhasználókat, akik már hozzá vannak rendelve ehhez az irányítópulthoz.

1. Új felhasználó vagy csoport hozzáadásához válassza a **Hozzáadás**lehetőséget.

    ![felhasználó hozzáadása az irányítópulthoz való hozzáféréshez](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Válassza ki azt a szerepkört, amely a megadható engedélyeket jelöli. Ebben a példában válassza a **közreműködő**lehetőséget.

1. Válassza ki a szerepkörhöz hozzárendelni kívánt felhasználót vagy csoportot. Ha nem látja a keresett felhasználót vagy csoportot a listában, használja a keresőmezőt. Az elérhető csoportok listája a Active Directoryban létrehozott csoportoktól függ.

1. Ha befejezte a felhasználók vagy csoportok hozzáadását, kattintson **az OK gombra**.

    A rendszer hozzáadja az új hozzárendelést a felhasználók listájához. A **hozzáférése** az **örökölt**helyett **hozzárendelésként** van felsorolva.

    ![hozzárendelt szerepkörök](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>További lépések

* A szerepkörök listáját lásd: [beépített szerepkörök az Azure-erőforrásokhoz](../role-based-access-control/built-in-roles.md).
* Az erőforrások kezelésével kapcsolatos további információkért lásd: [Az Azure-erőforrások kezelése a Azure Portal használatával](resource-group-portal.md).

