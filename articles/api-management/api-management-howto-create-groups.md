---
title: Fejlesztői fiókok kezelése az Azure-beli csoportok használatával API Management
titleSuffix: Azure API Management
description: Ismerje meg, hogyan kezelheti a fejlesztői fiókokat az Azure-beli csoportok használatával API Management
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
ms.openlocfilehash: 182f0ea93ddfb2ab64de1c15b36958537fa5431f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430710"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Csoportok létrehozása és használata a fejlesztői fiókok kezeléséhez az Azure-ban API Management

Az API Management szolgáltatásban csoportok használatával szabályozható a fejlesztők hozzáférése a termékhez. A termékek először láthatók a csoportok számára, majd a csoportokban lévő fejlesztők megtekinthetik és előfizethetik a csoportokhoz társított termékeket. 

Az API Management az alábbi megváltoztathatatlan rendszercsoportokkal rendelkezik:

* **Rendszergazdák** – A csoportot Azure-előfizető rendszergazdák alkotják. A rendszergazdák kezelik az API Management szolgáltatáspéldányokat, valamint ők hozzák létre az API-kat, a műveleteket és a fejlesztők által használt termékeket.
* **Fejlesztők** – A fejlesztői portál hitelesített felhasználói tartoznak ebbe a csoportba. A fejlesztők olyan ügyfelek, akik alkalmazásokat hoznak létre az API-k segítségével. A fejlesztők hozzáférhetnek a fejlesztői portálhoz, és olyan alkalmazásokat készíthetnek, amelyek egy API műveleteit hívják meg.
* **Vendégek** – A fejlesztői portál nem hitelesített felhasználói tartoznak ebbe a csoportba, például az egyik API Management példány fejlesztői portálját meglátogató leendő ügyfelek. A vendégek kaphatnak bizonyos szintű, csak olvasási hozzáférést, például megtekinthetnek API-kat, de nem hívhatják meg őket.

Ezeken a rendszercsoportokon kívül a rendszergazdák létrehozhatnak egyéni csoportokat vagy [áttelepíthetik a társított Azure Active Directory-bérlők külső csoportjait is][leverage external groups in associated Azure Active Directory tenants]. A fejlesztők mellett az egyéni és külső csoportoknak is lehet adni láthatóságot és hozzáférést az API-termékekhez. Például egy adott partnerszervezet fejlesztői számára létre lehet hozni egy egyéni csoportot, és hozzáférést lehet nekik biztosítani a megfelelő API-kat tartalmazó termék API-jaihoz. Egy felhasználó egyszerre több csoport tagja is lehet.

Ez az útmutató bemutatja, hogy egy API Management-példány rendszergazdája hogyan adhat hozzá új csoportokat, és hogyan társíthatja azokat termékekhez és fejlesztőknek.

A kiadói portálon a csoportok létrehozása és kezelése mellett a API Management REST API [csoport](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entitás használatával is létrehozhatja és kezelheti a csoportokat.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikkben található feladatok végrehajtása: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>Csoport létrehozása

Ez a szakasz bemutatja, hogyan vehet fel új csoportot a API Management-fiókjába.

1. Válassza a **csoportok** fület a képernyő bal oldalán.
2. Kattintson a **+ Hozzáadás**gombra.
3. Adjon meg egy egyedi nevet a csoportnak, és egy opcionális leírást.
4. Nyomja meg a **Create** (Létrehozás) gombot.

    ![Új csoport felvétele](./media/api-management-howto-create-groups/groups001.png)

A csoport létrehozása után a rendszer hozzáadja a **csoportok** listájához. <br/>A csoport **nevének** vagy **leírásának** szerkesztéséhez kattintson a csoport és a **Beállítások**nevére.<br/>A csoport törléséhez kattintson a csoport nevére, majd nyomja le a **delete**billentyűt.

Most, hogy létrejött a csoport, hozzá lehet rendelni a termékekhez és a fejlesztőhöz.

## <a name="associate-group-product"> </a>Csoport hozzárendelése termékhez

1. Válassza a bal oldali **termékek** lapot.
2. Kattintson a kívánt termék nevére.
3. Kattintson a **hozzáférés-vezérlés**gombra.
4. Kattintson a **+ Csoport hozzáadása**elemre.

    ![Csoport hozzárendelése termékhez](./media/api-management-howto-create-groups/groups002.png)
5. Válassza ki a hozzáadni kívánt csoportot.

    ![Csoport hozzárendelése termékhez](./media/api-management-howto-create-groups/groups003.png)

    Ha el szeretne távolítani egy csoportot a termékből, kattintson a **Törlés**gombra.

    ![Csoport törlése](./media/api-management-howto-create-groups/groups004.png)

Ha egy termék társítva van egy csoporthoz, akkor a csoport fejlesztői megtekinthetik és előfizethetik a terméket.

> [!NOTE]
> Azure Active Directory csoportok hozzáadásával kapcsolatban lásd: [fejlesztői fiókok engedélyezése Azure Active Directory használatával az Azure API Managementban](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Csoportok hozzárendelése a fejlesztőknek

Ez a szakasz bemutatja, hogyan lehet csoportokat társítani a tagokkal.

1. Válassza a **csoportok** fület a képernyő bal oldalán.
2. Válassza ki a **Members** (Tagok) elemet.

    ![Tag felvétele](./media/api-management-howto-create-groups/groups005.png)
3. Kattintson a **+ Hozzáadás** gombra, és válasszon ki egy tagot.

    ![Tag felvétele](./media/api-management-howto-create-groups/groups006.png)
4. Kattintson a **kiválasztás**gombra.

Miután hozzáadta a társítást a fejlesztőhöz és a csoporthoz, a **felhasználók** lapon tekintheti meg.

## <a name="next-steps"> </a>További lépések

* Miután hozzáadta a fejlesztőt egy csoporthoz, megtekintheti és előfizethet az adott csoporthoz társított termékekre. További információkért lásd: [termék létrehozása és közzététele az Azure-ban API Management][How create and publish a product in Azure API Management],
* A kiadói portálon a csoportok létrehozása és kezelése mellett a API Management REST API [csoport](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entitás használatával is létrehozhatja és kezelheti a csoportokat.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
