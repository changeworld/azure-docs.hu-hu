---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a TextMagic | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és TextMagic között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca7b900a0e2203fffb5240acd4ab5201fc9d52d7
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533029"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-textmagic"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a TextMagic

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a TextMagic a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az TextMagic-t az Azure AD-vel, a következőket teheti:

* A TextMagic-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a TextMagic az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* TextMagic egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A TextMagic támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

* A TextMagic **csak időben támogatja a** felhasználók kiépítési folyamatát

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-textmagic-from-the-gallery"></a>TextMagic hozzáadása a gyűjteményből

A TextMagic Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a TextMagic a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **TextMagic** kifejezést a keresőmezőbe.
1. Válassza ki a **TextMagic** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-textmagic"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a TextMagic

Konfigurálja és tesztelje az Azure AD SSO-t a TextMagic a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a TextMagic-ben.

Az Azure AD SSO és a TextMagic konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[TEXTMAGIC SSO konfigurálása](#configure-textmagic-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre TextMagic-teszt felhasználót](#create-textmagic-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-TextMagic rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **TextMagic** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    Az **azonosító** szövegmezőbe írja be a következő URL-címet: `https://my.textmagic.com/saml/metadata`

5. A TextMagic alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName**leképezéssel van leképezve. A TextMagic alkalmazás a **NameIdentifier** a **User. mail**használatával rendeli hozzá, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy rákattint a **Szerkesztés** ikonra, és megváltoztatja az attribútum-hozzárendelést.

    ![image](common/edit-attribute.png)

1. A fentiek mellett a TextMagic alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre is fel vannak töltve, de a követelménynek megfelelően áttekintheti őket.

    | Név |   Forrás attribútum| Névtér  |
    | --------------- | --------------- | --------------- |
    | vállalati | felhasználó. cégnév | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstName               | User. givenName |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastName            | felhasználó. vezetéknév |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Phone               | User. telephoneNumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **TextMagic beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a TextMagic.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **TextMagic**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-textmagic-sso"></a>TextMagic SSO konfigurálása

1. A TextMagic belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés TextMagic** gombra a TextMagic alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a TextMagic való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-5-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a TextMagic, nyisson meg egy új böngészőablakot, és jelentkezzen be a TextMagic vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Válassza ki a **fiók beállításait** a Felhasználónév alatt.

    ![TextMagic-konfiguráció](./media/textmagic-tutorial/config1.png)

5. Kattintson az **egyszeri bejelentkezés (SSO)** lapra, és töltse ki a következő mezőket:  

    ![TextMagic-konfiguráció](./media/textmagic-tutorial/config2.png)

    a. Az **Identity Provider-entitás azonosítója:** szövegmezőben illessze be az **Azure ad-azonosító**értékét, amelyet a Azure Portalból másolt.

    b. Az **Identity Provider egyszeri bejelentkezésének URL-címe:** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet a Azure Portalból másolt.

    c. Az **Identity Provider slo URL-címe:** szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-cím**értékét, amelyet a Azure Portalból másolt.

    d. Nyissa meg az **Alap-64 kódolású tanúsítványt** a jegyzettömbben, Azure Portal letöltve, másolja ki a tartalmat a vágólapra, majd illessze be a **nyilvános x509-tanúsítványba:** szövegmezőbe.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="create-textmagic-test-user"></a>TextMagic-tesztelési felhasználó létrehozása

Az alkalmazás a **felhasználó üzembe** helyezését támogatja, és a hitelesítéssel rendelkező felhasználók automatikusan jönnek létre az alkalmazásban. Az első bejelentkezéskor egyszer kell kitöltenie az adatokat az alárendelt fiók rendszerbe való aktiválásához.
Ez a szakasz nem tartalmaz műveleti elemeket.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a TextMagic csempére kattint, automatikusan be kell jelentkeznie arra a TextMagic, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A TextMagic kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

