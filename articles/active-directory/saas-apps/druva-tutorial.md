---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Druva | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Druva között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f019d818fb5a017d184bda8d773eb0aaf0f3645a
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944404"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Druva

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Druva a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Druva-t az Azure AD-vel, a következőket teheti:

* A Druva-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Druva az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Druva egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Druva támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A Druva SSO konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-druva-from-the-gallery"></a>Druva hozzáadása a gyűjteményből

A Druva Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Druva a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Druva** kifejezést a keresőmezőbe.
1. Válassza ki a **Druva** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Druva

Konfigurálja és tesztelje az Azure AD SSO-t a Druva a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Druva-ben.

Az Azure AD SSO és a Druva konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[DRUVA SSO konfigurálása](#configure-druva-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Druva-teszt felhasználót](#create-druva-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Druva rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Druva** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. Az **azonosító (entitás azonosítója)** szövegmezőbe írja be a következő karakterlánc-értéket: `DCP-login`.
    
    b. A **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** szövegmezőbe írja be a következő URL-címet: `https://cloud.druva.com/wrsaml/consume`.

1. Kattintson a **Save** (Mentés) gombra.

1. A Druva alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a Druva alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Név | Forrás attribútum|
    | ------------------- | -------------------- |
    | emailAddress | User. e-mail |
    | druva_auth_token | A DCP felügyeleti konzolról generált SSO-token idézőjelek nélkül.  Például: X-XXXXX-XXXX-S-A-M-P-L-E + TXOXKXEXNX =. Az Azure automatikusan hozzáadja az idézőjeleket az Auth-jogkivonat köré. |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. A **Druva beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Druva.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Druva**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-druva-sso"></a>Druva SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Druva vállalati webhelyre rendszergazdaként.

1. Kattintson a bal felső sarokban található Druva logóra, majd kattintson a **Druva-felhő beállításai**elemre.

    ![Beállítások](./media/druva-tutorial/ic795091.png "Beállítások")

1. Az **egyszeri bejelentkezés** lapon kattintson a **Szerkesztés**elemre.

    ![Egyszeri bejelentkezési beállítások](./media/druva-tutorial/ic795092.png "Egyszeri bejelentkezési beállítások")

1. Az **egyszeri bejelentkezési beállítások szerkesztése** oldalon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezési beállítások](./media/druva-tutorial/ic795095.png "Egyszeri bejelentkezési beállítások")

    1. Az **azonosító szolgáltató bejelentkezési URL-címe** szövegmezőben illessze be a **bejelentkezési URL-cím**értékét, amelyet a Azure Portal másolt.

    1. Nyissa meg a Base-64 kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be az azonosító- **szolgáltatói tanúsítvány** szövegmezőbe.

       > [!NOTE]
       > Ha engedélyezni szeretné az egyszeri bejelentkezést a rendszergazdák számára, válassza a **rendszergazdák jelentkezzen be a Druva felhőbe SSO-szolgáltatón keresztül** , és **engedélyezze a Failsafe hozzáférést a Druva Cloud Administrators (ajánlott)** jelölőnégyzetekhez. A Druva azt javasolja, hogy engedélyezze a **Failsafe a rendszergazdák számára** , hogy a identitásszolgáltató meghibásodása esetén hozzáférhessenek a DCP-konzolhoz. Azt is lehetővé teszi, hogy a rendszergazdák az SSO és a DCP jelszót is használják a DCP-konzol eléréséhez.

    1. Kattintson a **Save** (Mentés) gombra. Ez lehetővé teszi a Druva Cloud platformhoz való hozzáférést az SSO használatával.

### <a name="create-druva-test-user"></a>Druva-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Druva-ben. A Druva támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Druva-ben, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Druva csempére kattint, automatikusan be kell jelentkeznie arra a Druva, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Druva kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)