---
title: A StorSimple Virtual Array frissítés a 0.6-os kibocsátási megjegyzései |} A Microsoft Docs
description: A 0.6-os frissítést futtató StorSimple Virtual Array kritikus megoldatlan problémák és megoldásuk ismertetése
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: e984531feced2d61332e4c399848c12cd245a34a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60870706"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>A StorSimple Virtual Array frissítés a 0.6-os kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés

A következő kiadási megjegyzések a kritikus fontosságú megoldatlan problémák és a Microsoft Azure StorSimple Virtual Array frissítések megoldott problémák azonosításához.

A kibocsátási megjegyzésekben folyamatosan frissülnek, és ahogy ismertté kritikus problémák adódnak. A StorSimple Virtual Array üzembe helyezése, előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

0\.6-os frissítés felel meg a szoftververzió **10.0.10293.0**.

> [!IMPORTANT]
> - Azok zavart okozó frissítések, és indítsa újra az eszközt. I/o van folyamatban, ha az eszköz leállást. A frissítés alkalmazása részletes utasításokért ugorjon [0.6-os frissítés telepítése](storsimple-virtual-array-install-update-06.md).
>
> - Javasoljuk, hogy azonnal, mert tartalmaz a kritikus fontosságú biztonsági javítások 0.6-os frissítés telepítése.


## <a name="whats-new-in-the-update-06"></a>0\.6-os frissítés újdonságai
0\.6-os frissítés fontos frissítése, és közvetlenül kell telepíteni. Ez a frissítés a következő javításokat tartalmazza: 

- **Windows biztonsági javítások** – ebben a kiadásban **Windows kritikus fontosságú biztonsági javítások**. A következő biztonsági frissítéseket, a biztonsági problémák és a kapcsolódó javításaival kapcsolatos további részletekért tekintse át:
    - [2016. december biztonsági csak minőségi frissítés a Windows 8.1 és Windows Server 2012 R2-ben](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [2017. márciusi biztonsági csak minőségi frissítése a Windows 8.1 és Windows Server 2012 R2-ben](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [2017. május 9 – KB4019213 (csak biztonsági frissítés)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Állítsa vissza a javítás** – a korábbi kiadásokban volt egy hiba, amely megakadályozná a visszaállítás befejeződését. Ez a hiba nem határoztak meg ebben a kiadásban.


## <a name="issues-fixed-in-the-update-06"></a>A 0.6-os frissítés megoldott problémák

Az alábbi táblázat hibáinak javításai ebben a kiadásban összegzését tartalmazza.

| Nem. | Funkció | Probléma |
| --- | --- | --- |
| 1 |Biztonság| Ebben a kiadásban a kritikus fontosságú Windows biztonsági frissítéseket tartalmazza. Javasoljuk, hogy a frissítés azonnal telepítését.|
| 2 |Visszaállítás| A visszaállítás során hiba történt a versenyhelyzet, amely megakadályozza a visszaállítási feladat befejeződését. A hibajavítás a versenyhelyzet címeket.|


## <a name="known-issues-in-the-update-06"></a>0\.6-os frissítés ismert problémái

Az alábbi táblázat a StorSimple Virtual Array az ismert problémák összegzését tartalmazza, és a kiadási jelezve a korábbi kiadásokban a problémák tartalmazza.

| Nem. | Funkció | Probléma | Megkerülő megoldás és megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |Az előzetes kiadásban létrehozott virtuális eszközre nem lehet frissíteni egy támogatott általánosan elérhető verzióra. |Ezek a virtuális eszközök feladatátvételt kell végrehajtani a végleges kiadás vész-helyreállítási munkafolyamat használatával. |
| **2.** |Kiépített adatlemez |Miután ellátta bizonyos megadott méretű adatlemez és a megfelelő virtuális StorSimple-eszközt hozott létre, meg kell nem tartalomtól az adatlemezt. Elvesztését eredményezi, a helyi rétegeken az eszköz összes adatának tegye kísérletet. | |
| **3.** |Csoportházirend |Ha egy eszköz a tartományhoz, a Csoportházirend alkalmazása kedvezőtlen hatással lehet az eszköz műveletet. |Győződjön meg arról, hogy a virtuális tömb a saját szervezeti egység (OU) az Active Directory, és nem a csoportházirend-objektumok (GPO) beállítva rajta. |
| **4.** |Helyi webes felhasználói felületen |Ha az Internet Explorer (IE ESC) engedélyezve vannak a fejlett biztonsági funkcióknak, előfordulhat, hogy néhány helyi webes felhasználói Kezelőfelületi lapok például hibaelhárítás vagy karbantartási nem működik megfelelően. Gombok ezeken a lapokon is előfordulhat, hogy nem működik. |Kapcsolja ki az Internet Explorer fokozott biztonsági funkciók. |
| **5.** |Helyi webes felhasználói felületen |Hyper-V virtuális gépen a hálózati adapterek, a webes felhasználói felületén jelennek meg, 10 GB/s felületeihez. |Ez a viselkedés a Hyper-V egy másolatát. A Hyper-V a virtuális hálózati adapterek 10 GB/s mindig látható. |
| **6.** |Rétegzett kötetek vagy megosztások |A rétegzett kötetek nem támogatott. a StorSimple együtt használható alkalmazások zárolása bájttartományt. Ha bájt tartomány zárolás engedélyezve van, nem StorSimple rétegezést működik. |Ajánlott a mértékeket tartalmazza: <br></br>Kapcsolja ki az alkalmazáslogika a zárolás bájttartományt.<br></br>Válassza ki az alkalmazáshoz tartozó adatokat a rétegzett kötetek ellentétben a gyors helyi kötetek kell.<br></br>*Ismeret*: Ha engedélyezve van az bájt tartomány zárolás használatával helyileg rögzített kötetekről, a helyileg rögzített kötet lehet online még a visszaállítás befejezése előtt. Ezekben az esetekben ha a visszaállítás van folyamatban, majd meg kell várnia a visszaállítás befejeződését. |
| **7.** |Rétegzett megosztás |Nagy fájlok használata lassú réteg felskálázása eredményezhet. |Ha nagy méretű fájlok dolgozik, azt javasoljuk, hogy a megosztás méretének % 3-nál kisebb-e a legnagyobb fájlt. |
| **8.** |Használt kapacitás megosztások |Látni fogyasztás megoszthatja, amikor nem szerepel megjeleníthető adat a megosztáson. Ez a felhasználás azért, hogy a használt kapacitás megosztások metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |Csak az ugyanahhoz a tartományhoz, mint a forráseszközt a fájlkiszolgáló vész-helyreállítási hajtható végre. Ebben a kiadásban nem támogatott a vész-helyreállítási cél eszközhöz egy másik tartományban található. |Ez egy későbbi kiadástól kezdve van megvalósítva. További információért ugorjon [feladatátvétel és vészhelyreállítás a StorSimple Virtual Array helyreállítása](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |A StorSimple virtuális eszközre nem kezelhető ebben a kiadásban az Azure Powershellen keresztül. |A virtuális eszközök kezeléséhez az összes az Azure portal és a helyi webes felületén keresztül kell elvégezni. |
| **11.** |Jelszó módosítása |A virtuális tömb eszköz konzol csak fogad bemeneti az en-us billentyűzet formátum. | |
| **12.** |CHAP |A CHAP hitelesítő adatok ezután nem lehet eltávolítani. Emellett ha módosítja a CHAP hitelesítő adatokat, meg kell le a köteteket, és hogy azok online, a módosítás érvénybe léptetéséhez. |Ezzel a problémával egy későbbi kiadástól kezdve. |
| **13.** |iSCSI server |A "használt tárolási" az iSCSI-kötet jelenik meg a StorSimple-Eszközkezelő szolgáltatás és az iSCSI-gazdagép eltérő lehet. |Az iSCSI-gazdagép rendelkezik a fájlrendszer nézetet.<br></br>Az eszköz látja, ha a kötet volt a maximális méret lefoglalt blokkok. |
| **14.** |Fájlkiszolgáló |Ha egy fájl egy mappában van egy másik Data Stream (ADS) társítva, a HIRDETÉSEK nem biztonsági mentése vagy visszaállítása vész-helyreállítási, klónozás és az elemszintű helyreállítás. | |
| **15.** |Fájlkiszolgáló |Szimbolikus hivatkozások nem támogatottak. | |
| **16.** |Fájlkiszolgáló |Windows titkosított fájlrendszer (EFS által) során átmásolt védett, vagy a StorSimple Virtual Array fájl kiszolgáló eredményt konfigurációja nem támogatott tárolt fájlokat.  | |
| **17.** |Frissítések |Ha látja-e hiba kódja: 2359302 (hexadecimális 0x240006) közben a helyi felhasználói felületen gyorsjavítást telepíteni, majd ez azt jelenti, hogy a gyorsjavítás telepítve van az eszközön.   | |

## <a name="next-step"></a>Következő lépés
[0.6-os frissítés telepítése](storsimple-virtual-array-install-update-06.md) az a StorSimple Virtual Array.

## <a name="references"></a>Referencia
Egy régebbi kiadási Megjegyzés keres? Ugrás:

* [A StorSimple Virtual Array frissítés 0,5 kibocsátási megjegyzései](storsimple-virtual-array-update-05-release-notes.md)
* [A StorSimple Virtual Array frissítés 0,4 kibocsátási megjegyzései](storsimple-virtual-array-update-04-release-notes.md)
* [A StorSimple Virtual Array frissítés 0,3 kibocsátási megjegyzései](storsimple-ova-update-03-release-notes.md)
* [A StorSimple Virtual Array frissítés 0.1, 0.2-es és kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános rendelkezésre állási kibocsátási megjegyzései](storsimple-ova-pp-release-notes.md)

