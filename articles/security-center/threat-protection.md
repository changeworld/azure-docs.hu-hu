---
title: Fenyegetésvédelem az Azure Security Centerben
description: Ez a témakör a Azure Security Center veszélyforrások elleni védelmi funkciói által védett erőforrásokat ismerteti
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 031423d72aecacc1feed692aaa32bcd1ff7efbab
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282704"
---
# <a name="threat-protection-in-azure-security-center"></a>Fenyegetésvédelem az Azure Security Centerben

Ez az oldal röviden leírja a standard díjszabási szinten a felhasználók számára Azure Security Center által megjelenített biztonsági riasztások forrásait.

Ha Security Center fenyegetést észlel az alábbi környezet bármelyik területén, riasztást hoz létre. Ezek a riasztások ismertetik az érintett erőforrások részleteit, a javasolt szervizelési lépéseket, valamint bizonyos esetekben a logikai alkalmazások válaszként való aktiválásának lehetőségét.

Azt határozza meg, hogy a riasztást a Security Center hozza-e létre, vagy egy másik biztonsági terméktől Security Center fogadta-e, exportálhatja. Ha a riasztásokat az Azure Sentinelbe (vagy egy harmadik féltől származó SIEM-be) vagy más külső helyekre szeretné exportálni, kövesse a [riasztások és javaslatok exportálása](continuous-export.md)című témakör utasításait. 




## Veszélyforrások elleni védelem Windows rendszerű gépeken<a name="windows-machines"></a>

A Azure Security Center együttműködik az Azure-szolgáltatásokkal a Windows-alapú gépek monitorozásához és védeleméhez. Security Center az összes szolgáltatás riasztásait és szervizelési javaslatait egyszerűen használható formátumban jeleníti meg.

* A **Microsoft Defender ATP** <a name="windows-atp"></a> -Security Center a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) integrálásával kiterjeszti a felhőalapú munkaterhelés-védelmi platformokat. Együttesen átfogó végpont-észlelési és-reagálási (EDR) képességeket biztosítanak.

    > [!IMPORTANT]
    > A Microsoft Defender ATP-érzékelő automatikusan engedélyezve van a Security Center-t használó Windows-kiszolgálókon.

    Ha a Microsoft Defender ATP fenyegetést észlel, riasztást indít el. A riasztás a Security Center irányítópulton jelenik meg. Az irányítópulton megtekintheti a Microsoft Defender ATP-konzolt, és részletes vizsgálatot végezhet a támadás hatókörének felderítése érdekében. További információ a Microsoft Defender ATP szolgáltatással kapcsolatban: kiszolgálók beléptetése [a Microsoft DEFENDER ATP szolgáltatásba](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Összeomlási memóriakép elemzése** <a name="windows-dump"></a> – a szoftverek összeomlásakor egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában.

    Előfordulhat, hogy az összeomlást kártevő okozta, vagy kártevőt tartalmaz. A biztonsági termékek észlelésének elkerülése érdekében a kártevő szoftverek különböző formái egy fájl nélküli támadást használnak, amely elkerüli a lemezre írást, vagy titkosítja a lemezre írt összetevőket. Az ilyen típusú támadásokat nehéz felderíteni a hagyományos lemezes megközelítések használatával.

    A memória-elemzés használatával azonban az ilyen típusú támadásokat is kiderítheti. Az összeomlási memóriakép memóriájának elemzésével a Security Center képes azonosítani a támadás által használt technikákat. Előfordulhat például, hogy a támadás megkísérli a szoftver biztonsági réseit kihasználni, hozzáférni a bizalmas adatokhoz, és titokban megmarad egy feltört gépen belül. Security Center ez a művelet a gazdagépek minimális teljesítményére gyakorolt hatásával működik.

    Az összeomlási memóriakép elemzési riasztásával kapcsolatos részletekért tekintse meg a [riasztások hivatkozási táblázatát](alerts-reference.md#alerts-windows).

* A **fájlok közötti támadás észlelése** <a name="windows-fileless"></a> – a végpontokat célzó, nem gyakori támadások. Az észlelés elkerülése érdekében a fájlokkal nem rendelkező támadások rosszindulatú hasznos adatokat szúrnak be a memóriába. A támadó adattartalmai a feltört folyamatok memóriáján belül maradnak, és számos kártékony tevékenységet hajtanak végre.

    A fájlok közötti támadás észlelése, az automatizált memória kriminalisztikai módszerei azonosítják a fájlokra vonatkozó támadási segédanyagokat, technikákat és viselkedéseket. Ez a megoldás rendszeres időközönként ellenőrzi a gépet, és kinyeri az eredményeket közvetlenül a biztonsági szempontból kritikus folyamatok memóriájában.

    Megtalálhatja a kihasználat, a kódok befecskendezését és a kártékony hasznos adatok végrehajtását. A fájlok közötti támadás észlelése részletes biztonsági riasztásokat generál a riasztások osztályozásának, korrelációjának és az alsóbb szintű válaszidő felgyorsításához. Ez a megközelítés kiegészíti az eseményvezérelt EDR megoldásokat, és nagyobb észlelési lefedettséget biztosít.

    A fájlokba nem kerülő támadás észlelésével kapcsolatos riasztások részleteiért tekintse meg a [riasztások hivatkozási táblázatát](alerts-reference.md#alerts-windows).

> [!TIP]
> A Windows-riasztásokat a [biztonsági riasztások Azure Security Center](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)forgatókönyvek letöltésével szimulálhatja.






## Veszélyforrások elleni védelem Linux rendszerű gépeken<a name="linux-machines"></a>

A Security Center a Linux rendszerű gépekről naplózza a naplózási rekordokat **, az**egyik leggyakrabban használt Linux-naplózási keretrendszerben. a naplózott élet a fővonali kernelben. 

* **Linux auditált riasztások és Microsoft monitoring Agent (MMA) integráció** <a name="linux-auditd"></a> – az auditált rendszer egy kernel szintű alrendszerből áll, amelynek feladata a rendszerhívások figyelése. Egy megadott szabálykészlet alapján szűri őket, és üzeneteket küld nekik egy szoftvercsatorna számára. A Security Center a Microsoft monitoring Agent (MMA) szolgáltatáson belül a naplózott csomag funkcióit integrálja. Ez az integráció lehetővé teszi az auditált események gyűjtését az összes támogatott Linux-disztribúcióban, előfeltételek nélkül.  

    a naplózott rekordok gyűjtése, bővítése és összesítése az eseményekre a Linux MMA-ügynök használatával történik. Security Center folyamatosan bővíti a Linux-jeleket használó új elemzéseket a Felhőbeli és a helyszíni linuxos gépek kártékony viselkedésének észlelése érdekében. A Windows-funkciókhoz hasonlóan ezek az elemzések a gyanús folyamatokon, a kétes bejelentkezési kísérleteken, a kernel modul betöltésén és más tevékenységeken is kiterjedhetnek. Ezek a tevékenységek jelezhetik, hogy a gép támadás alatt áll, vagy megsértették.  

    A Linux-riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-linux)tartalmazza.

> [!TIP]
> A Linux-riasztásokat a Linux- [észlelések Azure Security Center](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)forgatókönyv letöltésével szimulálhatja.





## Veszélyforrások elleni védelem Azure App Service<a name="app-services"></a>

> [!NOTE]
> Ez a szolgáltatás jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

Security Center a felhő méretezésével azonosítja a App Serviceon futó alkalmazásokat célzó támadásokat. A támadók webes alkalmazásokat kereshetnek a gyengeségek megtalálásához és kiaknázásához. Az Azure-ban futó alkalmazásokra irányuló kérelmek az adott környezetbe való átirányításuk előtt több átjárón keresztül futnak, ahol megvizsgálják és naplózzák azokat. Ezeket az információkat a rendszer felhasználja a biztonsági rések és a támadók azonosítására, valamint a később felhasználható új mintázatok megismerésére.

Az Azure felhőalapú szolgáltatóként való láthatóságának használatával Security Center elemzi App Service belső naplókat, hogy azonosítsa a támadási módszereket több célponton. A módszertan például kiterjedt keresési és elosztott támadásokat tartalmaz. Ez a típusú támadás általában az IP-címek egy kis részhalmazát mutatja be, és a hasonló végpontokra való bejárási mintákat jeleníti meg több gazdagépen. A támadások egy sebezhető oldalra vagy beépülő modulra keresnek, és egyetlen gazdagép szempontjából nem azonosíthatók.

Ha Windows-alapú App Service csomagot futtat, Security Center a mögöttes munkaterületeket és virtuális gépeket is elérheti. A fent említett naplózási adatokkal együtt az infrastruktúra megtudhatja a történetet egy olyan új támadástól, amely a vadonban kering, és megsérül az ügyfél-gépeken. Ezért még akkor is, ha a webalkalmazás kihasználása után Security Center van telepítve, lehetséges, hogy észlelni tudja a folyamatos támadásokat.

A Azure App Service riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azureappserv)tekintheti meg.

App Service csomagokkal kapcsolatos további információkért lásd: [app Service csomagok](https://azure.microsoft.com/pricing/details/app-service/plans/).





## Veszélyforrások elleni védelem Azure-tárolók esetén<a name="azure-containers"></a>

> [!NOTE]
> Ez a szolgáltatás jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

A Security Center valós idejű veszélyforrások elleni védelmet biztosít a tároló környezetek számára, és riasztásokat hoz létre a gyanús tevékenységekhez. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét.

A Security Center különböző szinteken biztosítja a veszélyforrások elleni védelmet: 

* A **gazdagép szintjének** Security Center ügynöke (a standard szinten érhető el, a részletek [díjszabása](security-center-pricing.md) ) figyeli a Linuxot a gyanús tevékenységekhez. Az ügynök riasztásokat küld a csomópontból vagy a rajta futó tárolóból származó gyanús tevékenységekről. Ilyen tevékenység például a webrendszerhéj-észlelés és a kapcsolat ismert gyanús IP-címekkel.

    Az ügynök a tároló-specifikus elemzéseket figyeli, így mélyebb betekintést nyújt a tároló-környezet biztonságára. Riasztásokat indít az eseményekhez, például a privilegizált tárolók létrehozásához, az API-kiszolgálókhoz való gyanús hozzáféréshez, valamint a Secure Shell-(SSH-) kiszolgálókhoz, amelyek egy Docker-tárolón belül futnak.

    >[!IMPORTANT]
    > Ha úgy dönt, hogy nem telepíti az ügynököket a gazdagépekre, a fenyegetések elleni védelem előnyeinek és biztonsági riasztásoknak csak egy részhalmazát fogja kapni. A hálózati elemzéssel és a rosszindulatú kiszolgálókkal folytatott kommunikációval kapcsolatos riasztásokat továbbra is megkapja.

    A gazda szintű riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-containerhost)tartalmazza.


* Az AK-alapú **fürt szintjén**a fenyegetések elleni védelem a Kubernetes naplózási naplóinak elemzésén alapul. Az **ügynök** nélküli figyelés engedélyezéséhez adja hozzá az előfizetéshez a Kubernetes lehetőséget a **díjszabási & beállítások** lapon (lásd a [díjszabást](security-center-pricing.md)). A riasztások ezen a szinten történő létrehozásához Security Center figyeli az AK által felügyelt szolgáltatásokat az AK által beolvasott naplók használatával. Az ezen a szinten található események közé tartoznak például az elérhető Kubernetes-irányítópultok, a magas jogosultsági szintű szerepkörök létrehozása és a bizalmas csatlakoztatások létrehozása.

    >[!NOTE]
    > A Security Center biztonsági riasztásokat hoz létre az Azure Kubernetes szolgáltatás műveleteihez, illetve a Kubernetes beállítás engedélyezése után előforduló központi telepítések esetén az előfizetési beállításokban. 

    Az AK-beli fürt szintű riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-akscluster)tartalmazza.

Emellett a biztonsági kutatók globális csapata folyamatosan figyeli a fenyegetés tájképét. A felderített tároló-specifikus riasztásokat és biztonsági réseket adják hozzá.

> [!TIP]
> A tárolói riasztások szimulálása a [blogbejegyzés](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)utasításait követve végezhető el.







## Veszélyforrások elleni védelem az Azure hálózati rétegben<a name="network-layer"></a>

Security Center a hálózati rétegbeli elemzések a minta [IPFIX adatokon](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)alapulnak, amelyek az Azure Core-útválasztók által gyűjtött csomagok fejlécei. Ezen adatcsatorna alapján a Security Center gépi tanulási modelleket használ a kártékony forgalmi tevékenységek azonosítására és megjelölésére. A Security Center a Microsoft Threat Intelligence-adatbázist is használja az IP-címek dúsítására.

Bizonyos hálózati konfigurációk korlátozhatják Security Center a gyanús hálózati tevékenységekre vonatkozó riasztások generálását. A hálózati riasztások létrehozásához Security Center a következőket:

- A virtuális gép nyilvános IP-címmel rendelkezik (vagy egy nyilvános IP-címmel rendelkező terheléselosztó).

- A virtuális gép hálózati kimenő forgalmát nem blokkolja külső azonosító megoldás.

- A virtuális gép ugyanazzal az IP-címmel lett hozzárendelve, mint az a teljes óra, amelyben a gyanús kommunikáció történt. Ez a felügyelt szolgáltatás részeként létrehozott virtuális gépekre is vonatkozik (például: AK, Databricks).

Az Azure hálózati réteggel kapcsolatos riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-azurenetlayer)tartalmazza.

További információ arról, hogy a Security Center hogyan használhatók a hálózattal kapcsolatos jelek a veszélyforrások elleni védelem alkalmazásához: [a heurisztikus DNS-észlelések a Security Centerban](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).







## Veszélyforrások elleni védelem Azure Key Vault (előzetes verzió)<a name="azure-keyvault"></a>

> [!NOTE]
> Ez a szolgáltatás jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

A Azure Key Vault egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsok és a titkok, például a tanúsítványok, a kapcsolatok karakterláncai és a jelszavak számára. 

Azure Security Center magában foglalja az Azure-natív, komplex veszélyforrások elleni védelmet Azure Key Vault, amely egy további biztonsági intelligenciát biztosít. Security Center szokatlan és potenciálisan ártalmas kísérleteket észlel Key Vault fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi, hogy biztonsági szakértő nélkül kezeljék a fenyegetéseket, és nincs szükség a harmadik féltől származó biztonsági figyelő rendszerek felügyeletére.  

Ha rendellenes tevékenységek történnek, Security Center riasztásokat jelenít meg, és opcionálisan e-mailben küldi el őket az előfizetés-rendszergazdáknak. Ezek a riasztások tartalmazzák a gyanús tevékenység részleteit, valamint a fenyegetések kivizsgálásával és szervizelésével kapcsolatos javaslatokat. 

A Azure Key Vault riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azurekv)tekintheti meg.








## Veszélyforrások elleni védelem SQL Database és SQL Data Warehouse<a name="data-sql"></a>

A Azure SQL Database komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A riasztások akkor jelennek meg, ha gyanús adatbázis-tevékenységek, potenciális sebezhetőségek vagy SQL-injektálási támadások, valamint rendellenes adatbázis-hozzáférés és lekérdezési minták vannak.

A Azure SQL Database és az SQL komplex veszélyforrások elleni védelme az Azure SQL Database-adatbázisokat Azure SQL Database, a felügyelt példányokat, a Azure SQL Data Warehouse-adatbázisokat és az Virtual Machines Azure-beli SQL-kiszolgálókat tartalmazó speciális SQL-alapú biztonsági képességek [(ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) egységes csomag részét képezi.

További információkért lásd:

* [A komplex veszélyforrások elleni védelem engedélyezése a Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Az Azure-beli SQL serverek komplex veszélyforrások elleni védelmének engedélyezése Virtual Machines](security-center-iaas-advanced-data.md)
* [A veszélyforrások elleni védelmi riasztások listája SQL Database és SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)









## Veszélyforrások elleni védelem az Azure Storage-ban<a name="azure-storage"></a>

> [!NOTE]
> Ez a szolgáltatás jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

A tárolók komplex veszélyforrások elleni védelme (jelenleg csak a blob Storage esetében érhető el) szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését anélkül, hogy biztonsági szakértőnek kellene lennie, és segít a biztonsági monitorozási rendszerek kezelésében.

További információkért lásd:

* [A komplex veszélyforrások elleni védelem engedélyezése az Azure Storage-ban](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Az Azure Storage veszélyforrások elleni védelmi értesítéseinek listája](alerts-reference.md#alerts-azurestorage)


> [!TIP]
> Az Azure Storage-riasztásokat az [ebben a blogbejegyzésben](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)található utasításokat követve szimulálhatja.






## Veszélyforrások elleni védelem Azure Cosmos DB<a name="cosmos-db"></a>

A Azure Cosmos DB riasztások szokatlan és potenciálisan ártalmas kísérletekkel jönnek létre Azure Cosmos DB fiókok eléréséhez vagy kiaknázásához.

További információkért lásd:

* [A Azure Cosmos DB komplex veszélyforrások elleni védelme (előzetes verzió)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [A veszélyforrások elleni védelmi riasztások listája Azure Cosmos DB (előzetes verzió)](alerts-reference.md#alerts-azurecosmos)







## Veszélyforrások elleni védelem az Azure felügyeleti rétegben (Azure Resource Manager) (előzetes verzió)<a name ="management-layer"></a>

A Azure Resource Manager alapján Security Center védelmi réteg jelenleg előzetes verzióban érhető el.

A Security Center egy további védelmi réteget biztosít Azure Resource Manager események használatával, amely az Azure-beli vezérlési síkon tekinthető. A Azure Resource Manager rekordok elemzésével Security Center észleli a szokatlan vagy potenciálisan ártalmas műveleteket az Azure-előfizetési környezetben.

A Azure Resource Manager (előzetes verzió) riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azureresourceman)tekintheti meg.



>[!NOTE]
> Az előző elemzések közül több Microsoft Cloud App Security van. Ezen elemzések kihasználása érdekében aktiválni kell egy Cloud App Security licencet. Ha Cloud App Security licenccel rendelkezik, ezek a riasztások alapértelmezés szerint engedélyezve vannak. A riasztások letiltása:
>
> 1. A **Security Center** panelen válassza a **biztonsági házirend**elemet. A módosítani kívánt előfizetés esetében válassza a **beállítások szerkesztése**lehetőséget.
> 2. Válassza a **veszélyforrások észlelése**lehetőséget.
> 3. Az **integráció engedélyezése**területen törölje a jelet a **Microsoft Cloud app Security az adatai elérésének engedélyezése**jelölőnégyzetből, majd válassza a **Mentés**lehetőséget.

>[!NOTE]
>Security Center a biztonsággal kapcsolatos ügyféladatokat ugyanabban a földrajzi régióban tárolja, mint az erőforrása. Ha a Microsoft még nem telepített Security Center az erőforrás geo-ban, akkor az a Egyesült Államok tárolja azokat. Ha Cloud App Security engedélyezve van, a rendszer ezeket az adatokat a Cloud App Security földrajzi hely szabályainak megfelelően tárolja. További információkért lásd: [adattároló a nem regionális szolgáltatásokhoz](https://azuredatacentermap.azurewebsites.net/).



## Más Microsoft-szolgáltatásoktól származó biztonsági riasztások<a name="alerts-other"></a>

### Veszélyforrások elleni védelem az Azure WAF<a name="azure-waf"></a>

Az Azure Application Gateway egy olyan webalkalmazási tűzfalat (WAF) nyújt, amely központi védelmet biztosít a webalkalmazások számára a biztonsági rések és az azokat kihasználó támadások ellen.

A webalkalmazások egyre inkább a gyakran ismert biztonsági réseket kihasználó rosszindulatú támadásokra irányulnak. A Application Gateway WAF a 3,0-es alapszintű szabálykészlet alapján, vagy az Open Web Application biztonsági projekt 2.2.9 alapul. A WAF automatikusan frissül az új biztonsági rések elleni védelem érdekében. 

Ha rendelkezik Azure WAF-licenccel, a WAF-riasztások továbbítása a Security Center, és nincs szükség további konfigurálásra. A WAF által generált riasztásokkal kapcsolatos további információkért lásd: a [webalkalmazási TŰZFAL CRS-szabályait tartalmazó csoportok és szabályok](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### Veszélyforrások elleni védelem Azure DDoS Protection<a name="azure-ddos"></a>

Az elosztott szolgáltatásmegtagadási (DDoS) támadások könnyen végrehajthatók. Nagyszerű biztonsági szempontot jelentenek, különösen akkor, ha az alkalmazásokat a felhőbe helyezi át. 

A DDoS-támadás megpróbál kimeríteni egy alkalmazás erőforrásait, így az alkalmazás nem érhető el a legitim felhasználók számára. A DDoS-támadások az interneten keresztül elérhető végpontokat is megcélozhatja.

A DDoS-támadások elleni védelemhez vásároljon Azure DDoS Protection-licencet, és győződjön meg róla, hogy az alkalmazás kialakításának ajánlott eljárásait követi. A DDoS Protection különböző szolgáltatási szinteket biztosít. További információ: [Azure DDoS Protection Overview (áttekintés](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)).

A Azure DDoS Protection riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azureddos)tekintheti meg.


## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a veszélyforrások elleni védelmi funkciókkal kapcsolatos biztonsági riasztásokról, tekintse meg a következő cikkeket:

* [Az összes Azure Security Center-riasztás hivatkozási táblázata](alerts-reference.md)
* [Biztonsági riasztások az Azure Security Centerben](security-center-alerts-overview.md)
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
* [Biztonsági riasztások és javaslatok exportálása (előzetes verzió)](continuous-export.md)