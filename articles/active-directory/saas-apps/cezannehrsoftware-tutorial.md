---
title: 'Oktatóanyag: Azure Active Directory integráció a Cezanne HR szoftverrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Cezanne HR-szoftverek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa689e6138f8d965e59f7cfa7a85e0835301086c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158700"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Oktatóanyag: Azure Active Directory integráció a Cezanne HR szoftverrel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Cezanne HR-szoftvereket Azure Active Directory (Azure AD) használatával.
A Cezanne HR-szoftverek Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Cezanne HR szoftverekhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Cezanne HR szoftverbe (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Cezanne HR szoftverrel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Cezanne HR szoftver egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Cezanne HR szoftver támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Cezanne HR-szoftverek hozzáadása a katalógusból

A Cezanne HR-szoftverek Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cezanne HR-szoftvert a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A Cezanne HR-szoftverek katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **CEZANNE HR szoftver**kifejezést, válassza a **Cezanne HR-szoftver** az eredmény panelről lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Cezanne HR-szoftverek az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a Cezanne HR szoftverrel konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Cezanne HR-szoftverek kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az Cezanne HR szoftverrel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[CEZANNE HR szoftver egyszeri bejelentkezésének konfigurálása](#configure-cezanne-hr-software-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. A **[CEZANNE HR szoftver tesztelési felhasználójának létrehozása](#create-cezanne-hr-software-test-user)** – ha a Cezanne HR szoftverben a felhasználó Azure ad-Britta csatolt, Simon-t tartalmazó partnere van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az Cezanne HR szoftverrel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Cezanne HR szoftver** -integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Cezanne HR-szoftverek tartománya és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be a következő URL-címet: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és a válasz URL-címével. Az értékek megszerzéséhez lépjen kapcsolatba a [CEZANNE HR szoftverekkel](https://cezannehr.com/services/support/) foglalkozó ügyfélszolgálatával.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **CEZANNE HR szoftver beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-cezanne-hr-software-single-sign-on"></a>A Cezanne HR szoftver egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Cezanne HR-szoftverek bérlője számára.

2. A bal oldali navigációs panelen kattintson a **rendszer beállítása**elemre. Lépjen a **biztonsági beállítások**menüpontra. Ezután navigáljon az **egyszeri bejelentkezési konfigurációhoz**.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. A **felhasználók bejelentkezésének engedélyezése a következő egyszeri bejelentkezés (SSO) használatával** lapon jelölje be az **SAML 2,0** jelölőnégyzetet, és válassza a **Speciális konfiguráció** lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Kattintson az **új hozzáadása** gombra.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Hajtsa végre a következő lépéseket az **SAML 2,0 Identity Providers** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Adja meg az identitás szolgáltatójának nevét a **megjelenítendő név**mezőben.

    b. Az **entitás-azonosító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    c. Módosítsa az **SAML-kötést** "post" értékre.

    d. A **biztonsági jogkivonat szolgáltatás végpontja** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.

    e. A felhasználói azonosító attribútum neve szövegmezőbe írja be a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`értéket.

    f. Kattintson a **feltöltés** ikonra a letöltött tanúsítvány Azure Portal való feltöltéséhez.

    g. Kattintson az **OK** gombra.

6. Kattintson a **Save (Mentés** ) gombra.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon\@yourcompanydomain. Extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Cezanne HR-szoftverek elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Cezanne HR-szoftverek**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **CEZANNE HR-szoftverek**elemet.

    ![Az Cezanne HR szoftver hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-cezanne-hr-software-test-user"></a>Cezanne HR szoftver tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Cezanne HR-szoftverbe, a Cezanne HR szoftverbe kell azokat kiépíteni. Cezanne HR-szoftverek esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Cezanne HR Software céges webhelyére rendszergazdaként.

2. A bal oldali navigációs panelen kattintson a **rendszer beállítása**elemre. Lépjen a **felhasználók kezelése**gombra. Ezután navigáljon az **új felhasználó hozzáadása**lehetőséghez.

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Új felhasználó")

3. A **személy részletei** szakaszban hajtsa végre az alábbi lépéseket:

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Új felhasználó")

    a. **Belső felhasználó** beállítása a kikapcsolt értékre.

    b. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét, például a **Britta**nevet.  

    c. A **vezetékneve** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon**nevet.

    d. Az **e-mail** szövegmezőbe írja be a felhasználó (például Brittasimon@contoso.com) e-mail-címét.

4. A **fiókadatok** szakaszban hajtsa végre az alábbi lépéseket:

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Új felhasználó")

    a. A **Felhasználónév** szövegmezőbe írja be a felhasználó (például Brittasimon@contoso.com) e-mail-címét.

    b. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    c. Válassza ki a **HR Professional** **biztonsági szerepkört**.

    d. Kattintson az **OK** gombra.

5. Navigáljon az **egyszeri bejelentkezés** lapra, és válassza az **új hozzáadása** lehetőséget az **SAML 2,0 azonosítók** területen.

    ![Felhasználói](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Felhasználó")

6. Válassza ki **az identitás-** szolgáltatót, és a **felhasználói azonosító**szövegmezőben adja meg a Britta Simon-fiók e-mail-címét.

    ![Felhasználói](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Felhasználó")

7. Kattintson a **Save (Mentés** ) gombra.

    ![Felhasználói](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Felhasználó")

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Cezanne HR szoftver csempére kattint, automatikusan be kell jelentkeznie a Cezanne HR szoftverbe, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
