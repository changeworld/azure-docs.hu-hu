---
title: Microsoft Identity platform & OAuth 2.0-ban – meghatalmazott folyamat | Azure
description: Ez a cikk azt ismerteti, hogyan használhatók a HTTP-üzenetek a szolgáltatás és a szolgáltatás hitelesítésének megvalósításához a OAuth 2.0-s verziójának használatával.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 37ce80c94478d2250eae321f7a42bda64d441dea
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159642"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft Identity platform és OAuth 2,0-alapú folyamat


Az OAuth 2,0-es adatforgalom (OBO) arra szolgál, hogy az alkalmazás hogyan hívja meg a szolgáltatás/webes API-t, ami viszont egy másik szolgáltatást vagy webes API-t hív meg. Az a cél, hogy a delegált felhasználói identitást és engedélyeket a kérési láncon keresztül propagálja. Ahhoz, hogy a középső szintű szolgáltatás hitelesített kéréseket továbbítson az alárendelt szolgáltatásnak, a felhasználó nevében védenie kell egy hozzáférési jogkivonatot a Microsoft Identity platformon.

Ez a cikk azt ismerteti, hogyan lehet programozni közvetlenül az alkalmazás protokollját.  Ha lehetséges, javasoljuk, hogy a támogatott Microsoft hitelesítési kódtárakat (MSAL) használja a [jogkivonatok beszerzése és a biztonságos webes API-k hívása](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)helyett.  Tekintse meg az MSAL-t [használó példákat](sample-v2-code.md)is.

> [!NOTE]
>
> - A Microsoft Identity platform végpontja nem támogatja az összes forgatókönyvet és funkciót. Annak megállapításához, hogy a Microsoft Identity platform-végpontot kell-e használni, olvassa el a [Microsoft Identity platform korlátozásait](active-directory-v2-limitations.md)ismertetőt. 
> - A 2018-as számú, implicit folyamatból származtatott `id_token` nem használható az OBO flow-hoz. Az egyoldalas alkalmazások (Gyógyfürdők) **hozzáférési** jogkivonatot továbbítanak egy közepes szintű bizalmas ügyfélnek az OBO-folyamatok elvégzéséhez. További információ arról, hogy mely ügyfelek végezhetnek OBO-hívásokat: [korlátozások](#client-limitations).

## <a name="protocol-diagram"></a>Protokoll diagramja

Tegyük fel, hogy a felhasználó hitelesítése megtörtént egy olyan alkalmazáson, amely a [OAuth 2,0 engedélyező kódot](v2-oauth2-auth-code-flow.md) vagy egy másik bejelentkezési folyamatot használ. Ezen a ponton az alkalmazás rendelkezik egy hozzáférési jogkivonattal *az API* -hoz (A token a) a felhasználó jogcímeivel és a középső rétegbeli webes API (a API) eléréséhez szükséges engedélyekkel. Most az API-nak hitelesített kérést kell tennie az alárendelt webes API-nak (B API).

A követendő lépések az OBO-folyamatot alkotják, és az alábbi ábra segítségével magyarázható.

![A OAuth 2.0-alapú meghatalmazott folyamat megjelenítése](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Az ügyfélalkalmazás kérelmet küld az a API-nak az a token (az a API `aud` jogcímével).
1. Az API A hitelesíti a Microsoft Identity platform jogkivonat-kiállítási végpontját, és tokent kér a B API eléréséhez.
1. A Microsoft Identity platform token kiállítási végpontja ellenőrzi az API A hitelesítő adatait az A jogkivonattal együtt, és kiadja a B API (token B) hozzáférési jogkivonatát az A API-nak.
1. A "B" tokent az API-nak a B API-ra irányuló kérelem engedélyezési fejlécében a következő értékre állítja be.
1. A biztonságos erőforrás adatait a B API adja vissza az A API-hoz, és onnan az ügyfélnek.

> [!NOTE]
> Ebben az esetben a középső rétegbeli szolgáltatás nem rendelkezik felhasználói beavatkozással, hogy a felhasználó beleegyezik az alsóbb rétegbeli API eléréséhez. Ezért az alsóbb rétegbeli API-hoz való hozzáférés engedélyezésének lehetősége előzetesen megjelenik a jóváhagyás lépés részeként a hitelesítés során. Ha meg szeretné tudni, hogyan állíthatja be ezt az alkalmazásra, tekintse meg [a a középső rétegbeli alkalmazáshoz](#gaining-consent-for-the-middle-tier-application)való hozzájárulások beszerzése című témakört.

## <a name="service-to-service-access-token-request"></a>Szolgáltatás-szolgáltatás hozzáférési jogkivonat kérése

Hozzáférési jogkivonat igényléséhez a következő paraméterekkel hozzon végre egy HTTP-BEJEGYZÉST a bérlő-specifikus Microsoft Identity platform token-végponton.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Két eset attól függően, hogy az ügyfélalkalmazás egy megosztott titok vagy egy tanúsítvány által védett-e.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: hozzáférési jogkivonat-kérelem közös titokkal

Közös titkos kulcs használata esetén a szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| `grant_type` | Kötelező | A jogkivonat-kérelem típusa. JWT használó kérelmek esetén az értéknek `urn:ietf:params:oauth:grant-type:jwt-bearer`nak kell lennie. |
| `client_id` | Kötelező | Az alkalmazás (ügyfél) azonosítója, amelyhez [az Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lap hozzá van rendelve az alkalmazáshoz. |
| `client_secret` | Kötelező | Az Azure Portal-Alkalmazásregisztrációk lapon az alkalmazáshoz generált ügyfél-titkos kulcs. |
| `assertion` | Kötelező | A kérelemben használt jogkivonat értéke.  Ennek a tokennek rendelkeznie kell az ehhez az OBO-kérelemhez tartozó alkalmazás célközönségével (ezt az alkalmazást a `client-id` mező jelöli). |
| `scope` | Kötelező | A jogkivonat-kérelem hatókörének szóközzel tagolt listája. További információ: [hatókörök](v2-permissions-and-consent.md). |
| `requested_token_use` | Kötelező | Megadja a kérelem feldolgozásának módját. Az OBO-flow-ban az értéket `on_behalf_of`értékre kell beállítani. |

#### <a name="example"></a>Példa

A következő HTTP POST egy hozzáférési jogkivonatot és frissítési jogkivonatot kér `user.read` hatókörrel a https://graph.microsoft.com webes API-hoz.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: hozzáférési jogkivonat kérése tanúsítvánnyal

Egy tanúsítványhoz tartozó szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| `grant_type` | Kötelező | A jogkivonat-kérelem típusa. JWT használó kérelmek esetén az értéknek `urn:ietf:params:oauth:grant-type:jwt-bearer`nak kell lennie. |
| `client_id` | Kötelező |  Az alkalmazás (ügyfél) azonosítója, amelyhez [az Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lap hozzá van rendelve az alkalmazáshoz. |
| `client_assertion_type` | Kötelező | Az értéknek `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`nak kell lennie. |
| `client_assertion` | Kötelező | Egy, az alkalmazáshoz hitelesítő adatként regisztrált tanúsítvánnyal rendelkező (JSON webes jogkivonat). A tanúsítvány regisztrálásának és az állítás formátumának megismeréséhez lásd: [tanúsítvány hitelesítő adatai](active-directory-certificate-credentials.md). |
| `assertion` | Kötelező | A kérelemben használt jogkivonat értéke. |
| `requested_token_use` | Kötelező | Megadja a kérelem feldolgozásának módját. Az OBO-flow-ban az értéket `on_behalf_of`értékre kell beállítani. |
| `scope` | Kötelező | A jogkivonat-kérelem hatókörének szóközzel tagolt listája. További információ: [hatókörök](v2-permissions-and-consent.md).|

Figyelje meg, hogy a paraméterek majdnem ugyanazok, mint a közös titok által benyújtott kérelem esetében, kivéve, ha a `client_secret` paramétert két paraméter helyettesíti: egy `client_assertion_type` és `client_assertion`.

#### <a name="example"></a>Példa

A következő HTTP-bejegyzés olyan hozzáférési jogkivonatot kér, amelynek `user.read` hatóköre van a https://graph.microsoft.com webes API-hoz egy tanúsítvánnyal.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Szolgáltatás és szolgáltatás hozzáférési jogkivonat válasza

A sikeres válasz egy JSON-OAuth 2,0-válasz a következő paraméterekkel.

| Paraméter | Leírás |
| --- | --- |
| `token_type` | Megadja a jogkivonat típusának értékét. Az egyetlen típus, amelyet a Microsoft Identity platform támogat, `Bearer`. A tulajdonosi jogkivonatokkal kapcsolatos további információkért tekintse meg a [OAuth 2,0 engedélyezési keretrendszert: tulajdonosi jogkivonat használata (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | A jogkivonatban megadott hozzáférési hatókör. |
| `expires_in` | Az az időtartam (másodpercben), ameddig a hozzáférési jogkivonat érvényes. |
| `access_token` | A kért hozzáférési jogkivonat. A hívó szolgáltatás ezt a tokent használhatja a fogadó szolgáltatásban való hitelesítéshez. |
| `refresh_token` | A kért hozzáférési jogkivonat frissítési jogkivonata. A hívó szolgáltatás ezzel a jogkivonattal kérhet egy másik hozzáférési tokent az aktuális hozzáférési jogkivonat lejárta után. A frissítési token csak akkor van megadva, ha a `offline_access` hatókört kérték. |

### <a name="success-response-example"></a>Sikeres válasz – példa

Az alábbi példa egy, a https://graph.microsoft.com webes API hozzáférési jogkivonatára vonatkozó kérelemre adott sikeres választ mutat be.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> A fenti hozzáférési jogkivonat egy v 1.0-formázott jogkivonat. Ennek az az oka, hogy a token az elérni kívánt **erőforrás** alapján van megadva. A Microsoft Graph a 1.0-s verziójú tokenek elfogadására van beállítva, így a Microsoft Identity platform 1.0-s verziójú hozzáférési jogkivonatokat hoz létre, amikor az ügyfél a Microsoft Graph jogkivonatait kéri le. Csak az alkalmazásoknak kell megkeresniük a hozzáférési jogkivonatokat. Az ügyfeleknek **nem kell** megvizsgálniuk azokat.

### <a name="error-response-example"></a>Hiba-válasz példa

A jogkivonat-végpont egy hibaüzenetet ad vissza, amikor hozzáférési tokent próbál beszerezni az alsóbb rétegbeli API számára, ha az alsóbb rétegbeli API feltételes hozzáférési szabályzattal (például többtényezős hitelesítéssel) van beállítva. A középső rétegbeli szolgáltatásnak ezt a hibát fel kell vennie az ügyfélalkalmazás számára, hogy az ügyfélalkalmazás meg tudja adni a felhasználói beavatkozást a feltételes hozzáférési szabályzat teljesítése érdekében.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>A biztonságos erőforrás eléréséhez használja a hozzáférési jogkivonatot.

Most a középső rétegbeli szolgáltatás a fentiekben ismertetett token használatával hitelesítő kéréseket hozhat az alárendelt webes API-nak, ha a tokent a `Authorization` fejlécében állítja be.

### <a name="example"></a>Példa

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Beleegyezik a középső rétegbeli alkalmazásra

Az alkalmazás architektúrájának vagy használati módjától függően különböző stratégiákat tekinthet meg az OBO-folyamat sikerességének biztosításához. Minden esetben a végső cél a megfelelő hozzájárulás biztosítása, hogy az ügyfélalkalmazás meghívja a középső rétegbeli alkalmazást, és a középső rétegbeli alkalmazás jogosult legyen a háttérbeli erőforrás meghívására. 

> [!NOTE]
> Korábban a Microsoft-fiók rendszer (személyes fiókok) nem támogatta az "ismert ügyfélalkalmazás" mezőt, és nem tudta megjeleníteni a kombinált hozzájárulásukat.  Ez a szolgáltatás hozzáadva, és a Microsoft Identity platformon futó összes alkalmazás használhatja az gettign-beleegyezett az OBO-hívásokhoz. 

### <a name="default-and-combined-consent"></a>/.default és kombinált engedély

A középső rétegbeli alkalmazás hozzáadja az ügyfelet az ismert ügyfélalkalmazások listájához a jegyzékfájljában, majd az ügyfél egyszerre több és a középső rétegbeli alkalmazáshoz is elindíthat egy kombinált engedélyezési folyamatot. A Microsoft Identity platform végpontján ez a [`/.default` hatókör](v2-permissions-and-consent.md#the-default-scope)használatával végezhető el. Ha ismert ügyfélalkalmazások és `/.default`használatával indítja el a beleegyezési képernyőt, a beleegyezési képernyő a középső rétegbeli API- **ra vonatkozó engedélyeket** is megjeleníti, valamint a középső rétegbeli API számára szükséges engedélyeket is kéri. A felhasználó mindkét alkalmazáshoz hozzájárul, majd az OBO-folyamat működik.

### <a name="pre-authorized-applications"></a>Előzetesen jóváhagyott alkalmazások

Az erőforrások azt jelezhetik, hogy egy adott alkalmazásnak mindig van engedélye bizonyos hatókörök fogadására. Ez elsősorban akkor hasznos, ha az előtér-ügyfél és a háttérbeli erőforrás közötti kapcsolat zökkenőmentesebb. Egy erőforrás több előre engedélyezett alkalmazást is deklarálhat – bármely ilyen alkalmazás kérheti ezeket az engedélyeket egy OBO-folyamatba, és a felhasználó belefoglalása nélkül fogadhatja azokat.

### <a name="admin-consent"></a>Rendszergazdai jóváhagyás

A bérlői rendszergazda garantálhatja, hogy az alkalmazások jogosultak legyenek a szükséges API-k meghívására a középső szintű alkalmazás rendszergazdai beleegyezésének biztosításával. Ehhez a rendszergazda megkeresheti a középső rétegű alkalmazást a bérlőben, megnyithatja a szükséges engedélyek lapot, és engedélyt adhat az alkalmazás számára. Ha többet szeretne megtudni a rendszergazdai jogosultságokról, tekintse meg a [beleegyezett és az engedélyek dokumentációját](v2-permissions-and-consent.md).

### <a name="use-of-a-single-application"></a>Egyetlen alkalmazás használata

Bizonyos helyzetekben csak a középső rétegbeli és az előtér-ügyfél egyetlen párosítása lehet. Ebben a forgatókönyvben könnyebben lehet ezt egy alkalmazást létrehozni, és nem kell megtagadni a középső rétegbeli alkalmazás szükségességét. Az előtér-és a webes API-k közötti hitelesítéshez használhat cookie-kat, id_token vagy az alkalmazáshoz igényelt hozzáférési tokent. Ezt követően a kérelem beleegyezik az adott alkalmazástól a háttér-erőforráshoz.

## <a name="client-limitations"></a>Ügyfél korlátozásai

Ha az ügyfél az implicit folyamattal id_token kap, és az ügyfél a válasz URL-címében helyettesítő karaktereket is tartalmaz, akkor a id_token nem használható OBO-folyamathoz.  Az implicit engedélyezési folyamaton keresztül beszerzett hozzáférési tokenek azonban továbbra is beválthatók egy bizalmas ügyfél számára, még akkor is, ha a kezdeményező ügyfélhez a helyettesítő karakteres válasz URL-címe van regisztrálva.

## <a name="next-steps"></a>Következő lépések

További információ a OAuth 2,0 protokollról, valamint a szolgáltatás és a szolgáltatás hitelesítésének másik módja ügyfél-hitelesítő adatok használatával.

* [OAuth 2,0 ügyfél-hitelesítő adatok megadása a Microsoft Identity platformon](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2,0-programkód a Microsoft Identity platformon](v2-oauth2-auth-code-flow.md)
* [A `/.default` hatókör használata](v2-permissions-and-consent.md#the-default-scope)
