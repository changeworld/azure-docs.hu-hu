---
title: A StorSimple Virtual Array frissítés 0,5 kibocsátási megjegyzései |} A Microsoft Docs
description: A 0.5-ös frissítést futtató StorSimple Virtual Array kritikus megoldatlan problémák és megoldásuk ismertetése
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
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: 385d9126d578250064659153f6f0f54eec696790
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60870672"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>A StorSimple Virtual Array frissítés 0,5 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés

A következő kiadási megjegyzések a kritikus fontosságú megoldatlan problémák és a Microsoft Azure StorSimple Virtual Array frissítések megoldott problémák azonosításához.

A kibocsátási megjegyzésekben folyamatosan frissülnek, és ahogy ismertté kritikus problémák adódnak. A StorSimple Virtual Array üzembe helyezése, előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

0\.5-ös frissítés felel meg a szoftververzió **10.0.10290.0**.

> [!NOTE]
> Azok zavart okozó frissítések, és indítsa újra az eszközt. I/o van folyamatban, ha az eszköz leállást. A frissítés alkalmazása részletes utasításokért ugorjon [0.5-ös frissítés telepítése](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>0\.5-ös frissítés újdonságai
0\.5-ös frissítés elsősorban hibajavítás build. A fő fejlesztéseket és hibajavítások – a következők:

- **Biztonsági mentés rugalmasság fejlesztései** – ebben a kiadásban rendelkezik, amelyek javítják a biztonsági mentési rugalmasság javításokat. A korábbi kiadásokban a biztonsági mentések újrapróbált csak bizonyos kivételekhez. Ebben a kiadásban a biztonsági mentési kivételek újrapróbálkozik, és ellenállóbbá teszi a biztonsági másolatok.

- **Storage-használat monitorozására vonatkozó frissítések** -től 2017 június 30., a tárolás a használat monitorozása a StorSimple virtuális eszköz sorozat kivonjuk a forgalomból. Ez vonatkozik a virtuális tömbök frissítést futtató 0,4 vagy alacsonyabb a figyelési diagramokat. Ez a frissítés a tároló a használat monitorozása az Azure Portal használata továbbra is szükséges módosításokat tartalmaz. A kritikus frissítés 2017. június 30. Folytatja a figyelési funkció használata előtt.


## <a name="issues-fixed-in-the-update-05"></a>A 0.5-ös frissítés megoldott problémák

Az alábbi táblázat hibáinak javításai ebben a kiadásban összegzését tartalmazza.

| Nem. | Funkció | Probléma |
| --- | --- | --- |
| 1 |Biztonsági mentési rugalmasság| A korábbi kiadásokban a biztonsági mentések újrapróbált csak bizonyos kivételekhez. Ebben a kiadásban a biztonsági mentési kivételek való ismételt megkísérlésével rugalmasabb biztonsági másolatok készítése javítást tartalmaz.|
| 2 |Figyelés| A tároló a használat monitorozása a StorSimple virtuális eszköz sorozat elavulttá válik 2017. június 30. indítása. Ez a művelet hatással van a futó virtuális StorSimple-tömbök StorSimple-Eszközkezelő szolgáltatásban a figyelési diagramjait (1200-as modell). Ebben a kiadásban a frissítések, amelyek lehetővé teszik a storage a virtuális tömbök túli 2017. június 30. a használat monitorozása használatának folytatásához a felhasználó rendelkezik.|
| 3 |Fájlkiszolgáló| A korábbi kiadásokban a felhasználó sikerült véletlenül titkosított fájlok másolása a virtuális tömb. Ez a kiadás, amely nem teszik lehetővé a virtuális tömb a titkosított fájlok másolását a javítást tartalmaz. Ha az eszköz meglévő titkosított fájlok, a frissítés előtt, biztonsági mentések továbbra is sikertelen lesz mindaddig, amíg a titkosított fájlok törlődnek a rendszerből. |


## <a name="known-issues-in-the-update-05"></a>0\.5-ös frissítés ismert problémái

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

## <a name="next-step"></a>Következő lépés
[0.5-ös frissítés telepítése](storsimple-virtual-array-install-update-05.md) az a StorSimple Virtual Array.

## <a name="references"></a>Referencia
Egy régebbi kiadási Megjegyzés keres? Ugrás:

* [A StorSimple Virtual Array frissítés 0,4 kibocsátási megjegyzései](storsimple-virtual-array-update-04-release-notes.md)
* [A StorSimple Virtual Array frissítés 0,3 kibocsátási megjegyzései](storsimple-ova-update-03-release-notes.md)
* [A StorSimple Virtual Array frissítés 0.1, 0.2-es és kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános rendelkezésre állási kibocsátási megjegyzései](storsimple-ova-pp-release-notes.md)

