---
title: A MSAL használata a Azure Active Directory B2CLearn használatával | Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazások számára a Azure AD B2C és a jogkivonatok megszerzését a biztonságos webes API-k meghívásához. Ezek a webes API-k lehetnek Microsoft Graph, más Microsoft API-k, a másoktól származó webes API-k vagy a saját webes API-k.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: e25564e64410701754390024a5bcfd39321343e2
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696452"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>A Microsoft hitelesítési függvénytárának használata a Azure Active Directory B2C való együttműködéshez

A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazások fejlesztői számára, hogy [Azure Active Directory B2C (Azure ad B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)használatával hitelesítsék a felhasználókat a közösségi és a helyi identitásokkal. Azure AD B2C egy Identitáskezelés szolgáltatás. A használatával testreszabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik a profiljaikat, amikor az alkalmazásaikat használják.

A Azure AD B2C lehetővé teszi az alkalmazások felhasználói felületének kiírását és testreszabását, hogy zökkenőmentes élményt nyújtson ügyfeleinek.

Ez az oktatóanyag azt mutatja be, hogyan használható a MSAL az Azure AD B2C való együttműködéshez.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre saját [Azure ad B2C bérlőt](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), hozzon létre egyet most. Meglévő Azure AD B2C bérlőt is használhat.

## <a name="javascript"></a>JavaScript

A következő lépések bemutatják, hogyan használható egy egyoldalas alkalmazás a Azure AD B2C a regisztrációhoz, a bejelentkezéshez és a védett webes API meghívásához.

### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása

A hitelesítés megvalósításához először regisztrálnia kell az alkalmazást. Lásd: [az alkalmazás regisztrálása](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) a részletes lépésekhez.

### <a name="step-2-download-the-sample-application"></a>2\. lépés: a minta alkalmazás letöltése

Töltse le a mintát zip-fájlként, vagy klónozást a GitHubról:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>3\. lépés: a hitelesítés konfigurálása

1. Nyissa meg az **index. html** fájlt a mintában.

1. Konfigurálja a mintát az alkalmazás regisztrálása során korábban rögzített ügyfél-AZONOSÍTÓval és kulccsal. Módosítsa a kód következő sorait úgy, hogy lecseréli az értékeket a címtár és az API-k nevére:

   ```javascript
   // The current application coordinates were pre-registered in a B2C tenant.

    var appConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };

    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902" //This is your client/application ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    // create UserAgentApplication instance
    const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   ```

Az oktatóanyagban szereplő [felhasználói folyamat](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) neve **B2C_1_signupsignin1**. Ha más felhasználói folyamatot használ, állítsa be a **szolgáltató** értékét erre a névre.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>4\. lépés: az alkalmazás konfigurálása `b2clogin.com` használatára

A `b2clogin.com` a `login.microsoftonline.com` helyett átirányítási URL-címként használhatja. Ezt a Azure AD B2C alkalmazásban teheti meg, amikor beállít egy identitás-szolgáltatót a regisztrációhoz és a bejelentkezéshez.

A `b2clogin.com` `https://your-tenant-name.b2clogin.com/your-tenant-guid` kontextusában való használata a következő hatásokkal jár:

- A Microsoft szolgáltatásai kevesebb helyet foglalnak el a cookie-fejlécben.
- Az URL-címek már nem tartalmaznak hivatkozást a Microsoftnak. A Azure AD B2C alkalmazás például valószínűleg `login.microsoftonline.com`ra hivatkozik.

 `b2clogin.com`használatához frissítenie kell az alkalmazás konfigurációját.  

- A **validateAuthority** tulajdonságot állítsa `false`értékre, hogy az átirányítások `b2clogin.com` használatával is előfordulhatnak.

Az alábbi példa bemutatja, hogyan állíthatja be a tulajdonságot:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> A Azure AD B2C alkalmazás valószínűleg több helyen található `login.microsoftonline.com`ra hivatkozik, például a felhasználói folyamat referenciái és a jogkivonat-végpontok. Győződjön meg arról, hogy az engedélyezési végpont, a jogkivonat-végpont és a kiállító frissítve lett `your-tenant-name.b2clogin.com`használatára.

Kövesse [ezt a MSAL JavaScript-mintát](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) a JavaScript MSAL előzetes verziójának használatával (MSAL. js). A minta egy hozzáférési jogkivonatot kap, és a Azure AD B2C által védett API-t hív meg.

## <a name="next-steps"></a>Következő lépések

További információk:

- [Egyéni szabályzatok](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [A felhasználói felület testreszabása](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)