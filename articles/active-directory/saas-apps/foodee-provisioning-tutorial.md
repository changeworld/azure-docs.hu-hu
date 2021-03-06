---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés Foodee konfigurálása Azure Active Directory használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a Foodee felhasználói fiókjait.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 2195056ec66550063aba5ce5e2b977b51a6dc5e3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057813"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Foodee konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja a Azure Active Directory (Azure AD) a Foodee és az Azure AD-ben, hogy automatikusan kiépítse vagy felépítse a felhasználókat vagy csoportokat a Foodee.

> [!NOTE]
> A cikk az Azure AD-felhasználó kiépítési szolgáltatására épülő összekötőt ismerteti. A szolgáltatás működésének és működésének megismeréséhez, valamint a gyakori kérdések megválaszolásához tekintse meg a következő témakört: a felhasználók kiépítésének [automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory](../app-provisioning/user-provisioning.md)használatával.
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. Az előzetes verziójú funkciók Azure használati feltételeivel kapcsolatos további információkért tekintse meg az előnézetek [kiegészítő használati Feltételeit Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy teljesítette a következő előfeltételeket:

* Azure AD-bérlő
* [Foodee-bérlő](https://www.food.ee/about/)
* Rendszergazdai jogosultságokkal rendelkező Foodee felhasználói fiók

## <a name="assign-users-to-foodee"></a>Felhasználók Foodee rendelése 

Az Azure AD a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra vagy csoportokra van szükség az Azure AD-ben a Foodee eléréséhez. Miután elvégezte ezt a döntést, hozzárendelheti ezeket a felhasználókat vagy csoportokat a Foodee-hez a [felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)című részben leírtak szerint.

## <a name="important-tips-for-assigning-users-to-foodee"></a>Fontos Tippek a felhasználók Foodee való hozzárendeléséhez 

Amikor felhasználókat rendel hozzá, tartsa szem előtt a következő tippeket:

* Azt javasoljuk, hogy csak egyetlen Azure AD-felhasználót rendeljen hozzá a Foodee-hez, hogy tesztelje az automatikus felhasználó-kiépítés konfigurációját. Később további felhasználókat vagy csoportokat is hozzárendelhet.

* Amikor Foodee rendel hozzá egy felhasználót, a **hozzárendelés** ablaktáblán válassza ki a megfelelő alkalmazásspecifikus szerepkört (ha elérhető). Az *alapértelmezett hozzáférési* szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-foodee-for-provisioning"></a>Foodee beállítása a kiépítés számára

Mielőtt az Azure AD-vel konfigurálja az automatikus felhasználó-kiépítés Foodee, engedélyeznie kell a rendszer számára a tartományok közötti Identitáskezelés (SCIM) létesítését a Foodee-ben.

1. Jelentkezzen be a [Foodee](https://www.food.ee/login/)-be, majd válassza ki a BÉRLŐi azonosítóját.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. Az **Enterprise Portal**területen válassza az **egyszeri bejelentkezés**lehetőséget.

    ![A Foodee Enterprise Portal bal oldali menü](media/Foodee-provisioning-tutorial/scim.png)

1. Másolja az értéket az **API-jogkivonat** mezőbe későbbi használatra. A Foodee alkalmazás **üzembe** helyezés lapjának **titkos jogkivonat** mezőjébe írja be a Azure Portal.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Foodee hozzáadása a gyűjteményből

Az Azure AD-vel történő automatikus Foodee konfigurálásához az Azure ad Foodee kell hozzáadnia a felügyelt SaaS-alkalmazások listájához.

Az Azure AD Foodee hozzáadásához tegye a következőket:

1. A [Azure Portal](https://portal.azure.com)a bal oldali ablaktáblán válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory parancs](common/select-azuread.png)

1. Válassza a **vállalati alkalmazások** > **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza a panel tetején található **új alkalmazás** lehetőséget.

    ![Az új alkalmazás gomb](common/add-new-app.png)

1. A keresőmezőbe írja be a **Foodee**kifejezést, válassza az **Foodee** elemet az eredmények ablaktábláján, majd válassza a **Hozzáadás** lehetőséget az alkalmazás hozzáadásához.

    ![Foodee az eredmények listájában](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Automatikus felhasználó-kiépítés beállítása a Foodee 

Ebben a szakaszban az Azure AD-kiépítési szolgáltatást úgy konfigurálja, hogy az Azure AD-ben felhasználói vagy Foodee alapján hozzon létre, frissítsen és tiltsa le a felhasználókat vagy csoportokat.

> [!TIP]
> Az SAML-alapú egyszeri bejelentkezést a Foodee is engedélyezheti az [Foodee egyszeri bejelentkezési oktatóanyag](Foodee-tutorial.md)utasításait követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül is konfigurálhatja, bár ez a két szolgáltatás kiegészíti egymást.

Az Azure AD-beli Foodee automatikus felhasználó-kiépítés beállítása a következő módon:

1. A [Azure Portal](https://portal.azure.com)válassza a **vállalati alkalmazások** > **minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az **alkalmazások** listában válassza a **Foodee**lehetőséget.

    ![Az Foodee hivatkozás az alkalmazások listájában](common/all-applications.png)

1. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

1. A **létesítési mód** legördülő listában válassza az **automatikus**lehetőséget.

    ![Kiépítés lap](common/provisioning-automatic.png)

1. A **rendszergazdai hitelesítő adatok**területen tegye a következőket:

   a. A **bérlői URL-cím** mezőbe írja be a korábban lekért **https:\//concierge.Food.ee/scim/v2** értéket.

   b. A **titkos jogkivonat** mezőben adja meg a korábban lekért **API-jogkivonat** értékét.
   
   c. A **kapcsolat tesztelése**lehetőségre kattintva biztosíthatja, hogy az Azure ad csatlakozhasson a Foodee. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Foodee-fiókja rendszergazdai jogosultságokkal rendelkezik, majd próbálkozzon újra.

    ![A kapcsolat tesztelése hivatkozás](common/provisioning-testconnection-tenanturltoken.png)

1. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibaüzeneteket, majd jelölje be az **e-mail-értesítések küldése hiba** esetén jelölőnégyzetet.

    ![Az értesítő E-mail szövegmező](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **leképezések**területen válassza **a Azure Active Directory felhasználók szinkronizálása a Foodee**lehetőséget.

    ![Foodee felhasználói leképezések](media/Foodee-provisioning-tutorial/usermapping.png)

1. Az **attribútumok hozzárendelése**területen tekintse át az Azure ad-ből a Foodee-be szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Foodee *felhasználói fiókjainak* a frissítési műveletekhez való megfeleltetésére szolgálnak. 

    ![Foodee felhasználói leképezések](media/Foodee-provisioning-tutorial/userattribute.png)

1. A módosítások elvégzéséhez válassza a **Mentés**lehetőséget.
1. A **leképezések**területen válassza **a Azure Active Directory csoportok szinkronizálása a Foodee**lehetőséget.

    ![Foodee felhasználói leképezések](media/Foodee-provisioning-tutorial/groupmapping.png)

1. Az **attribútumok hozzárendelése**területen tekintse át az Azure ad-ből a Foodee-be szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Foodee tartozó *csoportfiókok* egyeztetésére szolgálnak a frissítési műveletekhez.

    ![Foodee felhasználói leképezések](media/Foodee-provisioning-tutorial/groupattribute.png)

1. A módosítások elvégzéséhez válassza a **Mentés**lehetőséget.
1. Adja meg a hatóköri szűrőket. Ha szeretné megtudni, hogyan tekintheti meg a [hatóköri szűrő oktatóanyagát](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md), olvassa el a következő témakört:.

1. Az Azure AD-kiépítési szolgáltatás Foodee való engedélyezéséhez a **Beállítások** szakaszban módosítsa a **kiépítési állapotot** **a**következőre:.

    ![A kiépítési állapot kapcsolója](common/provisioning-toggle-on.png)

1. A **Beállítások** **terület hatókör** legördülő listájában adja meg azokat a felhasználókat és csoportokat, amelyeket a Foodee kíván kiépíteni.

    ![A kiépítési hatókör legördülő listája](common/provisioning-scope.png)

1. Ha készen áll a létesítésre, válassza a **Mentés**lehetőséget.

    ![A kiépítési konfiguráció mentése gomb](common/provisioning-configuration-save.png)

Az előző művelet elindítja a **hatókör** legördülő listában definiált felhasználók vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ: meddig tart [a felhasználók kiépítése?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat. A jelentés az Azure AD-kiépítési szolgáltatás által a Foodee-on végrehajtott összes műveletet ismerteti. További információ: [a felhasználó kiépítési állapotának ellenõrzése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../app-provisioning/check-status-user-account-provisioning.md)során.

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)
