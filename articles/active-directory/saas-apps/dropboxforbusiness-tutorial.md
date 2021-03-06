---
title: 'Oktatóanyag: Azure Active Directory integráció a Dropbox for Business szolgáltatással | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a Dropbox for Business között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: df7fc366c5087e66c3022c212870397d77e6e34d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046764"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Oktatóanyag: a Dropbox vállalati integrációja Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Dropbox for businesst a Azure Active Directory (Azure AD) szolgáltatással. Ha az Azure AD-vel integrálja a Dropbox for businesst, a következőket teheti:

* Hozzáférés az Azure AD-hez, aki hozzáfér a Dropbox for Business szolgáltatáshoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Dropboxba a vállalati Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* Dropbox for Business egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

* Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A Dropbox for Business támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A Dropbox for Business támogatja a [felhasználók automatikus üzembe](dropboxforbusiness-tutorial.md) helyezését és megszüntetését
* A Dropbox konfigurálása után kikényszerítheti a munkamenet-vezérlést, amely a szervezet bizalmas adatainak valós idejű kiszűrése és beszivárgását is biztosítja. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-for-business-from-the-gallery"></a>A Dropbox for Business hozzáadása a katalógusból

Ha a Dropbox for Business integrációját szeretné konfigurálni az Azure AD-ben, akkor a katalógusból hozzá kell adnia a Dropbox for Business szolgáltatást a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Dropbox for Business** kifejezést a keresőmezőbe.
1. Válassza a **Dropbox for Business** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Az Azure AD SSO konfigurálása és tesztelése a Dropbox for Business használatával a **Britta Simon**nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a vállalati Dropboxban.

Az Azure AD SSO és a Business Dropbox vállalati verziójának konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.    
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
1. A **[Dropbox for Business SSO konfigurálása](#configure-dropbox-for-business-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Dropbox létrehozása az üzleti teszteléshez felhasználó](#create-dropbox-for-business-test-user)** – a Britta Simon a dropboxban található, a felhasználó Azure ad-képviseletéhez kapcsolódó partnere.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Dropbox for Business** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.dropbox.com/sso/<id>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be a következő értéket: `Dropbox`

    > [!NOTE]
    > Az előző bejelentkezési URL-cím értéke nem valós érték. A tényleges bejelentkezési URL-címmel frissítenie kell az értéket, amelyet az oktatóanyag későbbi részében ismertetünk.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. A **Dropbox for Business beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL


### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Dropbox vállalatoknak.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Dropbox vállalatoknak**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-dropbox-for-business-sso"></a>A Dropbox konfigurálása vállalati egyszeri bejelentkezéshez

1. Ha automatizálni szeretné a vállalati Dropboxon belüli konfigurációt, telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Business Dropbox for Business** lehetőségre, majd a Dropbox for Business alkalmazást irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Dropbox vállalati verzióba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-8-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Dropbox for Business szolgáltatást, nyisson meg egy új böngészőablakot, és lépjen a Dropbox for Business bérlőre, és jelentkezzen be a Dropbox for Business-bérlőbe. és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/ic769509.png "Egyszeri bejelentkezés konfigurálása")

4. Kattintson a **felhasználó ikonra** , és válassza a **Beállítások** lapot.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure1.png "Egyszeri bejelentkezés konfigurálása")

5. A bal oldali navigációs ablaktáblán kattintson a **felügyeleti konzol**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure2.png "Egyszeri bejelentkezés konfigurálása")

6. A **felügyeleti konzolon**kattintson a bal oldali navigációs ablaktábla **Beállítások** elemére.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure3.png "Egyszeri bejelentkezés konfigurálása")

7. Válassza az **egyszeri bejelentkezés** lehetőséget a **hitelesítés** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure4.png "Egyszeri bejelentkezés konfigurálása")

8. Az **egyszeri bejelentkezés** szakaszban hajtsa végre a következő lépéseket:  

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure5.png "Egyszeri bejelentkezés konfigurálása")

    a. Válassza a **kötelező** lehetőséget a legördülő listából az **egyszeri bejelentkezéshez**.

    b. Kattintson a **bejelentkezési URL-cím hozzáadása** elemre, majd az **Identity Provider bejelentkezési URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt, majd válassza a **kész**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure6.png "Egyszeri bejelentkezés konfigurálása")

    c. Kattintson a **tanúsítvány feltöltése**elemre, majd keresse meg a **Base64-kódolású tanúsítványfájl** , amelyet a Azure Portal letöltött.

    d. Kattintson a **Másolás hivatkozásra** , és illessze be a másolt értéket a **Dropbox for Business domain és URLs** szakasz **bejelentkezési URL-** szövegmezőbe a Azure Portal.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="create-dropbox-for-business-test-user"></a>Dropbox létrehozása üzleti tesztelési felhasználó számára

Ebben a szakaszban a Britta Simon nevű felhasználó jön létre a Dropbox vállalatnál. A Dropbox for Business támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Dropbox vállalatnál, akkor a hitelesítés után létrejön egy újat.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Dropbox for Business ügyfél-támogatási csoporthoz](https://www.dropbox.com/business/contact) .

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ha a hozzáférési panelen kiválasztja a Business Dropbox (üzlet) csempét, automatikusan be kell jelentkeznie a Dropbox for Business szolgáltatásba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Dropbox és a speciális láthatóság és vezérlések elleni védelem](https://docs.microsoft.com/cloud-app-security/protect-dropbox)