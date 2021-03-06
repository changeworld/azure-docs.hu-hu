---
title: Biztonsági riasztások a Azure Security Centerban | Microsoft Docs
description: Ez a témakör ismerteti a biztonsági riasztásokat, valamint a Azure Security Centerban elérhető különböző típusokat.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 514de1435519282335124bfd67bac82669240b78
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245511"
---
# <a name="security-alerts-in-azure-security-center"></a>Biztonsági riasztások az Azure Security Centerben

A Azure Security Center számos különböző típusú erőforráshoz biztosít riasztásokat. A Security Center riasztásokat hoz létre az Azure-ban üzembe helyezett erőforrásokról, valamint a helyszíni és hibrid felhőalapú környezetekben üzembe helyezett erőforrásokról is.

A biztonsági riasztásokat a speciális észlelések váltották ki, és csak a standard szintű Azure Security Center érhetők el. Ennek létezik egy ingyenesen elérhető próbaverziója. A választott tarifacsomagról a [Biztonsági szabályzat](security-center-pricing.md) beállításainál frissíthet. A díjszabással kapcsolatos további tudnivalókért keresse fel a [Security Center oldalát](https://azure.microsoft.com/pricing/details/security-center/).

## Válaszadás a mai fenyegetésekre <a name="respond-threats"></a>

Az elmúlt 20 évben jelentős változásokat figyelhettünk meg a fenyegetések területén. Korábban a vállalatoknak általában csak a webhelyük arculatának lerombolása miatt kellett aggódniuk, mert a támadókat főleg az érdekelte, hogy kipróbálják, „mit is lehetne tenni”. A mai támadók sokkal bonyolultabban és szervezettebben cselekszenek. Gyakran konkrét pénzügyi és stratégiai célokat követnek. Ezenkívül több erőforrás áll a rendelkezésükre, mert akár az is előfordulhat, hogy egy nemzetállam vagy a szervezett bűnözés biztosítja számukra az anyagi hátteret.

Ezek a változó realitások a támadó soraiban példátlan szintű professzionalizmust eredményeztek. Már nem a webhelyek megrongálása érdekli őket. Most már érdeklik az információk, a pénzügyi fiókok és a személyes adatok ellopása, amelyek segítségével készpénzt hozhatnak a nyílt piacon, vagy egy adott üzleti, politikai vagy katonai pozíciót is kihasználhatnak. Még nagyobb aggodalomra ad okot, hogy a pénzügyi célokat követő támadókon kívül olyanokkal is számolni kell, akik azért törnek be a hálózatokba, hogy kárt okozzanak az infrastruktúrában és a felhasználóknak.

Erre reagálva a szervezetek gyakran olyan adott pontokon működő megoldásokat alkalmaznak, amelyek a vállalat peremsávját vagy végpontjait védelmezik a támadások ismert jelei után kutatva. Ezek a megoldások általában nagy mennyiségű, alacsony megbízhatósági szintű riasztást eredményeznek, és ezeket a biztonsági elemzőknek szét kell válogatniuk és ki kell vizsgálniuk. A legtöbb szervezetnek nincs ideje és megfelelő szaktudása ahhoz, hogy reagáljon ezekre a riasztásokra, ezért sok probléma megoldatlan marad.  

Emellett a támadók kifejlődték a módszereiket számos aláírás-alapú védelem kialakulásához, és [alkalmazkodnak a felhőalapú környezetekhez](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Az új módszerek a fenyegetések egyre bővülő körének gyorsabb felismerését, azonnali észlelését és kiiktatását igénylik.

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?

A riasztások a Security Center által generált értesítések, amikor észlelik a fenyegetéseket az erőforrásokon. A Security Center rangsorolja és felsorolja a riasztásokat, valamint azokat az információkat, amelyek a probléma gyors kivizsgálásához szükségesek. A Security Center javaslatokat is tartalmaz a támadások megoldásához.

## Hogyan ismeri fel Security Center a fenyegetéseket? <a name="detect-threats"> </a>

A Microsoft biztonsági kutatói folyamatosan figyelik a megjelenő fenyegetéseket. A Microsoft globális jelenléte a felhőben és a helyszínen egyaránt a telemetria expanzív készletéhez férhet hozzá. Az adatkészletek széles körű és változatos gyűjteménye lehetővé teszi, hogy az új támadási mintákat és trendeket a helyszíni fogyasztói és nagyvállalati termékekben, valamint a online szolgáltatásokon is felfedezzék. Ennek eredményeképpen a Security Center gyorsan tudja frissíteni az észlelési algoritmusait, igazodva a támadók újabb és egyre összetettebb biztonságirés-kihasználási megoldásaihoz. Ez a módszer segít lépést tartani a fenyegetések gyors ütemben növekvő körével.

A valós fenyegetések észlelése és a téves pozitív állapotok csökkentése érdekében Security Center gyűjti, elemzi és integrálja az Azure-erőforrások és a hálózat naplófájljait. Együttműködik továbbá a csatlakoztatott partneri megoldásokkal, például a tűzfal-és Endpoint Protection-megoldásokkal. Security Center elemzi ezeket az információkat, és gyakran korrelál több forrásból származó információkat a fenyegetések azonosítása érdekében.

![A Security Center adatgyűjtése és adatábrázolása](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. A big data és a [gépi tanulási](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiák áttörést jelentő eredményeinek felhasználásával értékelhetők ki az események a teljes felhőalapú hálóban, és így olyan fenyegetések is észlelhetők, amelyeket manuális módszerekkel lehetetlen volna azonosítani, és lehetőség van a támadások fejlődésének előrejelzésére. Ezek a biztonsági elemzések a következők:

* **Integrált veszélyforrások felderítése**: a Microsoft-termékek és-szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft Security Response Center (MSRC) és a külső hírcsatornák globális fenyegetési intelligenciának kihasználásával az ismert hibás szereplőkre keres.
* **Viselkedési elemzés**: az ismert mintákat alkalmazza a kártékony viselkedés felderítésére.
* **Anomáliák észlelése**: statisztikai profilkészítést használ egy korábbi alapterv létrehozásához. Jelzi a meghatározott alapkonfigurációktól való olyan eltéréseket, amelyek potenciális támadásként értelmezhetők.

Az alábbi részek részletesebben tárgyalják ezeket az elemzéseket.

### <a name="integrated-threat-intelligence"></a>Integrált veszélyforrások felderítése

A Microsoft rendkívül nagy mennyiségű adattal rendelkezik a globális fenyegetésészlelési intelligencia keretein belül. A telemetria több forrásból, például az Azure-ból, az Office 365-ból, a Microsoft CRM Online-ból, a Microsoft Dynamics AX-ből, a outlook.com, a MSN.com, a Microsoft Digital Crimes Unit (DCU) és a Microsoft Security Response Center (MSRC) szolgáltatásból zajlik. A kutatók emellett a jelentős felhőalapú szolgáltatók és más harmadik féltől származó adatcsatornák között megosztott veszélyforrások felderítésére vonatkozó információkat is kapnak. Az Azure Security Center mindezeket az adatokat fel tudja használni arra, hogy értesítse Önt az ismert kártékony elemektől eredő fenyegetésekről.

### <a name="behavioral-analytics"></a>Működés elemzése

A működés elemzése olyan módszer, amely megvizsgálja és összehasonlítja az adatokat az ismert minták gyűjteményével. Ezek a minták azonban nem csak egyszerű aláírások. Meghatározásuk hatalmas adatkészletekre alkalmazott összetett gépi tanulási algoritmusokkal történt. Ezenkívül szakértő elemzők mélyrehatóan elemezték a kártékony működést a meghatározásukhoz. A Azure Security Center a viselkedési elemzések segítségével azonosíthatja a feltört erőforrásokat a virtuális gépek naplói, a virtuális hálózati eszközök naplói, a háló naplói, az összeomlási memóriaképek és más források elemzése alapján.

Ezen kívül más jelekkel is összefüggésbe kerül, hogy a széleskörű kampányt támogató bizonyítékot keressen. Az összefüggések felderítése segít azonosítani a feltörés meghatározott tüneteit mutató eseményeket. 

### <a name="anomaly-detection"></a>Rendellenességek észlelése

Az Azure Security Center további módszere a fenyegetések felderítésére a rendellenességek észlelése. A működés elemzésével (amely a nagy adatkészletekből kinyert ismert mintákon alapul) ellentétben a rendellenességek észlelése „testre szabottabb”, és az üzemelő példányokhoz tartozó alapkonfigurációkra összpontosít. Ez a módszer gépi tanulás alkalmazásával felméri az üzemelő példányok normál tevékenységeit, majd szabályokat hoz létre az olyan rendkívüli körülmények meghatározásához, amelyek a biztonságot érintő eseményre utalhatnak.

## <a name="how-are-alerts-classified"></a>Hogyan vannak besorolva a riasztások?

A Security Center súlyosságot rendel a riasztásokhoz, így könnyebben rangsorolhatja az egyes riasztásokban való részvétel sorrendjét, hogy az erőforrások biztonsága esetén azonnal elérhető legyen. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

> [!NOTE]
> A riasztás súlyossága másképp jelenik meg a portálon, és a 01-01-2019-es dátumot tartalmazó REST API verzióiban. Ha az API egy régebbi verzióját használja, frissítsen az alább ismertetett konzisztens élményre.

- **Magas:** Nagy valószínűséggel az erőforrás biztonsága sérül. Azonnal megtekintheti. Security Center a kártékony szándék és a riasztás kibocsátására szolgáló megállapítások terén is nagy megbízhatósággal rendelkezik. Például egy olyan riasztás, amely észleli egy ismert kártékony eszköz, például a Mimikatz végrehajtását, a hitelesítő adatok ellopására szolgáló közös eszköz.
- **Közepes:** Ez valószínűleg egy gyanús tevékenység, amely arra utalhat, hogy az erőforrás biztonsága sérül.
Security Center az elemzésben vagy a keresésben a közepes és a rosszindulatú szándék megbízhatósága közepes és magas. Ezek általában gépi tanulás vagy anomália-alapú észlelések. Például egy rendellenes helyről érkező bejelentkezési kísérlet.
- **Alacsony:** Ez egy jóindulatú pozitív vagy tiltott támadás lehet.
   * A Security Center nem eléggé biztos abban, hogy a szándék rosszindulatú, és a tevékenység ártalmatlan lehet. A napló törlése például olyan művelet, amely akkor fordulhat elő, ha egy támadó megpróbálja elrejteni a zeneszámokat, de sok esetben a rendszergazdák által végrehajtott rutin művelet.
   * Security Center általában nem derül ki, ha a támadások blokkolva lettek, kivéve, ha azt javasoljuk, hogy tekintse át a következőt:. 
- **Tájékoztatás:** A biztonsági incidensek részletezése során csak tájékoztató riasztások jelennek meg, vagy ha a REST API egy adott riasztási AZONOSÍTÓval használja. Az incidensek általában számos riasztásból állnak, amelyek némelyike csak tájékoztató jelleggel jelenhet meg, de a többi riasztás kontextusában érdemes lehet alaposabban megtekinteni. 
 

## <a name="continuous-monitoring-and-assessments"></a>Folyamatos monitorozás és értékelések

Azure Security Center a biztonsági kutatási és adatelemzési csapatokból származó előnyöket a Microsoftnál, akik folyamatosan figyelik a fenyegetések tájképének változásait. Ide tartoznak a következők:

* A fenyegetések **felderítésének figyelése**: a fenyegetések felderítése magában foglalja a meglévő vagy újonnan felmerülő fenyegetésekkel kapcsolatos mechanizmusokat, indikátorokat, következményeket és gyakorlati tanácsokat. Ez az információ elérhető a biztonsági közösség számára, és a Microsoft folyamatosan figyeli a fenyegetésekre vonatkozó intelligencia belső és külső forrásból származó hírcsatornáit.
* **Jelmegosztás**: megosztjuk és elemezzük a Microsoft felhőbeli és helyszíni szolgáltatásokat, kiszolgálókat és végponti eszközök tartalmazó kiterjedt portfóliójához kapcsolódó biztonsági csoportok megfigyeléseit.
* **A Microsoft biztonsági szakértői**: folyamatos kapcsolatot tartunk a Microsoft csapataival, amelyek tagjai meghatározott biztonsági szakterületekkel foglalkoznak, például igazságügyi szakértői tevékenységekkel és a webes támadások észlelésével.
* **Az észlelés finomhangolása**: a biztonsági szakértők az ügyfelek valós adatkészletein futtatják az algoritmusokat, és az ügyfelekkel együttműködve ellenőrzik az eredményeket. Az igazi és a téves találatok megjelölésével pontosítják a gépi algoritmusokat.

Ezek az egyesített erőfeszítések új és hatékonyabb észleléseket eredményeznek, amelyeknek azonnal hasznát veheti anélkül, hogy bármit is tennie kellene.

## Biztonsági riasztások <a name="security-alert-types"></a> típusai

Az alábbi témakörök a különböző riasztásokat ismertetik az erőforrástípusok szerint:

* [Riasztások a IaaS Windows rendszerű gépekhez](threat-protection.md#windows-machines)
* [IaaS Linux rendszerű gépekre vonatkozó riasztások](threat-protection.md#linux-machines)
* [Riasztások Azure App Service](threat-protection.md#app-services)
* [Azure-tárolókkal kapcsolatos riasztások](threat-protection.md#azure-containers)
* [Riasztások SQL Database és SQL Data Warehouse](threat-protection.md#data-sql)
* [Azure Storage-riasztások](threat-protection.md#azure-storage)
* [Riasztások Cosmos DB](threat-protection.md#cosmos-db)

A következő témakörök azt ismertetik, hogyan használják a Security Center az Azure-infrastruktúrával való integrálás során gyűjtött különböző telemetria, hogy az Azure-ban üzembe helyezett erőforrások további védelmi rétegeit is alkalmazni lehessen:

* [Riasztások az Azure felügyeleti rétegéhez (Azure Resource Manager) (előzetes verzió)](threat-protection.md#management-layer)
* [Riasztások Azure Key Vault (előzetes verzió)](threat-protection.md#azure-keyvault)
* [Riasztások az Azure hálózati réteghez](threat-protection.md#network-layer)
* [Más szolgáltatásokból származó riasztások](threat-protection.md#alerts-other)

## <a name="what-are-security-incidents"></a>Mik azok a biztonsági incidensek?

A biztonsági incidensek a kapcsolódó riasztások gyűjteményei, az egyes riasztások egyenkénti listázása helyett. A Security Center a [Felhőbeli intelligens riasztások korrelációját](security-center-alerts-cloud-smart.md) használja a különböző riasztások és az alacsony megbízhatósági jelzések biztonsági incidensekre való összekapcsolásához.

Az incidensek használatával a Security Center egyetlen nézetet biztosít a támadási kampányokról és az összes kapcsolódó riasztásról. Ez a nézet lehetővé teszi, hogy gyorsan megértse, milyen műveleteket tartott a támadó, és milyen erőforrásokat érintettek. További információ: [felhőalapú intelligens riasztás korrelációja](security-center-alerts-cloud-smart.md).

## <a name="security-alerts-in-azure-activity-log"></a>Biztonsági riasztások az Azure-beli tevékenység naplójában

Amellett, hogy a Azure Portalon vagy programozott módon elérhetővé válik, a biztonsági riasztások és incidensek naplózása eseményként történik az [Azure-tevékenység naplójában](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view). További információ az esemény-sémáról: [biztonsági riasztások az Azure-beli tevékenység naplójában](https://go.microsoft.com/fwlink/?linkid=2114113).

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, milyen típusú riasztások érhetők el a Security Centerban. További információkért lásd:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center – gyakori kérdések](https://docs.microsoft.com/azure/security-center/security-center-faq)

