---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a méhész Azure AD SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a méhész Azure AD SSO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26c8789a-1bfe-4371-94d5-febe34f0b0e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbac5f6b26ff5df9114eebdf850faff263f7cd78
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373169"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beekeeper-azure-ad-sso"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a méhész Azure AD SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a méhész Azure AD SSO-t Azure Active Directory (Azure AD) használatával. Ha integrálja a méhész Azure AD SSO-t az Azure AD-vel, a következőket teheti:

* A méhész Azure AD SSO-hoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a méhész Azure AD SSO-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A méhész Azure AD SSO egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A méhész Azure AD SSO támogatja **az SP-t és a identitásszolgáltató** kezdeményezett SSO-t
* A méhész Azure AD SSO **csak időben támogatja a** felhasználók üzembe helyezését
* Miután konfigurálta a méhész Azure AD SSO-t, kikényszerítheti a munkamenet-vezérlőket, amelyekkel valós időben biztosíthatja a szervezete bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-beekeeper-azure-ad-sso-from-the-gallery"></a>A méhész Azure AD SSO hozzáadása a katalógusból

A méhész Azure ad SSO Azure ad-be való integrálásának konfigurálásához hozzá kell adnia a méhész Azure AD SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **méhész Azure ad SSO** kifejezést a keresőmezőbe.
1. Válassza ki a **méhész Azure ad SSO** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beekeeper-azure-ad-sso"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a méhész Azure AD SSO-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a méhész Azure AD SSO használatával egy **B. Simon**nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a méhész Azure AD SSO-ban.

Az Azure AD SSO és a méhész Azure AD SSO konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[méhész Azure ad SSO konfigurálása](#configure-beekeeper-azure-ad-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre egy méhész Azure ad SSO-teszt felhasználót](#create-beekeeper-azure-ad-sso-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-beli, a méhész Azure ad SSO-beli partnere.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **méhész Azure ad SSO** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal** rendelkezik, és **identitásszolgáltató** kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    a. Kattintson a **metaadat-fájl feltöltése**elemre.

    ![Metaadat-fájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés**elemre.

    ![metaadat-fájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után a rendszer az alapszintű SAML-konfiguráció szakaszban automatikusan feltölti az **azonosítót** és a **Válasz URL-** értékeket.

    ![image](common/idp-intiated.png)

    > [!Note]
    > Ha az **azonosító** és a **Válasz URL-címe** nem kap automatikus polulated, akkor a követelménynek megfelelően adja meg manuálisan az értékeket.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<your_company>.beekeeper.io/login`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [méhész Azure ad SSO ügyfél-támogatási csapatával](mailto:support@beekeeper.io) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A méhész Azure AD SSO-alkalmazás meghatározott formátumban várja az SAML-kikötéseket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a méhész Azure AD SSO-alkalmazása néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name (Név) | Forrás attribútum|
    | ------------ | --------- |
    | FirstName | User. givenName |
    | LastName | felhasználó. vezetéknév |
    | e-mail | user.mail |
    | felhasználónév | User. egyszerű név |
    | Pozíció | user.jobtitle |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. A **méhészeti Azure ad SSO beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a méhész Azure AD SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **méhész Azure ad SSO**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-beekeeper-azure-ad-sso"></a>A méhész Azure AD SSO konfigurálása

Ha egyszeri bejelentkezést szeretne konfigurálni a **méhész Azure ad SSO** -oldalon, el kell küldenie a letöltött **összevonási METAADATOKat tartalmazó XML** -fájlt és a megfelelő másolt url-címeket a Azure Portalról a [méhész Azure ad SSO támogatási csapatának](mailto:support@beekeeper.io). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-beekeeper-azure-ad-sso-test-user"></a>Méhész Azure AD SSO-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a méhész Azure AD SSO-ban. A méhész Azure AD SSO támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a méhész Azure AD SSO-ban, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a méhész Azure AD SSO csempére kattint, automatikusan be kell jelentkeznie a méhész Azure AD SSO-be, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A méhész Azure AD SSO kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)