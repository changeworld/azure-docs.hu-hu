---
title: Felhasználói bejelentkezést biztosító webalkalmazás regisztrálása – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan regisztrálhat egy olyan webalkalmazást, amely aláírja a felhasználókat
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f2513b1fee022199f040ba0dcf83110c0b3b1365
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701569"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Felhasználók számára bejelentkező webalkalmazás: alkalmazás regisztrálása

Ez a cikk ismerteti az alkalmazások regisztrálási sajátosságait egy olyan webalkalmazáshoz, amely a felhasználókra jelentkezik.

Az alkalmazás regisztrálásához a következőket használhatja:

- A [webalkalmazás](#register-an-app-by-using-the-quickstarts)rövid útmutatói. Amellett, hogy kiválóan használja az alkalmazások létrehozását, a Azure Portalban található rövid útmutatók tartalmaznak egy **, a módosítást**elnevező gombot. Ezt a gombot használhatja a szükséges tulajdonságok beállításához még egy meglévő alkalmazáshoz is. A tulajdonságok értékeit a saját esetéhez kell igazítania. Különösen az alkalmazás webes API URL-címe valószínűleg eltér a javasolt alapértelmezetttől, ami a kijelentkezési URI-t is érinti.
- Az Azure Portal az [alkalmazás manuális regisztrálásához](#register-an-app-by-using-the-azure-portal).
- PowerShell és parancssori eszközök.

## <a name="register-an-app-by-using-the-quickstarts"></a>Alkalmazás regisztrálása a rövid útmutatók használatával

Ezeket a hivatkozásokat használhatja a webalkalmazás létrehozásához:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Alkalmazás regisztrálása a Azure Portal használatával

> [!NOTE]
> A használni kívánt portál eltérő lehet attól függően, hogy az alkalmazás a Microsoft Azure nyilvános felhőben, vagy egy nemzeti vagy szuverén felhőben fut-e. További információ: [National felhők](./authentication-national-cloud.md#app-registration-endpoints).


1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával. Másik lehetőségként jelentkezzen be a nemzeti felhőhöz választott Azure Portal.
1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókját a jobb felső sarokban. Ezután állítsa be a portál munkamenetét a kívánt Azure Active Directory (Azure AD) bérlőre.
1. A bal oldali ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást, majd válassza a **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
   1. Válassza ki az alkalmazás támogatott fiók-típusait. (Lásd: [támogatott fióktípus](./v2-supported-account-types.md).)
   1. A **név** szakaszban adjon meg egy értelmezhető nevet, amely megjelenik az alkalmazás felhasználói számára. Írja be például a következőt: **AspNetCore-WebApp**.
   1. Az **átirányítási URI**esetében adja hozzá az alkalmazás típusát és az URI célhelyet, amely a sikeres hitelesítés után a visszaadott jogkivonat-válaszokat fogja fogadni. Adja meg például a következőt: **https://localhost:44321** . Ezután válassza a **regisztráció**lehetőséget.
1. Válassza a **hitelesítés** menüt, majd adja hozzá a következő adatokat:
   1. A **Válasz URL-címéhez**adja hozzá a **web**típusú **https://localhost:44321/signin-oidc** .
   1. A **Speciális beállítások** szakaszban állítsa be **https://localhost:44321/signout-oidc** a **kijelentkezési URL-címet** .
   1. Az **Implicit engedély** területen válassza az **Azonosítói jogkivonatok** elemet.
   1. Kattintson a **Mentés** gombra.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

1. Amikor megjelenik az **Alkalmazás regisztrálása lap**, adja meg az alkalmazás regisztrációs adatait:
   1. Válassza ki az alkalmazás támogatott fiók-típusait. (Lásd: [támogatott fióktípus](./v2-supported-account-types.md).)
   1. A **név** szakaszban adjon meg egy értelmezhető nevet, amely megjelenik az alkalmazás felhasználói számára. Írja be például a következőt: **MailApp-openidconnect-v2**.
   1. Az **átirányítási URI (nem kötelező)** szakaszban válassza a kombinált lista **web** elemét, és adja meg a következő átirányítási uri-t: **https://localhost:44326/** .
1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Válassza a **hitelesítés** menüt.
1. A **Speciális beállítások** | **implicit támogatás** szakaszban válassza az **azonosító tokenek**elemet. Ehhez a mintához engedélyezni kell, hogy az [implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) engedélyezze a bejelentkezést a felhasználó számára.
1. Kattintson a **Mentés** gombra.

# <a name="javatabjava"></a>[Java](#tab/java)

1. Amikor megjelenik az **alkalmazás regisztrálása lap** , adja meg az alkalmazás megjelenítendő nevét. Írja be például a következőt: **Java-WebApp**.
1. Válassza **a fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban (például Skype, Xbox, Outlook.com)** , majd válassza a webalkalmazás **/API** lehetőséget az **alkalmazás típusához**.
1. Az alkalmazás regisztrálásához válassza a **regisztráció** lehetőséget.
1. A bal oldali menüben válassza a **hitelesítés**lehetőséget. Az **átirányítási URI**-k területen válassza a **web**lehetőséget.

1. Adjon meg két átirányítási URI-t: egyet a bejelentkezési oldalhoz, egy pedig a Graph oldalhoz. Mindkettő esetében ugyanazt a gazdagépet és portszámot kell használnia, amelyet a bejelentkezési oldal **/msal4jsample/Secure/aad** , valamint a felhasználói adatok oldalának **msal4jsample/Graph/Me** elemében követ.

   Alapértelmezés szerint a minta a következőket használja:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Ezt követően válassza a **Mentés** lehetőséget.

1. Válassza a **tanúsítványok & a titkok** lehetőséget a menüből.
1. Az **ügyfél titkai** szakaszban válassza az **új ügyfél titka**elemet, majd a következőket:

   1. Adja meg a kulcs leírását.
   1. Válassza ki a kulcs időtartamát **1 évben**.
   1. Válassza a **Hozzáadás** lehetőséget.
   1. Ha a kulcs értéke megjelenik, másolja később. Ez az érték nem jelenik meg újra, vagy bármilyen más módon lekérdezhető.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Amikor megjelenik az **Alkalmazás regisztrálása lap**, adja meg az alkalmazás regisztrációs adatait:
   1. A **név** szakaszban adjon meg egy értelmezhető nevet, amely megjelenik az alkalmazás felhasználói számára. Adja meg például a **Python-WebApp**értéket.
   1. A **támogatott fióktípus** módosítása **bármely szervezeti címtárban és személyes Microsoft-fiókban (például Skype, Xbox, Outlook.com)** .
   1. Az **átirányítási URI (nem kötelező)** szakaszban válassza a kombinált lista **web** elemét, és adja meg a következő átirányítási uri-t: **http://localhost:5000/getAToken** .
1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás **Áttekintés** lapján keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel később. Ehhez a projekthez a Visual Studio konfigurációs fájlját kell konfigurálnia.
1. A bal oldali menüben válassza a **tanúsítványok & Secrets**elemet.
1. Az **ügyfél titkai** szakaszban válassza az **új ügyfél titka**elemet, majd a következőket:

   1. Adja meg a kulcs leírását.
   1. Adja **meg az 1 év**kulcsának időtartamát.
   1. Válassza a **Hozzáadás** lehetőséget.
   1. Ha a kulcs értéke megjelenik, másolja. Erre később még szüksége lesz.
---

## <a name="register-an-app-by-using-powershell"></a>Alkalmazás regisztrálása a PowerShell használatával

> [!NOTE]
> Az Azure AD PowerShell jelenleg csak a következő támogatott fióktípus-típusokat hozza létre:
>
> - MyOrg (csak ebben a szervezeti könyvtárban lévő fiókok)
> - AnyOrg (fiókok bármely szervezeti címtárban)
>
> Létrehozhat egy olyan alkalmazást, amely a felhasználók személyes Microsoft-fiókjait (például Skype, Xbox vagy Outlook.com) is aláírja. Először hozzon létre egy több-bérlős alkalmazást. A támogatott fióktípus bármely szervezeti címtárban található fiókok. Ezután módosítsa az alkalmazás jegyzékfájljának `signInAudience` tulajdonságát a Azure Portal. További információ: 1,3. [lépés](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) a ASP.net Core oktatóanyagban. Ezt a lépést bármilyen nyelven általánosíthatja a Web Apps szolgáltatásban.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az alkalmazás kódjának konfigurálása](scenario-web-app-sign-user-app-configuration.md)
