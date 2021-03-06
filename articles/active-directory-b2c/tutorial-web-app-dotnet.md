---
title: 'Oktatóanyag: hitelesítés engedélyezése webalkalmazásokban'
titleSuffix: Azure AD B2C
description: Arra vonatkozó útmutató, hogyan használhatja az Azure Active Directory B2C-t felhasználói bejelentkezés biztosításához egy ASP.NET-es webalkalmazás esetén.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e4b56f18bf8a2ed1c22b00b8a57efdbf06eb7fa2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183319"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Oktatóanyag: hitelesítés engedélyezése webalkalmazásokban Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan használható a Azure Active Directory B2C (Azure AD B2C) a bejelentkezéshez, és hogyan regisztrálhat felhasználókat egy ASP.NET-webalkalmazásban. Azure AD B2C lehetővé teszi az alkalmazások számára, hogy a nyílt szabványos protokollok használatával hitelesítsék a közösségi fiókokat, a vállalati fiókokat és a Azure Active Directory fiókokat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás frissítése Azure AD B2C
> * A minta beállítása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Felhasználói folyamatokat hozhat létre](tutorial-create-user-flows.md) , amelyekkel engedélyezheti az alkalmazás felhasználói élményét.
* Telepítse a [Visual Studio 2019](https://www.visualstudio.com/downloads/) alkalmazást a **ASP.net és a webes fejlesztési** munkaterheléssel.

## <a name="update-the-application-registration"></a>Az alkalmazás regisztrációjának frissítése

Az előfeltételek részeként elvégzett oktatóanyagban egy webalkalmazást regisztrált Azure AD B2Cban. Az oktatóanyagban szereplő példával való kommunikáció engedélyezéséhez hozzá kell adnia egy átirányítási URI-t, és létre kell hoznia egy ügyfél titkos kulcsot (kulcs) a regisztrált alkalmazáshoz.

### <a name="add-a-redirect-uri-reply-url"></a>Átirányítási URI hozzáadása (válasz URL-címe)

Az alkalmazás frissítéséhez használhatja az aktuális **alkalmazások** vagy az új Unified **Alkalmazásregisztrációk (előzetes verzió)** felhasználói élményt. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **alkalmazások**lehetőséget, majd válassza ki a *webapp1* alkalmazást.
1. A **Válasz URL-cím**területen adja hozzá a `https://localhost:44316`.
1. Kattintson a **Mentés** gombra.
1. A Tulajdonságok lapon jegyezze fel az alkalmazás AZONOSÍTÓját a webalkalmazás konfigurálásakor egy későbbi lépésben való használatra.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, válassza a **tulajdonában lévő alkalmazások** fület, majd válassza ki a *webapp1* alkalmazást.
1. Válassza a **hitelesítés**lehetőséget, majd válassza **az új felület kipróbálása** (ha látható) lehetőséget.
1. A **web**területen válassza az **URI hozzáadása** hivatkozást, írja be a `https://localhost:44316`, majd kattintson a **Mentés**gombra.
1. Válassza az **Áttekintés** lehetőséget.
1. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** , hogy a webalkalmazás konfigurálásakor egy későbbi lépésben használhassa.

* * *

### <a name="create-a-client-secret"></a>Ügyfél titkos kulcsának létrehozása

Ezután hozzon létre egy ügyfél-titkot a regisztrált webalkalmazáshoz. A webalkalmazás kódjának mintája a következőt használja az identitás igazolására a jogkivonatok kérésekor.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>A minta konfigurálása

Ebben az oktatóanyagban egy olyan mintát konfigurál, amelyet letöltheti a GitHubról. A minta a ASP.NET használatával egyszerű feladatlistát biztosít. A minta a [Microsoft OWIN köztes összetevőket](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/)használja. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip), vagy a klónozza a GitHubon található mintát. A mintafájlt egy olyan mappába kell kibontani, ahol a teljes elérési út rövidebb, mint 260 karakter.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

A következő két projekt szerepel a minta megoldásban:

* **TaskWebApp** – Feladatlista létrehozása és szerkesztése. A minta a **regisztrációs vagy bejelentkezési** felhasználói folyamat használatával regisztrálja és bejelentkezik a felhasználók számára.
* **TaskService** – támogatja a Feladatlista létrehozását, olvasását, frissítését és törlését. Az API-t a Azure AD B2C és a TaskWebApp által védettnek nevezzük.

A mintát úgy változtathatja meg, hogy a bérlőben regisztrált alkalmazást használja, beleértve az alkalmazás AZONOSÍTÓját és a korábban rögzített kulcsot is. A létrehozott felhasználói folyamatokat is konfigurálhatja. A minta a konfigurációs értékeket a *web. config* fájlban megadott beállításokként határozza meg.

Frissítse a web. config fájlban lévő beállításokat a felhasználói folyamattal való együttműködéshez:

1. Nyissa meg a **B2C-WebAPI-DotNet** megoldást a Visual Studióban.
1. A **TaskWebApp** projektben nyissa meg a **web. config** fájlt.
    1. Frissítse `ida:Tenant` és `ida:AadInstance` értékét a létrehozott Azure AD B2C bérlő nevével. Például cserélje le a `fabrikamb2c`t a `contoso`ra.
    1. Cserélje le a `ida:ClientId` értékét a rögzített alkalmazás-AZONOSÍTÓra.
    1. Cserélje le az `ida:ClientSecret` értékét a feljegyzett kulcsra. Ha az ügyfél titkos kulcsa olyan előre definiált XML-entitásokat tartalmaz, mint például a (`<`) kisebb, mint (`>`), a jel (`&`) vagy a dupla idézőjel (`"`), el kell menekülnie ezeket a karaktereket a web. config fájlhoz való hozzáadása előtt.
    1. Cserélje le a `ida:SignUpSignInPolicyId` értékét a `b2c_1_signupsignin1`ra.
    1. Cserélje le a `ida:EditProfilePolicyId` értékét a `b2c_1_profileediting1`ra.
    1. Cserélje le a `ida:ResetPasswordPolicyId` értékét a `b2c_1_passwordreset1`ra.

## <a name="run-the-sample"></a>Minta futtatása

1. Megoldáskezelő kattintson a jobb gombbal a **TaskWebApp** projektre, majd kattintson a **beállítás indítási projektként**lehetőségre.
1. Nyomja le az **F5**billentyűt. Az alapértelmezett böngésző elindítja a helyi webhely címét: `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Válassza a **regisztráció/bejelentkezés** lehetőséget az alkalmazás felhasználójának való regisztrációhoz. A rendszer a **b2c_1_signupsignin1** felhasználói folyamatot használja.
1. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nem rendelkezik fiókkal, válassza a **regisztráció most**lehetőséget. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A regisztrációs munkafolyamat a felhasználó jelszavát és a felhasználói folyamatban definiált kért attribútumokat is gyűjti.
1. Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    ![A bejelentkezési vagy bejelentkezési munkafolyamat részeként megjelenő regisztrációs oldal](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. A **Létrehozás** gombra kattintva helyi fiókot hozhat létre a Azure ad B2C bérlőben.

Az alkalmazás felhasználója mostantól az e-mail-címük használatával bejelentkezhet, és használhatja a webalkalmazást.

A **Feladatlista** funkció azonban addig nem fog működni, amíg el nem végzi a következő oktatóanyagot a sorozatban, [oktatóanyag: Azure AD B2C használata a ASP.net webes API-k elleni védelemhez](tutorial-web-api-dotnet.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az alkalmazás frissítése Azure AD B2C
> * A minta beállítása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

Most lépjen a következő oktatóanyagra a webalkalmazás **Feladatlista** funkciójának engedélyezéséhez. Ebben az esetben egy webes API-alkalmazást kell regisztrálnia a saját Azure AD B2C bérlőben, majd módosítania kell a kódot, hogy a bérlőt használja az API-hitelesítéshez.

> [!div class="nextstepaction"]
> [Oktatóanyag: a ASP.NET webes API-k elleni védelem Azure Active Directory B2C használata >](tutorial-web-api-dotnet.md)
