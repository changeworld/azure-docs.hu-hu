---
title: Felhasználónév-keresés a bejelentkezéskor – Azure Active Directory | Microsoft Docs
description: A bejelentkezéskor a képernyőn megjelenő üzenetkezelés a Felhasználónév-keresést tükrözi Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024252"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>A Azure Active Directory bejelentkezési oldalain a Kezdőlap tartományának felderítése

Módosítjuk az Azure Active Directory (Azure AD) bejelentkezési folyamatát, hogy lehetővé tegyük az új hitelesítési módszerek használatát, és hogy javítsuk a használati élményt. A bejelentkezés során az Azure AD meghatározza, hogy a felhasználónak hol kell hitelesítést végeznie. Az Azure AD intelligens döntéseket hoz a bejelentkezési oldalon megadott felhasználónévhez tartozó szervezeti és felhasználói beállítások alapján. Ez egy újabb lépés végeredményben a jelszó nélküli használathoz, amelynél lehetővé válik majd további hitelesítő adatok, például a FIDO 2.0 használata is.

## <a name="home-realm-discovery-behavior"></a>A Kezdőlap tartományának felderítési viselkedése

A Kezdőlap tartomány felderítését a rendszer a bejelentkezéskor vagy egy örökölt alkalmazásokhoz tartozó Home Realm-felderítési szabályzatban megadott tartomány szabályozza. A felderítési viselkedésben például egy Azure Active Directory felhasználó írhatja be a felhasználónevét, de továbbra is a szervezete hitelesítőadat-gyűjtési képernyőjén fog megérkezni. Ez akkor fordul elő, ha a felhasználó helyesen adja meg a szervezet "contoso.com" tartománynevét. Ez a viselkedés nem teszi lehetővé, hogy a folyamatot az egyes felhasználók szintjén testre szabjuk.

A hitelesítő adatok szélesebb körének és a használhatóság növelésének támogatásához a bejelentkezési folyamat során Azure Active Directory Felhasználónév-keresési viselkedése frissül. Az új viselkedés intelligens döntéseket tesz, ha a bejelentkezési oldalon megadott Felhasználónév alapján beolvassa a bérlői és a felhasználói szintű beállításokat. Ennek lehetővé tételéhez Azure Active Directory ellenőrizze, hogy a bejelentkezési oldalon megadott felhasználónév létezik-e a megadott tartományban, vagy átirányítja a felhasználót a hitelesítő adatok megadásához.

A munka további előnye, hogy javul a hibaüzenetek. Íme néhány példa a továbbfejlesztett hibaüzenetekre, amikor olyan alkalmazásba jelentkezik be, amely csak Azure Active Directory felhasználókat támogat.

- A Felhasználónév típusa helytelen, vagy a Felhasználónév még nem lett szinkronizálva az Azure AD-vel:
  
    ![a Felhasználónév típusa helytelen, vagy nem található](./media/signin-realm-discovery/typo-username.png)
  
- A tartománynév a következő:
  
    ![a tartománynév helytelenül van begépelve, vagy nem található.](./media/signin-realm-discovery/typo-domain.png)
  
- A felhasználó egy ismert fogyasztói tartománnyal próbál bejelentkezni:
  
    ![Bejelentkezés ismert fogyasztói tartománnyal](./media/signin-realm-discovery/consumer-domain.png)
  
- A jelszó hibásan van begépelve, de a Felhasználónév pontos:  
  
    ![a jelszó típusa jó felhasználónévvel van ellátva](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Ez a funkció hatással lehet az összevont tartományokra, amelyek a régi tartományi szintű Kezdőlap-felderítésre támaszkodnak az összevonás kényszerítése érdekében. Az összevont tartományi támogatással kapcsolatos frissítések hozzáadásához tekintse meg a [Kezdőlap tartomány felderítése Microsoft 365 szolgáltatások bejelentkezését](https://azure.microsoft.com/updates/signin-hrd/)ismertető témakört. Addig is előfordulhat, hogy egyes szervezetek az alkalmazottakat olyan felhasználónévvel jelentkeznek be, amely nem szerepel a Azure Active Directoryban, de a megfelelő tartománynevet tartalmazza, mert a tartománynevek jelenleg a szervezet tartományi végpontja felé irányítják a felhasználókat. Az új bejelentkezési viselkedés nem teszi lehetővé ezt. A felhasználó értesítést kap a Felhasználónév kijavítani, és nem jogosult olyan felhasználónévvel való bejelentkezésre, amely nem szerepel a Azure Active Directoryban.
>
> Ha Ön vagy a szervezete olyan gyakorlattal rendelkezik, amelyek a régi viselkedéstől függenek, akkor fontos, hogy a szervezeti rendszergazdák frissíteni tudják az alkalmazottak bejelentkezési és hitelesítési dokumentációját, valamint hogy betanítsák az alkalmazottakat Azure Active Directory felhasználónevét a bejelentkezéshez.
  
Ha az új viselkedéssel kapcsolatos problémái vannak, hagyja meg a megjegyzéseit a jelen cikk **visszajelzések** szakaszában.  

## <a name="next-steps"></a>Következő lépések

[A bejelentkezési arculat testreszabása](../fundamentals/add-custom-domain.md)
