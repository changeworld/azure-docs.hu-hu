---
title: Bing Spell Check API V5 frissítése a v7-re
titleSuffix: Azure Cognitive Services
description: Az alkalmazás azon részeit azonosítja, amelyeket frissítenie kell a 7-es verzió használatára.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 545772a28a67310b12eb55cd1fb14e8d12a95a58
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500819"
---
# <a name="spell-check-api-upgrade-guide"></a>Spell Check API frissítési útmutató

Ez a frissítési útmutató a Bing Spell Check API 5. és 7. verziójának változásait azonosítja. Ez az útmutató segítséget nyújt az alkalmazás azon részeinek azonosításához, amelyeket frissítenie kell a 7-es verzió használatához.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószáma a V5 verzióról a v7-re változott. Például: `https://api.cognitive.microsoft.com/bing/v7.0/spellcheck`.

### <a name="error-response-objects-and-error-codes"></a>Hibás objektumok és hibakódok

- Az összes sikertelen kérelemnek tartalmaznia kell `ErrorResponse` egy objektumot a válasz törzsében.

- A következő mezőket adta hozzá az `Error` objektumhoz.  
  - `subCode`&mdash;A hibakódot különálló gyűjtőbe particionálja, ha lehetséges
  - `moreDetails`&mdash;További információ a `message` mezőben leírt hibáról
   

- Az V5-hibakódokat a következő lehetséges `code` és `subCode` értékekkel cserélte le.  
  
|Kód|SubCode|Leírás
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|A Bing visszaadja a ServerError, ha az alkódok bármely feltétele teljesül. A válasz tartalmazza ezeket a hibákat, ha a HTTP-állapotkód 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokkolt|A Bing visszaadja a InvalidRequest, ha a kérelem bármely része érvénytelen. Például hiányzik egy kötelező paraméter, vagy a paraméter értéke érvénytelen.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a HTTP-állapotkód 400.<br/><br/>Ha a hiba HttpNotAllowed, a 410-es HTTP-állapotkód.
|RateLimitExceeded||A Bing visszaadja a RateLimitExceeded, amikor a másodpercenkénti lekérdezések (QPS) vagy a havi lekérdezés (QPM) kvóta meghaladja a kvótát.<br/><br/>A Bing a 429-as HTTP-állapotkódot adja vissza, ha túllépte a QPS és a 403 értéket, ha túllépte a QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|A Bing visszaadja a InvalidAuthorization, ha a Bing nem tudja hitelesíteni a hívót. Például hiányzik a `Ocp-Apim-Subscription-Key` fejléc, vagy az előfizetési kulcs érvénytelen.<br/><br/>A redundancia akkor fordul elő, ha egynél több hitelesítési módszert ad meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|A Bing visszaadja a InsufficientAuthorization, ha a hívónak nincs engedélye az erőforrás elérésére. Ez akkor fordulhat elő, ha az előfizetési kulcs le van tiltva vagy lejárt. <br/><br/>Ha a hiba InsufficientAuthorization, a HTTP-állapotkód 403.

- A következő leképezi az előző hibakódokat az új kódokra. Ha az V5-hibakódok függőségét vette igénybe, frissítse a kódját ennek megfelelően.  
  
|5\. verzió kódja|7-es verzió kódja. alkód
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Letiltva|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError. ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Nincs implementálva|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blokkolt|InvalidRequest. Blocked

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Használati és megjelenítési követelmények](./UseAndDisplayRequirements.md)
