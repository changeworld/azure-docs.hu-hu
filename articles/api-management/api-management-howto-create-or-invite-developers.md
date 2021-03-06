---
title: Felhasználói fiókok kezelése az Azure API Managementban | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre vagy hívhat meg felhasználókat az Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 9de75b5537b3e267116882e7f151d893de575071
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072467"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Felhasználói fiókok kezelése az Azure-ban API Management

API Management a fejlesztők a API Management használatával elérhető API-k felhasználói. Ez az útmutató bemutatja, hogyan hozhat létre és hívhat meg fejlesztőket a API Management-példánnyal elérhetővé tenni kívánt API-k és termékek használatához. A felhasználói fiókok programozott kezelésével kapcsolatos információkért tekintse meg a [felhasználói entitás](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user) dokumentációját a [API Management Rest](/rest/api/apimanagement/) -referenciában.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő feladatok elvégzése: [Hozzon létre egy Azure API Management-példányt](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>Új fejlesztő létrehozása

Új felhasználó hozzáadásához kövesse az ebben a szakaszban ismertetett lépéseket:

1. Válassza a képernyő bal oldalán található **felhasználók** lapot.
2. Kattintson a **+ Hozzáadás**gombra.
3. Adja meg a megfelelő adatokat a felhasználó számára.
4. Nyomja meg az **Add** (Hozzáadás) gombot.

    ![Új felhasználó hozzáadása](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Alapértelmezés szerint az újonnan létrehozott fejlesztői fiókok **aktívak**, és a **fejlesztői** csoporthoz vannak társítva. Az **aktív** állapotban lévő fejlesztői fiókok az összes olyan API elérésére használhatók, amelyhez előfizetéssel rendelkeznek. Ha az újonnan létrehozott fejlesztőt további csoportokkal szeretné hozzárendelni, tekintse meg a [csoportok a fejlesztőkhöz][How to associate groups with developers]való hozzárendelését ismertető témakört.

## <a name="invite-developer"> </a>Fejlesztő meghívása
A fejlesztők meghívásához kövesse az ebben a szakaszban leírt lépéseket:

1. Válassza a képernyő bal oldalán található **felhasználók** lapot.
2. Kattintson a **+ meghívás**gombra.

Megerősítő üzenet jelenik meg, de az újonnan meghívott fejlesztő nem jelenik meg a listán, amíg el nem fogadja a meghívót. 

A fejlesztő meghívásakor a rendszer e-mailt küld a fejlesztőnek. Ez az e-mail sablon alapján jön létre, és testreszabható. További információ: az [e-mail-sablonok konfigurálása][Configure email templates].

A meghívás elfogadása után a fiók aktívvá válik.

## <a name="block-developer"></a> Fejlesztői fiók inaktiválása vagy újraaktiválása

Alapértelmezés szerint az újonnan létrehozott vagy meghívott fejlesztői fiókok **aktívak**. Fejlesztői fiók inaktiválásához kattintson a **Letiltás**elemre. Letiltott fejlesztői fiók újraaktiválásához kattintson az **aktiválás**gombra. A letiltott fejlesztői fiókok nem férhetnek hozzá a fejlesztői portálhoz, vagy nem hívhatnak meg API-kat. Felhasználói fiók törléséhez kattintson a **Törlés**gombra.

Egy felhasználó letiltásához kövesse az alábbi lépéseket.

1. Válassza a képernyő bal oldalán található **felhasználók** lapot.
2. Kattintson a letiltani kívánt felhasználóra.
3. Nyomja meg a **Letiltás**gombot.

## <a name="reset-a-user-password"></a>Felhasználói jelszó alaphelyzetbe állítása

A felhasználói fiókokkal való programozott munkához tekintse meg a felhasználói entitás dokumentációját a [API Management REST API](/rest/api/apimanagement/) -referenciában. A felhasználói fiók jelszavának egy adott értékre való visszaállításához használhatja a [felhasználó frissítése](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) műveletet, és megadhatja a kívánt jelszót.

## <a name="next-steps"></a>Következő lépések
A fejlesztői fiók létrehozása után hozzárendelheti a szerepkörökhöz, és előfizethet a termékekre és API-kra. További információ: [csoportok létrehozása és használata][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
