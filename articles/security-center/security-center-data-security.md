---
title: Az Azure Security Center által nyújtott adatbiztonság | Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik a védelmükről.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: a25bbd0f14d38a70624dbc58755c0e814753a181
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604178"
---
# <a name="azure-security-center-data-security"></a>Az Azure Security Center által nyújtott adatbiztonság
Az Azure Security Center biztonsággal kapcsolatos adatokat gyűjt és dolgoz fel (például konfigurációs információkat, metaadatokat, eseménynaplókat, összeomlási memóriaképeket és még sok mást), hogy segítsen az ügyfeleknek a fenyegetések megelőzésében, észlelésében és elhárításában. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig.

Ez a cikk bemutatja, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik a védelmükről.

## <a name="data-sources"></a>Adatforrások
Az Azure Security Center az alábbi forrásokból kapott adatok elemzésével biztosítja a biztonsági állapot áttekinthetőségét, azonosítja a biztonsági réseket, javításokat javasolj, valamint észleli az aktív fenyegetéseket:

- Azure Services: az adott erőforrás-szolgáltatóval kommunikálva üzembe helyezett Azure-szolgáltatások konfigurációjával kapcsolatos információkat használja.
- Hálózati forgalom: a hálózati forgalom metaadataiból vett mintát használja a Microsoft infrastruktúrájából, például a forrás és a cél IP-címét és portját, a csomagméretet és a hálózati protokollt.
- Partnermegoldások: Az integrált partnermegoldásoktól, például tűzfalaktól és kártevőirtó megoldásoktól érkező biztonsági riasztásokat használja.
- Saját virtuális gépek és kiszolgálók: Konfigurációs és biztonsági eseményekkel kapcsolatos információkat használ (például Windows-esemény- és auditnaplókat, IIS-naplókat, rendszernapló-üzeneteket és a virtuális gépekről származó összeomlási memóriaképeket). Ezenkívül a riasztások létrejöttekor az Azure Security Center pillanatfelvételt készíthet az érintett virtuálisgép-lemezről, és kinyerheti belőle a riasztással kapcsolatos gépösszetevőket (például a beállításjegyzék-fájlt vizsgálati célokra).


## <a name="data-protection"></a>Adatvédelem
**Az adatok elkülönítése**: az adatok logikailag elkülönítve vannak tárolva a szolgáltatás egyes összetevőiben. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes.

**Adathozzáférés**: Annak érdekében, hogy biztonsági javaslatokat biztosítsanak és megvizsgálják a lehetséges biztonsági fenyegetéseket, a Microsoft munkatársai hozzáférhetnek az Azure-szolgáltatások által gyűjtött vagy elemzett adatokhoz (például összeomlási memóriaképekhez, folyamat-létrehozási eseményekhez, a virtuálisgép-lemez pillanatképeihez és összetevőihez, amelyek véletlenül tartalmazhatnak virtuális gépekről származó ügyfél- és személyes adatokat). Betartjuk a [Microsoft Online Services és az adatvédelmi nyilatkozat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) feltételeit, amelyek szerint a Microsoft nem használja fel az ügyféladatokat, és nem veszi igénybe azokat hirdetési vagy hasonló kereskedelmi célokra. Az ügyféladatokat szükség esetén csak arra használjuk, hogy biztosítsuk Önnek az Azure-szolgáltatásokat, beleértve a szolgáltatások nyújtásának megfelelő célokat is. Minden, az ügyféladatokhoz fűződő jog az Ön tulajdonában marad.

**Az adatok felhasználása**: a Microsoft a különböző bérlőknél észlelt mintákat és fenyegetésre vonatkozó intelligenciát használ a megelőzési és észlelési funkcióihoz, és ezt az [adatvédelmi nyilatkozatában](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx) ismertetett adatvédelmi kötelezettségeinek megfelelően teszi.

## <a name="data-location"></a>Az adatok helye

**Saját munkaterület(ek)** : Az alábbi geók esetében van meghatározott munkaterület. Az Azure-beli virtuális gépekből összegyűjtött adatokat, beleértve az összeomlási memóriaképeket és bizonyos típusú riasztási adatokat, a legközelebbi munkaterület tárolja.

| Virtuális gép geo                              | Munkaterület geo |
|-------------------------------------|---------------|
| Egyesült Államok, Brazília, Dél-Afrika | Egyesült Államok |
| Kanada                              | Kanada        |
| Európa (kivéve az Egyesült Királyságot)   | Európa        |
| Egyesült Királyság                      | Egyesült Királyság |
| Ázsia (kivéve India, Japán, Korea, Kína)   | Ázsia és a Csendes-óceáni térség  |
| Dél-Korea                              | Ázsia és a Csendes-óceáni térség  |
| India                               | India         |
| Japán                               | Japán         |
| Kína                               | Kína         |
| Ausztrália                           | Ausztrália     |


A virtuálisgép-lemez pillanatképeit ugyanaz a tárfiók tárolja, mint a virtuálisgép-lemezt.

A más környezetben futó (pl. helyszíni) virtuális gépek és kiszolgálók esetében megadhatja a munkaterületet és régiót, ahol tárolni kívánja az összegyűjtött adatokat.

**Az Azure Security Center tárhelye**: A biztonsági riasztásokkal (többek között a partnerriasztásokkal) kapcsolatos információk tárolása a kapcsolódó Azure-erőforrás helyétől függően történik, míg a biztonsági állapottal és javaslatokkal kapcsolatos információk tárolása központilag, az ügyfél földrajzi helyétől függően az Egyesült Államokban vagy Európában történik.
Az Azure Security Center ideiglenes másolatokat gyűjt az összeomlási memóriaképek fájljairól, és elemzi ezeket a biztonsági réseket kihasználó támadások és a sikeres feltörések nyomai után kutatva. Az Azure Security Center az elemzéseket ugyanazon a földrajzi helyen végzi el, ahol a munkaterület található, és az elemzés után törli az ideiglenes másolatokat.

A gépösszetevők tárolása központilag történik, ugyanabban a régióban, ahol a virtuális gép is található.


## <a name="managing-data-collection-from-virtual-machines"></a>Az adatgyűjtés kezelése a virtuális gépeken

Ha bekapcsolja a Security Centert az Azure-ban, az adatgyűjtés bekapcsolódik minden előfizetésénél. Az előfizetések adatgyűjtését az Azure Security Center Biztonsági szabályzat részén is bekapcsolhatja. Amikor az adatgyűjtés be van kapcsolva, az Azure Security Center létrehozza a Microsoft Monitoring Agentet minden meglévő és újonnan létrehozott Azure-beli támogatott virtuális gépen.
A Microsoft Monitoring Agent megkeresi a biztonsághoz kapcsolódó különböző konfigurációkat, és eseményként felveszi őket a [Windows esemény-nyomkövetés](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) nyomkövetéseibe. Az operációs rendszer emellett az eseménynaplóba írandó eseményeket hoz létre a gép futtatása során. A gyűjtött adatok például a következők: az operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek, a bejelentkezett felhasználó és a bérlő azonosítója. A Microsoft Monitoring Agent kiolvassa az eseménynapló-bejegyzéseket és az ETW-nyomkövetéseket, és átmásolja őket az Ön munkaterületeire elemzés céljából. A Microsoft Monitoring Agent az összeomlási memóriaképeket is átmásolja a munkaterületeire, valamint engedélyezi a folyamat-létrehozási eseményeket és lehetővé teszi a parancssori naplózást.

Ha az Azure Security Center ingyenes verzióját használja, le is tilthatja a virtuális gépekről történő adatgyűjtést a biztonsági szabályzatban. A Standard szintű előfizetésekhez az adatgyűjtés kötelező. A virtuálisgép-lemez pillanatképeinek és összetevőinek gyűjtése akkor is engedélyezve lesz, ha letiltotta az adatgyűjtést.

## <a name="data-consumption"></a>Adathasználat

Az ügyfelek különböző adatstreamekből származó Security Center-adatokat használhatnak, az alábbiak szerint:

* **Azure-tevékenység**: minden biztonsági riasztás, jóváhagyott [Security Center igény](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) szerinti kérelmek, valamint az [adaptív alkalmazások](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)által létrehozott összes riasztás.
* **Azure monitor naplók**: minden biztonsági riasztás.


> [!NOTE]
> A biztonsági javaslatok REST API-n keresztül is feldolgozhatók. További információk: [A biztonsági erőforrás-szolgáltató REST API-ja – referencia](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx).

## <a name="see-also"></a>Lásd még
Ebből a dokumentumból megtudta, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik azok védelméről. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – Az Azure Security Center tervezésével, valamint a bevezetés során fontos elemekkel kapcsolatos útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának megfigyeléséhez.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
