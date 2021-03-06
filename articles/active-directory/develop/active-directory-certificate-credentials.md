---
title: A Microsoft Identity platform tanúsítványának hitelesítő adatai
titleSuffix: Microsoft identity platform
description: Ez a cikk a tanúsítvány hitelesítő adatainak regisztrálását és használatát ismerteti az alkalmazás hitelesítéséhez.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 26030c12d98d796ceb1f66f198aede6e40eebd94
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399018"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft Identity platform-alkalmazás hitelesítési tanúsítványának hitelesítő adatai

A Microsoft Identity platform lehetővé teszi, hogy az alkalmazások a saját hitelesítő adataikat használják a hitelesítéshez, például a [OAuth 2,0 ügyfél hitelesítő adataiban flowv 2.0](v2-oauth2-client-creds-grant-flow.md) -t és a [folyamaton](v2-oauth2-on-behalf-of-flow.md)kívüli folyamatot kell megadniuk.

Az egyik hitelesítő adat, amelyet az alkalmazás használhat a hitelesítéshez, egy JSON Web Token (JWT)-állítás, amely az alkalmazás tulajdonában lévő tanúsítvánnyal van aláírva.

## <a name="assertion-format"></a>Érvényesítési formátum
A Microsoft Identity platform az állítás kiszámításához használhatja a számos [JSON web token](https://jwt.ms/) -függvénytár egyikét a választott nyelven. A jogkivonat által végrehajtott információk a következők:

### <a name="header"></a>Fejléc

| Paraméter |  Megjegyzés |
| --- | --- |
| `alg` | **RS256** kell lennie |
| `typ` | **JWT** kell lennie |
| `x5t` | Az X. 509 tanúsítvány SHA-1 ujjlenyomatának kell lennie |

### <a name="claims-payload"></a>Jogcímek (hasznos adatok)

| Paraméter |  Megjegyzések |
| --- | --- |
| `aud` | Célközönség: **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** kell lennie |
| `exp` | Lejárati dátum: a jogkivonat lejárati dátuma. Az idő a (z) január 1-től 1970 (1970-01-01T0:0: 0Z) UTC szerint, a jogkivonat érvényességének lejárta előtt.|
| `iss` | Kiállító: a client_id (az ügyfélszolgáltatás alkalmazásspecifikus azonosítója) |
| `jti` | GUID: a JWT azonosítója |
| `nbf` | Nem előtte: az a dátum, amely előtt a jogkivonat nem használható. Az idő a (z) január 1-től 1970 (1970-01-01T0:0: 0Z) UTC szerint, a jogkivonat kiállításának időpontjáig. |
| `sub` | Tárgy: a `iss`esetében a client_id kell lennie (az ügyfélszolgáltatás alkalmazás-azonosítója) |

### <a name="signature"></a>Aláírás

Az aláírás a tanúsítvány alkalmazásával lett kiszámítva a [JSON web token RFC7519-specifikációban](https://tools.ietf.org/html/rfc7519) leírtak szerint.

## <a name="example-of-a-decoded-jwt-assertion"></a>Dekódolású JWT-érvényesítési példa

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Példa kódolt JWT-kijelentésre

A következő karakterlánc egy példa a kódolt állításra. Ha alaposan meggondolja, három szakaszt pont (.) választ el egymástól:
* Az első szakasz kódolja a fejlécet
* A második szakasz kódolja a hasznos adatokat
* Az utolsó szakasz az első két szakasz tartalmából származó tanúsítványokkal kiszámított aláírás.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>A tanúsítvány regisztrálása a Microsoft Identity platformmal

A tanúsítvány hitelesítő adatait a Microsoft Identity platformon található ügyfélalkalmazás alapján a következő módszerek bármelyikével társíthatja a Azure Portal használatával:

### <a name="uploading-the-certificate-file"></a>A tanúsítványfájl feltöltése

Az ügyfélalkalmazás Azure-alkalmazásának regisztrációja:
1. Válassza ki a **tanúsítványok & Secrets**elemet.
2. Kattintson a **tanúsítvány feltöltése** elemre, és válassza ki a feltölteni kívánt tanúsítványt.
3. Kattintson az **Hozzáadás** parancsra.
  A tanúsítvány feltöltése után a rendszer megjeleníti az ujjlenyomatot, a kezdési dátumot és a lejárati értékeket.

### <a name="updating-the-application-manifest"></a>Az alkalmazás jegyzékfájljának frissítése

A tanúsítvány birtokában a következőket kell kiszámítani:

- `$base64Thumbprint`, amely a tanúsítvány kivonatának Base64 kódolása
- `$base64Value`, amely a tanúsítvány nyers adatmennyiségének Base64 kódolása

Meg kell adnia egy GUID azonosítót is a kulcs azonosításához az alkalmazás jegyzékfájljában (`$keyId`).

Az ügyfélalkalmazás Azure-alkalmazásának regisztrációja:
1. Válassza ki a **jegyzékfájlt** az alkalmazás jegyzékfájljának megnyitásához.
2. Cserélje le a " *hitelesítő adatok* " tulajdonságot az új tanúsítvány adataira a következő séma használatával.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Mentse a módosításokat az alkalmazás-jegyzékfájlba, majd töltse fel a jegyzékfájlt a Microsoft Identity platformba.

   A `keyCredentials` tulajdonság többértékű, így több tanúsítványt is feltölthet a gazdagabb kulcsok kezeléséhez.

## <a name="code-sample"></a>Kódminta

> [!NOTE]
> A X5T fejlécét úgy kell kiszámítani, hogy a tanúsítvány kivonatával konvertálja egy Base 64 sztringre. A alkalmazásban C# végrehajtandó kód `System.Convert.ToBase64String(cert.GetCertHash());`.

A [.net Core Daemon Console alkalmazás a Microsoft Identity platformmal való használata](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) azt mutatja be, hogyan használja az alkalmazás a saját hitelesítő adatait a hitelesítéshez. Azt is bemutatja, hogyan [hozhat létre önaláírt tanúsítványt](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) a `New-SelfSignedCertificate` PowerShell-paranccsal. Emellett kihasználhatja a tanúsítványok létrehozását és az [alkalmazás-létrehozási parancsfájlok](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) használatát is, így kiszámíthatja az ujjlenyomatot, és így tovább.
