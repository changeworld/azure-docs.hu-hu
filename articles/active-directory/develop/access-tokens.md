---
title: Microsoft Identity platform – hozzáférési tokenek referenciája | Azure
description: Ismerje meg az Azure AD v 1.0 és a Microsoft Identity platform (v 2.0) végpontok által kibocsátott hozzáférési jogkivonatokat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/2/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 752e8745a1cf2965d2dc88eaeee9404cf596547a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263243"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft Identity platform hozzáférési jogkivonatok

A hozzáférési tokenek lehetővé teszik az ügyfelek számára az Azure által védett API-k biztonságos hívását. A Microsoft Identity platform hozzáférési jogkivonatai [JWTs](https://tools.ietf.org/html/rfc7519), Base64 kódolású JSON-objektumok, amelyek az Azure-ban vannak aláírva. Az ügyfeleknek a hozzáférési jogkivonatokat átlátszatlan karakterláncként kell kezelniük, mivel a jogkivonat tartalma csak az erőforráshoz készült. Az érvényesítési és hibakeresési célokra a fejlesztők a [JWT.MS](https://jwt.ms)-t használó webhelyekről is JWTs dekódolni. Az ügyfél elérheti a v 1.0 vagy a v 2.0 végpontok hozzáférési tokenjét különböző protokollok használatával.

Ha az ügyfél hozzáférési jogkivonatot kér, az Azure AD az alkalmazás fogyasztásának hozzáférési jogkivonatával kapcsolatos metaadatokat is ad vissza. Ez az információ tartalmazza a hozzáférési jogkivonat lejárati idejét, valamint azokat a hatóköröket, amelyekhez érvényes. Ezek az adatmennyiség lehetővé teszi, hogy az alkalmazás a hozzáférési tokenek elemzése nélkül végezzen intelligens gyorsítótárazást a hozzáférési jogkivonatok használatával.

Ha az alkalmazás olyan erőforrás (webes API), amelyet az ügyfelek igényelhetnek, a hozzáférési tokenek hasznos információkat biztosítanak a hitelesítéshez és az engedélyezéshez, például a felhasználóhoz, az ügyfélhez, a kibocsátóhoz, az engedélyekhez és más szolgáltatásokhoz.

A következő részekben megtudhatja, hogyan ellenőrizheti és használhatja az erőforrás a jogcímeket egy hozzáférési jogkivonatban.

> [!IMPORTANT]
> A hozzáférési jogkivonatok létrehozása a jogkivonat *célközönsége* alapján történik, ami azt jelenti, hogy a jogkivonatban lévő hatóköröket birtokló alkalmazás.  Az [alkalmazás jegyzékfájljában](reference-app-manifest.md#manifest-reference) `accessTokenAcceptedVersion` erőforrás-beállítással `2` lehetővé teszi, hogy az ügyfél a v 1.0 végpontot hívja meg a v 2.0 hozzáférési jogkivonatok fogadására.  Hasonlóképpen, ezért a hozzáférési jogkivonat [választható jogcímeinek](active-directory-optional-claims.md) az ügyfélhez való módosítása nem módosítja a kapott hozzáférési jogkivonatot, amikor a rendszer jogkivonatot kér `user.read`hoz, amely az erőforrás tulajdonában van.
> Ugyanezen okból az ügyfélalkalmazás személyes fiókkal (például hotmail.com vagy outlook.com) történő tesztelésekor előfordulhat, hogy az ügyfél által fogadott hozzáférési jogkivonat átlátszatlan karakterlánc. Ennek az az oka, hogy az elérni kívánt erőforrás örökölt MSA (Microsoft-fiók), és az ügyfél által nem értelmezhető jegyeket kért.

## <a name="sample-tokens"></a>Mintául szolgáló tokenek

a 1.0-s és a 2.0-s verzióbeli tokenek hasonlóak, és számos azonos jogcímet tartalmaznak. Itt talál példát.

### <a name="v10"></a>1\.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Tekintse meg ezt az 1.0-s tokent a [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd)-ben.

### <a name="v20"></a>2\.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Tekintse meg ezt a v 2.0-tokent a [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt)-ben.

## <a name="claims-in-access-tokens"></a>Hozzáférési jogkivonatokban lévő jogcímek

A JWTs három darabra oszlanak:

* **Fejléc** – információt nyújt [a jogkivonat érvényesítéséről](#validating-tokens) , beleértve a jogkivonat típusával és aláírásával kapcsolatos információkat.
* **Hasznos** adat – tartalmazza a felhasználóval vagy alkalmazással kapcsolatos összes fontos adatot, amely a szolgáltatás hívását kísérli meg.
* **Aláírás** – a jogkivonat érvényesítéséhez használt nyersanyag.

Minden egyes darabot egy pont (`.`) és külön Base64 kódolás választja el egymástól.

A jogcímek csak akkor jelennek meg, ha egy érték van kitöltve. Tehát az alkalmazásnak nem kell függőséget kialakítania egy jogcímet illetően. Ilyenek például a `pwd_exp` (nem minden bérlő igényli a jelszavak lejáratát) vagy `family_name` (az ügyfél hitelesítő adatai ([1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)) az alkalmazások nevében találhatók, amelyek nem rendelkeznek névvel). A hozzáférési jogkivonat érvényesítéséhez használt jogcímek mindig jelen lesznek.

> [!NOTE]
> Egyes jogcímek segítségével az Azure AD biztonságos tokeneket használhat újbóli használat esetén. Ezeket a rendszer a leírásban nem a nyilvános felhasználáshoz, hanem "átlátszatlan" jelöléssel jelölte meg. Ezek a jogcímek esetleg nem jelenhetnek meg a jogkivonatokban, és az újakat értesítés nélkül lehet felvenni.

### <a name="header-claims"></a>Fejléc jogcímei

|Jogcím | Formátum | Leírás |
|--------|--------|-------------|
| `typ` | String – mindig "JWT" | Azt jelzi, hogy a token egy JWT.|
| `nonce` | Sztring | A jogkivonat-Visszajátszási támadások elleni védelemhez használt egyedi azonosító. Az erőforrás rögzítheti ezt az értéket a visszajátszás elleni védelemhez. |
| `alg` | Sztring | Megadja a token aláírásához használt algoritmust (például "RS256"). |
| `kid` | Sztring | Megadja a jogkivonat aláírásához használt nyilvános kulcs ujjlenyomatát. Az 1.0-s és a v 2.0-s hozzáférési jogkivonatokban lett kibocsátva. |
| `x5t` | Sztring | A functions (használatban és értékben) ugyanaz, mint `kid`. `x5t` egy örökölt jogcím, amely csak a v 1.0 hozzáférési jogkivonatokban lett kibocsátva kompatibilitási célból. |

### <a name="payload-claims"></a>Hasznos adatokhoz tartozó jogcímek

| Jogcím | Formátum | Leírás |
|-----|--------|-------------|
| `aud` | Karakterlánc, alkalmazás-azonosító URI | Azonosítja a jogkivonat kívánt címzettjét. Az azonosító jogkivonatokban a célközönség az alkalmazáshoz rendelt alkalmazásspecifikus azonosító, amely a Azure Portalban van hozzárendelve az alkalmazáshoz. Az alkalmazásnak ellenőriznie kell ezt az értéket, és el kell utasítania a jogkivonatot, ha az érték nem egyezik. |
| `iss` | Karakterlánc, STS URI | Azonosítja azt a biztonságijogkivonat-szolgáltatást (STS), amely létrehozza és visszaadja a tokent, valamint azt az Azure AD-bérlőt, amelyben a felhasználó hitelesítése megtörtént. Ha a kiállított jogkivonat egy v 2.0-token (lásd a `ver` jogcímet), akkor az URI-ja `/v2.0`végződik. Az a GUID, amely azt jelzi, hogy a felhasználó egy Microsoft-fiók fogyasztó felhasználója `9188040d-6c67-4c5b-b112-36a304b66dad`. Az alkalmazásnak a jogcím GUID részét kell használnia, hogy korlátozza azon bérlők készletét, amelyek be tudnak jelentkezni az alkalmazásba, ha vannak ilyenek. |
|`idp`| Karakterlánc, általában egy STS URI | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. Ez az érték megegyezik a kiállítói jogcímek értékével, kivéve, ha a felhasználói fiók nem ugyanabban a bérlőben található, mint a kiállító – vendég. Ha a jogcím nem létezik, az azt jelenti, hogy a `iss` értéke használható helyette.  A szervezeti környezetben használt személyes fiókok esetében (például egy Azure AD-bérlőhöz meghívott személyes fiók) a `idp` jogcím a következő lehet: "live.com" vagy egy STS URI, amely tartalmazza a Microsoft-fiók bérlő `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| `iat` | int, egy UNIX timestamp | A "kiállító" érték azt jelzi, hogy a jogkivonat hitelesítése mikor történt. |
| `nbf` | int, egy UNIX timestamp | A "NBF" (nem korábban) jogcím azt az időpontot határozza meg, ameddig a JWT nem fogadható el a feldolgozáshoz. |
| `exp` | int, egy UNIX timestamp | Az "exp" (lejárati idő) jogcím azt a lejárati időt azonosítja, amely után a JWT nem fogadható el feldolgozásra. Fontos megjegyezni, hogy egy erőforrás még a token elutasítása előtt is elutasítja a jogkivonatot, például ha a hitelesítés megváltozása vagy jogkivonat-visszavonás észlelhető. |
| `aio` | Átlátszatlan karakterlánc | Az Azure AD által az adatok a jogkivonat újrafelhasználásához használt belső jogcímek. Az erőforrások nem használhatják ezt a jogcímet. |
| `acr` | Karakterlánc, "0" vagy "1" | Csak a v 1.0 jogkivonatokban jelennek meg. A "hitelesítési környezet osztálya" jogcím. A "0" érték azt jelzi, hogy a végfelhasználói hitelesítés nem felelt meg az ISO/IEC 29115 követelményeinek. |
| `amr` | Karakterláncok JSON-tömbje | Csak a v 1.0 jogkivonatokban jelennek meg. Azt határozza meg, hogy a jogkivonat tárgya hogyan lett hitelesítve. További részletekért tekintse meg [az AMR-jogcím szakaszt](#the-amr-claim) . |
| `appid` | Karakterlánc, GUID | Csak a v 1.0 jogkivonatokban jelennek meg. A tokent használó ügyfél alkalmazás-azonosítója. Az alkalmazás önállóan vagy egy felhasználó nevében működhet. Az alkalmazás azonosítója általában egy Application objektumot jelöl, de az Azure AD-ben egy egyszerű szolgáltatásnév-objektumot is jelenthet. |
| `appidacr` | "0", "1" vagy "2" | Csak a v 1.0 jogkivonatokban jelennek meg. Azt jelzi, hogy az ügyfél hogyan lett hitelesítve. Nyilvános ügyfél esetén az érték "0". Ha az ügyfél-azonosító és az ügyfél titkos kulcsa van használatban, az érték "1". Ha ügyféltanúsítvány lett használva a hitelesítéshez, az érték a következő: "2". |
| `azp` | Karakterlánc, GUID | Csak a v 2.0-tokenekben szerepel, `appid`helyettesíti. A tokent használó ügyfél alkalmazás-azonosítója. Az alkalmazás önállóan vagy egy felhasználó nevében működhet. Az alkalmazás azonosítója általában egy Application objektumot jelöl, de az Azure AD-ben egy egyszerű szolgáltatásnév-objektumot is jelenthet. |
| `azpacr` | "0", "1" vagy "2" | Csak a v 2.0-tokenekben szerepel, `appidacr`helyettesíti. Azt jelzi, hogy az ügyfél hogyan lett hitelesítve. Nyilvános ügyfél esetén az érték "0". Ha az ügyfél-azonosító és az ügyfél titkos kulcsa van használatban, az érték "1". Ha ügyféltanúsítvány lett használva a hitelesítéshez, az érték a következő: "2". |
| `preferred_username` | Sztring | A felhasználót jelölő elsődleges Felhasználónév. Ez lehet egy e-mail-cím, telefonszám vagy egy általános Felhasználónév megadott formátum nélkül. Az értéke változtatható, és idővel változhat. Mivel ez az érték változhat, nem használható az engedélyezési döntések elvégzéséhez.  Ez a Felhasználónév-útmutatók esetében is használható. A jogcím megszerzéséhez a `profile` hatókörre van szükség. |
| `name` | Sztring | Egy ember által olvasható értéket biztosít, amely azonosítja a jogkivonat tárgyát. Az érték nem garantált, hogy egyedi, megváltoztathatatlan, és kizárólag megjelenítési célokra való használatra készült. A jogcím megszerzéséhez a `profile` hatókörre van szükség. |
| `scp` | Karakterlánc, a hatókörök szóközzel tagolt listája | Annak az alkalmazásnak a hatóköre, amelyhez az ügyfélalkalmazás kérelmezte (és fogadta) a hozzájárulásukat. Az alkalmazásnak ellenőriznie kell, hogy ezek a hatókörök érvényesek-e az alkalmazás számára, és a hatókörök értéke alapján kell-e engedélyezési döntéseket hoznia. Csak a [felhasználói jogkivonatok](#user-and-application-tokens)részeként. |
| `roles` | Karakterláncok tömbje, engedélyek listája | Az alkalmazás által a kérelmező alkalmazásnak vagy felhasználónak megadott engedélyekkel rendelkező engedélyek készlete. Az [alkalmazás-jogkivonatok](#user-and-application-tokens)esetében ez az ügyfél-hitelesítő adatok ([v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)) felhasználói hatókörök helyett használatos.  [Felhasználói jogkivonatok](#user-and-application-tokens) esetén ez a felhasználó által a célalkalmazás számára hozzárendelt szerepkörökkel van feltöltve. |
| `wids` | [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) GUID-azonosítók tömbje | Az ehhez a felhasználóhoz rendelt bérlői szintű szerepköröket jelöli a [rendszergazdai szerepkörök lapon](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids)lévő szerepkörök szakasza alapján.  Ez a jogcím alkalmazáson alapul, az [alkalmazás jegyzékfájljának](reference-app-manifest.md)`groupMembershipClaims` tulajdonságán keresztül.  Az "all" vagy a "DirectoryRole" beállítás megadása kötelező.  Előfordulhat, hogy a jogkivonat-hossz miatti implicit folyamat során beszerzett jogkivonatok nem jelennek meg. |
| `groups` | GUID-azonosítók JSON-tömbje | A tulajdonos csoportjának tagságát képviselő objektumazonosítók benyújtása. Ezek az értékek egyediek (lásd: objektumazonosító), és biztonságosan használhatók a hozzáférés felügyeletéhez, például az erőforrásokhoz való hozzáférés engedélyezésének kényszerítéséhez. A csoportok jogcímben szereplő csoportok alkalmazáson belüli alapon vannak konfigurálva, az [alkalmazás jegyzékfájljának](reference-app-manifest.md)`groupMembershipClaims` tulajdonságán keresztül. A Null érték kizárja az összes csoportot, a "SecurityGroup" érték pedig csak Active Directory biztonsági csoportba tartozó tagságot tartalmaz, az "all" érték pedig a biztonsági csoportokat és az Office 365 terjesztési listáját is tartalmazza. <br><br>Tekintse meg az alábbi `hasgroups`-jogcímet, amely részletesen ismerteti a `groups` jogcím használatát az implicit támogatással. <br>Más folyamatok esetében, ha a felhasználó által birtokolt csoportok száma meghaladja a határértéket (150 az SAML, 200 for JWT esetében), a rendszer felvesz egy túllépési jogcímet a felhasználóhoz tartozó csoportok listáját tartalmazó Microsoft Graph végpontra. |
| `hasgroups` | Logikai | Ha van, mindig `true`, amely azt jelöli, hogy a felhasználó legalább egy csoportban van. Az JWTs implicit engedélyezési folyamatokban használt `groups`-jogcím helyett, ha a teljes csoportokra vonatkozó jogcím kiterjeszti az URI-töredéket az URL-cím hosszán túl (jelenleg 6 vagy több csoport). Azt jelzi, hogy az ügyfélnek a Microsoft Graph API-t kell használnia a felhasználó csoportjainak (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`) meghatározásához. |
| `groups:src1` | JSON-objektum | A nem hosszúságú jogkivonat-kérelmek esetében (lásd a fenti `hasgroups`), de még mindig túl nagy a tokenhez, a rendszer a felhasználó teljes csoportok listájára mutató hivatkozást tartalmaz. Elosztott jogcímként az SAML esetében új jogcímként JWTs az `groups` jogcím helyett. <br><br>**Példa JWT értékre**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | Karakterlánc, GUID | Az a rendszerbiztonsági tag, amelyről a jogkivonat adatokat érvényesít, például egy alkalmazás felhasználóját. Ez az érték nem módosítható, és nem rendelhető hozzá újra, és nem használható újra. Az engedélyezési ellenőrzések biztonságos végrehajtásához használható, például ha a jogkivonat egy erőforrás elérésére szolgál, és kulcsként használható az adatbázis tábláiban. Mivel a tulajdonos mindig szerepel az Azure AD által felmerülő jogkivonatokban, javasoljuk, hogy használja ezt az értéket egy általános célú engedélyezési rendszeren. A tulajdonos azonban egy páros-azonosító – egyedi egy adott alkalmazás-AZONOSÍTÓhoz. Ezért ha egyetlen felhasználó két különböző alkalmazásba jelentkezik be két különböző ügyfél-azonosítóval, akkor ezek az alkalmazások két különböző értéket kapnak a tulajdonos jogcímek számára. Az architektúrától és az adatvédelmi követelményektől függően előfordulhat, hogy ez nem szükséges. Lásd még a `oid`-jogcím (amely a bérlőn belüli alkalmazások esetében is megmarad). |
| `oid` | Karakterlánc, GUID | Egy objektum megváltoztathatatlan azonosítója a Microsoft Identity platformon, ebben az esetben egy felhasználói fiók. Emellett az engedélyezési ellenőrzések biztonságos elvégzésére, valamint az adatbázis tábláiban lévő kulcsra is felhasználható. Ez az azonosító egyedileg azonosítja a felhasználót az alkalmazások között – két különböző alkalmazás, amely ugyanazon a felhasználón jelentkezik be, ugyanazokat az értékeket kapja meg az `oid` jogcímben. Így `oid` a Microsoft online szolgáltatások, például a Microsoft Graph lekérdezése során is használható. A Microsoft Graph egy adott [felhasználói fiók](/graph/api/resources/user)`id` tulajdonságának adja vissza ezt az azonosítót. Mivel a `oid` lehetővé teszi több alkalmazás számára a felhasználók korrelációját, a jogcímek fogadásához a `profile` hatókörre van szükség. Vegye figyelembe, hogy ha egyetlen felhasználó több bérlőn is létezik, akkor a felhasználó minden bérlőn egy másik objektumazonosítót fog tartalmazni – ezeket a rendszer akkor is eltérő fióknak tekinti, ha a felhasználó ugyanazzal a hitelesítő adatokkal jelentkezik be az egyes fiókokba. |
| `tid` | Karakterlánc, GUID | Azt az Azure AD-bérlőt jelöli, amelyhez a felhasználó származik. Munkahelyi és iskolai fiókok esetén a GUID az a szervezet, amelyhez a felhasználó tartozik. A személyes fiókok esetében az érték `9188040d-6c67-4c5b-b112-36a304b66dad`. A jogcím megszerzéséhez a `profile` hatókörre van szükség. |
| `unique_name` | Sztring | Csak a v 1.0 jogkivonatokban jelennek meg. A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. Ez az érték nem garantált, hogy a bérlőn belül egyediek legyenek, és csak megjelenítési célokra használhatók. |
| `uti` | Átlátszatlan karakterlánc | Az Azure által a jogkivonatok újraellenőrzéséhez használt belső jogcím. Az erőforrások nem használhatják ezt a jogcímet. |
| `rh` | Átlátszatlan karakterlánc | Az Azure által a jogkivonatok újraellenőrzéséhez használt belső jogcím. Az erőforrások nem használhatják ezt a jogcímet. |
| `ver` | Karakterlánc, `1.0` vagy `2.0` | Megadja a hozzáférési jogkivonat verzióját. |

**Csoporton túli jogcímek**

Annak biztosítása érdekében, hogy a jogkivonat mérete ne haladja meg a HTTP-fejlécek méretének korlátait, az Azure AD korlátozza a csoportok jogcímeben foglalt objektumazonosítók számát. Ha a felhasználó több csoport tagja, mint a túlhasználati korlát (SAML-tokenek esetében 150, 200 JWT-token esetében), akkor az Azure AD nem bocsátja ki a groups jogcímet a jogkivonatban. Ehelyett a jogkivonat olyan túlhasználati jogcímet tartalmaz, amely azt jelzi, hogy az alkalmazás lekérdezi a Microsoft Graph API-t a felhasználó csoporttagság beolvasásához.

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
 }
 ```

Az [alkalmazás-létrehozási parancsfájlok](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) mappában megadott `BulkCreateGroups.ps1` segítségével tesztelheti a túlhasználatos forgatókönyveket.

#### <a name="v10-basic-claims"></a>v 1.0 alapszintű jogcímek

A következő jogcímek a v 1.0 jogkivonatokban lesznek felszámítva, ha vannak ilyenek, de alapértelmezés szerint nem szerepelnek a 2.0-s jogkivonatokban. Ha a 2.0-s verzióját használja, és szüksége van ezekre a jogcímek egyikére, kérje meg őket az [opcionális jogcímek](active-directory-optional-claims.md)használatával.

| Jogcím | Formátum | Leírás |
|-----|--------|-------------|
| `ipaddr`| Sztring | A felhasználó által hitelesített IP-cím. |
| `onprem_sid`| Karakterlánc, [SID-formátumban](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | Azokban az esetekben, amikor a felhasználó helyszíni hitelesítéssel rendelkezik, ez a jogcím a biztonsági azonosítóját adja meg. A `onprem_sid` az engedélyezéshez használhatja az örökölt alkalmazásokban.|
| `pwd_exp`| int, egy UNIX timestamp | Azt jelzi, hogy a felhasználó jelszava lejár-e. |
| `pwd_url`| Sztring | Egy URL-cím, amelyben a felhasználók elküldhetik a jelszavukat alaphelyzetbe. |
| `in_corp`| logikai | Azt jelzi, hogy az ügyfél bejelentkezik-e a vállalati hálózatról. Ha nem, a rendszer nem tartalmazza a jogcímet. |
| `nickname`| Sztring | A felhasználó további neve, amely az utónévtől és az utónévtől elválasztva.|
| `family_name` | Sztring | A felhasználó vezetéknevét, vezetéknevét vagy családjának nevét adja meg a felhasználói objektumban definiált módon. |
| `given_name` | Sztring | A felhasználó első vagy megadott nevét adja meg a felhasználói objektumra vonatkozóan. |
| `upn` | Sztring | A felhasználó felhasználóneve. Lehet telefonszám, e-mail-cím vagy formázatlan karakterlánc. Csak megjelenítési célokra kell használni, és a Felhasználónév-emlékeztetőket is meg kell adni az újrahitelesítési helyzetekben. |

#### <a name="the-amr-claim"></a>Az `amr` jogcím

A Microsoft-identitások különböző módokon hitelesíthetők, ami az alkalmazás szempontjából fontos lehet. Az `amr` jogcím egy olyan tömb, amely több elemet is tartalmazhat, például a `["mfa", "rsa", "pwd"]`t olyan hitelesítéshez, amely a jelszót és a hitelesítő alkalmazást egyaránt használta.

| Érték | Leírás |
|-----|-------------|
| `pwd` | Jelszó-hitelesítés, vagy egy felhasználó Microsoft-jelszava vagy egy alkalmazás ügyfél-titka. |
| `rsa` | A hitelesítés egy RSA-kulcs igazolásán alapul, például a [Microsoft Authenticator alkalmazással](https://aka.ms/AA2kvvu). Ez azt is magában foglalja, hogy a hitelesítés egy önaláírt JWT történt, amely egy szolgáltatás tulajdonában álló X509 tanúsítvánnyal rendelkezik. |
| `otp` | Egyszeri jelszó, amely e-mailt vagy szöveges üzenetet használ. |
| `fed` | A rendszer összevont hitelesítési állítást (például JWT vagy SAML) használt. |
| `wia` | Integrált Windows-hitelesítés |
| `mfa` | A többtényezős hitelesítés használatban volt. Ha ez jelen van, a többi hitelesítési módszer is szerepelni fog. |
| `ngcmfa` | A `mfa`egyenértékű, bizonyos speciális hitelesítőadat-típusok kiépítés céljából. |
| `wiaormfa`| A felhasználó Windows vagy MFA hitelesítő adatokat használt a hitelesítéshez. |
| `none` | Nem történt hitelesítés. |

## <a name="validating-tokens"></a>Tokenek ellenőrzése

Id_token vagy access_token érvényesítéséhez az alkalmazásnak a jogkivonat aláírását és a jogcímeket is ellenőriznie kell. A hozzáférési jogkivonatok érvényesítéséhez az alkalmazásnak ellenőriznie kell a kibocsátót, a célközönséget és az aláíró jogkivonatokat is. Ezeket érvényesíteni kell az OpenID-felderítési dokumentum értékeivel. A dokumentum bérlőtől független verziója például a következő helyen található: [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Az Azure AD-alapú middleware beépített funkciókat biztosít a hozzáférési jogkivonatok érvényesítéséhez, és böngészhet a [minták](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) között, és megkeresheti az Ön által választott nyelven.

Olyan kódtárakat és kódokat biztosítunk, amelyek bemutatják a jogkivonat-érvényesítés kezelését. Az alábbi információk azokra a felhasználókra vonatkoznak, akik meg szeretnék ismerni a mögöttes folyamatot. A JWT ellenőrzéséhez több harmadik féltől származó nyílt forráskódú kódtár is rendelkezésre áll – legalább egy lehetőség van a szinte minden platformra és nyelvre. Az Azure AD-hitelesítési kódtárakkal és a kódokkal kapcsolatos további információkért tekintse meg a [1.0](../azuread-dev/active-directory-authentication-libraries.md) -s és a [2.0](reference-v2-libraries.md)-s hitelesítési kódtárakat ismertető témakört.

### <a name="validating-the-signature"></a>Az aláírás ellenőrzése

A JWT három szegmenst tartalmaz, amelyeket a `.` karakter választ el egymástól. Az első szegmens a **fejléc**, a második a **törzs**, a harmadik pedig az **aláírás**. Az aláírási szegmens használatával ellenőrizheti a jogkivonat hitelességét, hogy az alkalmazás megbízható legyen.

Az Azure AD által kiállított jogkivonatok az iparági szabványnak megfelelő aszimmetrikus titkosítási algoritmusokkal (például RS256) vannak aláírva. A JWT fejléce a jogkivonat aláírásához használt kulcs-és titkosítási módszerről tartalmaz információkat:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

Az `alg` jogcím a jogkivonat aláírásához használt algoritmust jelzi, míg a `kid` jogcím azt a nyilvános kulcsot jelzi, amelyet a jogkivonat érvényesítéséhez használt.

Az Azure AD bármely adott időpontban aláírhat egy id_token a nyilvános titkos kulcsok egy adott készletének egyikével. Az Azure AD rendszeres időközönként elforgatja a kulcsok lehetséges készletét, így az alkalmazást úgy kell megírni, hogy a kulcs módosításait automatikusan kezeljék. Az Azure AD által használt nyilvános kulcsok frissítéseinek ellenőrzéséhez szükséges ésszerű gyakoriság 24 óránként.

Az aláírás ellenőrzéséhez szükséges aláíró kulcs adatokat a következő helyen található [OpenID Connect metaadat-dokumentum](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) használatával érheti el:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Próbálja ki ezt az [URL-címet](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) egy böngészőben!

Ez a metaadat-dokumentum:

* A egy JSON-objektum, amely számos hasznos információt tartalmaz, például az OpenID Connect hitelesítéshez szükséges különböző végpontok helyét.
* `jwks_uri`tartalmaz, amely megadja a jogkivonatok aláírásához használt nyilvános kulcsok helyét. A `jwks_uri` található JSON-webkulcs (JWK) tartalmazza az adott pillanatban használatban lévő összes nyilvános kulcsra vonatkozó információt.  A JWK formátumot a 7517- [es RFC-dokumentum](https://tools.ietf.org/html/rfc7517)írja le.  Az alkalmazás a JWT-fejlécben lévő `kid` jogcímet használva kiválaszthatja, hogy a dokumentum melyik nyilvános kulcsát használták egy adott token aláírására. Ezt követően az aláírás érvényesítése a helyes nyilvános kulccsal és a jelzett algoritmussal végezhető el.

> [!NOTE]
> A v 1.0 végpont a `x5t` és `kid` jogcímeket is visszaadja, míg a v 2.0-végpont csak a `kid` jogcímet válaszolja meg. A továbbításhoz javasoljuk, hogy a jogkivonatának érvényesítéséhez használja a `kid` jogcímet.

Az aláírás-ellenőrzés a dokumentum hatókörén kívül esik – számos nyílt forráskódú függvénytár érhető el, amely segítséget nyújt, ha szükséges.  A Microsoft Identity platformnak azonban van egy jogkivonat-aláíró kiterjesztése a Standards-Custom aláíró kulcsokra.

Ha az alkalmazás a [jogcímek leképezése](active-directory-claims-mapping.md) funkciójának eredményeként egyéni aláíró kulcsokkal rendelkezik, akkor az alkalmazás `jwks_uri` azonosítóját tartalmazó `appid` lekérdezési paramétert kell hozzáfűzni, amely az alkalmazás aláíró kulcsára vonatkozó információkra mutat, amelyeket az ellenőrzéshez kell használni. Például: a `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`tartalmaz.

### <a name="claims-based-authorization"></a>Jogcím-alapú hitelesítés

Az alkalmazás üzleti logikája ezt a lépést fogja megállapítani, néhány gyakori engedélyezési módszert az alábbiakban talál.

* Jelölje be a `scp` vagy `roles` jogcímet annak ellenőrzéséhez, hogy az összes jelen hatókör megfelel-e az API által közzétettnek, és engedélyezze az ügyfélnek a kért műveletet.
* Győződjön meg arról, hogy a hívó ügyfél számára engedélyezett az API meghívása az `appid` jogcím használatával.
* A hívó ügyfél hitelesítési állapotának ellenőrzése `appidacr` használatával – nem lehet 0, ha a nyilvános ügyfelek nem hívhatják meg az API-t.
* Tekintse át a korábbi `nonce` jogcímek listáját, és ellenőrizze, hogy a jogkivonat nincs-e újrajátszva.
* Győződjön meg arról, hogy a `tid` megfelel egy olyan bérlőnek, amely számára engedélyezett az API meghívása.
* Az `acr` jogcím használatával ellenőrizze, hogy a felhasználó végezte-e az MFA-t. Ezt a [feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)használatával kell kikényszeríteni.
* Ha a hozzáférési jogkivonatban kérte a `roles` vagy `groups` jogcímeket, ellenőrizze, hogy a felhasználó jogosult-e a művelet végrehajtására.
  * Az implicit folyamattal lekért tokenek esetében valószínűleg le kell kérdezni az ilyen [adatMicrosoft Grapht](https://developer.microsoft.com/graph/) , mivel gyakran túl nagy ahhoz, hogy illeszkedjen a tokenhez.

## <a name="user-and-application-tokens"></a>Felhasználói és alkalmazási jogkivonatok

Az alkalmazás a felhasználó nevében (a szokásos folyamat) vagy közvetlenül egy alkalmazásból (az ügyfél hitelesítő adataival ([v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)) keresztül kaphat jogkivonatokat. Ezek az alkalmazási tokenek azt jelzik, hogy ez a hívás egy alkalmazásból származik, és nem rendelkezik a felhasználó biztonsági mentésével. Ezek a jogkivonatok nagyjából azonosak, néhány különbséggel:

* Az alkalmazáshoz tartozó jogkivonatok nem rendelkeznek `scp` jogcímevel, és ehelyett `roles` jogcímet is tartalmazhatnak. Ebben az esetben a rendszer rögzíti az alkalmazás engedélyét (a delegált engedélyekkel szemben). A delegált és az alkalmazásra vonatkozó engedélyekkel kapcsolatos további információkért lásd: engedély és hozzájárulás ([v 1.0](../azuread-dev/v1-permissions-consent.md), [v 2.0](v2-permissions-and-consent.md)).
* Számos emberi jogcíme hiányzik, például `name` vagy `upn`.
* A `sub` és `oid` jogcímek azonosak lesznek.

## <a name="token-revocation"></a>Jogkivonat visszavonása

A frissítési tokeneket a rendszer bármikor, különböző okok miatt érvénytelenítheti vagy visszavonhatja. Ezek két fő kategóriába sorolhatók: időtúllépések és visszavonások.

### <a name="token-timeouts"></a>Jogkivonat-időtúllépések

A [jogkivonat-élettartam konfigurációjának](active-directory-configurable-token-lifetimes.md)használatával megváltoztatható a frissítési tokenek élettartama.  Normális és várható, hogy egyes tokenek használata nélkül megy (például a felhasználó nem nyitja meg az alkalmazást 3 hónapig), ezért lejár.  Az alkalmazások olyan forgatókönyveket fognak tapasztalni, amelyekben a bejelentkezési kiszolgáló elutasítja a frissítési jogkivonatot a kora miatt. 

* MaxInactiveTime: Ha a frissítési token nem lett használva a MaxInactiveTime által diktált időn belül, a frissítési token többé nem lesz érvényes.
* MaxSessionAge: Ha a MaxAgeSessionMultiFactor vagy a MaxAgeSessionSingleFactor értéke nem az alapértelmezett (a visszavonás után) értékre van állítva, akkor az újrahitelesítésre akkor van szükség, amikor a MaxAgeSession * eltelik.
* Példák:
  * A bérlőnek öt napja van egy MaxInactiveTime, és a felhasználó egy hétig ment a vakáción, így az Azure AD nem kapott új jogkivonat-kérelmet a felhasználótól 7 napon belül. Amikor a felhasználó legközelebb új jogkivonatot kér, megtalálják a frissítési jogkivonatot, és újra meg kell adniuk a hitelesítő adataikat.
  * Egy bizalmas alkalmazásnak egy nap MaxAgeSessionSingleFactor kell lennie. Ha a felhasználó hétfőn és kedden (25 óra elteltével) bejelentkezik, újra kell hitelesítenie.

### <a name="revocation"></a>Visszavont

A frissítési tokeneket a kiszolgáló visszavonhatja a hitelesítő adatok módosítása, illetve a használati vagy rendszergazdai műveletek miatt.  A frissítési tokenek két osztályba sorolhatók – ezek a bizalmas ügyfelek (a jobb szélső oszlop) és a nyilvános ügyfelek számára kiállítottak (az összes többi oszlop).   

|   | Jelszó alapú cookie | Jelszó alapú jogkivonat | Nem jelszó alapú cookie | Nem jelszó alapú jogkivonat | Bizalmas ügyfél jogkivonata |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| A jelszó lejár | Életben marad | Életben marad | Életben marad | Életben marad | Életben marad |
| Jelszó módosítva felhasználó által | Revoked | Revoked | Életben marad | Életben marad | Életben marad |
| A felhasználó nem SSPR | Revoked | Revoked | Életben marad | Életben marad | Életben marad |
| Rendszergazdai jelszó alaphelyzetbe állítása | Revoked | Revoked | Életben marad | Életben marad | Életben marad |
| A felhasználó visszavonja a frissítési jogkivonatokat a [PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) használatával | Revoked | Revoked | Revoked | Revoked | Revoked |
| A rendszergazda visszavonja a bérlő összes frissítési jogkivonatát a [PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) használatával | Revoked | Revoked |Revoked | Revoked | Revoked |
| Egyszeri kijelentkezés ([v 1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v 2.0](v2-protocols-oidc.md#single-sign-out) ) a weben | Revoked | Életben marad | Revoked | Életben marad | Életben marad |

> [!NOTE]
> A "nem jelszó alapú" bejelentkezési azonosító olyan eset, amikor a felhasználó nem adott meg jelszót a letöltéshez. Például a Windows Hello, egy FIDO2-kulcs vagy egy PIN-kód használatával.
>
> A Windows 10-es elsődleges frissítési tokenek (PRT) a hitelesítő adatok alapján elkülönülnek. A Windows Hello és a Password például a saját PRT-vel rendelkeznek, és egymástól elszigetelten vannak. Ha a felhasználó Hello hitelesítő adatokkal (PIN-kóddal vagy biometrikus azonosítóval) jelentkezik be, majd módosítja a jelszót, a rendszer visszavonja a korábban beszerzett jelszó-alapú PRT-t. A jelszó megadásával visszaigazolja a régi PRT-t, és egy újat kér.
>
> A frissítési tokenek nem lettek érvénytelenítve vagy visszavonva, ha új hozzáférési jogkivonatot és frissítési jogkivonatot kívánnak beolvasni.  Azonban az alkalmazásnak el kell vetnie a régit, amint a használatban van, és lecseréli az újat, mert az új jogkivonat új lejárati ideje van. 

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg az [Azure ad-`id_tokens`okkal](id-tokens.md).
* További információ az engedélyekről és a hozzájárulásról ( [1.0](../azuread-dev/v1-permissions-consent.md), [v 2.0](v2-permissions-and-consent.md)).
