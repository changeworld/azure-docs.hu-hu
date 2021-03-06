---
title: 'Oktatóanyag: Azure Active Directory integráció a Sage Intacct | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Sage Intacct között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b28390dba009226d493f5bfc6a5270b067f5bba0
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "73570475"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Oktatóanyag: a Sage Intacct integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Sage Intacct Azure Active Directory (Azure AD) szolgáltatással. Ha az Azure AD-vel integrálja a Sage Intacct-t, a következőket teheti:

* Az Azure AD-ben a Sage Intacct-hez hozzáférő vezérlő.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Sage Intacct az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Sage Intacct egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Sage Intacct támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-sage-intacct-from-the-gallery"></a>Sage-Intacct hozzáadása a katalógusból

A Sage Intacct Azure AD-be való integrálásának konfigurálásához hozzá kell adnia egy Sage Intacct a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Sage Intacct** kifejezést a keresőmezőbe.
1. Válassza ki a **Sage Intacct** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sage-intacct"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Sage Intacct

Konfigurálja és tesztelje az Azure AD SSO-t a Sage Intacct egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Sage Intacct-ben.

Az Azure AD SSO és a Sage Intacct konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
2. A **[Sage INTACCT SSO konfigurálása](#configure-sage-intacct-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Sage Intacct-teszt felhasználót](#create-sage-intacct-test-user)** – hogy rendelkezzen egy, a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli Intacct.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **bölcs Intacct** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be a következő URL-címet: `https://www.intacct.com/ia/acct/sso_response.phtml`

1. A Sage Intacct alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a Sage Intacct alkalmazás néhány további attribútumot vár az SAML-válaszba való visszatéréshez. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Név  |  Forrás attribútum|
    | ---------------| --------------- |
    | Vállalat neve | **Sage Intacct vállalati azonosító** |
    | név | Az értéknek meg kell egyeznie a Sage Intacct **felhasználói azonosítóval**, amelyet a **create Sage Intacct test User (bölcsek csoport létrehozása) felhasználói szakaszban**adhat meg, amelyet az oktatóanyag későbbi részében talál |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | Az értéknek meg kell egyeznie a Sage Intacct **összevont SSO felhasználói azonosítóval**, amelyet a **create Sage Intacct test User (zsálya létrehozása) felhasználói szakaszban**adhat meg, amelyet az oktatóanyag későbbi részében ismertetünk. |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson **az OK** gombra

    g. Kattintson a **Save** (Mentés) gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Sage Intacct beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a Sage Intacct való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **zsálya Intacct**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sage-intacct-sso"></a>A Sage Intacct SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Sage Intacct vállalati webhelyre rendszergazdaként.

1. Kattintson a **vállalat** lapra, majd a **vállalati adatok**elemre.

    ![Vállalati](./media/intacct-tutorial/ic790037.png "vállalat")

1. Kattintson a **Biztonság** fülre, majd a **Szerkesztés**elemre.

    ![Biztonsági](./media/intacct-tutorial/ic790038.png "Biztonság")

1. Az **egyszeri bejelentkezés (SSO)** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés](./media/intacct-tutorial/ic790039.png "egyszeri") bejelentkezéssel

    a. Válassza **az egyszeri bejelentkezés engedélyezése**lehetőséget.

    b. **Identitás-szolgáltató típusaként**válassza az **SAML 2,0**lehetőséget.

    c. A **kiállító URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító**értékét.

    d. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    e. Nyissa meg a **Base-64** kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be a **tanúsítvány** mezőbe.

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="create-sage-intacct-test-user"></a>Sage Intacct-teszt felhasználó létrehozása

Ha be szeretné állítani az Azure AD-felhasználók számára, hogy bejelentkezzenek a Sage Intacct, ki kell építeni a Sage Intacct-be. A Sage Intacct esetében a kiépítés manuális feladat.

**A felhasználói fiókok kiépítéséhez hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Sage Intacct** -bérlőbe.

1. Kattintson a **vállalat** lapra, majd a **felhasználók**elemre.

    ![Felhasználók](./media/intacct-tutorial/ic790041.png "")

1. Kattintson a **Hozzáadás** fülre.

    ![](./media/intacct-tutorial/ic790042.png "Hozzáadás") hozzáadása

1. A **felhasználói adatok** szakaszban hajtsa végre a következő lépéseket:

    ![Felhasználói adatok](./media/intacct-tutorial/ic790043.png "felhasználói adatai")

    a. Adja meg a felhasználói **adatok** szakaszban kiépíteni kívánt Azure ad-fiók **felhasználói azonosítóját**, **vezetéknevét** **, utónevét,** **e-mail-címét**, **címét**és **telefonszámát** .

    > [!NOTE]
    > Győződjön meg arról, hogy a fenti **felhasználói azonosító** és a **forrás attribútum** értéke, amely a Azure Portal **felhasználói attribútumok** szakaszának **Name** attribútumával van leképezve, azonosnak kell lennie.

    b. Válassza ki a létrehozni kívánt Azure AD-fiók **rendszergazdai jogosultságait** .

    c. Kattintson a **Save** (Mentés) gombra. 
    
    d. Az Azure AD-fiók tulajdonosa egy e-mailt kap, és egy hivatkozást követ, amely megerősíti a fiókját, mielőtt az aktívvá válna.

1. Kattintson az **egyszeri bejelentkezés** fülre, és győződjön meg arról, hogy az **összevont egyszeri bejelentkezés felhasználói azonosítója** az alábbi képernyőképen, valamint a **forrás attribútum** értéke, amely a Azure Portal **felhasználói attribútumok** szakaszának `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` van leképezve, azonosnak kell lennie.

    ![Felhasználói adatok](./media/intacct-tutorial/ic790044.png "felhasználói adatai")

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez használhatja a Sage Intacct által biztosított más Sage Intacct felhasználói fiókok létrehozására szolgáló eszközöket vagy API-kat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a Sage Intacct csempére kattint, automatikusan be kell jelentkeznie arra a Sage-Intacct, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

