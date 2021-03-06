---
title: 'Oktatóanyag: Azure Active Directory az egyszeri bejelentkezés (SSO) integrációja a tagok számára kék hozzáféréssel (BAM) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és kék hozzáférés a tagok számára (BAM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2680ada0-88e4-46bd-8f08-e9e7ae8aafcd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dd09f80721bb4180eb5f2c25f3315e6211c63cc
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085470"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blue-access-for-members-bam"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a tagok számára kék hozzáféréssel (BAM)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a tagok (BAM) kék hozzáférését Azure Active Directory (Azure AD) használatával. Ha a Blue Access for Members (BAM) integrációját az Azure AD-val integrálja, a következőket teheti:

* Vezérlés az Azure AD-ben, akik hozzáférnek a tagok (BAM) kék hozzáféréséhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a tagok (BAM) kék hozzáférésére az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Kék hozzáférés a tagok (BAM) egyszeri bejelentkezés (SSO) engedélyezett előfizetéséhez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.


* A Blue Access for Members (BAM) támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést




## <a name="adding-blue-access-for-members-bam-from-the-gallery"></a>Kék hozzáférés hozzáadása a tagokhoz (BAM) a katalógusból

Ahhoz, hogy az Azure AD-ben be lehessen állítani a tagok számára a kék hozzáférést (BAM), hozzá kell adnia egy kék hozzáférést a tagokhoz (BAM) a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Blue Access for Members (BAM)** kifejezést a keresőmezőbe.
1. Válassza a **Blue Access for Members (BAM)** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-blue-access-for-members-bam"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a tagoknak (BAM) való kék hozzáféréshez

Konfigurálja és tesztelje az Azure AD SSO-t kék hozzáféréssel a tagokhoz (BAM) egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a hozzá tartozó felhasználó között a Blue Access for Members (BAM) szolgáltatásban.

Az Azure AD SSO és a tagok (BAM) kék hozzáférésének beállításához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Adja meg a Blue Access for Members (BAM) egyszeri bejelentkezést](#configure-blue-access-for-members-bam-sso)** – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
    * **[Hozzon létre kék hozzáférést a Members (BAM) felhasználó](#create-blue-access-for-members-bam-test-user)** számára – a felhasználó Azure ad-beli képviseletéhez csatolt, "B. Simon", kék hozzáféréssel rendelkező tagok (BAM) számára.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **kék hozzáférés a tagok számára (BAM)** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `<Custom Domain Value>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<CUSTOMURL>/affwebservices/public/saml2assertionconsumer`

    c. Kattintson a **további URL-címek beállítása**elemre.

    d. A **továbbítási állapot** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<CUSTOMURL>/BAMSSOServlet/sso/BamInboundSsoServlet`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a továbbítási állapottal. Vegye fel a kapcsolatot [a Blue Access for Members (BAM) ügyfél-támogatási csapatával](https://www.bcbstx.com/contact-us) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Blue Access for Members (BAM) alkalmazás egy adott formátumban várja az SAML-jogcímeket, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Blue Access for Members (BAM) alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name (Név) |  Forrás attribútum|
    | ------------ | --------- |
    | ClientID | `<ClientID>` |
    | UID | `<UID>` |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. A " **kék hozzáférés beállítása a tagok számára (BAM)** " szakaszban másolja a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a tagoknak (BAM) a kék hozzáféréshez.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **kék hozzáférés a tagok számára (BAM)** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-blue-access-for-members-bam-sso"></a>A tagokhoz való kék hozzáférés beállítása (BAM) SSO

Ha be szeretné állítani az egyszeri bejelentkezést a **Blue Access for Members (BAM)** oldalára, el kell küldenie a letöltött **összevonási metaadatokat tartalmazó XML-fájlt** és a megfelelő másolt URL-CÍMEKET a [Tagok (BAM) támogatási csapatának a Azure Portalról kék hozzáférésre](https://www.bcbstx.com/contact-us). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-blue-access-for-members-bam-test-user"></a>Kék hozzáférés létrehozása tagok számára (BAM) tesztelési felhasználó

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Blue Access for Members (BAM) alkalmazásban. A Blue Access for Members ( [BAM) támogatási csapatának](https://www.bcbstx.com/contact-us) a felhasználók a Blue Access for Members (BAM) platformmal való hozzáadására használhatók. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a kék hozzáférés a tagoknak (BAM) csempére kattint, automatikusan be kell jelentkeznie a Blue Access for Members (BAM) szolgáltatásba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a tagok (BAM) kék hozzáférését az Azure AD-vel](https://aad.portal.azure.com/)