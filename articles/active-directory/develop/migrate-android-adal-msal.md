---
title: ADAL az Androidhoz készült MSAL-áttelepítési útmutatóhoz | Azure
description: Ismerje meg, hogyan telepítheti át a Azure Active Directory Authentication Library (ADAL) Android-alkalmazást a Microsoft hitelesítési tárba (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084055"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>ADAL a MSAL áttelepítési útmutató Android rendszerhez

Ez a cikk azokat a módosításokat mutatja be, amelyeket a Azure Active Directory hitelesítési függvénytárat (ADAL) használó alkalmazások áttelepíteni kell a Microsoft Authentication Library (MSAL) használatára.

## <a name="difference-highlights"></a>Különbségi csúcsfények

A ADAL az Azure Active Directory v 1.0-s végponttal működik. A Microsoft Authentication Library (MSAL) a Microsoft Identity platformmal működik – korábbi nevén Azure Active Directory v 2.0-végpont. A Microsoft Identity platform eltér a Azure Active Directory 1.0-s verziótól:

Támogatja
  - Szervezeti identitás (Azure Active Directory)
  - Nem szervezeti identitások, például Outlook.com, Xbox Live stb.
  - (Csak B2C) Összevont bejelentkezés a Google, a Facebook, a Twitter és az Amazon között

- A szabványokkal kompatibilis szabványok:
  - OAuth 2.0-s verzió
  - OpenID Connect (OIDC)

A MSAL nyilvános API fontos változásokat vezet be, beleértve a következőket:

- Új modell a tokenek eléréséhez:
  - A ADAL hozzáférést biztosít a tokenekhez a kiszolgálót jelképező `AuthenticationContext`on keresztül. A MSAL hozzáférést biztosít a tokenekhez az ügyfelet képviselő `PublicClientApplication`on keresztül. Az ügyfél-fejlesztőknek nem kell új `PublicClientApplication` példányt létrehozniuk minden olyan szolgáltató számára, amelyre a használatához szükségük van. Csak egy `PublicClientApplication` konfiguráció szükséges.
  - Az erőforrás-azonosítók mellett hatóköröket használó hozzáférési tokenek kérésének támogatása.
  - Növekményes beleegyezett támogatás támogatása. A fejlesztők hatókört igényelhetnek, mivel a felhasználó több és több funkciót is elérhet az alkalmazásban, beleértve azokat is, amelyek nem szerepelnek az alkalmazás regisztrálásakor.
  - A hatóságok már nem lesznek érvényesítve a Futtatás ideje alatt. Ehelyett a fejlesztő deklarálja az "ismert hatóságok" listáját a fejlesztés során.
- Token API módosításai:
  - A ADAL `AcquireToken()` először egy csendes kérést tesz elérhetővé. Ha ezt elmulasztja, interaktív kérelmet készít. Ennek a viselkedésnek a következtében egyes fejlesztők csak a `AcquireToken`ra támaszkodnak, ami azt eredményezte, hogy a felhasználó időnként váratlanul kéri a hitelesítő adatok megadását. A MSAL használatához a fejlesztőknek szándékosnak kell lennie, amikor a felhasználó felhasználói FELÜLETi kérést kap.
    - `AcquireTokenSilent` mindig egy csendes kérést eredményez, amely sikeres vagy sikertelen lesz.
    - `AcquireToken` mindig olyan kérést eredményez, amely felhasználói felületen keresztül kéri a felhasználót.
- A MSAL egy alapértelmezett böngészőből vagy egy beágyazott webes nézetből támogatja a bejelentkezést:
  - Alapértelmezés szerint a rendszer az eszköz alapértelmezett böngészőjét használja. Ez lehetővé teszi a MSAL számára, hogy egy vagy több bejelentkezett fiók esetében már jelen lehet a hitelesítési állapot (cookie-k) használata. Ha nincs hitelesítő állapot, az MSAL-n keresztül történő hitelesítés során a rendszer a hitelesítési állapot (cookie-k) segítségével hozza létre az adott böngészőben használni kívánt egyéb webalkalmazások előnyeit.
- Új kivétel modellje:
  - A kivételek világosabban határozzák meg, hogy milyen típusú hiba történt, és a fejlesztőnek hogyan kell megoldania.
- A MSAL támogatja a `AcquireToken` és a `AcquireTokenSilent` hívások paramétereinek objektumait.
- A MSAL támogatja a deklaratív konfigurációját:
  - Ügyfél-azonosító, átirányítási URI.
  - Beágyazott vs alapértelmezett böngésző
  - Hatóságok
  - HTTP-beállítások, például olvasási és kapcsolati időtúllépés

## <a name="your-app-registration-and-migration-to-msal"></a>Az alkalmazás regisztrálása és migrálása a MSAL-be

A MSAL használatához nem szükséges módosítania a meglévő alkalmazás regisztrációját. Ha szeretné kihasználni a növekményes/fokozatos jóváhagyást, érdemes áttekintenie a regisztrációt, hogy azonosítsa a Növekményesen igényelni kívánt hatóköröket. A hatókörökkel és a növekményes engedélyekkel kapcsolatos további információk a következők.

Ha az alkalmazás regisztrálva van a portálon, megjelenik az **API-engedélyek** lap. Itt megtalálhatók azon API-k és engedélyek (hatókörök) listája, amelyeket az alkalmazás jelenleg úgy konfigurált, hogy hozzáférést igényeljen. Emellett az egyes API-engedélyekhez társított hatókör-nevek listáját is megjeleníti.

### <a name="user-consent"></a>Felhasználói beleegyezett

A ADAL és a HRE v1 végpont esetében az első használatkor a felhasználó beleegyezik a saját erőforrásaiba. A MSAL és a Microsoft Identity platformmal Növekményesen is kérhető. A növekményes hozzájárulás olyan engedélyek esetében hasznos, amelyeket a felhasználó a magas jogosultsággal rendelkezők számára is megvizsgálhat, vagy ha nem, akkor az engedély szükségességének egyértelmű magyarázata nélkül is megkérdőjelezheti azt. Előfordulhat, hogy ezek az engedélyek ADAL a felhasználótól az alkalmazásba való bejelentkezést.

> [!TIP]
> Javasoljuk, hogy növekményes beleegyezést alkalmazzon olyan helyzetekben, ahol további kontextust kell megadnia a felhasználónak arról, hogy miért van szüksége az alkalmazásnak egy engedélyre.

### <a name="admin-consent"></a>Rendszergazdai jóváhagyás

A szervezeti rendszergazdák megadhatják, hogy az alkalmazás a szervezet minden tagjának nevében a szükséges engedélyekkel rendelkezik. Egyes szervezetek csak a rendszergazdák számára engedélyezik az alkalmazásoknak való hozzájárulásukat. A rendszergazdai jóváhagyáshoz meg kell adnia az alkalmazás által használt API-engedélyeket és hatóköröket az alkalmazás regisztrálásakor.

> [!TIP]
> Annak ellenére, hogy a MSAL használatával olyan hatókört is igényelhet, amely nem szerepel az alkalmazás regisztrációjában, javasoljuk, hogy frissítse az alkalmazás regisztrációját, hogy tartalmazza az összes olyan erőforrást és hatókört, amelyet a felhasználó bármikor engedélyezhet.

## <a name="migrating-from-resource-ids-to-scopes"></a>Áttelepítés erőforrás-azonosítóról hatókörökre

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>A hitelesítés és az engedélyezés kérése az első használathoz szükséges összes engedélyhez

Ha jelenleg a ADAL-t használja, és nem kell növekményes beleegyeznie, akkor a MSAL használatának legegyszerűbb módja, ha az új `AcquireTokenParameter` objektummal tesz `acquireToken` kérelmet, és beállítja az erőforrás-azonosító értékét.

> [!CAUTION]
> Nem lehet beállítani mindkét hatókört és egy erőforrás-azonosítót. A mindkettő beállítására tett kísérlet egy `IllegalArgumentException`fog eredményezni.

 Ez azt eredményezi, hogy ugyanazt a v1-es viselkedést fogja használni. Az alkalmazás regisztrálásához szükséges összes engedélyt a felhasználó az első interakció során kéri le.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Csak a szükséges engedélyek hitelesítése és kérése

A növekményes jóváhagyás kihasználása érdekében készítsen egy listát az alkalmazás által az alkalmazás regisztrálásakor használt engedélyekről (hatókörökről), és rendezze őket két listába a következő alapján:

- A bejelentkezés során a felhasználó első interakciójában milyen hatóköröket szeretne kérni.
- Az alkalmazás egy fontos szolgáltatásához társított engedélyek, amelyeknek a felhasználónak is meg kell magyaráznia.

Miután megszervezte a hatóköröket, rendezze az egyes listákat, hogy melyik erőforrás (API) számára kíván jogkivonatot kérni. Valamint minden olyan hatókört, amelyet a felhasználónak egyszerre kell engedélyeznie.

A MSAL kérelmének támogatásához használt Parameters objektum:

- `Scope`: azoknak a hatóköröknek a listája, amelyekhez engedélyeket kíván kérni és hozzáférési jogkivonatot kap.
- `ExtraScopesToConsent`: a hatókörök további listája, amelyekhez engedélyt kell kérni, amíg egy másik erőforráshoz hozzáférési jogkivonatot kér. A hatókörök ezen listája lehetővé teszi, hogy csökkentse a felhasználói hitelesítés kérelmezéséhez szükséges időt. Ez azt jelenti, hogy kevesebb felhasználói engedély vagy hozzájárulási kérés van.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migrálás a AuthenticationContext-ből a PublicClientApplications-be

### <a name="constructing-publicclientapplication"></a>PublicClientApplication építése

A MSAL használatakor egy `PublicClientApplication`hoz létre. Ez az objektum modellezi az alkalmazás identitását, és a kérelmeket egy vagy több hatóság számára teszi elérhetővé. Ezzel az objektummal konfigurálhatja az ügyfél identitását, az átirányítási URI-t, az alapértelmezett szolgáltatót, függetlenül attól, hogy az eszköz böngészőjét vagy a beágyazott webes nézetet, a naplózási szintet és egyebeket használja-e.

Ezt az objektumot a JSON használatával is konfigurálhatja, amelyet fájlként vagy tárolóként is megadhat az APK-ban lévő erőforrásként.

Bár ez az objektum nem egypéldányos, belsőleg megosztott `Executors` használ mind az interaktív, mind a csendes kérésekhez.

### <a name="business-to-business"></a>Üzleti tevékenység

A ADAL-ben minden olyan szervezet, amelytől hozzáférési jogkivonatokat kér, a `AuthenticationContext`külön példányát igényli. A MSAL-ben ez már nem követelmény. Megadhatja azt a szolgáltatót, amelyről a csendes vagy interaktív kérelem részeként jogkivonatot szeretne kérni.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migrálás a hatóság általi ellenőrzésből az ismert hatóságok felé

A MSAL nem rendelkezik jelölővel a hitelesítésszolgáltató érvényesítésének engedélyezéséhez vagy letiltásához. A ADAL-ben és a MSAL korai kiadásaiban a hitelesítésszolgáltató ellenőrzése lehetővé teszi, hogy a kód a lehetséges kártékony hatóságtól származó jogkivonatokat kérjen. A MSAL most lekéri a Microsoft számára ismert hatóságok listáját, és egyesíti a listát a konfigurációban megadott hatóságokkal.

> [!TIP]
> Ha Ön Azure-beli üzleti felhasználói (B2C) felhasználó, ez azt jelenti, hogy többé nem kell letiltania a szolgáltatói ellenőrzést. Ehelyett vegye fel az összes támogatott Azure AD B2C-szabályzatot a MSAL-konfigurációban található hatóságként.

Ha olyan szolgáltatót próbál használni, amely nem ismeri a Microsoftot, és nem tartalmazza a konfigurációt, akkor egy `UnknownAuthorityException`fog kapni.

### <a name="logging"></a>Naplózás
Mostantól a konfiguráció részeként deklaratív módon konfigurálhatja a naplózást, például:

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>Migrálás a UserInfo-ből a fiókba

A ADAL-ben a `AuthenticationResult` `UserInfo` objektumot biztosít a hitelesített fiókra vonatkozó információk lekéréséhez. A "felhasználó" kifejezést, amely emberi vagy szoftveres ügynököt jelentett, olyan módon lett alkalmazva, hogy megnehezíti a kommunikációt, hogy egyes alkalmazások támogatják a több fiókkal rendelkező egyetlen felhasználót (például egy emberi vagy egy szoftver-ügynököt).

Vegyünk egy bankszámlát. Több fiókkal is rendelkezhet több pénzügyi intézményben. Amikor megnyit egy fiókot, Ön (a felhasználó) hitelesítő adatokat kap, például egy ATM-kártyát & PIN-kódot, amely az egyenlegének, a számla kifizetésének és egyebeknek az elérésére szolgál az egyes fiókokhoz. Ezeket a hitelesítő adatokat csak az azokat kiállító pénzügyi intézményben lehet használni.

Az analógia, például a pénzügyi intézmény fiókjai esetében a Microsoft Identity platform fiókjai a hitelesítő adatok használatával érhetők el. Ezek a hitelesítő adatok regisztrálva vannak a-ban vagy a-ben, a Microsoft számára. Vagy a Microsoft által a szervezet nevében.

Ha a Microsoft Identity platform különbözik egy pénzügyi intézménytől, ebben az analógiában az, hogy a Microsoft Identity platform olyan keretrendszert biztosít, amely lehetővé teszi, hogy a felhasználók egy fiókot és a hozzájuk tartozó hitelesítő adatokat használják, hogy hozzáférjenek a következőhöz tartozó erőforrásokhoz: több személy és szervezet. Ez olyan, mint egy bank által kibocsátott kártya, még egy másik pénzügyi intézmény. Ez azért működik, mert a szóban forgó összes szervezet a Microsoft Identity platformot használja, amely lehetővé teszi, hogy az egyik fiók több szervezet között legyen használatban. Például:

A Sam Contoso.com működik, de a Fabrikam.com-hoz tartozó Azure-beli virtuális gépeket kezeli. Ahhoz, hogy a Sam felügyelje a fabrikam virtuális gépeket, engedélyezni kell az elérését. Ez a hozzáférés a Sam-fiók Fabrikam.com való hozzáadásával, valamint a fióknak a virtuális gépekkel való együttműködését lehetővé tevő szerepkör megadásával adható meg. Ezt a Azure Portal fogja elvégezni.

Ha Sam Contoso.com-fiókját a Fabrikam.com tagjaként adja hozzá, egy új rekordot hoz létre a fabrikam. com Azure Active Directory a Sam számára. A Sam rekordja a Azure Active Directoryban felhasználói objektumként ismert. Ebben az esetben ez a felhasználói objektum a Sam felhasználói objektumára mutat vissza a Contoso.com-ben. Sam a fabrikam felhasználói objektuma a Sam helyi ábrázolása, és a Sam-hoz társított fiók adatainak tárolására szolgál a Fabrikam.com környezetében. A Contoso.com-ben a Sam 's title vezető DevOps-tanácsadó. A fabrikam-ben a Sam címe a kivitelező – Virtual Machines. A Contoso.com-ben a Sam nem felelős a virtuális gépek kezeléséhez. A Fabrikam.com-ben ez az egyetlen feladat funkciója. A Sam még mindig csak egy hitelesítő adatokkal rendelkezik, amelyekkel nyomon követheti a Contoso.com által kiadott hitelesítő adatokat.

Sikeres `acquireToken` hívása után egy `IAccount` objektumra mutató hivatkozás jelenik meg, amelyet később `acquireTokenSilent` kérésekben használhat.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Ha olyan alkalmazással rendelkezik, amely a fiók által képviselt bérlők fiókjával kapcsolatos jogcímeket fér hozzá, akkor `IAccount` objektumokat `IMultiTenantAccount`ba. Ez az interfész `ITenantProfiles`térképét nyújtja a bérlői azonosító alapján, amely lehetővé teszi a fiókhoz tartozó jogcímek elérését az egyes bérlők által, az aktuális fiókhoz viszonyítva.

A `IAccount` gyökerében lévő jogcímek és a `IMultiTenantAccount` mindig tartalmazzák a hazai bérlő jogcímeit. Ha még nem hozott létre jogkivonatra vonatkozó kérelmet a hazai bérlőn belül, akkor ez a gyűjtemény üres lesz.

## <a name="other-changes"></a>Egyéb módosítások

### <a name="use-the-new-authenticationcallback"></a>Az új AuthenticationCallback használata

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>Migrálás az új kivételekre

A ADAL egyetlen kivételt tartalmaz, `AuthenticationException`, amely magában foglalja a `ADALError` enumerálás értékének beolvasására szolgáló metódust.
A MSAL-ben van egy kivételek hierarchiája, és mindegyikhez tartozik egy adott hibakód.

MSAL-kivételek listája

|Kivétel  | Leírás  |
|---------|---------|
| `MsalException`     | A MSAL által kiváltott alapértelmezett kivétel.  |
| `MsalClientException`     | Kidobás, ha a hiba ügyféloldali. |
| `MsalArgumentException`     | Ha egy vagy több bemeneti argumentum érvénytelen. |
| `MsalClientException`     | Kidobás, ha a hiba ügyféloldali. |
| `MsalServiceException`     | Ha a hiba a kiszolgáló oldalon van kiváltva. |
| `MsalUserCancelException`     | A rendszer eldobta, ha a felhasználó megszakította a hitelesítési folyamatot.  |
| `MsalUiRequiredException`     | Ha a jogkivonat nem frissíthető csendesen.  |
| `MsalDeclinedScopeException`     | Akkor következik be, ha a kiszolgáló egy vagy több kért hatókört visszautasított.  |
| `MsalIntuneAppProtectionPolicyRequiredException` | Akkor dobták, ha az erőforráshoz engedélyezve van a MAMCA védelmi szabályzat. |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError a MsalException ErrorCode

### <a name="adal-logging-to-msal-logging"></a>ADAL naplózása a MSAL-Naplózásba

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
