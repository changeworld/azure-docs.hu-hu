---
title: 'Oktatóanyag: Azure Active Directory integráció a Thoughtworks keveredik | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Thoughtworks között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a12d4dca61734275ef0e56dfe2a800c64dc52540
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233292"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Oktatóanyag: Azure Active Directory integráció a Thoughtworks keveredik

Ebből az oktatóanyagból megismerheti, hogyan integrálhatja a Thoughtworks keveredik a Azure Active Directory (Azure AD) szolgáltatással.
A Thoughtworks keveredik az Azure AD-vel az alábbi előnyökkel jár:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Thoughtworks.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Thoughtworks keveredik (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Thoughtworks-sel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Thoughtworks-alapú egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Thoughtworks keveredik az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Thoughtworks-gyűjtemény hozzáadása a katalógusból

A Thoughtworks az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Thoughtworks keveredik a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az alábbi lépések végrehajtásával adhat hozzá Thoughtworks a gyűjteményhez:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **Thoughtworks keveredik**, válassza az **Thoughtworks keveredik** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Thoughtworks keveredik az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Thoughtworks keveredik a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Thoughtworks keveredik a kapcsolódó felhasználó közötti kapcsolati kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Thoughtworks-mel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az Thoughtworks](#configure-thoughtworks-mingle-single-sign-on)** -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Thoughtworks kevert teszt felhasználóval](#create-thoughtworks-mingle-test-user)** , hogy a Britta Simon, a Thoughtworks-hez tartozó, a felhasználó Azure ad-képviseletéhez kapcsolódó partnere legyen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az Thoughtworks-mel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Thoughtworks keveredik** az alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A Thoughtworks a tartomány és az URL-címek egyszeri bejelentkezési információit vegyíti](common/sp-signonurl.png)

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [Thoughtworks-csoporttal](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. A **Thoughtworks keveredett beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Thoughtworks-alapú egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be a **Thoughtworks** a vállalati webhelyre rendszergazdaként.

2. Kattintson a **felügyelet** lapra, majd az SSO- **konfiguráció**elemre.
   
    ![Adminisztrátor lap](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO-konfiguráció")

3. Az **SSO-konfiguráció** szakaszban hajtsa végre a következő lépéseket:
   
    ![SSO-konfiguráció](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO-konfiguráció")
    
    a. A metaadat-fájl feltöltéséhez kattintson a **fájl kiválasztása**lehetőségre. 

    b. Kattintson a **módosítások mentése**gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: brittasimon@yourcompanydomain.extension. Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Thoughtworks keveredik.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Thoughtworks keveredik**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Thoughtworks keveredik**elemet.

    ![Az Thoughtworks keveredik hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-thoughtworks-mingle-test-user"></a>Thoughtworks-teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók be tudjanak jelentkezni, a Azure Active Directory felhasználónevek használatával kell kiépíteni őket a Thoughtworks keveredik az alkalmazásba. A Thoughtworks keveredik esetén a kiépítés manuális feladat.

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Thoughtworks a vállalati webhelyre rendszergazdaként.

2. Kattintson a **profil**elemre.
   
    ![Az első projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "Az első projekt")

3. Kattintson a **rendszergazda** lapra, majd a **felhasználók**elemre.
   
    ![Felhasználók](./media/thoughtworks-mingle-tutorial/ic785161.png "Felhasználók")

4. Kattintson az **új felhasználó**elemre.
   
    ![Új felhasználó](./media/thoughtworks-mingle-tutorial/ic785162.png "Új felhasználó")

5. Az **új felhasználó** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Új felhasználó párbeszédpanel](./media/thoughtworks-mingle-tutorial/ic785163.png "Új felhasználó")  
 
    a. Írja be a **bejelentkezési nevet**, a **megjelenítendő nevet**, **válassza a jelszó lehetőséget**, adjon meg egy érvényes Azure ad-fiók **jelszavát** , amelyet a kapcsolódó szövegmezőbe szeretne kiépíteni. 

    b. A **felhasználó típusa**beállításnál válassza a **teljes felhasználó**lehetőséget.

    c. Kattintson **a profil létrehozása**elemre.

>[!NOTE]
>Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Thoughtworks által biztosított Thoughtworks-létrehozási eszközt vagy API-t használhat.
> 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Thoughtworks keveredik csempe csempére kattint, akkor automatikusan be kell jelentkeznie arra a Thoughtworks, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

