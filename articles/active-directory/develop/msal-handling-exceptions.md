---
title: Hibák és kivételek (MSAL)
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan kezelheti a hibákat és kivételeket, a feltételes hozzáférést és a MSAL-alkalmazásokban felmerülő kihívásokat.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 018d0c3bc009f6063de75b9a479be650b2c06e7c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160844"
---
# <a name="handle-msal-exceptions-and-errors"></a>MSAL-kivételek és-hibák kezelése

Ez a cikk áttekintést nyújt a különböző típusú hibákról és javaslatokról a gyakori bejelentkezési hibák kezeléséhez.

## <a name="msal-error-handling-basics"></a>MSAL hibák kezelésének alapjai

A Microsoft Authentication Library (MSAL) kivételei az alkalmazások fejlesztői számára készültek – nem a végfelhasználók számára történő megjelenítéshez. A kivételek üzenetei nincsenek honosítva.

A kivételek és hibák feldolgozásakor használhatja a kivétel típusát és a hibakódot a kivételek megkülönböztetése érdekében.  A hibakódok listáját lásd: [hitelesítési és engedélyezési hibakódok](reference-aadsts-error-codes.md).

A bejelentkezési folyamat során hibák merülhetnek fel a beleegyezések, a feltételes hozzáférés (MFA, eszközkezelés, hely-alapú korlátozások), a jogkivonat kiállítása és a visszaváltás, valamint a felhasználói tulajdonságok miatt.

Az alkalmazás hibáinak kezelésével kapcsolatos további információkért tekintse meg a következő szakaszt, amely megfelel az Ön által használt nyelvnek.

## <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

A .NET-kivételek feldolgozásakor használhatja a kivétel típusát és a `ErrorCode` tagot a kivételek megkülönböztetésére. `ErrorCode` értékek a [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet)típusú állandók.

Tekintse meg a [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), a [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)és a [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet)mezőit is.

Ha a [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) eldobása megtörtént, próbálja meg a [hitelesítési és engedélyezési hibakódokat, és](reference-aadsts-error-codes.md) ellenőrizze, hogy a kód szerepel-e itt.

### <a name="common-net-exceptions"></a>Gyakori .NET-kivételek

Íme a gyakori kivételek, amelyek felmerülhetnek, és néhány lehetséges megoldás:  

| Kivétel | Hibakód | Kezelés|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: a felhasználó vagy a rendszergazda nem egyezett bele a (z) {appName} nevű, {appId} AZONOSÍTÓJÚ alkalmazás használatára. Interaktív engedélyezési kérelem küldése ehhez a felhasználóhoz és erőforráshoz.| Először be kell szereznie a felhasználói beleegyező engedélyt. Ha nem használja a .NET Core-ot (amely nem rendelkezik webes felhasználói felülettel), akkor a hívás (csak egyszer) `AcquireTokeninteractive`. Ha a .NET Core-t használja, vagy nem szeretne `AcquireTokenInteractive`t használni, a felhasználó megnyithatja az URL-címet a beleegyezés megadásához: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read. `AcquireTokenInteractive`hívása: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: a felhasználónak a többtényezős hitelesítés (MFA) használatára van szüksége.| Nincs megoldás. Ha az MFA konfigurálva van a bérlőhöz, és Azure Active Directory (HRE) úgy dönt, hogy kikényszeríti azt, akkor egy interaktív folyamatra kell visszaállítania, például `AcquireTokenInteractive` vagy `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: a Grant típus nem támogatott a */gyakori hibák* -vagy */consumers* -végpontokon. Használja a */Organizations* vagy a bérlő-specifikus végpontot. A */gyakori hibák*használta.| Ahogy az az Azure AD-ban is szerepel, a szolgáltatónak Bérlővel vagy egyéb */Organizations*kell rendelkeznie.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: a kérés törzsének a következő paramétert kell tartalmaznia: `client_secret or client_assertion`.| Ez a kivétel akkor fordulhat elő, ha az alkalmazása nincs nyilvános ügyfélalkalmazásként regisztrálva az Azure AD-ben. A Azure Portal szerkessze az alkalmazás jegyzékfájlját, és állítsa be a `allowPublicClient`t `true`re. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: nem azonosítható a bejelentkezett felhasználó| A függvénytár nem tudta lekérdezni az aktuálisan bejelentkezett Windows-felhasználót, vagy a felhasználó nem AD vagy HRE csatlakozott (a munkahelyhez csatlakoztatott felhasználók nem támogatottak). 1\. megoldás: a UWP győződjön meg arról, hogy az alkalmazás a következő képességekkel rendelkezik: vállalati hitelesítés, magánhálózat (ügyfél és kiszolgáló), felhasználói fiók adatai. 2\. megoldás: saját logikát implementálhat a Felhasználónév lekéréséhez (például john@contoso.com), és használhatja a felhasználónévhez tartozó `AcquireTokenByIntegratedWindowsAuth` űrlapot.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Ez a módszer Active Directory (AD) által közzétett protokollra támaszkodik. Ha a felhasználó a Azure Active Directory AD-biztonsági mentés nélkül lett létrehozva ("felügyelt" felhasználó), ez a metódus sikertelen lesz. Az AD-ben létrehozott és a HRE által támogatott felhasználók ("összevont" felhasználók) kihasználhatják ezt a nem interaktív hitelesítési módszert. Enyhítés: használjon interaktív hitelesítést.|

### `MsalUiRequiredException`

`AcquireTokenSilent()` `MsalError.InvalidGrantError`hívásakor a MSAL.NET által visszaadott általános állapotkódok egyike. Ez az állapotkód azt jelenti, hogy az alkalmazásnak újra meg kell hívnia a hitelesítési könyvtárat, de interaktív módban (AcquireTokenInteractive vagy AcquireTokenByDeviceCodeFlow a nyilvános ügyfélalkalmazások számára, és el kell végeznie egy kihívást a web Appsben). Ennek az az oka, hogy a hitelesítési jogkivonat kiállítása előtt további felhasználói beavatkozásra van szükség.

A `AcquireTokenSilent` meghibásodása esetén az az oka, hogy a jogkivonat-gyorsítótár nem rendelkezik a kérelemnek megfelelő jogkivonatokkal. A hozzáférési jogkivonatok 1 órán belül lejárnak, és `AcquireTokenSilent` egy új frissítési jogkivonat alapján próbál meg beolvasni egy újat (OAuth2 értelemben ez a "refresh token" folyamat). Ez a folyamat több okból is meghiúsulhat, például ha a bérlői rendszergazda szigorúbb bejelentkezési házirendeket konfigurál. 

A beavatkozás célja, hogy a felhasználó műveletet hajtson végre. Bizonyos feltételek egyszerűen feloldhatók a felhasználók számára (például elfogadják a használati feltételeket egyetlen kattintással), és néhány nem oldható fel a jelenlegi konfigurációval (például a szóban forgó gépnek egy adott vállalati hálózathoz kell csatlakoznia). Némi segítség a felhasználónak a többtényezős hitelesítés beállításában, vagy a Microsoft Authenticator telepítését az eszközön.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` besorolások enumerálása

A MSAL egy `Classification` mezőt tesz elérhetővé, amely lehetővé teszi, hogy jobb felhasználói élményt nyújtson, például hogy tájékoztassa a felhasználót arról, hogy a jelszavuk lejárt, vagy hogy bizonyos erőforrások használatához meg kell adnia az engedélyt. A támogatott értékek a `UiRequiredExceptionClassification` enumerálás részét képezik:

| Osztályozás    | Jelentés           | Ajánlott kezelési |
|-------------------|-------------------|----------------------|
| BasicAction | A feltételt a felhasználói interakció feloldható az interaktív hitelesítési folyamat során. | A AcquireTokenInteractively () hívása. |
| AdditionalAction | A feltételt az interaktív hitelesítési folyamaton kívül további javító interakciók is feloldhatók a rendszerrel. | Hívja meg a AcquireTokenInteractively () egy olyan üzenet megjelenítéséhez, amely ismerteti a javító műveletet. A hívó alkalmazás dönthet úgy, hogy elrejti a additional_actiont igénylő folyamatokat, ha a felhasználó nem valószínű, hogy elvégzi a javító műveletet. |
| MessageOnly      | A feltétel jelenleg nem oldható fel. Az interaktív hitelesítési folyamat elindításakor megjelenik egy üzenet, amely ismerteti a feltételt. | A AcquireTokenInteractively () hívásával megjeleníthető egy üzenet, amely ismerteti a feltételt. A AcquireTokenInteractively () UserCanceled-hibát ad vissza, miután a felhasználó beolvasta az üzenetet, és bezárja az ablakot. A hívó alkalmazás dönthet úgy, hogy elrejti a message_only eredményező folyamatokat, ha a felhasználó nem valószínű, hogy kihasználja az üzenetet.|
| ConsentRequired  | A felhasználói beleegyezés hiányzik vagy vissza lett vonva. | A AcquireTokenInteractively () hívásával adja meg a felhasználót a beleegyezés megadásához. |
| UserPasswordExpired | A felhasználó jelszava lejárt. | Hívja meg a AcquireTokenInteractively (), hogy a felhasználó alaphelyzetbe állíthatja a jelszavát. |
| PromptNeverFailed| Az interaktív hitelesítés a következő paraméterrel lett meghívva: prompt = soha, kényszerítve a MSAL, hogy a böngésző cookie-kra támaszkodjon, és ne jelenjen meg a böngésző. Ez nem sikerült. | A AcquireTokenInteractively () hívása prompt nélkül. None |
| AcquireTokenSilentFailed | A MSAL SDK nem rendelkezik elegendő információval a tokennek a gyorsítótárból való beolvasásához. Ennek oka az lehet, hogy a gyorsítótárban nincsenek tokenek, vagy nem található a fiók. A hibaüzenet további részleteket tartalmaz.  | A AcquireTokenInteractively () hívása. |
| Nincs    | További részletek nincsenek megadva. A feltételt a felhasználói interakció feloldható az interaktív hitelesítési folyamat során. | A AcquireTokenInteractively () hívása. |

## <a name="net-code-example"></a>.NET-kód – példa

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A MSAL. js olyan hibaüzeneteket biztosít, amelyek absztrakt és osztályozzák a gyakori hibák különböző típusait. Emellett felületet biztosít a hibák konkrét részleteinek eléréséhez, például a hibaüzenetek megfelelő kezeléséhez.

### <a name="error-object"></a>Hiba objektum

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

A hiba osztály kibővítésével a következő tulajdonságokat érheti el:
- `AuthError.message`: ugyanaz, mint a `errorMessage`.
- `AuthError.stack`: az eldobott hibák verem-nyomkövetése.

### <a name="error-types"></a>Hibák típusai

A következő típusú hibák érhetők el:

- `AuthError`: a MSAL. js függvénytár alapszintű hiba osztálya váratlan hibákhoz is használatos.

- `ClientAuthError`: Error osztály, amely az ügyfél-hitelesítéssel kapcsolatos problémát jelöli. A könyvtárból érkező hibák többsége ClientAuthErrors lesz. Ezek a hibák olyan dolgokból származnak, mint például a bejelentkezési módszer meghívása, ha a bejelentkezés már folyamatban van, a felhasználó megszakítja a bejelentkezést, és így tovább.

- `ClientConfigurationError`: a hiba osztály, kiterjeszti `ClientAuthError` a kérések előtt, amikor a megadott felhasználói konfigurációs paraméterek hibásak vagy hiányoznak.

- `ServerError`: a Error osztály a hitelesítési kiszolgáló által elküldést jelző karakterláncokat jelöli. Ezek lehetnek olyan hibák, mint például az érvénytelen kérelmek formátuma vagy paraméterei, vagy bármilyen más hiba, amely megakadályozza, hogy a kiszolgáló hitelesítse vagy engedélyezze a felhasználót.

- `InteractionRequiredAuthError`: a hiba osztály, a kibővíti az `ServerError`, hogy a kiszolgálói hibákat képviseljék, amelyek interaktív hívást igényelnek. Ezt a hibát `acquireTokenSilent`, ha a felhasználónak a kiszolgálóval való interakcióra van szüksége a hitelesítő adatok megadásához vagy a hitelesítéshez/engedélyezéshez való hozzájáruláshoz. A hibakódok a következők: `"interaction_required"`, `"login_required"`és `"consent_required"`.

Ha az átirányítási módszerekkel (`loginRedirect`, `acquireTokenRedirect`) történő hitelesítési folyamatok során hiba lép fel, regisztrálnia kell a visszahívást, amely sikeres vagy sikertelen volt, miután az átirányítás az `handleRedirectCallback()` metódus használatával történt a következő módon:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Az előugró élmény (`loginPopup`, `acquireTokenPopup`) módszere az ígért értéket adja vissza, így az ígéret mintáját (. then és. Catch) használhatja az alábbi módon történő kezeléséhez:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Interakciót igénylő hibák

A rendszer hibát ad vissza, ha nem interaktív módszert használ a tokenek beszerzéséhez, például `acquireTokenSilent`, de a MSAL nem tudta csendesen megtenni.

A lehetséges okok a következők:

- be kell jelentkeznie
- meg kell egyeznie
- egy többtényezős hitelesítési felülettel kell eljárnia.

A szervizelés olyan interaktív módszer meghívása, mint `acquireTokenPopup` vagy `acquireTokenRedirect`:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="pythontabpython"></a>[Python](#tab/python)

A Pythonhoz készült MSAL a legtöbb hibát az API-hívásból visszaadott értékként továbbítja a rendszer. A hiba a Microsoft Identity platform JSON-válaszát tartalmazó szótárként jelenik meg.

* A sikeres válasz tartalmazza a `"access_token"` kulcsot. A válasz formátumát az OAuth2 protokoll határozza meg. További információ: [5,1 sikeres válasz](https://tools.ietf.org/html/rfc6749#section-5.1)
* A hiba `"error"` és általában `"error_description"`t tartalmaz. A válasz formátumát az OAuth2 protokoll határozza meg. További információ: 5,2-es [hiba válasza](https://tools.ietf.org/html/rfc6749#section-5.2)

Ha a rendszer hibát ad vissza, a `"error_description"` kulcs egy emberi olvasási üzenetet tartalmaz; Ez általában egy Microsoft Identity platform hibakódot tartalmaz. A különböző hibakódokról a [hitelesítési és engedélyezési hibakódok](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes)című témakörben olvashat bővebben.

A MSAL for Python esetében a kivételek ritkák, mivel a legtöbb hibát egy hibaérték visszaadásával kezeli a rendszer. A `ValueError` kivétel csak akkor fordul elő, ha probléma van azzal, hogy hogyan próbálja használni a kódtárat – például ha az API-paraméter (ek) formátuma helytelen formátumú.

## <a name="javatabjava"></a>[Java](#tab/java)

A Java-MSAL háromféle kivétel létezik: `MsalClientException`, `MsalServiceException`és `MsalInteractionRequiredException`; minden, ami a `MsalException`tól örököl.

- a `MsalClientException` akkor fordul elő, ha a könyvtár vagy az eszköz helyi hibája következik be.
- `MsalServiceException` akkor kerül sor, amikor a biztonságos jogkivonat szolgáltatás (STS) hibaüzenetet ad vissza, vagy más hálózati hiba történik.
- az `MsalInteractionRequiredException` akkor fordul elő, ha a sikeres hitelesítéshez felhasználói felületi interakció szükséges.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` a kérelmekben visszaadott HTTP-fejléceket teszi elérhetővé az STS számára. Hozzáférés a `MsalServiceException.headers()` használatával

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Az `AcquireTokenSilently()` `InvalidGrantError`meghívásakor a Java MSAL által visszaadott leggyakoribb állapotkódok egyike. Ez azt jelenti, hogy a hitelesítési jogkivonat kiállítása előtt további felhasználói beavatkozásra van szükség. Az alkalmazásnak újra meg kell hívnia a hitelesítési függvénytárat, de interaktív módban, ha `AuthorizationCodeParameters` vagy `DeviceCodeParameters` küld nyilvános ügyfélalkalmazások számára.

A `AcquireTokenSilently` meghibásodása esetén az az oka, hogy a jogkivonat-gyorsítótár nem rendelkezik a kérelemnek megfelelő jogkivonattal. A hozzáférési tokenek egy órán belül lejárnak, és `AcquireTokenSilently` egy frissítési jogkivonat alapján megpróbál új újat kapni. A OAuth2 szempontjából ez a jogkivonat-folyamat frissítése. Ez a folyamat több okból is meghiúsulhat, például ha a bérlői rendszergazda szigorúbb bejelentkezési házirendeket konfigurál.

A hibát okozó bizonyos feltételek egyszerűen feloldhatók a felhasználók számára. Előfordulhat például, hogy el kell fogadniuk a használati feltételeket. Vagy lehet, hogy a kérés nem teljesíthető az aktuális konfigurációval, mert a gépnek egy adott vállalati hálózathoz kell kapcsolódnia.

A MSAL egy `reason` mezőt tesz elérhetővé, amelynek segítségével jobb felhasználói élményt biztosíthat. Előfordulhat például, hogy a `reason` mező arra utasítja a felhasználót, hogy a jelszavuk lejárt, vagy hogy meg kell adnia az egyes erőforrások használatának beleegyezikét. A támogatott értékek a `InteractionRequiredExceptionReason` enumerálás részét képezik:

| Ok | Jelentés | Ajánlott kezelési |
|---------|-----------|-----------------------------|
| `BasicAction` | A feltételt a felhasználói interakció feloldható az interaktív hitelesítési folyamat során. | `acquireToken` hívása interaktív paraméterekkel |
| `AdditionalAction` | A feltételt az interaktív hitelesítési folyamaton kívüli rendszerrel folytatott további javító interakciók is megoldhatók. | A `acquireToken` interaktív paraméterekkel meghívásával megjeleníthető egy üzenet, amely leírja, hogy milyen javító műveletet kell végrehajtania. A hívó alkalmazás dönthet úgy, hogy elrejti azokat a folyamatokat, amelyek további műveletet igényelnek, ha a felhasználó nem valószínű, hogy befejezi a javító műveletet. |
| `MessageOnly` | A feltétel jelenleg nem oldható fel. Interaktív hitelesítési folyamat elindítása a feltételt bemutató üzenet megjelenítéséhez. | A feltételt ismertető üzenet megjelenítéséhez hívja meg a `acquireToken`t interaktív paraméterekkel. a `acquireToken` a `UserCanceled` hibát fogja visszaadni, miután a felhasználó beolvasta az üzenetet, és bezárja az ablakot. Az alkalmazás dönthet úgy, hogy elrejti az üzenetet eredményező folyamatokat, ha a felhasználó nem valószínű, hogy kihasználja az üzenetet. |
| `ConsentRequired`| A felhasználói beleegyezés hiányzik vagy vissza lett vonva. |Hívja meg `acquireToken` interaktív paraméterekkel, hogy a felhasználó beleegyezik. |
| `UserPasswordExpired` | A felhasználó jelszava lejárt. | Hívja meg `acquireToken` interaktív paraméterrel, így a felhasználó alaphelyzetbe állíthatja a jelszavát |
| `None` |  További részletek. A feltételt a felhasználói interakció feloldható az interaktív hitelesítési folyamat során. | `acquireToken` hívása interaktív paraméterekkel |

### <a name="code-example"></a>Példa programkódra

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacostabiosmacos"></a>[iOS/macOS](#tab/iosmacos)

Az iOS-és macOS-hibák MSAL teljes listája a [MSALError enumerálásban](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)szerepel.

A rendszer a MSAL által létrehozott összes hibát `MSALErrorDomain` tartománnyal adja vissza.

Rendszerhibák esetén a MSAL az eredeti `NSError` a System API-ból adja vissza. Ha például a jogkivonat-beszerzés sikertelen a hálózati kapcsolat hiánya miatt, a MSAL hibát ad vissza a `NSURLErrorDomain` tartománnyal és `NSURLErrorNotConnectedToInternet` kóddal.

Javasoljuk, hogy legalább a következő két MSAL-hibát kezelje az ügyfél oldalán:

- `MSALErrorInteractionRequired`: a felhasználónak interaktív kérést kell tennie. Számos olyan feltételt okozhat, amely a hiba, például egy lejárt hitelesítési munkamenet vagy további hitelesítési követelmények iránti igényét eredményezi. A helyreállításhoz hívja meg a MSAL Interactive token Acquisition API-t. 

- `MSALErrorServerDeclinedScopes`: egy vagy több hatókör elutasítása megtörtént. Döntse el, hogy csak a megadott hatókörökkel folytatja-e a műveletet, vagy állítsa le a bejelentkezési folyamatot.

> [!NOTE]
> A `MSALInternalError` enumerálást csak referenciához és hibakereséshez érdemes használni. Ne próbálja meg automatikusan kezelni ezeket a hibákat futásidőben. Ha az alkalmazás a `MSALInternalError`alatti hibák valamelyikét tapasztalja, érdemes megmutatni egy általános felhasználói üzenetet, amely elmagyarázza, mi történt.

`MSALInternalErrorBrokerResponseNotReceived` például azt jelenti, hogy a felhasználó nem fejezte be a hitelesítést, és manuálisan visszaküldte az alkalmazást. Ebben az esetben az alkalmazásnak általános hibaüzenetet kell mutatnia arról, hogy a hitelesítés nem fejeződött be, és azt sugallja, hogy újra megpróbálják hitelesíteni magukat.

Az alábbi Objective-C mintakód bemutatja a gyakori hibák kezelésére vonatkozó ajánlott eljárásokat:

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>A feltételes hozzáférés és a jogcímek kihívásai

A tokenek csendes lekérése esetén előfordulhat, hogy az alkalmazás hibát jelez, ha egy olyan API-val kapcsolatos [feltételes hozzáférési jogcímet](../azuread-dev/conditional-access-dev-guide.md) igényel, mint például az MFA-szabályzat.

A hiba kezelésére szolgáló minta a jogkivonat interaktív beszerzése a MSAL használatával. A jogkivonat interaktív beszerzése arra kéri a felhasználót, hogy adja meg a szükséges feltételes hozzáférési szabályzatot.

Bizonyos esetekben, amikor feltételes hozzáférést igénylő API-t hív meg, az API-val kapcsolatos hiba esetén a jogcím-kihívás is megjelenhet. Ha például a feltételes hozzáférési házirend felügyelt eszközzel (Intune) rendelkezik, a hiba a következőhöz hasonló lesz [: AADSTS53000: az eszköznek az erőforrás eléréséhez](reference-aadsts-error-codes.md) vagy hasonlóhoz kell tartoznia. Ebben az esetben átadhatja a jogcímeket a beszerzési jogkivonat hívásában, hogy a rendszer kérje a felhasználótól a megfelelő szabályzat teljesítését.

### <a name="net"></a>.NET

Ha a MSAL.NET feltételes hozzáférést igénylő API-t hív meg, az alkalmazásnak kezelnie kell a jogcímek kivételeit. Ez olyan [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) fog megjelenni, ahol a [jogcím](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) tulajdonság nem üres.

A jogcím-kihívás kezeléséhez a `PublicClientApplicationBuilder` osztály `.WithClaim()` metódusát kell használnia.

### <a name="javascript"></a>JavaScript

Ha a tokeneket a MSAL. js használatával csendes úton (`acquireTokenSilent`használatával) kérdezi le, az alkalmazás hibákat kaphat, ha egy API-nak szüksége van egy [feltételes hozzáférési jogcímek](../azuread-dev/conditional-access-dev-guide.md) (például MFA-szabályzat) használatára.

A hiba kezelésére szolgáló minta egy interaktív hívást tesz lehetővé a token MSAL. js-ben való beszerzéséhez, például `acquireTokenPopup` vagy `acquireTokenRedirect` az alábbi példában látható módon:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

A jogkivonat interaktív beszerzése kéri a felhasználót, és lehetőséget biztosít számukra a kötelező feltételes hozzáférési szabályzat kielégítésére.

Ha feltételes hozzáférést igénylő API-t hív meg, az API-val kapcsolatos hiba esetén a jogcímek kérdését is megkaphatja. Ebben az esetben átadhatja a hibában visszaadott jogcímeket a `AuthenticationParameters.ts` osztály `claimsRequest` mezőjébe, hogy az megfeleljen a megfelelő házirendnek. 

További részletekért lásd: [további jogcímek igénylése](active-directory-optional-claims.md) .

### <a name="msal-for-ios-and-macos"></a>MSAL iOS és macOS rendszerekre

Az iOS és a macOS rendszerhez készült MSAL lehetővé teszi, hogy konkrét jogcímeket kérjen az interaktív és csendes jogkivonat-beszerzési forgatókönyvekben is.

Egyéni jogcímek igényléséhez adja meg a `claimsRequest` `MSALSilentTokenParameters` vagy `MSALInteractiveTokenParameters`.

További információért tekintse meg az [egyéni jogcímek kérése az MSAL használatával iOS és MacOS](request-custom-claims.md) rendszerhez című témakört.

## <a name="retrying-after-errors-and-exceptions"></a>Próbálkozás a hibák és kivételek után

A MSAL meghívásakor várhatóan saját újrapróbálkozási szabályzatokat kell megvalósítani. A MSAL HTTP-hívásokat kezdeményez a HRE szolgáltatáshoz, és alkalmanként hibák léphetnek fel, például a hálózat leállhat, vagy a kiszolgáló túlterhelt.  

### <a name="http-error-codes-500-600"></a>HTTP-hibakódok 500-600

A MSAL.NET egy egyszerű újrapróbálkozási mechanizmust valósít meg a 500-600-es HTTP-hibakódok miatti hibák esetén.

### <a name="http-429"></a>HTTP 429

Ha a szolgáltatás-jogkivonat-kiszolgáló (STS) túl sok kéréssel túl van terhelve, a 429 HTTP-hibát adja vissza, amely azt jelzi, hogy mennyi ideig nem próbálkozhat újra a `Retry-After` válasz mezőjében.

### <a name="net"></a>.NET

A [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) felületek `System.Net.Http.Headers.HttpResponseHeaders` tulajdonságként `namedHeaders`. A hibakód további információkat is használhat az alkalmazások megbízhatóságának növelése érdekében. Az ismertetett esetben a `RetryAfterproperty` (`RetryConditionHeaderValue`) és a számítást is használhatja, ha újra próbálkozik.

Íme egy példa egy Daemon-alkalmazásra, amely az ügyfél hitelesítő adatait használja. Ezt bármely, a jogkivonat beszerzésére szolgáló metódushoz igazíthatja.

```csharp
do
{
    retry = false;
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                           .ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (ex.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    …
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```
