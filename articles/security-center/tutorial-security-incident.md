---
title: Incidens-válasz oktatóanyaga-Azure Security Center
description: Ebből az oktatóanyagból megtudhatja, hogyan osztályozhatja a biztonsági riasztásokat, meghatározhatja a kiváltó okot & az incidensek hatókörét, és megkeresheti a biztonsági információkat.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: memildin
ms.openlocfilehash: 49e2af8fd2d71e1e5103f5337d5c7104c2904322
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559185"
---
# <a name="tutorial-respond-to-security-incidents"></a>Oktatóanyag: Reagálás a biztonsági incidensekre
A Security Center fejlett elemzési eszközök és fenyegetésfelderítés segítségével folyamatosan elemzi a hibrid felhőbeli számítási feladatokat, hogy figyelmeztesse az esetleges rosszindulatú tevékenységekre. Ráadásul a Security Centerbe más biztonsági termékekből és szolgáltatásokból is integrálhat riasztásokat, és egyéni riasztásokat is létrehozhat a saját mutatói és intelligens forrásai alapján. Amikor valami kivált egy riasztást, gyors reagálásra van szükség a problémák kivizsgálásához és elhárításához. Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Biztonsági riasztások osztályozása
> * Részletes vizsgálat egy biztonsági incidens gyökerének és kiterjedésének meghatározása céljából
> * Keresés a biztonsági adatok között a vizsgálat elősegítéséhez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyagban ismertetett funkciók végrehajtásához a Security Center Standard tarifacsomagjával kell rendelkeznie. Security Center Standard díjmentesen is kipróbálható. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). [Az Azure-előfizetés a Security Center Standard verziójába való felvételével](security-center-get-started.md) foglalkozó rövid útmutató végigvezeti azokon a lépéseken, amelyekkel frissíthet a Standard verzióra.

## <a name="scenario"></a>Alkalmazási helyzet
A Contoso vállalat nemrégiben áttelepítette egyes helyszíni erőforrásait az Azure-ba, beleértve egyes virtuális gépeken alapuló üzleti számítási feladatait és SQL-adatbázisait. A Contoso fő számítógépes biztonsági incidensmegoldó csapata, a Core Computer Security Incident Response Team (CSIRT) jelenleg nem tudja kivizsgálni a biztonsági problémákat, mivel a biztonsági intelligencia nem képezi jelenlegi incidensmegoldási eszközparkjuk szerves részét. Az integráció hiánya az észlelési szakasz során (túl sok téves riasztás), valamint a felmérési és a diagnosztikai szakaszok során is problémát jelent. Az áttelepítés részeként úgy döntöttek, hogy a probléma megoldásához igénybe veszik a Security Center segítségét.

Az áttelepítés első szakasza azzal ért véget, hogy az összes erőforrást üzembe helyezték, és a Security Center összes biztonsági ajánlását feldolgozták. A Contoso CSIRT a számítógépes biztonsági incidensek kezelésének központi eleme. A csapatot olyan személyek alkotják, akik biztonsági incidensek kezelésével foglalkoznak. A csapat tagjainak feladatai egyértelműen meg vannak határozva annak érdekében, hogy ne maradjon lefedetlen felelősségi terület.

A forgatókönyv szempontjából a Contoso CSIRT csapatának alábbi két tagjára fogunk összpontosítani:

![Az incidensmegoldás életciklusa](./media/tutorial-security-incident/security-center-incident-response.png)

Judit biztonsági üzemeltetéssel foglalkozik. Feladatai a következők:

* A biztonsági fenyegetések non-stop figyelése és megoldása.
* A problémák továbbítása szükség szerint a felhőbeli számítási feladat tulajdonosának vagy a biztonsági elemzőnek.

A Sam egy biztonsági elemző, és feladatai közé tartoznak a következők:

* A támadások kivizsgálása.
* A riasztások javítása.
* A számítási feladatok tulajdonosaival együttműködve a megoldások meghatározása és kivitelezése.

Amint látható, Judit és Sándor feladatai eltérőek, azonban együtt kell működniük a Security Center adatainak megosztásában.

## <a name="triage-security-alerts"></a>Biztonsági riasztások osztályozása
A Security Center segítségével minden biztonsági riasztást egy egyesített nézetben tekinthet meg. A biztonsági riasztások besorolása a súlyosságon alapul, és ahol lehetséges, a kapcsolódó riasztásokat a rendszer egyetlen biztonsági incidensben egyesíti. A riasztások és események osztályozásakor a következő feladatai vannak:

- További műveleteket nem igénylő riasztások elvetése, ha például a riasztás téves
- Ismert támadások elhárítása, például rosszindulatú IP-címről érkező hálózati forgalom blokkolása
- További vizsgálatot igénylő riasztások meghatározása


1. A Security Center főmenüjének **ÉSZLELÉS** területén válassza a **Biztonsági riasztások** elemet:

   ![Biztonsági riasztások](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)

2. A riasztások listájában kattintson egy biztonsági incidensre (egy riasztásgyűjteményre) az esemény részleteinek megjelenítéséhez. Megnyílik **A rendszer biztonsági incidenst észlelt** ablak.

   ![Biztonsági incidens](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. Ezen a képernyőn felül megtalálja a biztonsági incidens leírását, majd azon riasztások listáját, amelyek az esemény részeit képezik. További információért kattintson az alaposabban megvizsgálni kívánt riasztásra.

   ![Biztonsági incidens](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

   A riasztások típusa eltérő lehet. A riasztási típusokkal és az elhárításhoz szükséges lehetséges lépésekkel kapcsolatban tekintse meg [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) című cikket. A biztonságosan elvethető riasztások esetében jobb gombbal a riasztásra kattinthat, majd kiválaszthatja az **Elvetés** lehetőséget:

   ![Riasztás](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. Ha a hiba gyökere és a rosszindulatú tevékenység hatóköre nem ismert, további vizsgálatért lépjen a következő lépésre.

## <a name="investigate-an-alert-or-incident"></a>Egy riasztás vagy esemény vizsgálata
1. A **Biztonsági riasztás** oldalon kattintson a **Vizsgálat indítása** gombra (ha már megkezdte a vizsgálatot, a gomb neve **Vizsgálat folytatása** lesz).

   ![Vizsgálat](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

   A vizsgálati térkép a biztonsági riasztáshoz vagy eseményhez kapcsolódó entitások grafikus megjelenítése. Ha a térkép egy entitására kattint, az arra vonatkozó információk új entitásokként jelennek meg, és a térkép egyre bővül. A térképen kiválasztott entitás tulajdonságai kiemelve jelennek meg az oldal jobb oldalán található panelen. Az egyes lapokon megjelenő információk a kiválasztott entitástól függően változnak. A vizsgálati folyamat során a támadó lépéseinek jobb megértése érdekében tekintse át az összes kapcsolódó információt.

2. Ha több bizonyítékra van szüksége, vagy további vizsgálatra van szükség a talált entitásokkal kapcsolatban, lépjen a következő lépésre.

## <a name="search-data-for-investigation"></a>Keresés az adatok között a vizsgálathoz

A Security Center keresési képességeinek segítségével további bizonyítékokat kereshet a sérült rendszerekről, valamint további részleteket tárhat fel a vizsgálat tárgyát képező entitásokról.

Keresés indításához nyissa meg a **Security Center** irányítópultot, kattintson a bal oldali navigációs ablaktáblán a **Keresés** elemre, válassza ki a keresni kívánt entitásokat tartalmazó munkaterületet, írja be a keresési lekérdezést, majd kattintson a Keresés gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, továbbra is a Standard szintet futtassa, és hagyja engedélyezve az automatikus kiépítést. Ha nem folytatja a munkát, vagy vissza szeretne térni az Ingyenes szintre:

1. Térjen vissza a Security Center főmenüjébe, és válassza a **Biztonsági szabályzat** elemet.
2. Válassza ki az előfizetést vagy a szabályzatot, amelyet vissza szeretne állítani Ingyenes állapotba. Megnyílik a **Biztonsági szabályzat** képernyő.
3. A **SZABÁLYZAT ÖSSZETEVŐI** alatt válassza a **Tarifacsomag** lehetőséget.
4. Válassza az **Ingyenes** lehetőséget az előfizetés Standard szintről Ingyenes szintre való módosításához.
5. Kattintson a **Mentés** gombra.

Ha le szeretné tiltani az automatikus kiépítést:

1. Térjen vissza a Security Center főmenüjébe, és válassza a **Biztonsági szabályzat** elemet.
2. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést.
3. Az automatikus kiépítés letiltásához a **Biztonsági szabályzat – Adatgyűjtés** területen válassza a **Ki** lehetőséget az **Előkészítés** elemnél.
4. Kattintson a **Mentés** gombra.

>[!NOTE]
> Az automatikus kiépítés letiltása nem távolítja el a Microsoft Monitoring Agentet azon Azure-beli virtuális gépekről, ahol az ügynök üzembe lett helyezve. Az automatikus kiépítés letiltása korlátozza az erőforrások biztonsági monitorozását.
>

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban a Security Center azon funkcióit ismerte meg, amelyeket a rendszer egy biztonsági incidensre adott reakció során használ, például:

> [!div class="checklist"]
> * Biztonsági incidens, amely egy erőforráshoz tartozó összes riasztás együttese
> * Vizsgálati térkép, amely a biztonsági riasztáshoz vagy eseményhez kapcsolt entitások grafikus megjelenítése
> * Keresési képességek a sérült rendszerekre vonatkozó további bizonyítékok kereséséhez

További információk a Security Center vizsgálati funkciójáról:

> [!div class="nextstepaction"]
> [Incidensek és riasztások vizsgálata](security-center-investigation.md)
