---
title: Az Azure Multi-Factor Authentication engedélyezése
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti az Azure Multi-Factor Authenticationt egy felhasználói csoport számára, és hogyan tesztelheti a másodlagos tényezőt a bejelentkezési esemény során.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 253eb23be03c1cc0f2abf4ad1fed734426dc287d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154822"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-multi-factor-authentication"></a>Oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure Multi-Factor Authentication

A többtényezős hitelesítés (MFA) olyan folyamat, amelyben a rendszer a bejelentkezési esemény során kéri a felhasználót, hogy további azonosítási formákat keressen. Ezt a kérést lehet megadnia a mobiloddal vagy egy ujjlenyomat-vizsgálat megadásához. Ha a hitelesítés második formáját igényli, a biztonság megnövekszik, mivel ez a további tényező nem a támadók számára könnyen beszerezhető vagy duplikálható.

Az Azure Multi-Factor Authentication és a feltételes hozzáférési házirendek rugalmasságot biztosítanak a felhasználók számára, hogy az adott bejelentkezési események során engedélyezzék az MFA használatát.

Ennek az oktatóanyagnak a segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Feltételes hozzáférési szabályzat létrehozása az Azure-Multi-Factor Authentication felhasználók egy csoportjára való engedélyezéséhez
> * Az MFA-t kérő házirend-feltételek konfigurálása
> * Az MFA-folyamat tesztelése felhasználóként

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Egy prémium szintű Azure AD-vagy próbaverziós licenccel rendelkező Azure AD-bérlő használata.
    * Ha szükséges, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *Globális rendszergazdai* jogosultságokkal rendelkező fiók.
* Egy nem rendszergazda felhasználó, aki ismeri a jelszót, például *tesztfelhasználó*. Ebben az oktatóanyagban tesztelheti a végfelhasználói Azure Multi-Factor Authentication élményt ebben a fiókban.
    * Ha létre kell hoznia egy felhasználót, tekintse meg a rövid útmutató [: új felhasználók hozzáadása a Azure Active Directoryhoz](../add-users-azure-active-directory.md)című témakört.
* Olyan csoport, amelynek a nem rendszergazda felhasználó tagja, például *MFA-test-Group*. Ebben az oktatóanyagban engedélyezi az Azure Multi-Factor Authenticationt ehhez a csoporthoz.
    * Ha csoportot kell létrehoznia, tekintse meg [a csoport létrehozása és a tagok hozzáadása a Azure Active Directory-ben](../active-directory-groups-create-azure-portal.md)című témakört.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

Az Azure Multi-Factor Authentication engedélyezésének és használatának ajánlott módja feltételes hozzáférési szabályzatok használata. A feltételes hozzáférés lehetővé teszi, hogy olyan házirendeket hozzon létre és határozzon meg, amelyek reagálnak a bejelentkezési eseményekre, és további műveleteket kérjen, mielőtt a felhasználó hozzáférést kap egy alkalmazáshoz vagy szolgáltatáshoz.

![Áttekintő diagram a feltételes hozzáférés működéséről a bejelentkezési folyamat biztonságossá tételéhez](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

A feltételes hozzáférési szabályzatok lehetnek részletesek és egyediek, amelyek célja, hogy a felhasználók bárhol és bármikor termelékenyek legyenek, de a szervezetet is védik. Ebben az oktatóanyagban hozzunk létre egy alapszintű feltételes hozzáférési szabályzatot, amely arra kéri az MFA-t, amikor egy felhasználó bejelentkezik a Azure Portalba. A sorozat egy későbbi oktatóanyagában az Azure Multi-Factor Authentication a kockázatalapú feltételes hozzáférési szabályzattal konfigurálhatja.

Először hozzon létre egy feltételes hozzáférési szabályzatot, és rendelje hozzá a felhasználók tesztelési csoportját az alábbiak szerint:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) *globális rendszergazdai* jogosultságokkal rendelkező fiókkal.
1. Keresse meg és válassza ki a **Azure Active Directory**, majd a bal oldali menüben válassza a **Biztonság** elemet.
1. Válassza a **feltételes hozzáférés**, majd az **+ új házirend**elemet.
1. Adja meg a szabályzat nevét, például *MFA Pilot*.
1. A **hozzárendelések**területen válassza a **felhasználók és csoportok**, majd a **felhasználók és csoportok kiválasztása** választógombot.
1. Jelölje be a **felhasználók és csoportok**jelölőnégyzetet, majd **válassza ki** az elérhető Azure ad-felhasználók és-csoportok tallózását.
1. Keresse meg és válassza ki az Azure AD-csoportot, például *MFA-test-Group*, majd válassza a **kiválasztás**lehetőséget.

    [![](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png "Select your Azure AD group to use with the Conditional Access policy")](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Ha alkalmazni szeretné a csoportra vonatkozó feltételes hozzáférési szabályzatot, válassza a **kész**lehetőséget.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>A többtényezős hitelesítés feltételeinek konfigurálása

A létrehozott feltételes hozzáférési szabályzattal és a hozzárendelt felhasználók egy csoportjának definiálásával definiálhatja a szabályzatot kiváltó felhőalapú alkalmazásokat vagy műveleteket. Ezek a felhőalapú alkalmazások vagy műveletek olyan forgatókönyvek, amelyekkel további feldolgozásra van szükség, például az MFA bekéréséhez. Dönthet például úgy, hogy a pénzügyi alkalmazásokhoz való hozzáféréshez vagy a felügyeleti eszközök használatához további ellenőrzési kérés szükséges.

Ebben az oktatóanyagban konfigurálja a feltételes hozzáférési házirendet, hogy az MFA megkövetelése, amikor egy felhasználó bejelentkezik a Azure Portalba.

1. Válassza a **felhőalapú alkalmazások vagy műveletek**lehetőséget. Dönthet úgy, hogy alkalmazza a feltételes hozzáférési szabályzatot *minden felhőalapú alkalmazásra* , vagy *kiválasztja az alkalmazások lehetőséget*. A rugalmasság biztosítása érdekében bizonyos alkalmazásokat kizárhat a szabályzatból is.

    Ebben az oktatóanyagban a *beágyazás* lapon válassza az **alkalmazások kiválasztása** választógombot.

1. Válassza a **kiválasztás**lehetőséget, majd tallózzon a használható bejelentkezési események listájában.

    Ebben az oktatóanyagban válassza a **Microsoft Azure felügyelet** lehetőséget, hogy a házirend a bejelentkezési eseményekre vonatkozzon a Azure Portal.

1. A Select apps alkalmazásához válassza a **kiválasztás**, majd a **kész**lehetőséget.

    ![Válassza ki a feltételes hozzáférési szabályzatba felvenni kívánt Microsoft Azure felügyeleti alkalmazást](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

A hozzáférés-vezérlés lehetővé teszi a felhasználók számára a hozzáféréshez szükséges követelmények meghatározását, például egy jóváhagyott ügyfélalkalmazás vagy egy hibrid Azure AD-hez csatlakoztatott eszköz használatát. Ebben az oktatóanyagban konfigurálja a hozzáférés-vezérlést, hogy az MFA megkövetelése a bejelentkezési esemény során a Azure Portal.

1. A *hozzáférés-vezérlés*területen válassza az **Engedélyezés**lehetőséget, majd győződjön meg arról, hogy a **hozzáférési választógomb megadása** választógomb be van jelölve.
1. Jelölje be a **többtényezős hitelesítés megkövetelése**jelölőnégyzetet, majd válassza a **kiválasztás**lehetőséget.

A feltételes hozzáférési szabályzatok csak akkor állíthatók be *jelentésre* , ha szeretné megtekinteni, hogy a konfiguráció milyen hatással lenne a felhasználókra vagy *ki* , ha most nem szeretné a használati szabályzatot használni. Az oktatóanyaghoz tartozó felhasználók tesztelési csoportjaként engedélyezheti a szabályzatot, majd tesztelheti az Azure Multi-Factor Authentication.

1. Állítsa be a *házirend engedélyezése* kapcsolót **be értékre.**
1. A feltételes hozzáférési szabályzat alkalmazásához válassza a **Létrehozás**lehetőséget.

## <a name="test-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication tesztelése

Lássuk a feltételes hozzáférési szabályzatot és az Azure Multi-Factor Authentication működés közben. Először jelentkezzen be egy olyan erőforrásba, amely nem igényel MFA-t az alábbiak szerint:

1. Nyisson meg egy új böngészőablakot InPrivate-vagy inkognitóban-módban, és keresse meg [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Jelentkezzen be a nem rendszergazda felhasználóval, például *tesztfelhasználó*. Nincs rákérdezés az MFA betöltésére.
1. Zárja be a böngészőablakot.

Most jelentkezzen be a Azure Portalba. Mivel a Azure Portal a feltételes hozzáférési házirendben a további ellenőrzés megköveteléséhez lett konfigurálva, egy Azure Multi-Factor Authentication-kérést kap.

1. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://portal.azure.com](https://portal.azure.com).
1. Jelentkezzen be a nem rendszergazda felhasználóval, például *tesztfelhasználó*. Az Azure Multi-Factor Authentication regisztrálásához és használatához regisztrálnia kell. Az utasításokat követve fejezze be a folyamatot, és ellenőrizze, hogy sikeresen bejelentkezett-e a Azure Portalba.

    ![A bejelentkezéshez kövesse a böngészőben megjelenő utasításokat, majd a regisztrált multi-Factor Authentication-kérést](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Zárja be a böngészőablakot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szeretné, hogy a feltételes hozzáférési szabályzattal engedélyezze az Azure Multi-Factor Authentication konfigurálását az oktatóanyag részeként, törölje a szabályzatot a következő lépésekkel:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Keresse meg és válassza ki a **Azure Active Directory**, majd a bal oldali menüben válassza a **Biztonság** elemet.
1. Válassza a **feltételes hozzáférés**lehetőséget, majd válassza ki a létrehozott szabályzatot, például *MFA Pilot*
1. Válassza a **Törlés**lehetőséget, majd erősítse meg, hogy törölni kívánja a szabályzatot.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte az Azure Multi-Factor Authentication használatát a kiválasztott felhasználói csoportok feltételes hozzáférési házirendjeivel. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Feltételes hozzáférési szabályzat létrehozása az Azure Multi-Factor Authentication Azure AD-felhasználók csoportjának engedélyezéséhez
> * Az MFA-t kérő házirend-feltételek konfigurálása
> * Az MFA-folyamat tesztelése felhasználóként

> [!div class="nextstepaction"]
> [Jelszó visszaírási engedélyezése önkiszolgáló jelszó-visszaállításhoz (SSPR)](tutorial-enable-writeback.md)
