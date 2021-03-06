---
title: Bővített biztonságos pontszám (előzetes verzió) Azure Security Center
description: A fokozottan biztonságos pontszám (előzetes verzió) és a biztonsági vezérlők leírása Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2019
ms.author: memildin
ms.openlocfilehash: ad0d8e751e79a23b2e7ac7b9da934f141ca9aa55
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086532"
---
# <a name="the-enhanced-secure-score-preview"></a>A fokozottan biztonságos pontszám (előzetes verzió) 

Ez a cikk bemutatja a bővített biztonságos pontszámot (jelenleg előzetes verzióban), a kapcsolódó biztonsági ellenőrzéseket és az általuk nyújtott előnyöket. Emellett a pontszám kiszámításának módját is ismerteti.

## <a name="introduction-to-secure-score"></a>A biztonságos pontszám bemutatása

Azure Security Center két fő célja van: az aktuális biztonsági helyzet megértéséhez, valamint a biztonság hatékony és hatékony fejlesztéséhez. A Security Center központi aspektusa, amely lehetővé teszi a célok elérését biztonságos Pontszámként.

Security Center folyamatosan felméri az erőforrásokat, az előfizetéseket és a szervezetet a biztonsági problémákra. Ezután összesíti az összes megállapítást egyetlen pontszámba, így eldöntheti, hogy az aktuális biztonsági helyzet: minél magasabb a pontszám, annál alacsonyabb az azonosított kockázati szint. A pontszám segítségével nyomon követheti a szervezet biztonsági erőfeszítéseit és projektjeit. 

A *fokozottan* biztonságos pontszám (jelenleg előzetes verzióban érhető el) a **támadási felületek középpontjában** áll, és három előnnyel jár:

- **Biztonsági vezérlők** – a biztonsági javaslatok mostantól olyan logikai készletekbe vannak csoportosítva, amelyek jobban tükrözik a sebezhető támadási felületet. További információ: [a biztonságos pontszám kiszámítása](secure-score-security-controls.md#how-the-secure-score-is-calculated) alább.

- A **teljes pontszám jobban tükrözi az általános testtartási** pontokat, melyeket a javaslat szintjén ítéltek oda. Ezzel a fejlesztéssel a pontszám csak akkor fog javulni, ha egy vezérlőelemen belül egy adott erőforráshoz tartozó *összes* javaslatot szervizeli. Ez azt jelenti, hogy a pontszám csak akkor nő, ha az erőforrások biztonsága javul. 

- Az **egyes támadási felületek biztonsági állapota láthatóbbá** válik – a biztonsági ellenőrzés pontszámának bemutatásával a biztonságos pontszám oldal lesz az a hely, ahol részletesen megtekintheti, hogy a szervezet hogyan védi az egyes támadási felületeket.

A bővített biztonsági pontszám százalékként jelenik meg, ahogy az alábbi képernyőképen is látható:

[![a fokozottan biztonságos pontszám (előzetes verzió) már tartalmaz egy százalékot](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)


## <a name="locating-your-secure-score"></a>A biztonságos pontszám megkeresése

Security Center megjeleníti a pontszámot kiemelten: ez az első dolog, ami az Áttekintés oldalon látható. Ha a a dedikált biztonságos pontszám oldalra kattint, a pontszám az előfizetés alapján lebontva jelenik meg. Kattintson egy előfizetésre, és tekintse meg a rangsorolt javaslatok részletes listáját, valamint azt, hogy milyen hatással lehet a szervizelését az előfizetés pontszámára.

## <a name="how-the-secure-score-is-calculated"></a>A biztonságos pontszám kiszámításának módja 

Az előzetes verzió előtt az egyes ajánlásokat egyenként Security Center tekinteni, és a súlyossága alapján értéket rendelt hozzájuk. A biztonsági helyzet javítására szolgáló biztonsági csapatoknak a találatok teljes listája alapján rangsorolni kellett a Security Center ajánlásaira adott válaszokat. Minden alkalommal, amikor egy adott erőforrásra vonatkozó javaslatot szervizelt, a biztonságos pontszám javult.

A biztonságos pontszám fejlesztésének részeként a javaslatok mostantól **biztonsági vezérlőkbe**vannak csoportosítva. A vezérlők a biztonsági javaslatok és a javaslatok megvalósítását segítő útmutatások. A vezérlőelemek a kapcsolódó javaslatok logikai csoportjai. A pontokat a javaslat szintjén már nem nyújtják. Ehelyett a pontszám csak akkor fog javulni, ha a vezérlőn belül egy adott erőforráshoz tartozó *összes* javaslatot szervizeli.

Az egyes biztonsági ellenőrzéseknek az általános biztonsági pontszámhoz való hozzájárulása egyértelműen a javaslatok oldalon látható.

[![a fokozottan biztonságos pontszám (előzetes verzió) bevezeti a biztonsági vezérlőket](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

A biztonsági ellenőrzés összes lehetséges pontjának lekéréséhez minden erőforrásnak meg kell felelnie a biztonsági ellenőrzésen belüli összes biztonsági javaslatnak. Security Center például több javaslattal is rendelkezik a felügyeleti portok biztonságossá tételével kapcsolatban. A múltban a kapcsolódó és az egymástól függő ajánlások némelyikét orvosolhatja, miközben mások megoldatlanul maradnak, és a biztonságos pontszám javulni fog. A tárgyilagosan megvizsgálva egyszerűen azt is megteheti, hogy a biztonság még nem javult, amíg meg nem oldotta őket. Most pedig az összeset ki kell javítania, hogy különbséget tegyen a biztonságos pontszámával.

Például a "rendszerfrissítések alkalmazása" nevű biztonsági vezérlő legfeljebb hat ponttal rendelkezik, amelyet az elemleírásban láthat a vezérlő lehetséges növelési értékeként:

[a "rendszerfrissítések alkalmazása" biztonsági vezérlő ![](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

A "System updates" (rendszerfrissítések alkalmazása) lehetőség a fenti képernyőképen a "2% (1 pont)" értékre mutat. Ez azt jelenti, hogy ha szervizelni kívánja az összes javaslatot, a pontszám 2%-kal (ebben az esetben egy pont) fog növekedni. Az egyszerűség kedvéért a javaslatok listájának "lehetséges növekedés" oszlopában szereplő értékek egész számra vannak kerekítve. Az elemleírások a pontos értékeket mutatják:

* Maximális **pontszám** – az összes, a vezérlőn belüli javaslat végrehajtásával elnyerhető pontok maximális száma. Egy vezérlőelem maximális pontszáma a vezérlőelem relatív jelentőségét jelzi. A maximális pontszám értékekkel osztályozhatja, hogy mely problémákkal kell először dolgozni. 
* **Lehetséges növekedés** – a vezérlőn belül elérhető további pontok. Ha ezeket a pontokat hozzá szeretné adni a biztonságos pontszámhoz, javítsa az összes vezérlő javaslatát. A fenti példában a vezérlőhöz megjelenített egy pont valójában 0,96 pont.
* **Aktuális pontszám** – a vezérlő aktuális pontszáma. Az egyes vezérlőelemek a teljes pontszám irányába járulnak hozzá. Ebben a példában a vezérlő a 5,04-as számmal járul hozzá. 

### <a name="calculations---understanding-your-score"></a>Számítások – a pontszám megismerése

|Metrika|Képlet és példa|
|-|-|
|**Biztonsági vezérlő aktuális pontszáma**|<br>![egyenlet a biztonsági vezérlő aktuális pontszámának kiszámításához](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Minden egyes biztonsági ellenőrzés hozzájárul a biztonsági pontszám eléréséhez. A vezérlőn belüli javaslat által érintett összes erőforrás a vezérlő aktuális pontszámának irányába járul hozzá. Az egyes vezérlők aktuális pontszáma *a vezérlőben lévő erőforrások* állapotának mértéke.<br>![elemleírások, amelyek a biztonsági vezérlő aktuális pontszámának kiszámításakor használt értékeket mutatják](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Ebben a példában a 6 maximális pontszáma 78-re osztható, mert ez az egészséges és a nem kifogástalan erőforrások összege.<br>6/78 = 0,0769<br>A jelenlegi pontszám a (4) kifogástalan állapotú erőforrások számával való szorzását eredményezi:<br>0,0769 * 4 = **0,31**<br><br>|
|**Biztonsági pontszám**<br>Egyetlen előfizetés|<br>![A jelenlegi biztonságos pontszám kiszámításához használt egyenlet](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Egyszeri előfizetés biztonságos pontszáma minden engedélyezett vezérlővel](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Ebben a példában egyetlen előfizetés van az összes rendelkezésre álló biztonsági vezérlővel (a 60-pontok lehetséges maximális pontszáma). A pontszám 28 pontot mutat a lehetséges 60-ből, a fennmaradó 32 pont pedig a biztonsági vezérlők "lehetséges pontszám növelésének" számadatait tükrözi.<br>![A vezérlőelemek listája és a lehetséges pontszám növekedése](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Biztonsági pontszám**<br>Több előfizetés|<br>Az összes előfizetés összes erőforrásának aktuális pontszáma hozzáadva, a számítás pedig azonos, mint egyetlen előfizetés esetén.<br><br>Több előfizetés megtekintésekor a biztonságos pontszám kiértékeli az összes engedélyezett szabályzaton belüli összes erőforrást, és a biztonsági vezérlők maximális pontszámára vonatkozó együttes hatásukat.<br>![biztonságos pontszám több előfizetéshez az összes engedélyezett vezérlővel](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Az összesített pontszám **nem** átlag; Ehelyett az összes előfizetés összes erőforrása állapotának kiértékelt testtartása.<br>Itt is, ha a javaslatok lapra lép, és hozzáadja az elérhető lehetséges pontokat, akkor a jelenlegi pontszám (24) és a rendelkezésre álló maximális pontszám (60) közötti különbség jelenik meg.|
||||

## <a name="improving-your-secure-score"></a>A biztonságos pontszám javítása

A biztonságos pontszám javítása érdekében javítsa a javaslatok listáját a biztonsági javaslatok kijavításával. Az egyes javaslatokat minden erőforráshoz manuálisan, vagy a **gyors javítás** használatával javíthatja. lehetőség (ha elérhető) egy, az erőforrások egy csoportjára vonatkozó javaslatra vonatkozó szervizelés alkalmazásához. További információ: [javaslatok szervizelése](security-center-remediate-recommendations.md).

Csak a beépített javaslatok befolyásolhatják a biztonságos pontszámot.

## <a name="security-controls-and-their-recommendations"></a>Biztonsági vezérlők és javaslataik

Az alábbi táblázat a Azure Security Center biztonsági vezérlőit sorolja fel. Az egyes vezérlőknél megtekintheti a biztonságos pontszámhoz adható pontok maximális számát, ha a vezérlőben felsorolt *összes* javaslatot kijavítja az *összes* erőforráshoz. 

> [!TIP]
> Ha másképp szeretné szűrni vagy rendezni a listát, másolja és illessze be az Excelbe.

|Biztonsági ellenőrzés|Maximális biztonságos pontszám pontok|Javaslatok|
|----------------|:-------------------:|---------------|
|**MFA engedélyezése**|10|– Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon<br>-Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon<br>– Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak|
|**Biztonságos felügyeleti portok**|8|-Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken<br>– A virtuális gépeket hálózati biztonsági csoporttal kell társítani<br>– A felügyeleti portokat be kell zárni a virtuális gépeken|
|**Rendszerfrissítések alkalmazása**|6|– A figyelési ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken<br>– A figyelési ügynököt a virtuálisgép-méretezési csoportokra kell telepíteni<br>– A figyelési ügynököt telepíteni kell a gépekre<br>– Az operációs rendszer verziószámát frissíteni kell a Cloud Service szerepköreihez<br>– A virtuálisgép-méretezési csoportokban lévő rendszerfrissítéseket telepíteni kell<br>– A rendszer-frissítéseket telepíteni kell a gépekre<br>– A gépeket újra kell indítani a rendszerfrissítések alkalmazásához<br>– A Kubernetes-szolgáltatásokat nem sebezhető Kubernetes-verzióra kell frissíteni<br>– A figyelési ügynököt telepíteni kell a virtuális gépekre|
|**Sebezhetőségek javítása**|6|– Az SQL-kiszolgálókon engedélyezni kell a speciális adatbiztonságot<br>– A Azure Container Registry lemezképekben található biztonsági réseket szervizelni kell (előzetes verzió)<br>– Az SQL-adatbázisok biztonsági réseit szervizelni kell<br>– A sebezhetőségeket a sebezhetőségi felmérési megoldással kell szervizelni<br>– Az SQL felügyelt példányain engedélyezni kell a sebezhetőségi felmérést<br>– Az SQL-kiszolgálókon engedélyezni kell a sebezhetőségi felmérést<br>– A sebezhetőség-felmérési megoldásnak telepítve kell lennie a virtuális gépeken|
|**Titkosítás engedélyezése nyugalmi állapotban**|4|– A lemezes titkosítást a virtuális gépeken kell alkalmazni<br>-Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás<br>– Az Automation-fiók változóit titkosítani kell<br>– Service Fabric fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani<br>-Az SQL Server TDE-védőt a saját kulccsal kell titkosítani|
|**Adatforgalom titkosítása**|4|– Az API-alkalmazás csak HTTPS-kapcsolaton keresztül érhető el<br>– Függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el<br>– Csak a Redis Cache biztonságos kapcsolatait kell engedélyezni<br>– Engedélyezze a biztonságos átvitelt a Storage-fiókoknak.<br>– A webalkalmazásnak csak HTTPS protokollon keresztül kell elérhetőnek lennie|
|**Hozzáférés és engedélyek kezelése**|4|– Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni<br>-Az elavult fiókokat el kell távolítani az előfizetésből<br>– A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből<br>– A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből<br>– Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből<br>– Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből<br>– Az előfizetéshez több tulajdonost kell hozzárendelni<br>-Szerepköralapú Access Control (RBAC) használata szükséges a Kubernetes-szolgáltatásokban (előzetes verzió)<br>– Service Fabric-fürtöknek csak Azure Active Directory kell használnia az ügyfél-hitelesítéshez|
|**Biztonsági konfigurációk javítása**|4|-Pod biztonsági házirendeket kell meghatározni a Kubernetes-szolgáltatásokban (előzetes verzió)<br>– A tároló biztonsági konfigurációinak javításait orvosolni kell<br>– A gépek biztonsági beállításainak javításait szervizelni kell<br>– A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell<br>– A figyelési ügynököt telepíteni kell a virtuális gépekre<br>– A figyelési ügynököt telepíteni kell a gépekre<br>– A figyelési ügynököt a virtuálisgép-méretezési csoportokra kell telepíteni<br>– A figyelési ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken|
|**Jogosulatlan hálózati hozzáférés korlátozása**|4|-Az IP-továbbítást a virtuális gépen le kell tiltani<br>-A Kubernetes-szolgáltatásokban (előzetes verzió) meg kell határozni a jóváhagyott IP-tartományokat.<br>-(Elavult) App Services hozzáférését korlátozni kell (előzetes verzió)<br>-(Elavult) a IaaS NSG webalkalmazásaira vonatkozó szabályokat meg kell erősíteni<br>– A virtuális gépeket hálózati biztonsági csoporttal kell társítani<br>– A CORS nem teszi lehetővé minden erőforrás számára az API-alkalmazás elérését<br>– A CORS nem teszi lehetővé minden erőforrás számára a függvényalkalmazás elérését<br>– A CORS nem teszi lehetővé minden erőforrás számára a webalkalmazás elérését<br>– A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz<br>– A távoli hibakeresést ki kell kapcsolni függvényalkalmazás<br>– A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz<br>– A hozzáférésnek korlátozva kell lennie az internetre irányuló virtuális gépekkel rendelkező, engedékeny hálózati biztonsági csoportoknak<br>– Az internetre irányuló virtuális gépek hálózati biztonsági csoportjának szabályait meg kell szigorítani|
|**Adaptív alkalmazások vezérlésének alkalmazása**|3|-Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken<br>– A figyelési ügynököt telepíteni kell a virtuális gépekre<br>– A figyelési ügynököt telepíteni kell a gépekre<br>– A figyelési ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken|
|**Az adatbesorolás alkalmazása**|2|– Az SQL-adatbázisok bizalmas adatait be kell sorolni (előzetes verzió)|
|**Alkalmazások elleni védelem a DDoS-támadások ellen**|2|-DDoS Protection szabványt engedélyezni kell|
|**Endpoint Protection engedélyezése**|2|– Az Endpoint Protection állapotával kapcsolatos hibák elhárítása a virtuálisgép-méretezési csoportokban<br>– Az Endpoint Protection állapotával kapcsolatos problémákat fel kell oldani a gépeken<br>-Endpoint Protection-megoldást kell telepíteni a virtuálisgép-méretezési csoportokra<br>-Endpoint Protection-megoldás telepítése virtuális gépeken<br>– A figyelési ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken<br>– A figyelési ügynököt a virtuálisgép-méretezési csoportokra kell telepíteni<br>– A figyelési ügynököt telepíteni kell a gépekre<br>– A figyelési ügynököt telepíteni kell a virtuális gépekre<br>-Az Endpoint Protection megoldás telepítése a gépekre|
|**Naplózás és naplózás engedélyezése**|1|– Az SQL Server naplózását engedélyezni kell<br>– A App Services diagnosztikai naplóit engedélyezni kell<br>– A Azure Data Lake Store diagnosztikai naplóit engedélyezni kell<br>– A Azure Stream Analytics diagnosztikai naplóit engedélyezni kell<br>– A Batch-fiókokban engedélyezni kell a diagnosztikai naplókat.<br>– A Data Lake Analytics diagnosztikai naplóit engedélyezni kell<br>– Az Event hub diagnosztikai naplóit engedélyezni kell<br>– A IoT Hub diagnosztikai naplóit engedélyezni kell<br>– A Key Vault diagnosztikai naplóit engedélyezni kell<br>– A Logic Apps diagnosztikai naplóit engedélyezni kell<br>– A keresési szolgáltatásban engedélyezni kell a diagnosztikai naplókat.<br>– A Service Bus diagnosztikai naplóit engedélyezni kell<br>– A Virtual Machine Scale Sets diagnosztikai naplóit engedélyezni kell<br>-Metrikus riasztási szabályokat kell konfigurálni a Batch-fiókokon<br>– Az SQL-naplózási beállításoknak a kritikus tevékenységek rögzítésére konfigurált műveleti csoportokkal kell rendelkezniük<br>– Az SQL-kiszolgálókat az 90 napnál hosszabb naplózási megőrzési napokon kell konfigurálni.|
|**Ajánlott biztonsági eljárások megvalósítása**|0|– A tűzfal-és virtuális hálózati konfigurációval rendelkező Storage-fiókok hozzáférését korlátozni kell<br>– Az RootManageSharedAccessKey kivételével minden engedélyezési szabályt el kell távolítani az Event hub-névtérből<br>– Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni<br>– Meg kell határozni az Event hub-példány engedélyezési szabályait<br>– A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra<br>– A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra<br>– Az SQL Server speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához<br>– A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon<br>– Az összetett veszélyforrások elleni védelem összes típusát engedélyezni kell az SQL felügyelt példány speciális biztonsági beállításainál<br>– A rendszergazdák és az előfizetések tulajdonosainak szóló e-mail-értesítések engedélyezése az SQL Server speciális biztonsági beállításaiban<br>– A komplex veszélyforrások elleni védelem típusait "all" értékre kell állítani az SQL Server speciális biztonsági beállításaiban<br>– Az alhálózatokat hálózati biztonsági csoporttal kell társítani<br>– Az összetett veszélyforrások elleni védelem összes típusát engedélyezni kell az SQL Server speciális biztonsági beállításaiban|
||||

## <a name="secure-score-faq"></a>Biztonságos pontszám – gyakori kérdések

### <a name="why-has-my-secure-score-gone-down"></a>Miért esett le a biztonságos pontszám?
Az ebben a bővített biztonsági pontszámban bevezetett változásokkal az erőforrásokra vonatkozó összes javaslatot meg kell oldania a pontok fogadására. A pontszámok a 0-10-es méretre is módosultak.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Ha egy biztonsági ellenőrzésben csak három négy javaslat közül hármat találok, akkor a biztonságos Pontszámom módosul?
Nem Ez addig nem változik meg, amíg az egyetlen erőforráshoz tartozó összes ajánlást nem szervizeli. Egy vezérlőelem maximális pontszámának lekéréséhez az összes erőforrásra vonatkozóan az összes javaslatot szervizelni kell.

### <a name="will-this-enhanced-secure-score-replace-the-existing-secure-score"></a>A fokozottan biztonságos pontszám a meglévő biztonságos pontszám helyébe lép? 
Igen, de egy ideig, hogy az átmenet megkönnyítése érdekében egymás mellett futnak.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Ha egy javaslat nem alkalmazható a számomra, és letiltom a szabályzatban, akkor a biztonsági ellenőrzésem teljesül, és a biztonságos pontszám frissítve lett?
Igen. Javasoljuk, hogy tiltsa le a javaslatokat, ha azok nem alkalmazhatók a környezetben. A konkrét javaslatok letiltásával kapcsolatos utasításokért lásd: [biztonsági szabályzatok letiltása](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Ha egy biztonsági vezérlő nulla pontot biztosít a biztonságos pontszám felé, figyelmen kívül hagyhatom?
Bizonyos esetekben a vezérlőelem maximális pontszáma nullánál nagyobb lesz, de a hatás nulla. Ha az erőforrások kijavításának növekményes pontszáma elhanyagolható, a rendszer nulla értékűre kerekíti. Ne hagyja figyelmen kívül ezeket az ajánlásokat, mivel azok továbbra is biztonsági javítást tesznek lehetővé. Az egyetlen kivétel a "további ajánlott eljárás" vezérlő. Szervizelését ezekkel az ajánlásokkal nem növeli a pontszámát, de a teljes biztonságot is növeli.

## <a name="next-steps"></a>Következő lépések

Ez a cikk a fokozottan biztonságos pontszámot és az új biztonsági ellenőrzéseket ismerteti. A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket:

- [Tudnivalók a javaslatok különböző elemeiről](security-center-recommendations.md)
- [Útmutató a javaslatok megoldásához](security-center-remediate-recommendations.md)