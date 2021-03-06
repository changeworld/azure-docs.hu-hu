---
title: Telepített StorSimple-eszköz hibáinak megoldása | Microsoft Docs
description: Ismerteti, hogyan diagnosztizálhatja és javíthatja a jelenleg üzembe helyezett és működő StorSimple-eszközön előforduló hibákat.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: ca79e4240c1a82e46bea44a9d018a3c681920480
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933296"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Operatív StorSimple-eszköz hibáinak megoldása
> [!NOTE]
> A StorSimple klasszikus portálja elavult. A StorSimple-eszközkezelők automatikusan átkerülnek az új Azure Portalra az elavulási ütemezésnek megfelelően. Erről az áthelyezésről kapni fog egy e-mailt és egy Portal-értesítést. Ez a dokumentum hamarosan el lesz távolítva. Ha kérdései vannak az áthelyezéssel kapcsolatban, tekintse meg a [Gyakori kérdések: Váltás az Azure Portalra](storsimple-8000-move-azure-portal-faq.md) szakaszt.

## <a name="overview"></a>Áttekintés
Ez a cikk hasznos hibaelhárítási útmutatást nyújt a StorSimple-eszköz üzembe helyezése és működtetése után esetlegesen felmerülő konfigurációs problémák megoldásához. Ismerteti a gyakori problémákat, a lehetséges okokat és a javasolt lépéseket, amelyek segítségével megoldhatja a Microsoft Azure StorSimple futtatásakor felmerülő problémákat. Ez az információ a StorSimple helyszíni fizikai eszközre és a StorSimple virtuális eszközre is vonatkozik.

A cikk végén megtalálja a Microsoft Azure StorSimple művelet során felmerülő hibakódok listáját, valamint a hibák megoldásához szükséges lépéseket. 

## <a name="setup-wizard-process-for-operational-devices"></a>A telepítővarázsló működési eszközeinek folyamata
A telepítővarázsló ([meghívó-hcssetupwizard parancsmagot][1]) használatával ellenőrizze az eszköz konfigurációját, és szükség esetén végezze el a szükséges javítási műveleteket.

Ha egy előzőleg konfigurált és működő eszközön futtatja a telepítővarázslót, a folyamat eltérő lesz. Csak a következő bejegyzéseket módosíthatja:

* IP-cím, alhálózati maszk és átjáró
* Elsődleges DNS-kiszolgáló
* Elsődleges NTP-kiszolgáló
* Nem kötelező webproxy-konfiguráció

A telepítővarázsló nem hajtja végre a jelszó-gyűjtéssel és az eszközök regisztrálásával kapcsolatos műveleteket.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>A telepítővarázsló következő futtatása során fellépő hibák
A következő táblázat ismerteti azokat a hibákat, amelyek akkor fordulhatnak elő, amikor a telepítővarázsló egy operatív eszközön fut, lehetséges okai a hibák, és a javasolt műveletek a megoldáshoz. 

| Nem. | Hibaüzenet vagy feltétel | Lehetséges okok | Javasolt művelet |
|:--- |:--- |:--- |:--- |
| 1 |350032-es hiba: ez az eszköz már inaktiválva van. |Ez a hiba akkor jelenik meg, ha a telepítővarázsló egy inaktivált eszközön fut. |A további lépésekért [forduljon a Microsoft ügyfélszolgálatához](storsimple-contact-microsoft-support.md). Inaktivált eszköz nem helyezhető üzembe. Az eszköz újbóli aktiválása előtt szükség lehet a gyári beállítások visszaállítására. |
| 2 |Meghívó-Hcssetupwizard parancsmagot: ERROR_INVALID_FUNCTION (kivétel a HRESULT: 0x80070001) |A DNS-kiszolgáló frissítése sikertelen. A DNS-beállítások globális beállítások, és az összes engedélyezett hálózati adapteren alkalmazhatók. |Engedélyezze a felületet, és alkalmazza újra a DNS-beállításokat. Ez megzavarhatja a hálózatot más engedélyezett felületek esetében, mivel ezek a beállítások globálisak. |
| 3 |Az eszköz úgy tűnik, hogy online állapotban van a StorSimple Manager Service Portalon, de ha megpróbálja befejezni a minimális telepítést, és menti a konfigurációt, a művelet meghiúsul. |A kezdeti beállítás során a webproxy nincs konfigurálva, még akkor is, ha valóban van proxykiszolgáló. |A hiba megkereséséhez használja a [test-HcsmConnection parancsmagot][2] . Ha nem tudja elhárítani a problémát, [lépjen kapcsolatba Microsoft ügyfélszolgálata](storsimple-contact-microsoft-support.md) . |
| 4 |Meghívás – Hcssetupwizard parancsmagot: az érték nem a várt tartományon belül esik. |Az alhálózati maszk helytelenül állítja elő ezt a hibát. A lehetséges okok a következők: <ul><li> Az alhálózati maszk hiányzik vagy üres.</li><li>Az IPv6-előtag formátuma helytelen.</li><li>Az interfész felhőben engedélyezve van, de az átjáró hiányzik vagy helytelen.</li></ul>Vegye figyelembe, hogy a rendszer automatikusan engedélyezi a 0 értéket, ha a telepítővarázsló segítségével konfigurálja azokat. |A probléma meghatározásához használja a 0.0.0.0 vagy a 256.256.256.256 alhálózatot, és tekintse meg a kimenetet. Szükség szerint adja meg az alhálózati maszk, az átjáró és az IPv6-előtag helyes értékeit. |

## <a name="error-codes"></a>Hibakódok
A hibák numerikus sorrendben vannak felsorolva.

| Hiba száma | Hiba szövege vagy leírása | Ajánlott felhasználói művelet |
|:--- |:--- |:--- |
| 10502 |Hiba történt a Storage-fiókhoz való hozzáférés során. |Várjon néhány percet, majd próbálkozzon újra. Ha a hiba továbbra is fennáll, vegye fel a kapcsolatot Microsoft ügyfélszolgálata a következő lépésekhez. |
| 40017 |A biztonsági mentési művelet sikertelen volt, mert a biztonsági mentési házirendben megadott kötet nem található az eszközön. |Próbálja megismételni a biztonsági mentési műveletet, ha a hiba továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. a következő lépésekhez. |
| 40018 |A biztonsági mentési művelet meghiúsult, mert a biztonsági mentési házirendben megadott kötetek egyike sem található az eszközön. |Próbálja megismételni a biztonsági mentési műveletet, ha a hiba továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. a következő lépésekhez. |
| 390061 |A rendszer foglalt vagy nem érhető el. |Várjon néhány percet, majd próbálkozzon újra. Ha a hiba továbbra is fennáll, vegye fel a kapcsolatot Microsoft ügyfélszolgálata a következő lépésekhez. |
| 390143 |Hiba történt a 390143-es hibakód miatt. (Ismeretlen hiba.) |Ha a hiba továbbra is fennáll, vegye fel a kapcsolatot Microsoft ügyfélszolgálata a következő lépésekhez. |

## <a name="next-steps"></a>Következő lépések
Ha nem tudja feloldani a problémát, [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-contact-microsoft-support.md) segítségért. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
