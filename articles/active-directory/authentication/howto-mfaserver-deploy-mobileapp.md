---
title: Azure MFA-kiszolgáló Mobile App Web Service – Azure Active Directory
description: Konfigurálja az MFA-kiszolgálót leküldéses értesítések a Microsoft Authenticator alkalmazást használó felhasználóknak történő küldésére.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f62aacf8264eb583c523b6ee785f1908f0241644
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848153"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>A mobilalkalmazásos hitelesítés engedélyezése az Azure Multi-Factor Authentication-kiszolgálóval

A Microsoft Authenticator alkalmazás egy további sávon kívüli ellenőrzési lehetőséget kínál. Automatikus telefonhívás vagy SMS küldése helyett a felhasználó bejelentkezésekor az Azure Multi-Factor Authentication értesítést küld le a Microsoft Authenticator alkalmazásba a felhasználó okostelefonján vagy táblagépén. A felhasználó egyszerűen az **Ellenőrzés** (vagy a PIN-kód megadása után a „Hitelesítés”) elemre koppint a bejelentkezés befejezéséhez.

Ha a telefonon a vétel nem megbízható, kétlépéses ellenőrzést biztosító mobilalkalmazás használata ajánlott. Ha az alkalmazást OATH token előállítására használja, nincs szükség hálózatra vagy internetkapcsolatra.

> [!IMPORTANT]
> 2019. július 1-től a Microsoft már nem kínál új, az MFA-kiszolgálót az új üzemelő példányokhoz. Azok a felhasználók, akik a többtényezős hitelesítést szeretnék megkövetelni a felhasználóknak, felhőalapú Azure-Multi-Factor Authentication kell használniuk. Azok a meglévő ügyfelek, akik aktiválták az MFA-kiszolgálót a július 1. előtt, le tudják tölteni a legújabb verziót, a jövőbeli frissítéseket, és az aktiválási hitelesítő adatokat a szokásos módon létrehozzák.

> [!IMPORTANT]
> Ha telepítette az Azure Multi-Factor Authentication-kiszolgáló 8.x-es vagy újabb verzióját, a következő lépések legtöbbjére nincs szükség. A mobilalkalmazások hitelesítését [a mobilalkalmazás konfigurálását](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server) ismertető szakasz lépéseit követve állíthatja be.

## <a name="requirements"></a>Követelmények

A Microsoft Authenticator alkalmazás használatához az Azure Multi-Factor Authentication-kiszolgáló 8.x-es vagy újabb verzióját kell futtatnia.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>A mobilalkalmazás beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón

1. A Multi-Factor Authentication-kiszolgáló konzoljában kattintson a Felhasználói portál ikonra. Ha a felhasználók vezérelhetik a hitelesítési módszereiket, a Beállítások lapon a **Módszer kiválasztásának engedélyezése a felhasználóknak** lehetőség alatt jelölje be a **Mobile App** jelölőnégyzetét. Ha a szolgáltatás nincs engedélyezve, a végfelhasználóknak kapcsolatba kell lépniük a támogatási szolgálattal a Mobile App aktiválásának befejezéséhez.
2. Jelölje be a **Mobilalkalmazás aktiválásának engedélyezése a felhasználóknak** jelölőnégyzetet.
3. Jelölje be a **Felhasználó beléptetésének engedélyezése** jelölőnégyzetet.
4. Kattintson a **Mobile App** ikonra.
5. A **Fiók neve** mezőben adja meg a fiókhoz tartozó mobilalkalmazásban megjeleníteni kívánt cég- vagy szervezetnevet.
   ![MFA-kiszolgáló konfigurálása – A Mobile App beállításai](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Következő lépések

- [Speciális, az Azure Multi-Factor Authenticationre és külső VPN-ekre vonatkozó forgatókönyvek](howto-mfaserver-nps-vpn.md).
