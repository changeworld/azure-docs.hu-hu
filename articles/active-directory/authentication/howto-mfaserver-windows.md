---
title: Windows-hitelesítés és Azure MFA-kiszolgáló – Azure Active Directory
description: A Windows-hitelesítés és az Azure Multi-Factor Authentication-kiszolgáló üzembe helyezése.
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
ms.openlocfilehash: faab28a714b1a62e1e34de5b07119aa3018db24e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263659"
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Windows-hitelesítés és Azure Multi-Factor Authentication-kiszolgáló

Az Azure Multi-Factor Authentication-kiszolgáló Windows-hitelesítés szakaszának segítségével engedélyezheti és konfigurálhatja a Windows-hitelesítést alkalmazásokhoz. A Windows-hitelesítés beállítása előtt tartsa szem előtt az alábbi listát:

* Beállítás után indítsa újra a terminálszolgáltatások Azure Multi-Factor Authentication szolgáltatását a beállítás életbe léptetéséhez.
* Ha az „Azure Multi-Factor Authentication felhasználói egyeztetés megkövetelése” lehetőség be van jelölve, és Ön nem szerepel a felhasználói listán, az újraindítás után nem fog tudni bejelentkezni a számítógépre.
* A Megbízható IP-címek attól függnek, hogy az alkalmazás képes-e biztosítani az ügyfél IP-címének hitelesítését. Jelenleg csak a Terminálszolgáltatások támogatott.  

> [!IMPORTANT]
> 2019. július 1-től a Microsoft már nem kínál új, az MFA-kiszolgálót az új üzemelő példányokhoz. Azok a felhasználók, akik a többtényezős hitelesítést szeretnék megkövetelni a felhasználóknak, felhőalapú Azure-Multi-Factor Authentication kell használniuk. Azok a meglévő ügyfelek, akik aktiválták az MFA-kiszolgálót a július 1. előtt, le tudják tölteni a legújabb verziót, a jövőbeli frissítéseket, és az aktiválási hitelesítő adatokat a szokásos módon létrehozzák.

> [!NOTE]
> Ez a szolgáltatás nem támogatott a Terminálszolgáltatások védelmének biztosítására Windows Server 2012 R2-n.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Az alkalmazások Windows-hitelesítéssel történő biztonságossá tételéhez kövesse az alábbi eljárást

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a Windows-hitelesítés ikonra.
   Windows-hitelesítés ![MFA-kiszolgálón](./media/howto-mfaserver-windows/windowsauth.png)
2. Jelölje be a **Windows-hitelesítés engedélyezése** jelölőnégyzetet. A jelölőnégyzet alapértelmezés szerint nincs bejelölve.
3. Az Alkalmazások lap révén a rendszergazda konfigurálni tud egy vagy több alkalmazást a Windows-hitelesítésre.
4. Kiszolgáló vagy alkalmazás kiválasztása – meghatározza, hogy a kiszolgáló/alkalmazás engedélyezve van-e. Kattintson az **OK** gombra.
5. Kattintson a **Hozzáadás…** gombra.
6. A Megbízható IP-címek lapon beállíthatja, hogy a rendszer kihagyja az Azure Multi-Factor Authenticationt adott IP-címekről származó Windows-munkamenetek esetén. Ha például az alkalmazottak az alkalmazást az irodából és otthonról használják, dönthet úgy, hogy nem szeretné, ha a telefonjaik folyamatosan csörögnének az Azure Multi-Factor Authentication miatt az irodában. Ehhez az irodai alhálózatot Megbízható IP-címek bejegyzésként kell megadni.
7. Kattintson a **Hozzáadás…** gombra.
8. Válassza az **Egyetlen IP**-cím lehetőséget, ha egyetlen IP-címet szeretne kihagyni.
9. Válassza az **IP-címtartomány** lehetőséget, ha egy teljes IP-címtartományt szeretne kihagyni. Példa: 10.63.193.1–10.63.193.100.
10. Válassza az **Alhálózat** lehetőséget, ha egy IP-címtartományt szeretne megadni alhálózat megjelöléssel. Adja meg az alhálózat kezdő IP-címét, és válassza ki a megfelelő alhálózati maszkot a legördülő listából.
11. Kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések

- [Külső felektől származó VPN-készülékek konfigurálása Azure MFA-kiszolgálóhoz](howto-mfaserver-nps-vpn.md)

- [Meglévő hitelesítési infrastruktúrájának kiegészítése az Azure MFA NPS bővítményével](howto-mfa-nps-extension.md)
