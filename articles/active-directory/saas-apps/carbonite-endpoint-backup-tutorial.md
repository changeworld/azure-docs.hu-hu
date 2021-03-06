---
title: 'Oktatóanyag: Azure Active Directory integráció a Carbonite Endpoint Backup szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Carbonite-végpont biztonsági mentése között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b78091f1-2f06-4c2c-8541-72aee0b5a322
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e078cb7daa787b9fe5e8bc996b36f0fef198f41c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879673"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Oktatóanyag: A Carbonite-végpont biztonsági mentésének integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Carbonite-végpontok biztonsági mentését Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Carbonite-végpont biztonsági mentését, a következőket teheti:

* A Carbonite-végpont biztonsági mentéséhez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Carbonite Endpoint Backup szolgáltatásba az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Carbonite Endpoint Backup egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Carbonite Endpoint Backup támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>A Carbonite-végpont biztonsági mentésének hozzáadása a katalógusból

A Carbonite-végpont biztonsági mentésének Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Carbonite-végpont biztonsági mentését a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Carbonite Endpoint Backup** kifejezést a keresőmezőbe.
1. Válassza ki a **Carbonite-végpont biztonsági mentését** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálja és tesztelje az Azure AD SSO-t a Carbonite Endpoint Backup segítségével egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Carbonite Endpoint Backup szolgáltatásban.

Az Azure AD SSO és a Carbonite-végpont biztonsági mentésének konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. A **[Carbonite-végpont biztonsági mentésének SSO](#configure-carbonite-endpoint-backup-sso)** -beállítása – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. A **[Carbonite-végpont biztonsági mentési tesztelési felhasználójának létrehozása](#create-carbonite-endpoint-backup-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó, B. Simon-beli, a Carbonite-végpont biztonsági másolatában található
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/), a **Carbonite Endpoint Backup** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az alapszintű **SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az alábbi URL-címek egyikét:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com`|
    | `https://red-apac.mysecuredatavault.com`|
    | `https://red-fr.mysecuredatavault.com`|
    | `https://red-emea.mysecuredatavault.com`|
    | `https://kamino.mysecuredatavault.com`|
    | | |

    b. A **Válasz URL-címe** szövegmezőbe írja be az alábbi URL-címek egyikét:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | | |

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az alábbi URL-címek egyikét:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/`|
    | `https://red-apac.mysecuredatavault.com/`|
    | `https://red-fr.mysecuredatavault.com/`|
    | `https://red-emea.mysecuredatavault.com/`|
    | | |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. A **Carbonite-végpont biztonsági mentésének beállítása** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>A Carbonite-végpont biztonsági mentési egyszeri bejelentkezésének konfigurálása

1. A Carbonite-végpont biztonsági másolatán belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Carbonite Endpoint Backup beállítása** gombra a Carbonite-végpont biztonsági mentési alkalmazásának irányításához. Itt adja meg a rendszergazdai hitelesítő adatokat a Carbonite Endpoint Backup szolgáltatásba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Carbonite-végpont biztonsági mentését, nyisson meg egy új böngészőablakot, és jelentkezzen be a Carbonite-végpont biztonsági mentési céges webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Kattintson a **vállalatra** a bal oldali ablaktáblán.

    ![Carbonite-végpont biztonsági mentési konfigurációja ](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Kattintson az **egyszeri bejelentkezés**lehetőségre.

    ![Carbonite-végpont biztonsági mentési konfigurációja ](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Kattintson az **Engedélyezés** elemre, majd kattintson a **beállítások szerkesztése** elemre a konfiguráláshoz.

    ![Carbonite-végpont biztonsági mentési konfigurációja ](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Az **egyszeri bejelentkezési** beállítások oldalon hajtsa végre a következő lépéseket:

    ![Carbonite-végpont biztonsági mentési konfigurációja ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Az **identitás-szolgáltató neve** szövegmezőbe illessze be azt az **Azure ad-azonosító** értéket, amelyet a Azure Portal másolt.

    1. Az **Identity Provider URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    1. Kattintson a **fájl kiválasztása** lehetőségre a letöltött **tanúsítvány (Base64)** fájljának a Azure Portal való feltöltéséhez.

    1. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban engedélyezi, hogy a B. Simon az Azure egyszeri bejelentkezést használja a Carbonite-végpont biztonsági mentéshez való hozzáférés megadásával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza ki a **Carbonite-végpont biztonsági mentése**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-carbonite-endpoint-backup-test-user"></a>Carbonite-végpont biztonsági mentési tesztelési felhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be a Carbonite-végpont biztonsági mentési céges webhelyre rendszergazdaként.

1. Kattintson a bal oldali ablaktáblán a **felhasználók** elemre, majd kattintson a **felhasználó hozzáadása**elemre.

    ![Felhasználó hozzáadása a Carbonite Endpoint Backup szolgáltatásban](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. A **felhasználó hozzáadása** oldalon hajtsa végre a következő lépéseket:

    ![Felhasználó hozzáadása a Carbonite Endpoint Backup szolgáltatásban](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Adja meg a felhasználó **e-mail-címét**, **utónevét**és **vezetéknevét** , és adja meg a szükséges engedélyeket a felhasználónak a szervezeti követelményeknek megfelelően.

    1. Kattintson a **Felhasználó hozzáadása** parancsra.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Carbonite-végpont biztonsági mentési csempére kattint, automatikusan be kell jelentkeznie a Carbonite-végpont biztonsági másolatából, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)