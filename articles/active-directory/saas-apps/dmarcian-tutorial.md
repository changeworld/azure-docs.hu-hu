---
title: 'Oktatóanyag: Azure Active Directory integráció a dmarcian-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és dmarcian között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602c885deca429b56417181971ced495831ba5d3
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823699"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Oktatóanyag: A dmarcian integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a dmarcian a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az dmarcian-t az Azure AD-vel, a következőket teheti:

* A dmarcian-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a dmarcian az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* dmarcian egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* a dmarcian támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO

## <a name="adding-dmarcian-from-the-gallery"></a>Dmarcian hozzáadása a gyűjteményből

A dmarcian Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a dmarcian a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **dmarcian** kifejezést a keresőmezőbe.
1. Válassza ki a **dmarcian** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálja és tesztelje az Azure AD SSO-t a dmarcian a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a dmarcian-ben.

Az Azure AD SSO és a dmarcian konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[DMARCIAN SSO konfigurálása](#configure-dmarcian-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre dmarcian-teszt felhasználót](#create-dmarcian-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-dmarcian rendelkezik.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **dmarcian** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Ezek az értékek nem valósak. Ezeket az értékeket a tényleges azonosító, a válasz URL-cím és a bejelentkezési URL-cím alapján fogja frissíteni, amelyet az oktatóanyag későbbi részében ismertetünk.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Dmarcian SSO konfigurálása

1. A dmarcian belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés dmarcian** gombra a dmarcian alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a dmarcian való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a dmarcian, nyisson meg egy új böngészőablakot, és jelentkezzen be a dmarcian vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Kattintson a **profil** elemre a jobb felső sarokban, és navigáljon a **Beállítások**pontra.

    ![A beállítások](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Görgessen le, és kattintson az **egyszeri bejelentkezés** szakaszra, majd a **Konfigurálás**elemre.

    ![Az egyetlen](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Az **SAML egyszeri bejelentkezés** lapon állítsa be az állapotot **engedélyezve** értékre, és hajtsa végre a következő lépéseket:

    ![A hitelesítés](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Az **Dmarcian hozzáadása az identitás** -szolgáltatóhoz szakaszban kattintson a **Másolás** elemre, és másolja be a példányhoz tartozó **felhasználói szolgáltatás URL-címét** , és illessze be a **válasz URL-címe** szövegmezőbe a Azure Portal alapszintű SAML- **konfiguráció szakaszában** .

    * Az **Dmarcian hozzáadása az identitás** -szolgáltatóhoz szakaszban kattintson a **Másolás** elemre a példányhoz tartozó **entitás-azonosító** másolásához, majd illessze be az **azonosító** szövegmezőbe az alapszintű **SAML-konfigurációs szakaszban** Azure Portal.

    * A **hitelesítés beállítása** szakasz **identitás-szolgáltató metaadatai** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét**, amelyet a Azure Portalból másolt.

    * A **hitelesítés beállítása** szakaszban a **Attribute utasítások** szövegmezőbe illessze be az URL-címet`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * A **bejelentkezési URL-cím beállítása** szakaszban másolja be a példányhoz tartozó **bejelentkezési URL-címet** , és ILLESSZE be a **bejelentkezési URL** SZÖVEGMEZŐbe az alapszintű **SAML-konfiguráció szakaszának** Azure Portal.

        > [!Note]
        > A **bejelentkezési URL-címet** a szervezete szerint módosíthatja.

    * Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a dmarcian.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **dmarcian**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-dmarcian-test-user"></a>Dmarcian-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a dmarcian, a dmarcian kell kiépíteni őket. A dmarcian-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a dmarcian biztonsági rendszergazdaként.

2. Kattintson a jobb felső sarokban található **profil** elemre, és navigáljon a **felhasználók kezeléséhez**.

    ![A felhasználó](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Az **egyszeri bejelentkezés felhasználói** szakasz jobb oldalán kattintson az **új felhasználó hozzáadása**elemre.

    ![A felhasználó hozzáadása](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Az **új felhasználó hozzáadása** felugró ablakban végezze el a következő lépéseket:

    ![Az új felhasználó](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Az **új felhasználói e-mail** szövegmezőbe írja be a felhasználó, például a **brittasimon\@contoso.com**-e-mail-címét.

    b. Ha rendszergazdai jogosultságot szeretne adni a felhasználónak, válassza a rendszergazda **felhasználó létrehozása**lehetőséget.

    c. Kattintson a **felhasználó hozzáadása**.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a dmarcian csempére kattint, automatikusan be kell jelentkeznie arra a dmarcian, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

