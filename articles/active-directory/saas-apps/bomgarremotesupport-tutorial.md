---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a BeyondTrust távoli támogatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és BeyondTrust távoli támogatás között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff21c3ee7721c82232e668ddb9645895080cf79
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082067"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a BeyondTrust távoli támogatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a BeyondTrust távoli támogatását Azure Active Directory (Azure AD) használatával. Ha a BeyondTrust távoli támogatását az Azure AD-vel integrálja, a következőket teheti:

* A BeyondTrust távoli támogatáshoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek, hogy BeyondTrust a távoli támogatást az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* BeyondTrust távoli támogatás egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A BeyondTrust távoli támogatása támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A BeyondTrust-támogatás **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>BeyondTrust távoli támogatásának hozzáadása a katalógusból

A BeyondTrust távoli támogatásának az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a BeyondTrust távoli támogatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **BeyondTrust távoli támogatás** kifejezést a keresőmezőbe.
1. Válassza a **BeyondTrust távoli támogatás** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a BeyondTrust távoli támogatásához

Konfigurálja és tesztelje az Azure AD SSO-t BeyondTrust távoli támogatással a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a BeyondTrust-távoli támogatásban.

Az Azure AD SSO és a BeyondTrust távoli támogatásának konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[BeyondTrust távoli támogatásának egyszeri bejelentkezéses](#configure-beyondtrust-remote-support-sso)** beállítása – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre BeyondTrust-alapú távoli támogatási teszt felhasználót](#create-beyondtrust-remote-support-test-user)** – ha a BeyondTrust-hez tartozó, a felhasználó Azure ad-képviseletéhez kapcsolódó távoli támogatásban található B. Simon partnere.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **BeyondTrust távoli támogatás** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<HOSTNAME>.bomgar.com/saml`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `https://<HOSTNAME>.bomgar.com`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Ezeket az értékeket az oktatóanyag későbbi részében ismertetjük.

1. A BeyondTrust távoli támogatási alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a BeyondTrust távoli támogatási alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name (Név) |  Forrás attribútum|
    | ---------------| ----------|
    | givenName | User. givenName |
    | EmailAddress | user.mail |
    | Name (Név) | user.userprincipalname |
    | Felhasználónév | user.userprincipalname |
    | Csoportok | User. groups |
    | Egyedi felhasználói azonosító | user.userprincipalname |

    > [!NOTE]
    > Ha Azure AD-csoportokat rendel a BeyondTrust távoli támogatási alkalmazáshoz, a "jogcímek visszaadott csoportok" beállítást kell módosítania a nincs értékről a SecurityGroup. A csoportok az alkalmazásba lesznek importálva az objektum-azonosítók szerint. Az Azure AD-csoport objektumazonosító az Azure Active Directory felületen található tulajdonságok ellenőrzésével érhető el. Erre szükség lesz az Azure AD-csoportok megfelelő csoportházirendekhez való hivatkozásához és hozzárendeléséhez.

1. Az egyedi felhasználói azonosító beállításakor ezt az értéket NameID: **állandó**értékre kell beállítani. A felhasználónak a megfelelő csoportházirendek helyes azonosításához és az engedélyekhez való hozzárendeléséhez szükség van egy állandó azonosítóra. A Szerkesztés ikonra kattintva megnyithatja a **felhasználói attribútumok & jogcímek** párbeszédpanelt az egyedi felhasználói azonosító értékének szerkesztéséhez.

1. A **jogcím kezelése** szakaszban kattintson a **név azonosítójának kiválasztása** lehetőségre, és állítsa az értéket **állandó** értékre, majd kattintson a **Mentés**gombra.

    ![Felhasználói attribútumok és jogcímek](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. A BeyondTrust-alapú **távoli támogatás beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a BeyondTrust távoli támogatásához.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **BeyondTrust távoli támogatás**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-beyondtrust-remote-support-sso"></a>A BeyondTrust távoli támogatásának egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a BeyondTrust-rendszergazdaként a távoli támogatás szolgáltatásba.

1. Kattintson az **állapot** menüre, és másolja ki az **azonosítót**, a **Válasz URL-címét** és a **bejelentkezési URL-címet** , és használja ezeket az értékeket a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    ![A BeyondTrust távoli támogatásának konfigurálása](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. Navigáljon a BeyondTrust távoli támogatás belépési pont felületére `https://support.example.com/login` ahol a **support.example.com** a berendezés elsődleges állomásneve, és a rendszergazdai hitelesítő adataival hitelesíti magát.

1. Navigáljon a **felhasználók & biztonsági** > **biztonsági szolgáltatók**elemre.

1. A legördülő menüben válassza ki az **SAML** elemet, majd kattintson a **szolgáltató létrehozása** gombra.

1. Az identitás-szolgáltató beállításai szakaszban lehetősége van az identitás-szolgáltató metaadatainak feltöltésére. Keresse meg a Azure Portal letöltött metaadat-XML-fájlt, majd kattintson a **feltöltés** gombra. Az **entitás-azonosító**, az **egyszeri bejelentkezési szolgáltatás URL-címe** és a tanúsítvány automatikusan feltöltve lesz, és a **protokoll kötését** a **http post**értékre kell módosítani. Lásd az alábbi képernyőképet:

    ![A BeyondTrust távoli támogatásának konfigurálása](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>BeyondTrust távoli támogatási teszt felhasználó létrehozása

A felhasználó-kiépítési beállításokat itt fogjuk konfigurálni. Az ebben a szakaszban használt értékeket a Azure Portal **felhasználói attribútumok & jogcímek** szakasza hivatkozik. Ezt úgy konfiguráltuk, hogy az a létrehozáskor már importált alapértelmezett értékek legyenek, azonban az érték szükség esetén testreszabható.

![Felhasználó létrehozása](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> A csoportok és az e-mail-attribútum nem szükséges ehhez a megvalósításhoz. Ha Azure AD-csoportokat használ, és hozzárendeli őket a BeyondTrust távoli támogatási csoportházirendekhez az engedélyekhez, a csoport objektum-AZONOSÍTÓját a Azure Portal és az "elérhető csoportok" szakaszban elhelyezett tulajdonságok segítségével kell hivatkozni. A művelet befejezését követően az objektum-azonosító/AD-csoport elérhető lesz a Csoportházirendhez való hozzárendeléshez az engedélyek számára.

![Felhasználó létrehozása](./media/bomgarremotesupport-tutorial/config-user2.png)

![Felhasználó létrehozása](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> Másik lehetőségként beállíthatja a egy SAML2 biztonsági szolgáltató alapértelmezett csoportházirendjét is. Ennek a beállításnak a megadásával a a csoportházirendben megadott engedélyek alapján az SAML-n keresztül hitelesítő összes felhasználót hozzárendeli. Az általános tagok házirend a BeyondTrust-alapú távoli támogatás/privilegizált távoli hozzáférés korlátozott engedélyekkel való használatának része, amely a hitelesítés tesztelésére és a felhasználók megfelelő szabályzatokhoz való hozzárendelésére használható. A felhasználók nem töltik be a egy SAML2-felhasználók listájára a belépési pont > felhasználóval & a biztonságot az első sikeres hitelesítési kísérletig. A csoportházirendekkel kapcsolatos további információk a következő hivatkozáson találhatók: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a BeyondTrust távoli támogatás csempére kattint, automatikusan be kell jelentkeznie a BeyondTrust távoli támogatására, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a BeyondTrust távoli támogatását az Azure AD-vel](https://aad.portal.azure.com/)
