---
title: Azure Service Bus-alkalmazások elszigetelése az kimaradások és a katasztrófák ellen
description: Ez a cikk az alkalmazások lehetséges Azure Service Bus kimaradás elleni védeleméhez nyújt technikákat.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 2a7f5d5eacb2d03e64ae95d34e1cf0bd37bbc7f2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259252"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Ajánlott eljárások az alkalmazások Service Bus kimaradások és katasztrófák elleni elszigeteléséhez

A kritikus fontosságú alkalmazások működésének folyamatosnak kell lennie, még a nem tervezett kimaradások vagy katasztrófák jelenléte esetén is. Ez a cikk azokat a technikákat ismerteti, amelyekkel Service Bus-alkalmazásokat lehet védeni a lehetséges szolgáltatás-kimaradások vagy a katasztrófák ellen.

A rendszer leállást határoz meg, mert a Azure Service Bus ideiglenes nem érhető el. A leállás a Service Bus egyes összetevőit érintheti, például az üzenetküldési tárolót, vagy akár a teljes adatközpontot is. A probléma javítása után Service Bus újra elérhetővé válik. A leállás általában nem okoz üzenetet vagy más adatvesztést. Egy adott üzenetküldési tároló nem érhető el egy példa az összetevő meghibásodására. Egy adatközpontra kiterjedő kimaradás például az adatközpont meghibásodása, vagy hibás Datacenter hálózati kapcsoló. A leállás néhány perctől akár néhány napig is eltarthat.

A katasztrófa egy Service Bus skálázási egység vagy adatközpont végleges elvesztéseként van meghatározva. Előfordulhat, hogy az adatközpont újra elérhetővé válik. A katasztrófák általában egy vagy több üzenet vagy más adatbázis elvesztését okozzák. Ilyen katasztrófák például a következők: tűz, árvíz vagy földrengés.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Kimaradások és katasztrófák elleni védelem – Service Bus Premium
A magas rendelkezésre állás és a vész-helyreállítási fogalmak közvetlenül a Azure Service Bus Premium csomagba vannak építve, mindkettő ugyanazon a régión (Availability Zonesn keresztül) és különböző régiókban (a földrajzi katasztrófa utáni helyreállításon keresztül).

### <a name="geo-disaster-recovery"></a>Földrajzi katasztrófa utáni helyreállítás

A Service Bus Premium a Geo-vész-helyreállítást támogatja a névtér szintjén. További információ: [Azure Service Bus földrajzi katasztrófa utáni helyreállítás](service-bus-geo-dr.md). A csak [prémium SKU](service-bus-premium-messaging.md) -hoz elérhető vész-helyreállítási funkció a metaadatok vész-helyreállítását valósítja meg, és az elsődleges és másodlagos vész-helyreállítási névterekre támaszkodik.

### <a name="availability-zones"></a>Rendelkezésre állási zónák

A Service Bus Premium SKU támogatja a [Availability Zones](../availability-zones/az-overview.md), amely az ugyanazon az Azure-régióban található, hibátlanul elszigetelt helyszíneket biztosít.

> [!NOTE]
> A prémium szintű Azure Service Bus Availability Zones támogatása csak olyan Azure- [régiókban](../availability-zones/az-overview.md#services-support-by-region) érhető el, ahol rendelkezésre áll a rendelkezésre állási zónák.

Engedélyezheti a rendelkezésre állási zónák a csak az új névterek az Azure portal használatával. A Service Bus nem támogatja a meglévő névterek áttelepítését. Miután engedélyezte a a névtérben nem tiltható le a zone redudancy.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Kimaradások és katasztrófák elleni védelem – Service Bus standard
Az adatközpont-kimaradások elleni rugalmasság érdekében a szabványos üzenetkezelési csomag használata esetén Service Bus két módszert támogat: az *aktív* és a *passzív* replikálást. Minden megközelítés esetében, ha egy adott üzenetsor vagy témakör elérhető marad egy adatközpont-leállás jelenlétében, akkor mindkét névtérben létrehozhatja. Mindkét entitás ugyanazzal a névvel rendelkezhet. Az elsődleges várólista például elérhető a **contosoPrimary.servicebus.Windows.net/myQueue**alatt, míg a másodlagos párja a **contosoSecondary.servicebus.Windows.net/myQueue**területen érhető el.

>[!NOTE]
> Az **aktív replikálás** és a **passzív replikálás** beállítása általános célú megoldások, és nem a Service Bus specifikus szolgáltatásai. A replikációs logika (2 különböző névtérnek való küldés) a küldő alkalmazásokban él, és a fogadónak egyéni logikával kell rendelkeznie az ismétlődő észleléshez.

Ha az alkalmazás nem igényel állandó küldő és fogadó közötti kommunikációt, az alkalmazás tartós ügyféloldali várólistát valósíthat meg az üzenetek elvesztésének megakadályozásához és a küldőnek az átmeneti Service Bus hibák elleni védelméhez.

### <a name="active-replication"></a>Aktív replikáció
Az aktív replikáció mindkét névtérben entitásokat használ az összes művelethez. Minden olyan ügyfél, amely üzenetet küld, két példányban küldi el ugyanazt az üzenetet. Az első másolatot az elsődleges entitás (például **contosoPrimary.servicebus.Windows.net/Sales**) küldi el a rendszer, az üzenet második másolatát pedig elküldi a másodlagos entitásnak (például **contosoSecondary.servicebus.Windows.net/Sales**).

Az ügyfél mindkét várólistából fogad üzeneteket. A fogadó feldolgozza az üzenet első másolatát, a másodikat pedig letiltja. A duplikált üzenetek kihagyásához a küldőnek egyedi azonosítóval kell megcímkézni az összes üzenetet. Az üzenet mindkét példányát ugyanazzal az azonosítóval kell megjelölni. Az üzenet címkézéséhez használhatja a [BrokeredMessage. MessageID][BrokeredMessage.MessageId] vagy a [BrokeredMessage. label][BrokeredMessage.Label] tulajdonságot, illetve az egyéni tulajdonságot is. A fogadónak meg kell őriznie a már fogadott üzenetek listáját.

A [Service Bus standard szintű][Geo-replication with Service Bus Standard Tier] mintákkal történő földrajzi replikálás az üzenetkezelési entitások aktív replikálását mutatja be.

> [!NOTE]
> Az aktív replikálási megközelítés megduplázza a műveletek számát, ezért ez a megközelítés magasabb költségeket eredményezhet.
> 
> 

### <a name="passive-replication"></a>Passzív replikálás
A hibamentes esetben a passzív replikálás csak a két üzenetküldési entitás egyikét használja. Az ügyfél elküldi az üzenetet az aktív entitásnak. Ha az aktív entitáson végrehajtott művelet meghiúsul, és az aktív entitást futtató adatközpontot jelző hibakód nem érhető el, akkor az ügyfél az üzenet másolatát küldi el a biztonsági mentési entitásnak. Ekkor az aktív és a biztonsági mentési entitások váltanak ki szerepköröket: a küldő ügyfél úgy tekinti a régi aktív entitást, hogy az új biztonsági mentési entitás legyen, és a régi biztonsági mentési entitás az új aktív entitás. Ha mindkét küldési művelet meghiúsul, a két entitás szerepkörei változatlanok maradnak, és a rendszer hibát ad vissza.

Az ügyfél mindkét várólistából fogad üzeneteket. Mivel előfordulhat, hogy a fogadó két példányban kapja meg ugyanazt az üzenetet, a fogadónak meg kell szüntetnie az ismétlődő üzeneteket. Az ismétlődéseket az aktív replikációval megegyező módon tilthatja le.

Általánosságban elmondható, hogy a passzív replikáció gazdaságosabb, mint az aktív replikáció, mivel a legtöbb esetben csak egy műveletet hajt végre. A késés, az átviteli sebesség és a pénzügyi díj megegyezik a nem replikált helyzettel.

A passzív replikáció használatakor a következő esetekben az üzenetek elvesznek vagy kétszer is fogadhatók:

* **Üzenet késleltetése vagy elvesztése**: tegyük fel, hogy a küldő sikeresen elküldött egy M1-es üzenetet az elsődleges várólistába, majd a várólista elérhetetlenné válik, mielőtt a fogadó megkapja az M1-et. A küldő egy későbbi üzenetet küld a másodlagos várólistának. Ha az elsődleges várólista átmenetileg nem érhető el, a fogadó megkapja az M1-et, miután a várólista újra elérhetővé válik. Vészhelyzet esetén előfordulhat, hogy a fogadó soha nem kapja meg az M1-et.
* **Ismétlődő fogadás**: tegyük fel, hogy a küldő egy üzenetet küld az elsődleges várólistának. Service Bus sikeresen feldolgozza az m-t, de nem küld választ. Miután a küldési művelet túllépte az időkorlátot, a küldő egy azonos másolatot küld az m-ről a másodlagos várólistára. Ha a fogadó el tudja fogadni az m első példányát, mielőtt az elsődleges várólista elérhetetlenné válik, a fogadó körülbelül egy alkalommal megkapja az m másolatát. Ha a fogadó nem tudja fogadni az m első példányát, mielőtt az elsődleges várólista elérhetetlenné válik, a fogadó kezdetben csak az m második példányát kapja meg, de az elsődleges várólista elérhetővé válása után egy második példányt kap az m-től.

A [Service Bus standard szintű geo-replikáció][Geo-replication with Service Bus Standard Tier] az üzenetkezelési entitások passzív replikálását mutatja be.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Továbbítási végpontok védelme adatközpont-kimaradások vagy katasztrófák ellen
[Azure Relay](../service-bus-relay/relay-what-is-it.md) végpontok földrajzi replikálása lehetővé teszi egy olyan szolgáltatás számára, amely egy továbbító végpontot tesz elérhetővé Service Bus kimaradások jelenlétében. A földrajzi replikálás eléréséhez a szolgáltatásnak két továbbító végpontot kell létrehoznia különböző névterekben. A névtereknek különböző adatközpontokban kell lenniük, és a két végpontnak eltérő névvel kell rendelkeznie. Egy elsődleges végpont például elérhető a **contosoPrimary.servicebus.Windows.net/myPrimaryService**alatt, míg a másodlagos párja a **contosoSecondary.servicebus.Windows.net/mySecondaryService**területen érhető el.

A szolgáltatás ezután mindkét végponton figyeli a szolgáltatást, és az ügyfél bármelyik végponton keresztül hívhatja a szolgáltatást. Egy ügyfélalkalmazás véletlenszerűen kiválasztja az egyik továbbítót elsődleges végpontként, és elküldi a kérést az aktív végpontnak. Ha a művelet hibakód miatt meghiúsul, ez a hiba azt jelzi, hogy a továbbítási végpont nem érhető el. Az alkalmazás megnyit egy csatornát a biztonsági mentési végponthoz, és újra kiadja a kérést. Ekkor az aktív és a biztonsági mentési végpontok kapcsolói szerepkörök: az ügyfélalkalmazás a régi aktív végpontot tekinti az új biztonsági mentési végpontnak, a régi biztonsági mentési végpont pedig az új aktív végpont lesz. Ha mindkét küldési művelet meghiúsul, a két entitás szerepkörei változatlanok maradnak, és a rendszer hibát ad vissza.

## <a name="next-steps"></a>Következő lépések
A vész-helyreállítással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Azure Service Bus geo-vész-helyreállítás](service-bus-geo-dr.md)
* [Azure SQL Database üzletmenet folytonossága][Azure SQL Database Business Continuity]
* [Rugalmas alkalmazások tervezése az Azure számára][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
