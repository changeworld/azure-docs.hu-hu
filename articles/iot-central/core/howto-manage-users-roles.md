---
title: Felhasználók és szerepkörök kezelése az Azure IoT Central alkalmazásban | Microsoft Docs
description: Rendszergazdaként, hogyan kezelheti az Azure IoT Central-alkalmazás felhasználóit és szerepköreit
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 8826ec5b8876a3f9e5b613641cc0d759545f04c4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018949"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Felhasználók és szerepkörök kezelése a IoT Central alkalmazásban



Ez a cikk azt ismerteti, hogyan adhat hozzá, szerkeszthet és törölhet felhasználókat az Azure IoT Central alkalmazásban. A cikk azt is ismerteti, hogyan kezelheti a szerepköröket az Azure IoT Central alkalmazásban.

Az **Adminisztráció** szakasz eléréséhez és használatához **rendszergazdai** szerepkörrel kell rendelkeznie egy Azure IoT Central-alkalmazáshoz. Ha létrehoz egy Azure IoT Central alkalmazást, a rendszer automatikusan hozzáadja az alkalmazáshoz tartozó **rendszergazdai** szerepkörhöz.

## <a name="add-users"></a>Felhasználók hozzáadása

Minden felhasználónak rendelkeznie kell egy felhasználói fiókkal, mielőtt bejelentkeznek, és hozzá tudnak férni egy Azure IoT Central alkalmazáshoz. Az Azure IoT Central támogatja a Microsoft-fiókok (MSAs) és a Azure Active Directory (Azure AD) fiókjait. Azure Active Directory csoportok jelenleg nem támogatottak az Azure IoT Centralban.

További információ: [Microsoft-Fiók Súgó](https://support.microsoft.com/products/microsoft-account?category=manage-account) és gyors útmutató [: új felhasználók hozzáadása Azure Active Directoryhoz](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Ha felhasználót szeretne felvenni egy IoT Central alkalmazásba, lépjen a **felhasználók** lapra az **Adminisztráció** szakaszban.
    
    > [!div class="mx-imgBorder"]
    >![Felhasználók kezelése](media/howto-manage-users-roles/manage-users-pnp.png)

1. Felhasználó hozzáadásához a **felhasználók** lapon válassza a **+ felhasználó hozzáadása**elemet.

1. Válasszon egy szerepkört a felhasználó számára a **szerepkör** legördülő menüből. További információ a jelen cikk szerepkörök [kezelése](#manage-roles) című szakaszában található szerepkörökről.

    > [!div class="mx-imgBorder"]
    >![felhasználó hozzáadása és szerepkör kiválasztása](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Egy egyéni szerepkörbe tartozó felhasználó, aki más felhasználók hozzáadására engedélyt ad nekik, csak a saját szerepkörük alapján adhat hozzá felhasználókat a szerepkörhöz, vagy kevesebb jogosultsággal.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>A felhasználókhoz rendelt szerepkörök szerkesztése

A szerepkörök hozzárendelése után nem módosíthatók. A felhasználóhoz rendelt szerepkör módosításához törölje a felhasználót, majd adja hozzá újra a felhasználót egy másik szerepkörrel.

> [!NOTE]
> A hozzárendelt szerepkörök a IoT Central alkalmazásra vonatkoznak, és az Azure Portalról nem kezelhetők.

## <a name="delete-users"></a>Felhasználók törlése

A felhasználók törléséhez jelöljön ki egy vagy több jelölőnégyzetet a **felhasználók** lapon. Ezután válassza a **Törlés** elemet.

## <a name="manage-roles"></a>Szerepkörök kezelése

A szerepkörök segítségével szabályozhatja, hogy a szervezeten belül kik végezhetnek el különböző feladatokat IoT Centralban. Az alkalmazás felhasználóihoz három beépített szerepkör rendelhető. Ha finomabb vezérlőre van szüksége, [létrehozhat egyéni szerepköröket](#create-a-custom-role) is.

> [!div class="mx-imgBorder"]
> ![szerepkörök kijelölésének kezelése](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Rendszergazda

A **rendszergazdai** szerepkörben lévő felhasználók az alkalmazás minden részét kezelhetik és vezérelhetik, beleértve a számlázást is.

Az alkalmazást létrehozó felhasználó automatikusan hozzá lesz rendelve a **rendszergazdai** szerepkörhöz. A **rendszergazdai** szerepkörnek mindig legalább egy felhasználónak kell lennie.

### <a name="builder"></a>Szerkesztő

A **Builder** szerepkör felhasználói kezelhetik az alkalmazás minden részét, de nem módosíthatják a felügyeletet vagy a folyamatos adatexportálási lapokat.

### <a name="operator"></a>Művelet

A **kezelői** szerepkörben lévő felhasználók az eszköz állapotát és állapotát tudják figyelni. Nem módosíthatják az eszközök sablonjait, vagy felügyelhetik az alkalmazást. Az operátorok hozzáadhatnak és törölhetnek eszközöket, kezelhetik az eszközök készleteit, és futtathatják az elemzéseket és a feladatokat. 

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Ha a megoldás finomabb hozzáférés-vezérlést igényel, egyéni szerepköröket hozhat létre egyéni engedélyekkel. Egyéni szerepkör létrehozásához navigáljon az alkalmazás **felügyelet** szakaszának **szerepkörök** lapjára. Ezután válassza az **+ Új szerepkör**lehetőséget, és adja meg a szerepkör nevét és leírását. Válassza ki a szerepkör által igényelt engedélyeket, majd válassza a **Mentés**lehetőséget.

A felhasználókat ugyanúgy adhatja hozzá az egyéni szerepkörhöz, ahogyan a felhasználókat egy beépített szerepkörhöz adja.

> [!div class="mx-imgBorder"]
> Egyéni szerepkör létrehozása ![](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Egyéni szerepkör beállításai

Egyéni szerepkör meghatározásakor kiválaszthatja, hogy a felhasználó milyen engedélyekkel rendelkezik, ha tagja a szerepkörnek. Bizonyos engedélyek másoktól függenek. Ha például hozzáadja az **alkalmazás-irányítópultok frissítése** jogosultságot egy szerepkörhöz, a rendszer automatikusan hozzáadja az **alkalmazás megtekintése irányítópultok** engedélyt. Az alábbi táblázatok összefoglalják a rendelkezésre álló engedélyeket és azok függőségeit, amelyek egyéni szerepkörök létrehozásakor használhatók.

#### <a name="managing-devices"></a>Eszközök kezelése

**Eszköz sablonjának engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Kezelés | Megtekintés <br/> Egyéb függőségek: eszközök példányainak megtekintése  |
| Teljes hozzáférés | Megtekintés, kezelés <br/> Egyéb függőségek: eszközök példányainak megtekintése |

**Eszköz-példány engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None <br/> Egyéb függőségek: megtekintheti az eszközök sablonjait és az eszközök csoportjait |
| Frissítés | Megtekintés <br/> Egyéb függőségek: megtekintheti az eszközök sablonjait és az eszközök csoportjait  |
| Létrehozás | Megtekintés <br/> Egyéb függőségek: megtekintheti az eszközök sablonjait és az eszközök csoportjait  |
| Törlés | Megtekintés <br/> Egyéb függőségek: megtekintheti az eszközök sablonjait és az eszközök csoportjait  |
| Parancsok végrehajtása | Frissítés, megtekintés <br/> Egyéb függőségek: megtekintheti az eszközök sablonjait és az eszközök csoportjait  |
| Teljes hozzáférés | Megtekintheti, frissítheti, létrehozhatja, törölheti, végrehajthatja a parancsokat <br/> Egyéb függőségek: megtekintheti az eszközök sablonjait és az eszközök csoportjait  |

**Eszközök csoportjai engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None <br/> Egyéb függőségek: az eszközök sablonjainak és az eszközök példányainak megtekintése |
| Frissítés | Megtekintés <br/> Egyéb függőségek: az eszközök sablonjainak és az eszközök példányainak megtekintése   |
| Létrehozás | Megtekintés, frissítés <br/> Egyéb függőségek: az eszközök sablonjainak és az eszközök példányainak megtekintése   |
| Törlés | Megtekintés <br/> Egyéb függőségek: az eszközök sablonjainak és az eszközök példányainak megtekintése   |
| Teljes hozzáférés | Megtekintés, frissítés, létrehozás, törlés <br/> Egyéb függőségek: az eszközök sablonjainak és az eszközök példányainak megtekintése |

**Eszköz kapcsolati kezelési engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Példány olvasása | None <br/> Egyéb függőségek: eszköz-sablonok, eszközcsoport, eszköz-példányok megtekintése |
| Példány kezelése | None |
| Globális olvasás | None   |
| Globális kezelés | Globális olvasás |
| Teljes hozzáférés | Példány olvasása, példány kezelése, globális olvasás, globális kezelés. <br/> Egyéb függőségek: eszköz-sablonok, eszközcsoport, eszköz-példányok megtekintése |

**Feladatok engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None <br/> Egyéb függőségek: megtekintheti az eszközök sablonjait, az eszköz példányait és az eszközök csoportjait |
| Frissítés | Megtekintés <br/> Egyéb függőségek: megtekintheti az eszközök sablonjait, az eszköz példányait és az eszközök csoportjait |
| Létrehozás | Megtekintés, frissítés <br/> Egyéb függőségek: megtekintheti az eszközök sablonjait, az eszköz példányait és az eszközök csoportjait |
| Törlés | Megtekintés <br/> Egyéb függőségek: megtekintheti az eszközök sablonjait, az eszköz példányait és az eszközök csoportjait |
| Végrehajtás | Megtekintés <br/> Egyéb függőségek: megtekintheti az eszközök sablonjait, az eszköz példányait és az eszközök csoportjait; Eszköz példányainak frissítése; Parancsok végrehajtása az eszköz példányain |
| Teljes hozzáférés | Megtekintés, frissítés, létrehozás, törlés, végrehajtás <br/> Egyéb függőségek: megtekintheti az eszközök sablonjait, az eszköz példányait és az eszközök csoportjait; Eszköz példányainak frissítése; Parancsok végrehajtása az eszköz példányain |

**Szabályok engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None <br/> Egyéb függőségek: eszközök sablonjainak megtekintése |
| Frissítés | Megtekintés <br/> Egyéb függőségek: eszközök sablonjainak megtekintése |
| Létrehozás | Megtekintés, frissítés <br/> Egyéb függőségek: eszközök sablonjainak megtekintése |
| Törlés | Megtekintés <br/> Egyéb függőségek: eszközök sablonjainak megtekintése |
| Teljes hozzáférés | Megtekintés, frissítés, létrehozás, törlés <br/> Egyéb függőségek: eszközök sablonjainak megtekintése |

#### <a name="managing-the-app"></a>Az alkalmazás kezelése

**Alkalmazásbeállítások engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Frissítés | Megtekintés   |
| Másolás | Megtekintés <br/> Egyéb függőségek: eszközbeállítások, eszközök példányai, eszközcsoport, irányítópultok, adatexportálás, védjegyezés, Súgó hivatkozások, egyéni szerepkörök, szabályok |
| Törlés | Megtekintés   |
| Teljes hozzáférés | Megtekintés, frissítés, másolás, törlés <br/> Egyéb függőségek: megtekintheti az eszközöket, az eszközöket, az alkalmazás-irányítópultokat, az adatexportálást, a branding, a Súgó hivatkozásait, az egyéni szerepköröket |

**Alkalmazásspecifikus sablon exportálási engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Exportálás | Megtekintés <br/> Egyéb függőségek: eszközbeállítások, eszközök példányai, eszközcsoport, irányítópultok, adatexportálás, védjegyezés, Súgó hivatkozások, egyéni szerepkörök, szabályok |
| Teljes hozzáférés | Megtekintés, exportálás <br/> Egyéb függőségek: megtekintheti az eszközöket, az eszközöket, az alkalmazás-irányítópultokat, az adatexportálást, a branding, a Súgó hivatkozásait, az egyéni szerepköröket |

**Számlázási engedélyek**

| Name (Név) | Függőségek |
| ---- | -------- |
| Kezelés | None     |
| Teljes hozzáférés | Kezelés |

#### <a name="managing-users-and-roles"></a>Felhasználók és szerepkörök kezelése

**Egyéni szerepkörök engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None |
| Frissítés | Megtekintés |
| Létrehozás | Megtekintés, frissítés |
| Törlés | Megtekintés |
| Teljes hozzáférés | Megtekintés, frissítés, létrehozás, törlés |

**Felhasználói kezelési engedélyek**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None <br/> Egyéb függőségek: egyéni szerepkörök megtekintése |
| Hozzáadás | Megtekintés <br/> Egyéb függőségek: egyéni szerepkörök megtekintése |
| Törlés | Megtekintés <br/> Egyéb függőségek: egyéni szerepkörök megtekintése |
| Teljes hozzáférés | Megtekintés, Hozzáadás, törlés <br/> Egyéb függőségek: egyéni szerepkörök megtekintése |

> [!NOTE]
> Egy egyéni szerepkörbe tartozó felhasználó, aki más felhasználók hozzáadására engedélyt ad nekik, csak a saját szerepkörük alapján adhat hozzá felhasználókat a szerepkörhöz, vagy kevesebb jogosultsággal.

#### <a name="customizing-the-app"></a>Az alkalmazás testreszabása

**Alkalmazás-irányítópult engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Frissítés | Megtekintés   |
| Létrehozás | Megtekintés, frissítés |
| Törlés | Megtekintés   |
| Teljes hozzáférés | Megtekintés, frissítés, létrehozás, törlés |

**Személyes irányítópultok engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Frissítés | Megtekintés   |
| Létrehozás | Megtekintés, frissítés   |
| Törlés | Megtekintés   |
| Teljes hozzáférés | Megtekintés, frissítés, létrehozás, törlés |

**A branding, a favicon és a Colors engedélyek**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Frissítés | Megtekintés   |
| Teljes hozzáférés | Megtekintés, frissítés |

**Súgó hivatkozásainak engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Frissítés | Megtekintés   |
| Teljes hozzáférés | Megtekintés, frissítés |

#### <a name="extending-the-app"></a>Az alkalmazás kiterjesztése

**Adatexportálási engedélyek**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Frissítés | Megtekintés   |
| Létrehozás | Megtekintés, frissítés  |
| Törlés | Megtekintés   |
| Teljes hozzáférés | Megtekintés, frissítés, létrehozás, törlés |

**API-jogkivonat engedélyei**

| Name (Név) | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Létrehozás | Megtekintés   |
| Törlés | Megtekintés   |
| Teljes hozzáférés | Megtekintés, létrehozás, törlés |

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan kezelheti a felhasználókat és a szerepköröket az Azure IoT Central alkalmazásban, a javasolt következő lépés a [számla kezelésének](howto-view-bill.md)megismerése.
