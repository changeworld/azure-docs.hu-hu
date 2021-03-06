---
title: 'Azure VPN Gateway: pont – hely kapcsolatok hibakeresése: Mac OS X-ügyfelek'
description: P2S Mac OS X VPN kapcsolatok hibaelhárítása
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f88053c93884e10e46a0f7d70106bda67b057562
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425721"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>A Mac OS X VPN-ügyfelek a pont – hely VPN-kapcsolatok hibaelhárítása

Ez a cikk segít a Mac OS X használata a natív VPN-ügyfél és az IKEv2 pont – hely kapcsolati problémák elhárításához. A VPN-ügyfél a Mac rendszerben az IKEv2 protokollhoz nagyon egyszerű, és nem teszi lehetővé a sok testreszabáshoz. Nincsenek ellenőrizendő csak négy beállítások:

* Kiszolgáló címe
* Távoli azonosítója
* Helyi azonosítója
* Hitelesítési beállítások
* Operációsrendszer-verzió (10.11 vagy újabb)


## <a name="VPNClient"></a>Tanúsítványalapú hitelesítés – problémamegoldás
1. Ellenőrizze a VPN-ügyfél beállításait. Nyissa meg a **hálózati beállítást** a Command + Shift billentyűkombináció lenyomásával, majd írja be a "VPN" parancsot a VPN-ügyfél beállításainak ellenőrzéséhez. A listából kattintson a VPN-bejegyzés, meg kell vizsgálni.

   ![Az IKEv2-alapú hitelesítés](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Ellenőrizze, hogy a **kiszolgáló címe** a teljes FQDN, és tartalmazza-e a cloudapp.net.
3. A **távoli azonosítónak** meg kell egyeznie a kiszolgáló címeként (az ÁTJÁRÓ teljes tartományneve).
4. A **helyi azonosítónak** meg kell egyeznie az ügyféltanúsítvány **tárgyával** .
5. Kattintson a **hitelesítési beállítások** elemre a hitelesítési beállítások lap megnyitásához.

   ![Hitelesítési beállítások](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Ellenőrizze, hogy a **tanúsítvány** ki van-e választva a legördülő listából.
7. Kattintson a **kiválasztás** gombra, és ellenőrizze, hogy a megfelelő tanúsítvány van-e kiválasztva. A módosítások mentéséhez kattintson **az OK** gombra.

## <a name="ikev2"></a>Felhasználónév és jelszó hitelesítésének hibakeresése

1. Ellenőrizze a VPN-ügyfél beállításait. Nyissa meg a **hálózati beállítást** a Command + Shift billentyűkombináció lenyomásával, majd írja be a "VPN" parancsot a VPN-ügyfél beállításainak ellenőrzéséhez. A listából kattintson a VPN-bejegyzés, meg kell vizsgálni.

   ![Az IKEv2 felhasználónév-jelszó](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Ellenőrizze, hogy a **kiszolgáló címe** a teljes FQDN, és tartalmazza-e a cloudapp.net.
3. A **távoli azonosítónak** meg kell egyeznie a kiszolgáló címeként (az ÁTJÁRÓ teljes tartományneve).
4. A **helyi azonosító** üres is lehet.
5. Kattintson a **hitelesítési beállítások** gombra, és ellenőrizze, hogy a "username" lehetőség ki van-e választva a legördülő listából.

   ![Hitelesítési beállítások](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Győződjön meg arról, hogy a helyes hitelesítő adatok vannak megadva.

## <a name="additional"></a>További lépések

Ha kipróbálja az előző lépéseket, és minden megfelelően van konfigurálva, töltse le a [Wireshark](https://www.wireshark.org/#download) , és hajtson végre egy csomagot.

1. Szűrje az *ISAKMP* -t, és tekintse meg a **IKE_SA** csomagokat. Tekintse meg az SA-javaslat részleteit a **hasznos adatok: biztonsági társítás**lehetőség alatt. 
2. Ellenőrizze, hogy az ügyfél és a kiszolgáló rendelkezik-e a közös.

   ![csomag](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Ha nincs kiszolgálói válasz a hálózati nyomkövetéseken, ellenőrizze, hogy engedélyezve van-e a IKEv2 protokoll az Azure Gateway Configuration lapon a Azure Portal webhelyén.

## <a name="next-steps"></a>Következő lépések
További segítségért lásd: [Microsoft ügyfélszolgálata](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
