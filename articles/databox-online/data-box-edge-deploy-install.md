---
title: Oktatóanyag a telepítéshez – kicsomagolás, állvány, kábel Azure Data Box Edge fizikai eszköz | Microsoft Docs
description: A Azure Data Box Edge telepítésének második oktatóanyaga a fizikai eszköz kicsomagolásával, állványával és kábeles csatlakoztatásával kapcsolatos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: fe74db34e62a80935954c6cfc2e591d49a84b0b7
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263948"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Oktatóanyag: Azure Data Box Edge telepítése

Ez az oktatóanyag a Data Box Edge fizikai eszköz üzembehelyezési módját ismerteti. A telepítési eljárás magában foglalja az eszköz kicsomagolását, az állvány csatlakoztatását és a kábelezést. 

A telepítés elvégzése körülbelül két órát is igénybe vehet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz csatlakoztatása
> * Az eszköz bekábelezése

## <a name="prerequisites"></a>Előfeltételek

A fizikai eszközök telepítésének előfeltételei a következők:

### <a name="for-the-data-box-edge-resource"></a>Data Box Edge-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Elvégezte a [Azure Data Box Edge üzembe helyezésének előkészítése](data-box-edge-deploy-prep.md)című témakör összes lépését.
    * Létrehozott egy Data Box Edge erőforrást az eszköz üzembe helyezéséhez.
    * Létrehozta az eszköz a Data Box Edge-erőforrással való aktiválásához szükséges aktiválási kulcsot.

 
### <a name="for-the-data-box-edge-physical-device"></a>Data Box Edge fizikai eszköz esetén

Az eszköz üzembe helyezése előtt:

- Győződjön meg róla, hogy az eszköz biztonságosan működik-e egy sima, stabil és szintű munkafelületen.
- Győződjön meg arról, hogy az eszköz beállításához kijelölt helyen van:
    - Standard AC-áramellátás független forrásból

        -VAGY-
    - Szünetmentes áramforrást (UPS) tartalmazó rack Power Distribution Unit (PDU)
    - Azon állványon elérhető 1U-tárolóhely, amelyhez csatlakoztatni kívánja az eszközt

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén

Előzetes teendők

- Tekintse át a Data Box Edge üzembe helyezésének hálózati követelményeit, és konfigurálja az adatközponti hálózatot a követelmények szerint. További információkért lásd [a Data Box Edge hálózati követelményeit](data-box-edge-system-requirements.md#networking-port-requirements) ismertető szakaszt.

- Győződjön meg arról, hogy az eszköz optimális működéséhez 20 Mbps sebesség szükséges.


## <a name="unpack-the-device"></a>Az eszköz kicsomagolása

Az eszköz egyetlen dobozban érkezik. Az eszköz kibontásához hajtsa végre a következő lépéseket. 

1. Helyezze a csomagot egy sima, vízszintes felületre.
2. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a doboz vagy a csomagolás súlyosan sérült, ne nyissa meg. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e.
3. Bontsa ki a dobozt. A csomag felbontása után ellenőrizze, hogy megvannak-e a következők:
    - Egyetlen ház Data Box Edge eszköz
    - Két tápkábel
    - Egy vasúti készlet szerelvény
    - Biztonsági, környezeti és szabályozási tájékoztató füzet

Ha nem kapta meg az itt felsorolt összes elemet, forduljon Data Box Edge támogatási szolgálathoz. A következő lépés az eszköz csatlakoztatása.


## <a name="rack-the-device"></a>Az eszköz állványra szerelése

Az eszközt a standard 19 hüvelykes állványra kell telepíteni. A következő eljárással csatlakoztathatja az eszközt egy standard 19 hüvelykes állványra.

> [!IMPORTANT]
> A Data Box Edge-eszközöket a megfelelő működéshez kiszolgálószekrénybe kell szerelni.


### <a name="prerequisites"></a>Előfeltételek

- Mielőtt elkezdené, olvassa el a biztonsági, környezeti és szabályozási információs füzet biztonsági utasításait. Ezt a füzetet az eszközzel szállították.
- Kezdje el a sínek telepítését a rendelkezésre álló, a rack ház aljához legközelebb eső helyen.
- A kiépített vasúti csatlakoztatási konfigurációhoz:
    -  Nyolc csavart kell megadnia: #10-32, #12-24, #M5 vagy #M6. A csavarok fő átmérőjének 10 mm-nél (0,4 ") kisebbnek kell lennie.
    -  Egy lapos csavarhúzóra van szüksége.

### <a name="identify-the-rail-kit-contents"></a>A Rail Kit tartalmának azonosítása

Keresse meg a következő összetevőket a Rail Kit szerelvény telepítéséhez:
1. Két A7-es Dell ReadyRails II csúszó vasúti szerelvény
2. Két Hook-és hurok-pánt

    ![A vasúti készlet tartalmának azonosítása](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Eszköz nélküli sínek (szögletes furat vagy kerek furatos állványok) telepítése és eltávolítása

> [!TIP]
> Ez a lehetőség kevésbé fontos, mert nem szükséges eszközöket telepíteni és eltávolítani a síneket a menet nélküli négyzetbe vagy kerek lyukakba az állványokon.

1. Helyezze a bal és a jobb oldali sín **elülső** részeit, és az összes végpontot a függőleges rack karimák elülső oldalán lévő lyukakba vigye.
2. Illessze be az egyes végpontokat a kívánt U-szóközök alsó és felső részén.
3. Folytassa a sín hátsó végét egészen addig, amíg teljes mértékben nem ül a függőleges állvány peremén, és a retesz a helyére kattan. Ismételje meg ezeket a lépéseket úgy, hogy az előtér-darabot a függőleges rack karimán pozícionálja és helyezze el.
4. A sínek eltávolításához húzza le a zárolás feloldása gombot a végének középpontjában, és törölje az egyes korlátokat.

    ![Eszköz nélküli sínek telepítése és eltávolítása](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Eszközön futó sínek (többszálú furatok) telepítése és eltávolítása

> [!TIP]
> Ez a beállítás azért szükséges, mert egy eszközre (_egy lapos csavarhúzóra_) van szükség a sínek a rackben lévő menetes kör alakú lyukakba való telepítéséhez és eltávolításához.

1. Távolítsa el a PIN-kód első és hátsó szerelvényeit egy lapos csavarhúzó használatával.
2. A sínre reteszelő alszerelvények lekérése és elforgatásával távolítsa el őket a beépítési zárójelből.
3. Csatolja a bal és a jobb oldali szerelvényt az elülső függőleges rack karimához két pár csavart használva.
4. Csúsztassa a bal és a jobb oldali zárójelet a hátsó függőleges rack karimák felé, és csatolja őket két pár csavar használatával.

    ![Eszközre telepített sínek telepítése és eltávolítása](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>A rendszer telepítése rackben

1. Húzza ki a belső diát a rackből, amíg a helyükre nem zár.
2. Keresse meg a rendszer mindkét oldalán a hátsó vasúti patthelyzetet, és csökkentse azokat a hátsó, a dia szerelvényeken található nyílásokkal. A rendszer lefelé forgatása, amíg az összes vasúti patthelyzet be nem illeszkedik a J-slotba.
3. A befelé irányuló bekapcsolás után a zárolási kar kattintson a helyére.
4. Nyomja le mindkét síneken a slide-Release Lock gombokat, és csúsztassa a rendszerét a rackbe.

    ![Rendszer telepítése rackben](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>A rendszer eltávolítása a rackből

1. Keresse meg a rögzítési kart a belső sínek oldalain.
2. Oldja fel az egyes kart úgy, hogy a kiadási helyükre elforgatja.
3. A rendszer szilárdan fogja megfogni a rendszer oldalait, és addig húzza azt addig, amíg a sínre nem állnak a J-slotok. Emelje fel a rendszer felszínét a rackből, és helyezze el egy szint felületén.

    ![Rendszer eltávolítása a rackből](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>A Slam-zár bevonása és felszabadítása

> [!NOTE]
> A Slam-zárakkal nem rendelkező rendszerek esetén a rendszer a csavarok segítségével gondoskodik a rendszerről, az eljárás 3. lépésében leírtak szerint.

1. Az előtérben keresse meg a Slam-zárat a rendszer egyik oldalán.
2. A zárolások automatikusan felvesznek, mivel a rendszer leküldi az állványra, és a zárolásokra való ráhúzással felszabadítja azokat.
3. A rendszer a rackben vagy más instabil környezetekben történő szállításának biztosításához keresse meg a rögzített csavart az egyes zárolások alatt, és minden csavart egy #2 Phillips csavarhúzóval kell megszigorítani.

    ![A Slam-zárak bevonása és kiadása](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Az eszköz bekábelezése

Irányítsa a kábeleket, majd csatlakoztassa az eszközt. Az alábbi eljárások azt ismertetik, hogyan lehet a Data Box Edge eszközét az áramellátáshoz és a hálózathoz csatlakozni.

Az eszköz kábelezésének megkezdése előtt a következőkre lesz szüksége:

- A Data Box Edge fizikai eszköz, a kicsomagolt és a rack csatlakoztatása.
- Két tápkábel.
- Legalább egy 1-GbE RJ-45 hálózati kábel a felügyeleti felülethez való csatlakozáshoz. Az eszközön két 1-GbE hálózati adapter (egy felügyeleti és egy adathálózati) található.
- Egy 25-GbE SFP+ rézkábel minden konfigurálni kívánt adathálózati adapterhez. Legalább egy adathálózati adaptert a 2. port, a 3. port, az 5. port vagy a 6-os port közül kell csatlakoztatni az internethez (az Azure-hoz való csatlakozással).  
- Hozzáférés két energiaellátási egységhez (ajánlott).

> [!NOTE]
> - Ha csak egy adathálózati adaptert csatlakoztat, javasoljuk, hogy használjon 25/10 GbE hálózati adaptert, például a 3. portot, a 4-es portot, az 5. portot vagy a 6-os PORTOT az Azure-ba való adatküldéshez. 
> - A legjobb teljesítmény érdekében és nagy mennyiségű adat kezeléséhez fontolja meg az összes adatport csatlakoztatását.
> - A Data Box Edge eszköznek csatlakoznia kell az adatközpont-hálózathoz, hogy az adatforrás-kiszolgálókról is betöltse az adatgyűjtést.

Data Box Edge eszközön:

- Az előlapon a lemezmeghajtók és a főkapcsoló gomb található.

    - Az eszköz elején 10 lemezes tárolóhely található.
    - A 0. tárolóhely 240 GB-os SATA-meghajtót használ operációsrendszer-lemezként. Az 1. bővítőhely üres, a 2 – 9. bővítőhely pedig adatlemezként használt SSD-NVMe.
- A háttérrendszer redundáns tápegységeket (PSUs) tartalmaz.
- A hátsó síkon hat hálózati adapter van:

    - Két 1 GB/s illesztőfelület.
    - 4 25 – Gbps felületek, amelyek 10 GB/s illesztőfelületként is használhatók.
    - Egy alaplapi felügyeleti vezérlő (BMC).

- A háttérrendszer két hálózati kártyával rendelkezik, amelyek megfelelnek a 6 portnak:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

A hálózati kártyák által támogatott kábelek, kapcsolók és adóvevők teljes listájáért nyissa meg a [Cavium FastlinQ 41000 Series együttműködési mátrixot](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
A következő lépésekkel csatlakoztassa az eszközt az áramellátáshoz és a hálózathoz.

1. Azonosítsa az eszköz hátsó síkja különböző portokat.

    ![Kábeles eszköz hátsó síkja](./media/data-box-edge-deploy-install/backplane-cabled.png)

2. Keresse meg a lemezes tárolóhelyeket és a főkapcsoló gombot az eszköz elején.

    ![Az eszköz elülső síkja](./media/data-box-edge-deploy-install/device-front-plane-labeled-1.png)

3. Csatlakoztassa a tápkábeleket a burkolat egy-egy tápegységéhez. A magas rendelkezésre állás biztosításához az egyes tápegységeket különböző áramforrásokhoz csatlakoztassa.
4. Csatlakoztassa a tápkábeleket a kiszolgálószekrény áramelosztó egységeihez (PDU-k). Ügyeljen arra, hogy a két tápegység külön áramforrást használjon.
5. Nyomja meg a főkapcsolót az eszköz bekapcsolásához.
6. Kapcsolódjon az 1 – GbE hálózati adapter 1-es PORTJÁhoz a fizikai eszköz konfigurálásához használt számítógéphez. A PORT 1 a dedikált felügyeleti felület.
7. A PORT 2, PORT 3, PORT 4, PORT 5 vagy PORT 6 közül legalább egyet az adatközponti hálózathoz/internethez kell csatlakoztatni.

    - A PORT 2 csatlakoztatása esetén használja az RJ-45 hálózati kábelt.
    - A 10/25 GbE hálózati adapterek esetében használja az SFP + Copper kábeleket.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a Data Box Edge témaköröket, például a következőket:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz állványra szerelése
> * Az eszköz bekábelezése

A következő oktatóanyag az eszköz csatlakoztatását, beállítását és aktiválását mutatja be.

> [!div class="nextstepaction"]
> [Data Box Edge összekapcsolásának és beállításának beállítása](./data-box-edge-deploy-connect-setup-activate.md)
