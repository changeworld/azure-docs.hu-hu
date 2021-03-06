---
title: Cookie-definíciók
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C használt cookie-k definícióit tartalmazza.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189514"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>A Azure AD B2C cookie-definíciói

A következő szakaszokban a Azure Active Directory B2C (Azure AD B2C) által használt cookie-kkal kapcsolatos információkat talál.

## <a name="samesite"></a>SameSite

Az Microsoft Azure AD B2C szolgáltatás kompatibilis a SameSite böngésző-konfigurációkkal, beleértve a `Secure` attribútummal való `SameSite=None` támogatását is.

A webhelyekhez való hozzáférés védelme érdekében a böngészők egy új, biztonságos alapértelmezett modellt vezetnek be, amely feltételezi, hogy az összes cookie-t védeni kell a külső hozzáféréstől, hacsak másként nincs megadva. Ennek a változásnak a megkezdéséhez a Chrome böngésző az első lépés a [chrome 80-es](https://www.chromium.org/updates/same-site)verziójában a 2020 februárjában. A Chrome változásának előkészítésével kapcsolatos további információkért lásd [: fejlesztőknek: Felkészülés az új SameSite = none; Biztonságos cookie-beállítások](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) a Chromium blogon.

A fejlesztőknek az új cookie-beállítást kell használniuk, `SameSite=None`, hogy kijelölje a cookie-kat a helyek közötti hozzáféréshez. Ha a `SameSite=None` attribútum létezik, akkor további `Secure` attribútumot kell használni, hogy a helyek közötti cookie-k csak HTTPS-kapcsolatokon keresztül legyenek elérhetők. Érvényesítse és tesztelje az összes alkalmazást, beleértve a Azure AD B2Ct használó alkalmazásokat is.

További információkért lásd:

* [SameSite-cookie-változások kezelése a Chrome böngészőben](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Az ügyfelek webhelyeire és a Microsoft szolgáltatásaira és termékeire gyakorolt hatása a Chrome 80-es vagy újabb verziójában](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Sütik

A következő táblázat felsorolja a Azure AD B2C használt cookie-kat.

| Név | Domain | Lejárati | Cél |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Böngésző- [munkamenet](session-behavior.md) vége | Felhasználói tagsági adattárolást tart fenn a bérlők között. A bérlők a felhasználók és a tagsági szint (rendszergazda vagy felhasználó) tagjai. |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | A kérelmeknek a megfelelő éles példányra való továbbítására szolgál. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | A tranzakciók nyomon követésére szolgál (a Azure AD B2C hitelesítési kérések száma) és az aktuális tranzakcióval. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | Az SSO-munkamenet fenntartásához használatos. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md)vége, sikeres hitelesítés | A kérelem állapotának fenntartásához használatos. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | A CRSF-védelemhez használt, helyek közötti kérelem hamis jogkivonata. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | Azure AD B2C hálózati útválasztáshoz használatos. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | Környezet |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | Az erőforrás-szolgáltató bérlő tagsági adattárolására szolgál. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | A Relay cookie tárolására szolgál. |
