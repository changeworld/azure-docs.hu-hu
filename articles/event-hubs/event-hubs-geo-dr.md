---
title: GEO-disaster recovery – Azure Event Hubs |} A Microsoft Docs
description: Feladatátvétel földrajzi régiót használnak, és hajtsa végre az Azure Event Hubs vész-helyreállítási
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 40db6e9f429569bc19641aa5f0f371f287db7b18
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281469"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Az Azure Event Hubs - Geo-vészhelyreállítás 

Ha a teljes Azure-régiók vagy-adatközpontok (ha nincsenek használatban [rendelkezésre állási zónák](../availability-zones/az-overview.md) ) a tapasztalatok leállását tapasztalják, kritikus fontosságú, hogy az adatfeldolgozás továbbra is egy másik régióban vagy adatközpontban működjön. Így a *geo-* vész-helyreállítás és a *geo-replikáció* minden vállalat számára fontos funkció. Az Azure Event Hubs geo-vészhelyreállítás és georeplikáció útján, a névterek szintjén is támogatja. 

> [!NOTE]
> A földrajzi katasztrófa utáni helyreállítási funkció csak a [standard és a dedikált SKU](https://azure.microsoft.com/pricing/details/event-hubs/)esetében érhető el.  

## <a name="outages-and-disasters"></a>Leállások és katasztrófák kezelése

Fontos megjegyzés: a "leállások" és "katasztrófák." megkülönböztetése A *leállás* az Azure Event Hubs ideiglenes nem érhető el, és hatással lehet a szolgáltatás egyes összetevőire, például az üzenetküldési tárolóra vagy akár a teljes adatközpontra is. Azonban Miután a probléma megoldódott, az Event Hubs ismét elérhetővé válik. Egy kimaradás általában nem okoz az üzenetek vagy egyéb adatok elvesztését. Egy példa az ilyen kimaradás lehet áramszünet esetén az adatközpontban. Néhány leállások veszteségként csak rövid kapcsolat átmeneti vagy hálózati problémák miatt. 

A *katasztrófa* a Event Hubs-fürt, az Azure-régió vagy az adatközpont állandó vagy hosszú távú elvesztéseként van meghatározva. A régió vagy az Adatközpont előfordulhat, hogy előfordulhat, hogy nem ismét elérhetővé válik, vagy előfordulhat, hogy nem működik az óra vagy nap. Az ilyen katasztrófák példák fire, -elárasztás vagy földrengés. Olyan állandó válik az egyes üzenetek, események vagy egyéb adatok elvesztését okozhatja. Azonban a legtöbb esetben kell adatvesztés nélküli, és üzeneteket helyreállíthatók legyenek, amint az Adatközpont biztonsági mentése.

Az Azure Event Hubs Geo-disaster recovery funkcióját a vészhelyreállítási megoldást. A fogalmakat és az ebben a cikkben leírt munkafolyamatot alkalmazni a vészhelyreállítási forgatókönyveket, és nem átmeneti vagy ideiglenes valamilyen okból kimaradás lép. A Microsoft Azure vész-helyreállítási részletes megvitatását [ebben a cikkben](/azure/architecture/resiliency/disaster-recovery-azure-applications)találja.

## <a name="basic-concepts-and-terms"></a>Alapfogalommal és kifejezéssel

A vész-helyreállítási szolgáltatás metaadatainak vész-helyreállítási valósítja meg, és az elsődleges és másodlagos vész-helyreállítási névterek támaszkodik. 

A földrajzi katasztrófa utáni helyreállítási funkció csak a [standard és a dedikált SKU](https://azure.microsoft.com/pricing/details/event-hubs/) esetében érhető el. Nem kell, végezze el a kapcsolati karakterlánc módosításokat, a kapcsolat alias-n keresztül.

Ez a cikk a következő kifejezéseket használjuk:

-  *Alias*: az Ön által beállított vész-helyreállítási konfiguráció neve. Az alias egyetlen stabil teljes tartománynévként (FQDN) kapcsolati karakterláncban biztosít. Alkalmazások ez alias a kapcsolati karakterlánc használatával csatlakozni a névtérhez. 

-  *Elsődleges/másodlagos névtér*: az aliasnak megfelelő névterek. Az elsődleges névtér "aktív", és fogadja az üzeneteket (Ez lehet egy meglévő vagy új névtér). A másodlagos névtérre "passzív", és nem fogadhat üzeneteket. A metaadatok között is szinkronizálva, így mindkettő is zökkenőmentesen fogadja az üzeneteket alkalmazás kódja vagy kapcsolati karakterlánc módosítása nélkül. Győződjön meg arról, hogy csak az aktív névteret fogadja az üzeneteket, az aliast kell használnia. 

-  *Metaadatok*: olyan entitások, mint az Event hubok és a fogyasztói csoportok; a névtérhez társított szolgáltatás tulajdonságai. Vegye figyelembe, hogy csak az entitások és a beállításaik automatikusan replikálja. Üzenetek és események nem lesznek replikálva. 

-  *Feladatátvétel*: a másodlagos névtér aktiválása folyamatban van.

## <a name="supported-namespace-pairs"></a>Támogatott névtér-párok
Az elsődleges és a másodlagos névterek következő kombinációi támogatottak:  

| Elsődleges névtér | Másodlagos névtér | Támogatott | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Igen | 
| Standard | Dedikált | Igen | 
| Dedikált | Dedikált | Igen | 
| Dedikált | Standard | Nem | 

> [!NOTE]
> Ugyanahhoz a dedikált fürthöz tartozó névtereket nem lehet párosítani. A különálló fürtökben található névtereket is párosíthatja. 

## <a name="setup-and-failover-flow"></a>A telepítő és a feladatátvételi folyamat

A következő szakaszban áttekintjük a feladatátvételi folyamat, és azt ismerteti, hogyan állítható be a kezdeti feladatátvétel. 

![1][]

### <a name="setup"></a>Beállítás

Először létrehoz vagy egy meglévő elsődleges névtér és a egy új másodlagos névteret, majd párosítsa a két. A párosítás kínál, amellyel csatlakozni alias. Alias használata, mert nem rendelkezik a kapcsolati karakterláncok módosítása. Csak új névteret a párosítás feladatátvételi lehet hozzáadni. Végül, hozzá kell adnia bizonyos figyelőket észleli, ha szükség-e a feladatátvételt. A legtöbb esetben a szolgáltatás kiterjedt ökoszisztémáját egy részét képezi, így az automatikus feladatátvételt is ritkán lehetségesek, például nagyon gyakran feladatátvételt kell végrehajtani szinkronban vannak a többi alrendszer vagy infrastruktúra.

### <a name="example"></a>Példa

Ebben a forgatókönyvben egy példa fontolja meg egy értékesítés pont (POS) megoldás, amely az üzenetek vagy a eseményeket bocsát ki. Az Event Hubs események néhány egymáshoz és újraformázása megoldást, amely ezután továbbítja a megfeleltetett adatokat további feldolgozás céljából egy másik rendszerre továbbítja. Ezen a ponton az összes ezekben a rendszerekben előfordulhat, hogy futhat ugyanazon Azure-régióban. A döntést, mikor és milyen része a feladatátvételt az infrastruktúra adatáramlásra függ. 

Automatizálhatja a feladatátvétel rendszerek figyelése, vagy a személyre szabott figyelési megoldásokkal. Azonban az ilyen automation vesz igénybe, további tervezési és a munka, amely a jelen cikk hatókörébe esik.

### <a name="failover-flow"></a>Feladatátvétel folyamatábrája

Kezdeményezze a feladatátvételt, ha két lépésre szükség:

1. Egy másik kimaradás során, érdemes lehet feladatátvételi képes újra. Ezért egy másik passzív névtér beállítása, és frissítse a párosítást. 

2. Kérje le a korábbi elsődleges névtérből üzeneteket, ha megint elérhetővé válik. Ezt követően, hogy a névtér használhat rendszeres üzenetkezelési kívül a geo-helyreállítási beállításai, vagy törölje a régi elsődleges névteret.

> [!NOTE]
> Csak a sikertelen előre szemantika támogatottak. Ebben a forgatókönyvben átadja a feladatokat, és ezután újból párosítsa egy új névteret. Visszavétel nem támogatott. Ha például az SQL-fürt. 

![2][]

## <a name="management"></a>Kezelés

Ha állított be; például, a nem megfelelő régiók párosítva a kezdeti beállítás során, a két névtér párosítási bármikor megszakítható. Ha azt szeretné, a párosított névterek adatokként rendszeres névterek, az alias törlése.

## <a name="samples"></a>Példák

A [githubon található minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) bemutatja, hogyan kell beállítani és kezdeményezni a feladatátvételt. Ez a minta azt mutatja be, a következő fogalmak:

- Az Event Hubs Azure Resource Manager használata az Azure Active Directory szükséges beállításokat. 
- A mintakód végrehajtásához szükséges lépéseket. 
- Küldhet és fogadhat, a jelenlegi elsődleges névtérből. 

## <a name="considerations"></a>Megfontolások

Vegye figyelembe az alábbi szempontokat figyelembe kell venni ebben a kiadásban:

1. A tervezés szerint Event Hubs geo-vész-helyreállítás nem replikálja az adatait, ezért nem használhatja fel az elsődleges Event hub régi eltolási értékét a másodlagos esemény központján. Javasoljuk, hogy az Event receivert a következők egyikével indítsa újra:

- *EventPosition. FromStart ()* – ha szeretné beolvasni az összes adatközpontot a másodlagos esemény központján.
- *EventPosition. FromEnd ()* – ha szeretné beolvasni az összes új adatforrást a másodlagos esemény központhoz való kapcsolódáskor.
- *EventPosition. FromEnqueuedTime (datetime)* – ha szeretné beolvasni a másodlagos Event hub-ban fogadott összes adat egy adott dátumtól és időponttól kezdve.

2. A feladatátvételi tervezés során is figyelembe kell venni az idő tényező. Például ha elveszíti a kapcsolatot a 15-20 percnél hosszabb ideig, dönthet, hogy kezdeményezze a feladatátvételt. 
 
3. Az a tény, hogy az adatok nem replikálódik, az azt jelenti, hogy jelenleg aktív munkamenetek nem lesznek replikálva. Ezenkívül duplikáltelem-észlelési és ütemezett üzenetek előfordulhat, hogy nem működik. Az új munkamenetek, ütemezett üzenetek és új ismétlődések fog működni. 

4. Egy összetett elosztott infrastruktúra feladatátvétele legalább egyszer [kipróbálható](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) . 

5. Entitások szinkronizálása körülbelül 50-100 entitást percenkénti némi időt is igénybe vehet.

## <a name="availability-zones"></a>Rendelkezésre állási zónák 

A Event Hubs standard SKU támogatja a [Availability Zones](../availability-zones/az-overview.md), amely az Azure-régiókban a hibáktól elkülönített helyet biztosít. 

> [!NOTE]
> Az Azure Event Hubs standard Availability Zones támogatása csak olyan [Azure-régiókban](../availability-zones/az-overview.md#services-support-by-region) érhető el, ahol elérhetők a rendelkezésre állási zónák.

Engedélyezheti a rendelkezésre állási zónák a csak az új névterek az Azure portal használatával. Az Event Hubs nem támogatja a meglévő névterek áttelepítésének. Miután engedélyezte a a névtérben nem tiltható le a zone redudancy.

![3][]

## <a name="next-steps"></a>Következő lépések

* A [githubon található minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) egy egyszerű munkafolyamaton keresztül megy át, amely egy geo-párosítást hoz létre, és feladatátvételt kezdeményez a vész-helyreállítási forgatókönyvek esetében.
* A [REST API hivatkozás](/rest/api/eventhub/disasterrecoveryconfigs) a Geo-vész-helyreállítási konfigurációt végző API-kat ismerteti.

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

- Bevezetés az Event Hubs használatába
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
