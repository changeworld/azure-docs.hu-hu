---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ThousandEyes | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ThousandEyes között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd179049f88454c62244cf1819cee08ef78d0633
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373241"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ThousandEyes

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ThousandEyes a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az ThousandEyes-t az Azure AD-vel, a következőket teheti:

* A ThousandEyes-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a ThousandEyes az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ThousandEyes egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A ThousandEyes támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A ThousandEyes támogatja az [ **automatikus** felhasználó-kiépítés használatát](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-thousandeyes-from-the-gallery"></a>ThousandEyes hozzáadása a gyűjteményből

A ThousandEyes Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ThousandEyes a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ThousandEyes** kifejezést a keresőmezőbe.
1. Válassza ki a **ThousandEyes** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a ThousandEyes

Konfigurálja és tesztelje az Azure AD SSO-t a ThousandEyes a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a ThousandEyes-ben.

Az Azure AD SSO és a ThousandEyes konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[THOUSANDEYES SSO konfigurálása](#configure-thousandeyes-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre ThousandEyes-teszt felhasználót](#create-thousandeyes-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ThousandEyes rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **ThousandEyes** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban az alkalmazás előre konfigurálva van, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés** ) gombra kattintva.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet: `https://app.thousandeyes.com/login/sso`

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **ThousandEyes beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension értéket. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a ThousandEyes.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **ThousandEyes**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-thousandeyes-sso"></a>ThousandEyes SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a **ThousandEyes** vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **Beállítások**elemre.

    ![Beállítások](./media/thousandeyes-tutorial/ic790066.png "beállításai")

3. Kattintson a **fiók** lehetőségre

    ![Fiók](./media/thousandeyes-tutorial/ic790067.png "fiókja")

4. Kattintson a **biztonság & hitelesítés** fülre.

    ![Biztonság & hitelesítési](./media/thousandeyes-tutorial/ic790068.png "Biztonság & hitelesítés")

5. Az **egyszeri bejelentkezés beállítása** szakaszban hajtsa végre a következő lépéseket:

    Egyszeri ![Bejelentkezés]beállítása(./media/thousandeyes-tutorial/ic790069.png "egyszeri bejelentkezésre")

    a. Válassza az **egyszeri bejelentkezés engedélyezése**lehetőséget.

    b. A **bejelentkezési oldal URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    c. A **kijelentkezési oldal URL-címe** szövegmezőben illessze be a **KIjelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    d. **Identitáskezelés kiállítójának** szövegmezője: illessze be az **Azure ad-azonosítót**, amelyet a Azure Portalból másolt.

    e. Az **ellenőrző tanúsítvány**területen kattintson a **fájl kiválasztása**lehetőségre, majd töltse fel a Azure Portalból letöltött tanúsítványt.

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="create-thousandeyes-test-user"></a>ThousandEyes-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a ThousandEyes-ben. A ThousandEyes támogatja az automatikus felhasználó-kiépítés használatát, amely alapértelmezés szerint engedélyezve van. További részletekért tekintse [meg az automatikus](thousandeyes-provisioning-tutorial.md) felhasználó-kiépítés konfigurálását ismertető témakört.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a ThousandEyes vállalati webhelyre rendszergazdaként.

2. Kattintson a **Beállítások**elemre.

    ![Beállítások](./media/thousandeyes-tutorial/IC790066.png "beállításai")

3. Kattintson a **fiók**lehetőségre.

    ![Fiók](./media/thousandeyes-tutorial/IC790067.png "fiókja")

4. Kattintson a **fiókok & felhasználók** lapra.

    ![Fiókok & felhasználói](./media/thousandeyes-tutorial/IC790073.png "fiókok & felhasználók")

5. A **felhasználók & fiókok hozzáadása** szakaszban hajtsa végre a következő lépéseket:

    Felhasználói ![fiókok hozzáadása](./media/thousandeyes-tutorial/IC790074.png "felhasználói fiókok hozzáadása")

    a. A **név** szövegmezőbe írja be a (z **) B. Simon**nevű felhasználó nevét.

    b. Az **e-mail** szövegmezőbe írja be a következőhöz hasonló felhasználó e-mail címét: b.simon@contoso.com.

    b. Kattintson **az új felhasználó hozzáadása a fiókhoz**lehetőségre.

    > [!NOTE]
    > A Azure Active Directory fiók tulajdonosa egy e-mailt kap, amely tartalmazza a fiók megerősítésére és aktiválására szolgáló hivatkozást is.

> [!NOTE]
> A ThousandEyes által biztosított egyéb ThousandEyes-létrehozási eszközöket vagy API-kat használhatja Azure Active Directory felhasználói fiókok kiépítéséhez.


## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a ThousandEyes csempére kattint, automatikusan be kell jelentkeznie arra a ThousandEyes, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A ThousandEyes kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [A felhasználók üzembe helyezésének konfigurálása](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)