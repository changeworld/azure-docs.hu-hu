---
title: 'Oktatóanyag: a Workteam konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Workteam.
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
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: e9b1e93cf543836b282525c53756752630d5e4f6
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062793"
---
# <a name="tutorial-configure-workteam--for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Workteam konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Workteam és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Workteam.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Workteam-bérlő](https://workte.am/pricing.html)
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Workteam-ben.

## <a name="assigning-users-to-workteam"></a>Felhasználók kiosztása a Workteam 

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Workteam való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Workteam az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workteam"></a>Fontos Tippek a felhasználók Workteam való hozzárendeléséhez 

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Workteam-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Workteam rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-workteam--for-provisioning"></a>Workteam beállítása a kiépítés számára

Az Azure AD-vel való automatikus Workteam konfigurálása előtt engedélyeznie kell a SCIM-létesítést a Workteam-on.

1. Jelentkezzen be a [Workteam](https://app.workte.am/account/signin)-be. Kattintson a **szervezeti beállítások** > **Beállítások**elemre.

    ![Workteam](media/workteam-provisioning-tutorial/settings.png)

2. Görgessen az aljára, és engedélyezze a Workteam kiépítési képességeit.

    ![Workteam](media/workteam-provisioning-tutorial/icon.png)

3. Másolja ki az **alap URL-címet** és a **tulajdonosi jogkivonatot**. Ezek az értékek a Workteam alkalmazás kiépítés lapjának **bérlő URL-címe**és **titkos jogkivonat** mezőjében lesznek megadva a Azure Portal.

    ![Workteam](media/workteam-provisioning-tutorial/scim.png)


## <a name="add-workteam--from-the-gallery"></a>Workteam hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Workteam konfigurálásához hozzá kell adnia a Workteam az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha Workteam szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Workteam**kifejezést, válassza az **Workteam** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Workteam az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workteam"></a>Automatikus felhasználó-kiépítés beállítása a Workteam  

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Workteam alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Workteam számára, az [Workteam egyszeri bejelentkezés oktatóanyagában](workteam-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-workteam--in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Workteam az Azure AD-ben:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Workteam**lehetőséget.

    ![Az Workteam hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg az **alap URL-címet** és a **tulajdonosi jogkivonat** azon értékeit, amelyeket korábban a **bérlői URL-címben** és a **titkos jogkivonatban** lekért. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Workteam. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Workteam-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Workteam**lehetőséget.

    ![Workteam felhasználói leképezések](media/workteam-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Workteam. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Workteam felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Workteam felhasználói attribútumai](media/workteam-provisioning-tutorial/userattribute.png)

11. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Az Azure AD-kiépítési szolgáltatás Workteam való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

13. Adja meg a Workteam kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

14. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, hogy [mennyi ideig tart a felhasználók kiépítése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Workteam-on végrehajtott összes műveletet ismertetik. További információ: [a felhasználó kiépítési állapotának ellenõrzése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../app-provisioning/check-status-user-account-provisioning.md)során.

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)
