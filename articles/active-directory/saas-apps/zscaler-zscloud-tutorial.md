---
title: 'Oktatóanyag: Azure Active Directory integráció a Zscaler ZSCloud | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Zscaler ZSCloud között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 43d7e58f0c267afe8a22c217d9800abb041df8cb
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "68723062"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Oktatóanyag: Azure Active Directory integráció a Zscaler ZSCloud

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Zscaler-ZSCloud a Azure Active Directory (Azure AD) szolgáltatással.
A Zscaler ZSCloud és az Azure AD integrálásával az alábbi előnyökkel jár:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Zscaler ZSCloud.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Zscaler ZSCloud (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Zscaler ZSCloud való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Zscaler ZSCloud egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Zscaler ZSCloud támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A Zscaler ZSCloud **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Zscaler-ZSCloud hozzáadása a gyűjteményből

A Zscaler-ZSCloud Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Zscaler-ZSCloud a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Zscaler ZSCloud a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **Zscaler ZSCloud**, válassza a **Zscaler ZSCloud** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Zscaler ZSCloud az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Zscaler ZSCloud-mel konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Zscaler ZSCloud kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Zscaler-ZSCloud való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Zscaler ZSCloud egyszeri bejelentkezés konfigurálása](#configure-zscaler-zscloud-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Zscaler-ZSCloud-teszt felhasználó létrehozása](#create-zscaler-zscloud-test-user)** – a Britta Simon-nek a felhasználó Azure ad-képviseletéhez kapcsolódó Zscaler-ZSCloud.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Zscaler-ZSCloud való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Zscaler ZSCloud** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Zscaler ZSCloud tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **bejelentkezési URL** szövegmezőbe írja be a felhasználók által a ZScaler ZSCloud alkalmazásba való bejelentkezéshez használt URL-címet.

    > [!NOTE]
    > Frissítenie kell az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez forduljon a Zscaler ZSCloud-ügyfélszolgálati [csapatához](https://help.zscaler.com/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. A Zscaler ZSCloud-alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A fentieken kívül a Zscaler ZSCloud alkalmazás néhány további attribútumot vár, amelyeket az SAML-válaszban vissza kell adni. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:
    
    | Név | Forrás attribútum |
    | ---------| ------------ |
    | memberOf     | User. assignedroles |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.
    
    f. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > [Ide kattintva](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) megtudhatja, hogyan konfigurálhatja a szerepkört az Azure ad-ben

7. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

8. A **Zscaler ZSCloud beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-zscaler-zscloud-single-sign-on"></a>Zscaler-ZSCloud egyszeri bejelentkezés konfigurálása

1. A Zscaler-ZSCloud belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítő Zscaler ZSCloud** lehetőségre a Zscaler ZSCloud alkalmazásra. A Zscaler ZSCloud való bejelentkezéshez adja meg a rendszergazdai hitelesítő adatokat. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Egyszeri bejelentkezés beállítása](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Zscaler ZSCloud, nyisson meg egy új böngészőablakot, és jelentkezzen be a Zscaler ZSCloud vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Lépjen az **adminisztráció > hitelesítés > hitelesítési beállítások** lapra, és hajtsa végre a következő lépéseket:
   
    ![Felügyeleti](./media/zscaler-zscloud-tutorial/ic800206.png "Felügyelet")

    a. A hitelesítés típusa területen válassza az **SAML**elemet.

    b. Kattintson az **SAML konfigurálása**elemre.

5. Az **SAML szerkesztése** ablakban hajtsa végre a következő lépéseket:, majd kattintson a Mentés gombra.  
            
    ![Felhasználók kezelése & hitelesítéssel](./media/zscaler-zscloud-tutorial/ic800208.png "Felhasználók kezelése & hitelesítéssel")
    
    a. Az **SAML-portál URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-címet** .

    b. A **bejelentkezési név attribútum** szövegmezőbe írja be a **NameID**nevet.

    c. Kattintson a **feltöltés**gombra, és töltse fel a **nyilvános SSL-tanúsítványban**Azure Portal letöltött Azure SAML-aláíró tanúsítványt.

    d. Az **SAML automatikus kiépítés engedélyezése**.

    e. A **felhasználó megjelenített név attribútuma** szövegmezőbe írja be a **DisplayName** értéket, ha engedélyezni szeretné az SAML automatikus kiépítési lehetőséget a DisplayName attribútumokhoz.

    f. A **Csoportnév-attribútum** szövegmezőbe írja be a **memberof** értéket, ha engedélyezni szeretné az SAML automatikus kiépítési lehetőséget a memberOf attribútumaihoz.

    g. A **részleg neve attribútumban** adja meg a **részleget** , ha engedélyezni szeretné az SAML automatikus kiépítés részleg attribútumait.

    h. Kattintson a **Save** (Mentés) gombra.

6. A **felhasználói hitelesítés konfigurálása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Felügyelet](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. Vigye az egérmutatót a bal alsó sarokban található **aktiválási** menü fölé.

    b. Kattintson az **aktiválás**gombra.

## <a name="configuring-proxy-settings"></a>Proxybeállítások konfigurálása
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Proxybeállítások konfigurálása az Internet Explorerben

1. Indítsa el az **Internet Explorert**.

2. **Az Internetbeállítások párbeszédpanel** megnyitásához válassza az **eszközök** menü **Internetbeállítások** elemét.   
    
     ![Internetbeállítások](./media/zscaler-zscloud-tutorial/ic769492.png "Internetbeállítások")

3. Kattintson a **kapcsolatok** fülre.   
  
     ![Kapcsolatok](./media/zscaler-zscloud-tutorial/ic769493.png "Connections (Kapcsolatok)")

4. A LAN- **Beállítások** párbeszédpanel megnyitásához kattintson a **LAN-beállítások** elemre.

5. A proxykiszolgáló szakaszban hajtsa végre a következő lépéseket:   
   
    ![Proxykiszolgáló](./media/zscaler-zscloud-tutorial/ic769494.png "Proxykiszolgáló")

    a. Válassza **a proxykiszolgáló használata a LAN**-hoz lehetőséget.

    b. A címek szövegmezőbe írja be az **átjáró értéket. Zscaler ZSCloud.net**.

    c. A port szövegmezőbe írja be a következőt: **80**.

    d. Válassza **a proxykiszolgáló kihagyása helyi címeknél**lehetőséget.

    e. A **helyi hálózati (LAN) beállítások** párbeszédpanel bezárásához kattintson **az OK** gombra.

6. Az **Internetbeállítások** párbeszédpanel bezárásához kattintson **az OK** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: brittasimon@yourcompanydomain.extension. Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Zscaler-ZSCloud.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Zscaler ZSCloud**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Zscaler ZSCloud**elemet.

    ![Az Zscaler ZSCloud hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza ki a **Britta Simon** elemet a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. A **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő felhasználói szerepkört a listában, majd kattintson a képernyő alján található **kiválasztás** gombra.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

    >[!NOTE]
    >Az alapértelmezett hozzáférési szerepkör nem támogatott, mivel ez megszakítja a kiépítés folyamatát, ezért az alapértelmezett szerepkör nem választható ki a felhasználó kiosztása során.

### <a name="create-zscaler-zscloud-test-user"></a>Zscaler ZSCloud-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Zscaler ZSCloud-ben. A Zscaler ZSCloud támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a Zscaler ZSCloud, a hitelesítés után létrejön egy új.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [Zscaler ZSCloud támogatási csapatával](https://help.zscaler.com/).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Zscaler ZSCloud csempére kattint, automatikusan be kell jelentkeznie arra a Zscaler-ZSCloud, amelyhez be szeretné állítani az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

