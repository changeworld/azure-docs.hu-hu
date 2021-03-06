---
title: Mi a hibrid identitás a Azure Active Directory?
description: A hibrid identitás közös felhasználói identitással rendelkezik a hitelesítéshez és az engedélyezéshez mind a helyszínen, mind a felhőben.
keywords: az Azure AD Connect bemutatása, az Azure AD Connect áttekintése, mi az Azure AD Connect, az Active Directory telepítése
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3d681dd06f07f6174e31b59cccf42df5dc16a1e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779853"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>Mi a hibrid identitás a Azure Active Directory?

Napjainkban a vállalatok és a vállalatok egyre többen használják a helyszíni és a felhőalapú alkalmazásokat.  Felhasználók igényelnek, ezeknek az alkalmazásoknak hozzáférést a helyszínen és a felhőben. A felhasználók helyszíni és Felhőbeli kezelése is kihívást jelentő forgatókönyveket jelent. 

A Microsoft identitáskezelési megoldások kiterjednek a helyszíni és felhőalapú képességek.  Ezeket a megoldásokat hozzon létre egy általános felhasználói identitást, hitelesítés és engedélyezés az összes erőforráshoz, helytől függetlenül. Erre **hibrid identitás**.

A hibrid identitás az Azure AD-vel és a hibrid Identitáskezelés kezelésével ezek a forgatókönyvek válnak elérhetővé.

Ha hibrid identitást szeretne elérni az Azure AD-vel, a forgatókönyvek függvényében a három hitelesítési módszer egyikét használhatja.   A következők: 

- **[A Jelszókivonat-szinkronizálás (nál)](whatis-phs.md)**  
- **[Az átmenő hitelesítés (ESP)](how-to-connect-pta.md)**  
- **[Összevonás (AD FS)](whatis-fed.md)** 

Ezek a hitelesítési módszerek is biztosítanak [egyszeri bejelentkezéses](how-to-connect-sso.md) képességeket.  Egyszeri bejelentkezéses automatikusan bejelentkezik a felhasználók mikor legyenek a vállalati eszközeiket a vállalati hálózathoz csatlakozik.

További információkért lásd: [válassza ki a megfelelő hitelesítési módszert az Azure Active Directory hibrid identitáskezelési megoldás](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn). 

## <a name="common-scenarios-and-recommendations"></a>Gyakori forgatókönyvek és javaslatok 

Szeretnénk bemutatni néhány gyakori hibrid identitás- és hozzáféréskezelési helyzetet és a hozzájuk ajánlott hibrid identitáskezelési lehetősége(ke)t. 

|Cél:|PHS és SSO<sup>1</sup>| PTA és SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|A helyszíni Active Directoryban létrehozott új felhasználói, kapcsolattartói és csoportfiókok automatikus szinkronizálása a felhőbe|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Saját bérlő beállítása az Office 365 hibrid forgatókönyvekhez.|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Lehetővé teheti a felhasználók számára a bejelentkezést és a felhőalapú szolgáltatások elérését a helyszíni jelszavuk használatával.|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Egyszeri bejelentkezés implementálása vállalati hitelesítő adatok használatával.|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|  
|Ügyeljen arra, hogy a rendszer ne tárolja a jelszó-kivonatokat a felhőben.| |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Felhőalapú multi-Factor Authentication megoldások engedélyezése.|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Helyszíni multi-Factor Authentication megoldások engedélyezése.| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Támogatás intelligens kártyás hitelesítés a saját felhasználók számára. <sup>4</sup>| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|A jelszó lejárati értesítéseinek megjelenítése az Office-Portálon és a Windows 10 asztalán.| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Jelszókivonat-szinkronizálás egyszeri bejelentkezéssel. 
> 
> <sup>2</sup> Átmenő hitelesítés és egyszeri bejelentkezés.  
> 
> <sup>3</sup> Összevont egyszeri bejelentkezés AD FS-sel.  
>  
> <sup>4</sup> Az AD FS a vállalati nyilvános kulcsú infrastruktúrával is integrálható, hogy tanúsítványokkal is be lehessen jelentkezni. Ezek a tanúsítványok lehetnek megbízható regisztrációs csatornákon (például MDM-en vagy csoportházirend-objektumon) keresztül kiosztott szoftveres tanúsítványok, intelligenskártya-tanúsítványok (beleértve a PIV/CAC-kártyákat) vagy Vállalati Windows Hello- (tanúsítvány-megbízhatósági) megoldások. Az intelligens kártyás hitelesítés támogatásával kapcsolatos további információkat [ebben a blogban](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/) talál. 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>A Azure AD Connect használatára vonatkozó licencfeltételek

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>További lépések 

- [Mi az Azure AD Connect és a Connect Health?](whatis-azure-ad-connect.md) 
- [Mi az a Jelszókivonat-szinkronizálás (nál)?](whatis-phs.md) 
- [Mi az átmenő hitelesítés (ESP)?](how-to-connect-pta.md) 
- [Mi az összevonás?](whatis-fed.md) 
- [Egyszeri bejelentkezés a Mi?](how-to-connect-sso.md) 

