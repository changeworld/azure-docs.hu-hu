---
title: 'Oktatóanyag: identitás-szolgáltatók hozzáadása az alkalmazásokhoz'
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan adhat identitás-szolgáltatókat Azure Active Directory B2C alkalmazásaihoz a Azure Portal használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f49061210ca8e3c106b0569f77a67d1f10757a1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183516"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Oktatóanyag: identitás-szolgáltatók hozzáadása az alkalmazásokhoz Azure Active Directory B2C

Az alkalmazásaiban engedélyezheti, hogy a felhasználók különböző identitás-szolgáltatókkal jelentkezzenek be. Az *identitás-szolgáltató* identitási adatokat hoz létre, tart karban és felügyel, miközben hitelesítési szolgáltatásokat biztosít az alkalmazásoknak. A Azure Portal használatával adhat hozzá Azure Active Directory B2C (Azure AD B2C) által támogatott identitás-szolgáltatókat a [felhasználói folyamatokhoz](user-flow-overview.md) .

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az Identity Provider-alkalmazások létrehozása
> * Az identitás-szolgáltatók hozzáadása a bérlőhöz
> * Az identitás-szolgáltatók hozzáadása a felhasználói folyamathoz

Általában csak egy identitás-szolgáltatót használ az alkalmazásaiban, de lehetősége van további hozzáadására. Ez az oktatóanyag bemutatja, hogyan adhat hozzá Azure AD-identitás-szolgáltatót és egy Facebook-identitást az alkalmazásához. Ha mindkét identitás-szolgáltatót hozzáadja az alkalmazáshoz, nem kötelező megadni. Más identitás-szolgáltatók is hozzáadhatók, például az [Amazon](identity-provider-amazon.md), a [GitHub](identity-provider-github.md), a [Google](identity-provider-google.md), a [LinkedIn](identity-provider-linkedin.md), a [Microsoft](identity-provider-microsoft-account.md)vagy a [Twitter](identity-provider-twitter.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy felhasználói folyamatot](tutorial-create-user-flows.md) , amely lehetővé teszi a felhasználók számára az alkalmazásba való regisztrációt és bejelentkezést.

## <a name="create-applications"></a>Alkalmazások létrehozása

Az identitás-szolgáltatói alkalmazások biztosítják az azonosítót és a kulcsot a Azure AD B2C Bérlővel való kommunikáció engedélyezéséhez. Az oktatóanyag ezen szakaszában létrehoz egy Azure AD-alkalmazást és egy Facebook-alkalmazást, amelyből azonosítókat és kulcsokat kap, hogy hozzáadja az identitás-szolgáltatót a bérlőhöz. Ha csak az egyik identitás-szolgáltatót adja hozzá, csak az adott szolgáltatóhoz kell létrehoznia az alkalmazást.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory-alkalmazás létrehozása

Ha engedélyezni szeretné a bejelentkezést az Azure AD-beli felhasználók számára, regisztrálnia kell egy alkalmazást az Azure AD-bérlőn belül. Az Azure AD-bérlő nem azonos a Azure AD B2C Bérlővel.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás nevét. Például: `Azure AD B2C App`.
1. Az **ebben a szervezeti könyvtárban lévő fiókok** kijelölésének elfogadása csak ehhez az alkalmazáshoz.
1. Az **átirányítási URI**esetében fogadja el a **web** értékét, és írja be az alábbi URL-címet minden kisbetűvel, és cserélje le a `your-B2C-tenant-name`t a Azure ad B2C bérlő nevére.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Például: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Az összes URL-nek most a [B2clogin.com](b2clogin.md)-t kell használnia.

1. Válassza a **regisztráció**lehetőséget, majd jegyezze fel az **alkalmazás (ügyfél) azonosítóját** , amelyet egy későbbi lépésben használ.
1. Az alkalmazás menü **kezelés** területén válassza a **tanúsítványok & Secrets**, majd az **új ügyfél titka**elemet.
1. Adja meg az ügyfél titkos kódjának **leírását** . Például: `Azure AD B2C App Secret`.
1. Válassza ki a lejárati időszakot. Ehhez az alkalmazáshoz fogadja el az **1 év**kiválasztását.
1. Válassza a **Hozzáadás**lehetőséget, majd jegyezze fel az új ügyfél titkos kulcsának értékét, amelyet egy későbbi lépésben használ.

### <a name="create-a-facebook-application"></a>Facebook-alkalmazás létrehozása

Ha Facebook-fiókot szeretne használni a Azure AD B2C identitás-szolgáltatóként, létre kell hoznia egy alkalmazást a Facebookon. Ha még nem rendelkezik Facebook-fiókkal, a [https://www.facebook.com/](https://www.facebook.com/)címen kérheti le.

1. A Facebook-fiók hitelesítő adataival jelentkezzen be a [facebookba a fejlesztők számára](https://developers.facebook.com/) .
1. Ha még nem tette meg, regisztrálnia kell Facebook-fejlesztőként. Ehhez válassza az első **lépések** lehetőséget az oldal jobb felső sarkában, fogadja el a Facebook szabályzatait, és végezze el a regisztráció lépéseit.
1. Válassza **a saját alkalmazások** lehetőséget, majd **hozza létre az alkalmazást**.
1. Adjon meg egy **megjelenítendő nevet** és egy érvényes **kapcsolattartási e-mailt**.
1. Kattintson az **alkalmazás-azonosító létrehozása**elemre. Előfordulhat, hogy el kell fogadnia a Facebook-platform szabályzatait, és el kell végeznie egy online biztonsági ellenőrzését.
1. Válassza a **beállítások** > **alapszintű**lehetőséget.
1. Válasszon egy **kategóriát**, például `Business and Pages`. Ez az érték a Facebook számára szükséges, de a Azure AD B2C nem használja.
1. A lap alján válassza a **platform hozzáadása**lehetőséget, majd válassza a **webhely**lehetőséget.
1. A **webhely URL-címe**mezőbe írja be a `https://your-tenant-name.b2clogin.com/` helyére a bérlő nevét `your-tenant-name`.
1. Adja meg az **adatvédelmi szabályzat URL**-címét, például `http://www.contoso.com/`. Az adatvédelmi szabályzat URL-címe az az oldal, amelyet az alkalmazásra vonatkozó adatvédelmi információk biztosítására tart fenn.
1. Válassza a **módosítások mentése**lehetőséget.
1. Az oldal tetején jegyezze fel az **alkalmazás azonosítójának**értékét.
1. Az **alkalmazás titka**mellett válassza a **Megjelenítés** lehetőséget, és jegyezze fel az értékét. Az alkalmazás AZONOSÍTÓját és az alkalmazás titkos kulcsát is használhatja a Facebook identitás-szolgáltatóként való konfigurálásához a bérlőben. Az **alkalmazás titkos kulcsa** egy fontos biztonsági hitelesítő adat, amelyet biztonságosan kell tárolnia.
1. Válassza a **termékek**melletti plusz jelre, majd a **Facebook-Bejelentkezés**alatt válassza a **beállítás**lehetőséget.
1. A bal oldali menü **Facebook-Bejelentkezés** területén válassza a **Beállítások**lehetőséget.
1. Az **érvényes OAuth átirányítási URI**-k mezőben adja meg a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Cserélje le a `your-tenant-name`t a bérlő nevére. A lap alján kattintson a **módosítások mentése** gombra.
1. Ahhoz, hogy a Facebook-alkalmazás elérhető legyen a Azure AD B2C számára, kattintson az oldal jobb felső sarkában található **állapot** -választóra, majd kapcsolja be **, hogy az** alkalmazás nyilvános legyen, majd kattintson a **Confirm (megerősítés**) gombra. Ezen a ponton az állapotnak a **fejlesztéstől** az **élő**értékre kell váltania.

## <a name="add-the-identity-providers"></a>Az identitás-szolgáltatók hozzáadása

Miután létrehozta az alkalmazást a hozzáadni kívánt identitás-szolgáltatóhoz, adja hozzá az identitás-szolgáltatót a bérlőhöz.

### <a name="add-the-azure-active-directory-identity-provider"></a>A Azure Active Directory identitás-szolgáltató hozzáadása

1. Győződjön meg arról, hogy a Azure AD B2C bérlőt tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **identitás-szolgáltatók**, majd az **új OpenID Connect Provider**lehetőséget.
1. Adjon meg egy **nevet**. Adja meg például a *contoso Azure ad*-t.
1. A **metaadatok URL-címéhez**írja be az alábbi URL-címet, amely a `your-AD-tenant-domain` az Azure ad-bérlő tartománynevével helyettesíti:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Az **ügyfél-azonosító**mezőben adja meg a korábban rögzített alkalmazás azonosítóját.
1. Az **ügyfél titkos kulcsa**mezőben adja meg a korábban rögzített ügyfél-titkot.
1. Hagyja meg a **hatókör**, a **Válasz típusa**és a **válasz mód**alapértelmezett értékeit.
1. Választható Adja meg a **Domain_hint**értékét. Például: *ContosoAD*. A [tartományi útmutatók](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) olyan irányelvek, amelyek egy alkalmazás hitelesítési kérelmében szerepelnek. Felhasználhatják a felhasználót az összevont identitásszolgáltató bejelentkezési oldalára. Vagy egy több-bérlős alkalmazás is felhasználhatja, hogy a felhasználó egyenesen a márkás Azure AD bejelentkezési oldalára felgyorsítsa a bérlőt.
1. Az **Identity Provider jogcímek leképezése**területen adja meg a következő jogcím-hozzárendelési értékeket:

    * **Felhasználói azonosító**: *OID*
    * **Megjelenítendő név**: *név*
    * **Utónév**: *given_name*
    * **Vezetéknév**: *family_name*
    * **E-mail**: *unique_name*

1. Kattintson a **Mentés** gombra.

### <a name="add-the-facebook-identity-provider"></a>A Facebook-identitás szolgáltatójának hozzáadása

1. Válassza az **identitás-szolgáltatók**, majd a **Facebook**lehetőséget.
1. Adjon meg egy **nevet**. Például: *Facebook*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott Facebook-alkalmazás alkalmazás-azonosítóját.
1. Az **ügyfél titka**mezőben adja meg a rögzített alkalmazás titkát.
1. Kattintson a **Mentés** gombra.

## <a name="update-the-user-flow"></a>A felhasználói folyamat frissítése

Az előfeltételek részeként elvégzett oktatóanyagban létrehozott egy felhasználói folyamatot a *B2C_1_signupsignin1*nevű regisztrációhoz és bejelentkezéshez. Ebben a szakaszban a *B2C_1_signupsignin1* felhasználói folyamathoz adja hozzá az identitás-szolgáltatókat.

1. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget, majd válassza ki a *B2C_1_signupsignin1* felhasználói folyamatot.
2. Válassza az **identitás-szolgáltatók**lehetőséget, majd válassza ki a hozzáadott **Facebook** és **contoso Azure ad** Identity Providers szolgáltatást.
3. Kattintson a **Mentés** gombra.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. A létrehozott felhasználói folyamat áttekintés lapján válassza a **felhasználói folyamat futtatása**lehetőséget.
1. Az **alkalmazás**lapon válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** `https://jwt.ms`nak kell megjelennie.
1. Válassza a **felhasználói folyamat futtatása**lehetőséget, majd jelentkezzen be egy korábban hozzáadott identitás-szolgáltatóval.
1. Ismételje meg az 1 – 3. lépést a többi hozzáadott Identity Provider esetében.

Ha a bejelentkezési művelet sikeres, a rendszer átirányítja `https://jwt.ms` a dekódolású jogkivonatot, amely a következőhöz hasonlóan jelenik meg:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Az Identity Provider-alkalmazások létrehozása
> * Az identitás-szolgáltatók hozzáadása a bérlőhöz
> * Az identitás-szolgáltatók hozzáadása a felhasználói folyamathoz

Következő lépésként megtudhatja, hogyan szabhatja testre a felhasználók számára megjelenített lapok felhasználói felületét az alkalmazásokban:

> [!div class="nextstepaction"]
> [Az alkalmazások felhasználói felületének testreszabása Azure Active Directory B2C](tutorial-customize-ui.md)
