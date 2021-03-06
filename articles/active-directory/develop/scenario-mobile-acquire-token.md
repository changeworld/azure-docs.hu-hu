---
title: Token beszerzése webes API-k meghívásához (Mobile apps) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást. (Token beszerzése az alkalmazáshoz.)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 9427235f47a31da75426559a4285634ab2837577
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132453"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>A webes API-kat meghívó mobil alkalmazások jogkivonatának beolvasása

Ahhoz, hogy az alkalmazás meghívja a védett webes API-kat, hozzáférési jogkivonatra van szüksége. Ez a cikk végigvezeti a tokenek Microsoft Authentication Library (MSAL) használatával történő beszerzésének folyamatán.

## <a name="define-a-scope"></a>Hatókör definiálása

Ha jogkivonatot kér, meg kell határoznia egy hatókört. A hatókör határozza meg, hogy az alkalmazás milyen adataihoz férhet hozzá.  

A hatókör definiálásának legegyszerűbb módja a kívánt webes API-`App ID URI` és a hatókör `.default`ának összevonása. Ez a definíció közli a Microsoft Identity platformmal, hogy az alkalmazás a portálon beállított összes hatókört igényli.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Jogkivonatok beolvasása

### <a name="acquire-tokens-via-msal"></a>Jogkivonatok beszerzése a MSAL-on keresztül

A MSAL lehetővé teszi, hogy az alkalmazások csendes és interaktív módon szerezzenek jogkivonatokat. `AcquireTokenSilent()` vagy `AcquireTokenInteractive()`meghívásakor a MSAL egy hozzáférési jogkivonatot ad vissza a kért hatókörökhöz. A helyes minta egy csendes kérelem létrehozása, majd egy interaktív kérelemre való visszatérés.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

Először próbálkozzon a tokenek beszerzésével:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";
    
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
        
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        // Access token to call the Web API
        NSString *accessToken = result.accessToken;
    }
        
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```
 
```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"
        
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in
            
    guard let authResult = result, error == nil else {
                
    let nsError = error! as NSError
                
    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {
                    
            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }
            
    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

Ha a MSAL visszaadja a `MSALErrorInteractionRequired`, akkor próbálja meg interaktív módon beszerezze a jogkivonatokat:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error) 
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        NSString *accessToken = result.accessToken;
    }
}];
```

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

Az iOS-és macOS-MSAL különböző módosítókat támogat a jogkivonat interaktív vagy csendes lekéréséhez:
* [Token beolvasásának általános paraméterei](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Az interaktív jogkivonat beolvasására szolgáló paraméterek](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [A csendes jogkivonat beolvasására szolgáló paraméterek](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

Az alábbi példa a minimális kódot mutatja be, hogy a jogkivonat interaktív legyen. A példa a Microsoft Graph használatával olvassa be a felhasználó profilját.

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>Kötelező paraméterek a MSAL.NET

`AcquireTokenInteractive` csak egy kötelező paraméterrel rendelkezik: `scopes`. A `scopes` paraméter a karakterláncokat sorolja fel, amelyek meghatározzák azokat a hatóköröket, amelyekhez jogkivonat szükséges. Ha a jogkivonat Microsoft Graph, akkor a szükséges hatóköröket az egyes Microsoft Graph API-k API-referenciájában találja. A hivatkozásban lépjen az "engedélyek" szakaszra. 

Ha például [a felhasználó névjegyeit szeretné listázni](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), használja a "user. Read", a "Contacts. Read" hatókört. További információ: [Microsoft Graph engedélyek referenciája](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Androidon megadhatja a szülő tevékenységet, amikor a `PublicClientApplicationBuilder`használatával hozza létre az alkalmazást. Ha az adott időpontban nem határozza meg a fölérendelt tevékenységet, később megadhatja azt `.WithParentActivityOrWindow` használatával, a következő szakaszban leírtak szerint. Ha a szülő tevékenységet adja meg, akkor a jogkivonat az interakció után visszakerül a fölérendelt tevékenységbe. Ha nem adja meg, akkor a `.ExecuteAsync()` hívás kivételt dob.

#### <a name="specific-optional-parameters-in-msalnet"></a>Speciális választható paraméterek a MSAL.NET

A következő szakaszokban a MSAL.NET választható paramétereinek ismertetése olvasható. 

##### <a name="withprompt"></a>WithPrompt

A `WithPrompt()` paraméter a felhasználóval való interaktivitást vezérli egy kérdés megadásával.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Az osztály a következő konstansokat határozza meg:

- `SelectAccount` kényszeríti a biztonságijogkivonat-szolgáltatást (STS), hogy bemutassa a fiók – kijelölés párbeszédpanelt. A párbeszédpanel tartalmazza azokat a fiókokat, amelyekhez a felhasználó rendelkezik munkamenettel. Ezt a lehetőséget akkor használhatja, ha engedélyezni szeretné a felhasználó számára a különböző identitások közötti választást. Ez a beállítás a MSAL `prompt=select_account` küldését hajtja az identitás-szolgáltatónak. 
    
    Az alapértelmezett `SelectAccount` állandó, és a rendelkezésre álló információk alapján hatékonyan biztosítja a lehető legjobb élményt. A rendelkezésre álló információk közé tartozhat a fiók, a felhasználó munkamenetének jelenléte stb. Ne módosítsa ezt az alapértelmezést, ha nem rendelkezik megfelelő indokkal.
- `Consent` lehetővé teszi, hogy a felhasználó jóváhagyása akkor is megkérdezzen, ha a hozzájárulást korábban megadták. Ebben az esetben a MSAL `prompt=consent` küld az identitás-szolgáltatónak. 

    Előfordulhat, hogy a `Consent` állandót szeretné használni a biztonsággal irányított alkalmazásokban, ahol a szervezet irányításához a felhasználóknak az alkalmazás használatának minden egyes alkalmával meg kell tekinteniük a belefoglalt hozzáférés párbeszédpanelt.
- `ForceLogin` lehetővé teszi a szolgáltatás számára, hogy hitelesítő adatokra kérje a felhasználót, még akkor is, ha a kérés nem szükséges. 

    Ez a beállítás akkor lehet hasznos, ha a jogkivonat-beszerzés sikertelen, és engedélyezni szeretné a felhasználó számára a bejelentkezést. Ebben az esetben a MSAL `prompt=login` küld az identitás-szolgáltatónak. Érdemes lehet ezt a lehetőséget használni a biztonsággal irányított alkalmazásokban, ahol a szervezet irányításához a felhasználónak minden alkalommal be kell jelentkeznie, amikor az alkalmazás egyes részeihez hozzáférnek.
- a `Never` csak a .NET 4,5 és a Windows-futtatókörnyezet (WinRT) esetében használható. Ez a konstans nem kéri a felhasználót, de megpróbálja használni a rejtett beágyazott webes nézetben tárolt cookie-t. További információ: [webböngészők használata a MSAL.net](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers).

    Ha ez a beállítás meghiúsul, akkor `AcquireTokenInteractive` kivételt jelez, hogy a felhasználói felületi interakcióra van szükség. Ezután egy másik `Prompt` paramétert kell használnia.
- `NoPrompt` nem küld üzenetet az identitás-szolgáltatónak. 

    Ez a beállítás csak a Azure Active Directory B2C szerkesztési profiljaihoz használható. További információ: B2C- [specifikusak](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Egy speciális forgatókönyvben használja a `WithExtraScopeToConsent`-módosítót, ahol azt szeretné, hogy a felhasználó előzetes beleegyezést nyújtson több erőforráshoz. Ezt a módosítót akkor használhatja, ha nem szeretne növekményes beleegyezést használni, amelyet általában a MSAL.NET vagy a Microsoft Identity platform 2,0-mel használ. További információ: [a felhasználó beleegyezett több erőforrásra](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

Példa a következő kódra: 

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Egyéb választható paraméterek

A `AcquireTokenInteractive`további választható paramétereinek megismeréséhez tekintse meg a [AcquireTokenInteractiveParameterBuilder dokumentációját](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Jogkivonatok beszerzése a protokollon keresztül

A jogkivonatok lekéréséhez nem ajánlott közvetlenül a protokollt használni. Ha így tesz, az alkalmazás nem támogatja az egyszeri bejelentkezést (SSO), az Eszközkezelőt és a feltételes hozzáférést érintő forgatókönyveket.

Ha a protokollt használja a Mobile apps-jogkivonatok beszerzéséhez, két kérést kell tennie: 

* Engedélyezési kód beszerzése.
* Egy jogkivonat kódjának cseréje.

#### <a name="get-an-authorization-code"></a>Engedélyezési kód beszerzése

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Hozzáférés kérése és a jogkivonat frissítése

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes API meghívása](scenario-mobile-call-api.md)
