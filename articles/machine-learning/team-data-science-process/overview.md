---
title: Mi a csoportos adatelemzési folyamat?
description: Itt olyan adatelemzési módszer, prediktív elemzési megoldások és intelligens alkalmazásokat.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 1/10/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 10d6e562301e089700940ac5dfb212bcc4e09653
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79088079"
---
# <a name="what-is-the-team-data-science-process"></a>Mi a csoportos adatelemzési folyamat?

A csoportos adatelemzési folyamat (TDSP) egy rugalmas és iteratív adatelemzési módszer, amelynek célja prediktív elemzési megoldások és intelligens alkalmazások hatékony. A TDSP segít a csapatmunka és a tanulás fejlesztésében azzal, hogy a csapat szerepkörei hogyan működnek együtt a legjobban. A TDSP a Microsoft és más iparági vezetők által ajánlott eljárásokat és struktúrákat tartalmaz, amelyek segítenek az adatelemzési kezdeményezések sikeres megvalósításában. A cél a vállalatok segítése az elemzési programjuk előnyeinek teljes körű megvalósításában.

Ez a cikk áttekintést TDSP és fő összetevőit. Az itt található folyamat általános leírását biztosítjuk, amely különböző típusú eszközökkel valósítható meg. További csatolt témakörökben található egy részletes leírását a tevékenységeket és a szerepkörök részt vesz a folyamat élettartama biztosítunk. -A használatával egy adott Microsoft-eszközök és infrastruktúra, a teams szolgáltatásban a TDSP megvalósításához használt TDSP megvalósításához is útmutatást.

## <a name="key-components-of-the-tdsp"></a>A TDSP fő összetevői

TDSP a következő fő összetevőből áll:

- **Adatelemzési életciklus** definíciója
- **Szabványosított projekt szerkezete**
- Az adatelemzési projektek **infrastruktúrája és erőforrásai**
- A projekt végrehajtásához szükséges **eszközök és segédprogramok**


## <a name="data-science-lifecycle"></a>Adatelemzési életciklus

A csoportos adatelemzési folyamat (TDSP) biztosít egy életciklussal, építse fel az adatelemzési projektek fejlesztését. Az életciklus a sikeres projektek követésének teljes lépéseit ismerteti.

Ha más adatelemzési életciklust használ, például a [ropogós DM-](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)t, a [KDD](https://wikipedia.org/wiki/Data_mining#Process) -t vagy a szervezet saját egyéni folyamatát, akkor továbbra is használhatja a feladat-alapú TDSP a fejlesztési életciklusok kontextusában. Magas szinten ezek különböző módszereket rendelkezik nagy részét. 

Ennek az életciklusnak úgy lett kialakítva, amelyek az intelligens alkalmazások része adatelemzési projektek. Ezek az alkalmazások üzembe helyezése a machine learning és a mesterséges intelligenciát használó modellek prediktív elemzőeszközöket. A feltáró adatelemzési projektek vagy az improvizált elemzési projektek is hasznosak lehetnek ennek a folyamatnak a használatával. Azonban ezekben az esetekben a leírt lépések nincs szükség.    

Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

* **Üzleti ismeretek**
* **Adatgyűjtés és-megértés**
* **Modellezés**
* **Üzembe helyezés**
* **Ügyfél-elfogadás**

Itt látható a **csoportos adatelemzési folyamat életciklusának**vizuális ábrázolása. 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

A TDSP-életciklus egyes szakaszaihoz tartozó célok, feladatok és dokumentációs összetevők leírása a [csoportos adatelemzési folyamat életciklusa](lifecycle.md) című témakörben található. Ezekről a feladatokról és összetevők-projekt szerepkörök tartoznak:

- Felhőmegoldás-fejlesztő mérnök
- Projektvezető
- Adatelemző
- Projektvezető 

A következő ábra összetevőkről (zöld) (a vízszintes tengely) életciklusának minden egyes fázisa társított ezen szerepkörök (a függőleges tengely) és (a kék) feladatok rács nézetét jeleníti meg. 

[![TDSP – szerepkörök és feladatok](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Szabványos projektstruktúra

Az összes projekt megosztása a könyvtárstruktúra és sablonok használatával projekt dokumentumok kellene megkönnyíti a projektjeikhez további információt a csoport tagjai. Kód és a dokumentumok a verziókezelő rendszer (virtuális) például a Git, TFS Subversion engedélyezése és együttműködést vannak tárolva. Követési feladatokat és a szolgáltatások a nyomon követési rendszer például a Jira, Rally és az Azure DevOps-projektkezelési lehetővé teszi, hogy közelebb nyomon követését, a kód egyes funkciókra. Az ilyen nyomkövetési is lehetővé teszi a csapatok beszerzése jobban költségekről. TDSP azt javasolja, különálló-tárházat az egyes projektek létrehozása a alapú VC-k verziószámozása, információ-biztonság és együttműködést. A szabványos struktúra az összes projekt segítségével hozhat létre az intézményi ismeretek a szervezeten belül.

Sablonok a mappastruktúrát és a nem szabványos helyekre szükséges dokumentumok biztosítunk. A mappastruktúra rendszerezi a fájlokat, amelyek az adatok feltárása és a szolgáltatás kivonása kódját tartalmazza, és, jegyezze fel a modell az ismétlések. Ezek a sablonok megkönnyítik a csapattagok, melyben megismerheti a mások által végzett munka, illetve új tagokat felvenni a csapatok számára. Könnyű megtekintése és módosítása a dokumentumok sablonjainak markdown formátumban. A sablonok segítségével az egyes projektek legfontosabb kérdéseivel biztosíthatók a feladatlisták, így biztosítható, hogy a probléma megfelelően legyen meghatározva, és hogy a termékek megfeleljenek a várt minőségnek. Példák erre vonatkozóan:

- egy projekt bérlő az üzleti probléma megoldására és a projekt hatókörének dokumentálása
- dokumentum szerkezetét és a nyers adatok statisztikai adatok jelentések
- modell-jelentést a dokumentum a származtatott szolgáltatásai
- például ROC-görbét vagy MSE modell teljesítmény-mérőszámok


[![TDSP – címtárak](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

A könyvtár szerkezete a [githubról](https://github.com/Azure/Azure-TDSP-ProjectTemplate)klónozott lehet.

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktúra- és adatelemzési projektek források  

Tdsp-vel megosztott analytics és a tárolási infrastruktúra kezelése ajánlásokat:

- felhőbeli fájlrendszerek adatkészletek tárolására. 
- adatbázisok
- a big data (Hadoop és Spark) fürtök 
- Machine learning szolgáltatás 

Az elemzési és tárolási infrastruktúrát, ahol a nyers és a feldolgozott adatkészletek tárolva vannak, a felhőben vagy a helyszínen lehet. Ez az infrastruktúra lehetővé teszi, hogy reprodukálható elemzése. Azt is elkerülheti duplikációt tartalmaz, ami inkonzisztens állapotát és infrastruktúrával kapcsolatos felesleges költségeket. Eszközök állnak rendelkezésre a megosztott erőforrások kiépítéséhez, azok nyomon követésére és ezen erőforrások biztonságos kapcsolatot minden egyes csapattag engedélyezése. Emellett tanácsos projekt tagjai konzisztens számítási környezet létrehozásához. Másik csapattag ezután replikálja, és kísérletek ellenőrzése.

Íme egy példa egy csoport több projekten dolgozik, és a megosztási különböző felhőalapú elemzési infrastruktúra-összetevőket.

[![TDSP – infrastruktúra](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Eszközöket és segédprogramokat tehet projekt végrehajtása

A szervezetek folyamatok nagyobb kihívást jelent. Megvalósítása adatok adatelemzési folyamat- és életciklus segítségével csökkentheti az akadályok és azok elfogadását konzisztenciájának biztosított eszközökkel. TDSP-eszközök és parancsfájlok TDSP a bevezetést a csapat gyorsan elindíthatja a kezdeti készletét nyújtja. Emellett elősegíti a adattudományi életciklus adatáttekintés és modellezés alapkonfiguráció például a gyakran előforduló feladatok némelyikének automatizálását. Egy jól definiált struktúra van megadva, hogy a felhasználók közösen használják a megosztott eszközöket és segédprogramokat a csapatuk megosztott kódjának tárházában. A csapata vagy a szervezeten belül más projektek majd is javítható ezeket az erőforrásokat. Tdsp-vel is tervek eszközök és segédprogramok használatával a teljes közösségi hozzájárulások engedélyezéséhez. A TDSP segédprogramok klónozása a [githubról](https://github.com/Azure/Azure-TDSP-Utilities)lehetséges.


## <a name="next-steps"></a>Következő lépések

[Csoportos adatelemzési folyamat: szerepkörök és feladatok](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Felvázolja a kulcsfontosságú személyzeti szerepköröket és azokhoz kapcsolódó feladatait egy adatelemzési csapat számára, amely egységesíti a folyamatot. 
