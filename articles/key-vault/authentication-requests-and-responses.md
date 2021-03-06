---
title: Hitelesítés, kérelmek és válaszok
description: Hitelesítés az AD-ben a Key Vault használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: b84084dafe79a8caa9cdb70bc201f68834079845
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196076"
---
# <a name="authentication-requests-and-responses"></a>Hitelesítés, kérelmek és válaszok

Azure Key Vault támogatja a JSON formátumú kérelmeket és válaszokat. A Azure Key Vaultra irányuló kérelmeket egy érvényes Azure Key Vault URL-címre irányítja a HTTPS protokollal, néhány URL-paraméterrel és JSON-kódolású kérelem és válasz Törzsével.

Ez a témakör a Azure Key Vault szolgáltatásra vonatkozó jellemzőket ismerteti. Az Azure REST-felületek (például hitelesítés/engedélyezés és hozzáférési token beszerzése) használatával kapcsolatos általános információkért tekintse meg az [azure REST API referenciát](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>Kérés URL-címe  
 A kulcskezelő műveletek a HTTP POST használatával végeznek HTTP-TÖRLÉSt, beolvasást, javítást, PUT és HTTP POST és titkosítási műveleteket a meglévő kulcsfontosságú objektumokon. Azok az ügyfelek, amelyek nem támogatják az adott HTTP-műveleteket, a HTTP-BEJEGYZÉST is használhatják az X-HTTP-Request fejléc használatával a kívánt művelet megadásához. azok a kérelmek, amelyek általában nem igénylik a törzset, üres törzset is tartalmazhatnak a HTTP POST használatakor, például ha a POST törlés helyett a POST parancsot használják.  

 A Azure Key Vault objektumainak működéséhez az alábbi URL-címek használhatók:  

- TESTKEY nevű kulcs létrehozása Key Vault use-`PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- IMPORTEDKEY nevű kulcs importálása Key Vault use-`POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- KERESÉSI KIFEJEZÉSKÉNT nevű titkos kód beszerzése egy Key Vault use-`GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Kivonat aláírása egy TESTKEY nevű kulccsal Key Vault use-`POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  A Key Vaultra irányuló kérések szolgáltatója mindig a következő, `https://{keyvault-name}.vault.azure.net/`  

  A kulcsok mindig a/Keys útvonalon tárolódnak, a titkokat a rendszer mindig a/Secrets elérési út alatt tárolja.  

## <a name="api-version"></a>API-verzió  
 A Azure Key Vault szolgáltatás támogatja a protokollok verziószámozását a régebbi verziójú ügyfelekkel való kompatibilitás biztosításához, de az ügyfelek számára nem minden képesség lesz elérhető. Az ügyfeleknek a `api-version` lekérdezési karakterlánc paraméterrel kell megadniuk az általa támogatott protokoll verziószámát, mivel nincs alapértelmezett érték.  

 A Azure Key Vault protokoll verziói a Date számozási sémát egy {éééé} használatával követik. {MM}. {DD} formátum.  

## <a name="request-body"></a>A kérelem törzse  
 A HTTP-specifikációnak megfelelően a GET műveletekhez nem tartozhatnak kérelem törzse, és a POST és PUT műveleteknek kérelem törzstel kell rendelkezniük. A TÖRLÉSi műveletek törzse nem kötelező a HTTP-ben.  

 Hacsak a művelet leírása másként nincs jelezve, a kérelem törzsének tartalomtípusa csak Application/JSON lehet, és tartalmaznia kell egy szerializált JSON-objektumot, amely a tartalomtípusnak felel meg.  

 Hacsak a művelet leírása másként nincs jelezve, az Accept kérelem fejlécének tartalmaznia kell az Application/JSON adathordozó típusát.  

## <a name="response-body"></a>Választörzs  
 Hacsak a művelet leírása másként nincs megadva, a sikeres és a sikertelen műveletek válasz törzsének tartalomtípusa az Application/JSON lesz, és részletes információkat tartalmaz.  

## <a name="using-http-post"></a>HTTP POST használata  
 Előfordulhat, hogy egyes ügyfelek nem tudják használni bizonyos HTTP-műveleteket, például a javítást vagy a TÖRLÉSt. Azure Key Vault támogatja a HTTP POST alternatívát ezen ügyfelek esetében, feltéve, hogy az ügyfél az "X-HTTP-METHOD" fejlécet is tartalmazza az eredeti HTTP-művelethez. A HTTP POST támogatását a jelen dokumentumban meghatározott API-k esetében is fel kell tüntetni.  

## <a name="error-responses"></a>Hibaüzenetek  
 A hibakezelés a HTTP-állapotkódok használatát fogja használni. A tipikus eredmények a következők:  

- 2xx – sikeres: normál működéshez használatos. A válasz törzse a várt eredményt fogja tartalmazni.  

- 3xx – átirányítás: a "nincs módosítva" értékkel rendelkező 304 visszatérhet a feltételes lekérések teljesítéséhez. A jövőben más 3xx-kódokat is használhat a DNS-és elérésiút-változások jelzésére.  

- 4xx – ügyfélalkalmazás: helytelen kérések, hiányzó kulcsok, szintaktikai hibák, érvénytelen paraméterek, hitelesítési hibák stb. használata. A válasz törzse részletes hibaüzenetet fog tartalmazni.  

- 5xx – kiszolgálóhiba: belső kiszolgálóhiba esetén használatos. A válasz törzse az összegzett hibaüzeneteket fogja tartalmazni.  

  A rendszer úgy van kialakítva, hogy proxy vagy tűzfal mögött működjön. Ezért előfordulhat, hogy egy ügyfél más hibakódokat kap.  

  Ha probléma merül fel, a Azure Key Vault a válasz törzsében is visszaadja a hibaüzeneteket. A válasz törzse JSON formátumú, és a következőket veszi át:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Hitelesítés  
 A Azure Key Vault összes kérését hitelesíteni kell. Azure Key Vault támogatja Azure Active Directory hozzáférési jogkivonatokat, amelyek a OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)] használatával szerezhetők be. 
 
 Az alkalmazás regisztrálásával és a Azure Key Vault használatának hitelesítésével kapcsolatos további információkért lásd: [az ügyfélalkalmazás regisztrálása az Azure ad](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad)-ben.
 
 A hozzáférési jogkivonatokat a HTTP-engedélyezési fejléc használatával kell elküldeni a szolgáltatásnak:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Ha nincs megadva hozzáférési jogkivonat, vagy ha a szolgáltatás nem fogadja el a jogkivonatot, a rendszer HTTP 401-es hibát ad vissza az ügyfélnek, és tartalmazza a WWW-Authenticate fejlécet, például:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 A WWW-Authenticate fejléc paramétereinek a következők:  

-   engedélyezés: a OAuth2-engedélyezési szolgáltatás címe, amely a kérelem hozzáférési jogkivonatának beszerzésére használható.  

-   erőforrás: az engedélyezési kérelemben használandó erőforrás neve (https://vault.azure.net).  

## <a name="see-also"></a>Lásd még:  
 [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
