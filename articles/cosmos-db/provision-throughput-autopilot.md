---
title: Hozzon létre Azure Cosmos-tárolókat és-adatbázisokat az Autopilot módban.
description: Ismerje meg az előnyöket, használati eseteket, valamint az Azure Cosmos-adatbázisok és-tárolók üzembe helyezését Autopilot módban.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246655"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Azure Cosmos-tárolók és-adatbázisok létrehozása Autopilot módban (előzetes verzió)

Az Azure Cosmos DB segítségével manuális és Autopilot (automatikus) módban is kioszthatja a tárolók átviteli sebességét. Ez a cikk az Autopilot mód előnyeit és használati eseteit ismerteti.

> [!NOTE]
> Az Autopilot mód jelenleg nyilvános előzetes verzióban érhető el. [Az Autopilot csak új adatbázisok és tárolók esetében engedélyezhető](#create-a-database-or-a-container-with-autopilot-mode) . A meglévő tárolók és adatbázisok esetében nem érhető el.

Az átviteli sebesség manuális kiépítés mellett mostantól az Azure Cosmos-tárolókat is konfigurálhatja Autopilot módban. Az Autopilot módban konfigurált tárolók és adatbázisok **automatikusan és azonnal méretezhetik az alkalmazás által igényelt kiépített átviteli sebességet anélkül, hogy ez befolyásolná a számítási feladat rendelkezésre állását, késését, sebességét vagy teljesítményét globálisan.**

A tárolók és adatbázisok robotpilóta-módban való konfigurálásakor meg kell adnia a maximális átviteli sebességet, `Tmax` nem lehet túllépni. A tárolók ezt követően átméretezhetik az átviteli sebességet, hogy `0.1*Tmax < T < Tmax`ek legyenek. Más szóval, a tárolók és az adatbázisok a számítási feladatok igénye alapján azonnal méretezhetők, a maximális átviteli sebesség értékének 10%-ában, amelyet a beállított maximális átviteli sebességre konfiguráltak. Egy Autopilot-adatbázison vagy-tárolón bármikor módosíthatja a maximális átviteli sebesség (`Tmax`) beállítást. Az Autopilot lehetőséggel az 400 RU/s minimális átviteli sebesség/tároló vagy adatbázis már nem alkalmazható.

Az Autopilot előzetes verziójában a tárolón vagy az adatbázison megadott maximális átviteli sebességnél a rendszer a számított tárolási korláton belül lehetővé teszi a működést. Ha túllépi a tárolási korlátot, a maximális átviteli sebesség automatikusan magasabb értékre van igazítva. Ha az adatbázis szintjének átviteli sebességét robotpilóta módban használja, az adatbázison belül engedélyezett tárolók száma a következőképpen számítható ki: `0.001*TMax`. Ha például 20 000 Autopilot RU/s-t épít ki, akkor az adatbázis 20 tárolóval rendelkezhet.

## <a name="benefits-of-autopilot-mode"></a>Az Autopilot mód előnyei

Az Autopilot módban konfigurált Azure Cosmos-tárolók a következő előnyöket nyújtják:

* **Egyszerű:** Az Autopilot módban lévő tárolók megszüntetik az összetettséget a kiépített átviteli sebesség (RUs) és a kapacitás manuális kezeléséhez a különböző tárolók esetében.

* **Skálázható:** Az Autopilot módban lévő tárolók zökkenőmentesen méretezhetők a kiosztott átviteli kapacitás igény szerint. Az ügyfélkapcsolatok, az alkalmazások és a meglévő SLA-kat nem érintik.

* **Költséghatékony:** Ha robotpilóta-módban konfigurált tárolókat használ, csak azokat az erőforrásokat kell fizetnie, amelyeket a számítási feladatoknak óránként kell használniuk.

* **Magasan elérhető:** Az Autopilot módban lévő tárolók ugyanazt a globálisan elosztott, hibatűrő, magas rendelkezésre állású hátteret használják, amely biztosítja az adattartósságot és a magas rendelkezésre állást.

## <a name="use-cases-of-autopilot-mode"></a>Az Autopilot mód használatának esetei

Az Autopilot módban konfigurált Azure Cosmos-tárolók használati esetei a következők:

* **Változó számítási feladatok:** Ha egy könnyű használatú alkalmazást futtat, és a maximális kihasználtság 1 óra, akkor naponta többször vagy évente többször is elvégezheti az órák számát. Ilyenek például az emberi erőforrások, a költségvetések és az operatív jelentéskészítési alkalmazások. Ilyen esetekben az Autopilot módban konfigurált tárolók használhatók, és a továbbiakban nem kell manuálisan kiépíteni a csúcs-vagy az átlagos kapacitást.

* **Előre nem látható számítási feladatok:** Ha olyan munkaterheléseket futtat, amelyeken a nap folyamán adatbázis-használat van, de a tevékenységek is nehezen megbecsülhető. Ilyen például egy olyan forgalmi hely, amely az időjárás-előrejelzés változásakor meghaladó aktivitást lát. Az Autopilot üzemmódban konfigurált tárolók úgy módosítják a kapacitást, hogy megfeleljenek az alkalmazás maximális terhelésének, és a leskálázás a tevékenység túllépését eredményezi.

* **Új alkalmazások:** Ha új alkalmazást telepít, és nem biztos benne, hogy mekkora mennyiségű kiosztott átviteli sebességre van szükség. Az Autopilot módban konfigurált tárolók automatikusan méretezhetők az alkalmazás kapacitási igényeire és követelményeire.

* **Ritkán használt alkalmazások:** Ha olyan alkalmazást használ, amely naponta, hetente vagy havonta többször is használatban van, például egy kis mennyiségű alkalmazás/Web/Blog webhelyen.

* **Fejlesztési és tesztelési adatbázisok:** Ha a fejlesztők a munkaidőben tárolókat használnak, de nem szükségesek éjszakára vagy hétvégére. Az Autopilot módban konfigurált tárolók esetében a rendszer a használaton kívüli minimálisra méretezi le azokat.

* **Ütemezett üzemi munkaterhelések/lekérdezések:** Ha egy adott tárolón ütemezett kérelmek/műveletek/lekérdezések sorozata van, és ha vannak olyan tétlen időszakok, amikor egy abszolút alacsony átviteli sebességen szeretne futni, mostantól könnyedén elvégezhető. Ha egy ütemezett lekérdezés/kérelem egy Autopilot módban konfigurált tárolóhoz van elküldve, a rendszer a szükséges mértékben automatikusan felskálázást végez, és futtatja a műveletet.

Az előző problémák megoldásához nem csupán nagy mennyiségű időt kell igénybe venni a megvalósításban, de összetettséget is bevezetnek a konfigurációban vagy a kódban, és gyakran kézi beavatkozásra van szükségük a megoldáshoz. Az Autopilot mód lehetővé teszi, hogy a fenti forgatókönyvek a dobozból is elérhetők legyenek, így többé nem kell aggódnia ezekkel a problémákról.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Összehasonlítás – kézi üzemmódban konfigurált tárolók és Autopilot mód

|  | Manuális módban konfigurált tárolók  | Robotpilóta-módban konfigurált tárolók |
|---------|---------|---------|
| **Kiosztott átviteli sebesség** | Manuálisan kiépítve. | Automatikusan és azonnal méretezhető a munkaterhelés-használati minták alapján. |
| **Kérelmek/műveletek korlátozása (429)**  | Előfordulhat, hogy a felhasználás meghaladja a kiosztott kapacitást. | Nem fog történni, ha a felhasznált átviteli sebesség az Autopilot módban kiválasztott maximális átviteli sebességen belül van.   |
| **Kapacitástervezés** |  Meg kell tennie a kezdeti kapacitás megtervezését és a szükséges átviteli sebesség kiépítését. |    Nem kell aggódnia a kapacitás megtervezése miatt. A rendszer automatikusan gondoskodik a kapacitás megtervezéséről és a kapacitások kezeléséről. |
| **Díjszabás** | Manuálisan kiépített RU/s óránként. | Az egyszeri írási régió fiókjai esetében óradíjat használ a robotpilóta (RU/s) óránkénti díjszabása alapján. <br/><br/>A több írási régióval rendelkező fiókok esetében nem számítunk fel külön díjat a robotpilóta számára. Az óránkénti átviteli sebességért kell fizetnie, ugyanazzal a több főkiszolgálós RU/s-díj használatával. |
| **Legmegfelelőbb a számítási feladatok típusaihoz** |  Kiszámítható és stabil számítási feladatok|   Kiszámíthatatlan és változó számítási feladatok  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Adatbázis vagy tároló létrehozása robotpilóta-móddal

Az Autopilot-t konfigurálhatja új adatbázisokhoz vagy tárolók létrehozásához a Azure Portalon keresztül. A következő lépésekkel hozzon létre egy új adatbázist vagy tárolót, engedélyezze az Autopilot használatát, és határozza meg a maximális átviteli sebességet (RU/s).

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) vagy a [Azure Cosmos db Explorerben.](https://cosmos.azure.com/)

1. Navigáljon a Azure Cosmos DB-fiókjához, és nyissa meg a **adatkezelő** lapot.

1. Válassza az **új tároló elemet.** Adja meg az adatbázis, a tároló és a partíciós kulcs nevét. Az **átviteli sebesség**területen válassza ki az **Autopilot** beállítást, és válassza ki azt a maximális átviteli sebességet (ru/s), amelyet az adatbázis vagy a tároló nem léphet túl az Autopilot beállítás használatakor.

   ![Tároló létrehozása és az Autopilot átviteli sebességének konfigurálása](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Kattintson az **OK** gombra.

Az **adatbázis-átviteli sebesség** kiosztása lehetőség kiválasztásával létrehozhat egy olyan megosztott átviteli sebességű adatbázist, amely robotpilóta-móddal rendelkezik.

## <a id="autopilot-limits"></a>Az Autopilot átviteli sebessége és tárolási korlátai

Az alábbi táblázat az Autopilot mód különböző lehetőségeinek maximális teljes és tárolási korlátját mutatja be:

|Maximális átviteli sebesség korlátja  |Maximális tárolási korlát  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20 000 RU/s  |  200 GB  |
|100 000 RU/s    |  1 TB   |
|500 000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Következő lépések

* Tekintse át az [Autopilot – gyakori kérdések](autopilot-faq.md)című szakaszt.
* További információ a [logikai partíciókhoz](partition-data.md).
* Útmutató az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiépítéséhez.
* Útmutató az [átviteli sebesség Azure Cosmos-adatbázison](how-to-provision-database-throughput.md)való kiépítéséhez.
