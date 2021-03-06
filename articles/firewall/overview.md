---
title: Mi az Azure Firewall?
description: Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 02/26/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 5f1672b53fa9bd8c8126fefd092e1be78a844ab9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241247"
---
# <a name="what-is-azure-firewall"></a>Mi az Azure Firewall?

![ICSA-tanúsítvány](media/overview/icsa-cert-firewall-small.png)

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Ez egy teljesen állapot-nyilvántartó tűzfal, amely beépített, magas rendelkezésre állású és korlátlan Felhőbeli méretezhetőséggel rendelkezik.

![Tűzfal áttekintése](media/overview/firewall-threat.png)

Központilag hozhatja létre, érvényesítheti és naplózhatja az alkalmazás- és hálózatelérési szabályzatokat az előfizetésekre és a virtuális hálózatokra vonatkozóan. Az Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózat erőforrásaihoz, így a külső tűzfalak azonosíthatják a virtuális hálózatból érkező forgalmat.  A szolgáltatás teljesen integrálva van az Azure Monitorral a naplózás és az elemzés érdekében.

Az Azure Firewall az alábbi szolgáltatásokat kínálja:

## <a name="built-in-high-availability"></a>Beépített magas rendelkezésre állás

A magas rendelkezésre állás beépített, így nincs szükség további terheléselosztó megadására, és nincs szükség a konfigurálásra.

## <a name="availability-zones"></a>Rendelkezésre állási zónák

A Azure Firewall az üzembe helyezés során konfigurálható úgy, hogy a nagyobb rendelkezésre állás érdekében több Availability Zones is kiterjedhet. A Availability Zones a rendelkezésre állás 99,99%-os üzemidőre növekszik. További információ: Azure Firewall [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Ha két vagy több Availability Zones van kiválasztva, a 99,99%-os rendelkezésre állási SLA-t ajánljuk.

A szolgáltatáshoz a 99,95%-os SLA-t használó szabványos szolgáltatási szabványnak megfelelően a Azure Firewallt is hozzárendelheti egy adott zónához.

Egy rendelkezésre állási zónában üzembe helyezett tűzfal esetében nincs további díj. A Availability Zoneshoz társított bejövő és kimenő adatátvitelek esetében azonban további költségek is rendelkezésre állnak. További információ: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/).

Azure Firewall Availability Zones a Availability Zones támogató régiókban érhetők el. További információ: [Mi a Availability Zones az Azure-ban?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Availability Zones csak az üzembe helyezés során állítható be. Meglévő tűzfal nem konfigurálható úgy, hogy tartalmazza a Availability Zones.

További információ a Availability Zonesről: [Mi az az Azure Availability Zones?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Korlátlan felhőalapú skálázhatóság

Az Azure Firewall akármeddig felskálázható a változó hálózati forgalom kezeléséhez, így a költségvetést nem szükséges a csúcsforgalomhoz igazítania.

## <a name="application-fqdn-filtering-rules"></a>Alkalmazások teljes tartománynevére vonatkozó szűrési szabályok

A kimenő HTTP/S-forgalom vagy az Azure SQL-forgalom (előzetes verzió) a teljes tartománynevek (FQDN) megadott listájára korlátozható, beleértve a Wild kártyákat is. Ez a szolgáltatás nem igényel SSL-megszakítást.

## <a name="network-traffic-filtering-rules"></a>Hálózati forgalomra vonatkozó szűrési szabályok

Központilag hozhat létre *engedélyező* vagy *tiltó* hálózatszűrési szabályokat forrás és cél IP-cím, port és protokoll alapján. Az Azure Firewall teljes mértékben állapotalapú, így képes megkülönböztetni különböző típusú kapcsolatok érvényes csomagjait. A szabályok több előfizetésen és virtuális hálózaton érvényesíthetők és naplózhatók.

## <a name="fqdn-tags"></a>FQDN-címkék

Az FQDN-címkékkel egyszerűen engedélyezheti a jól ismert Azure-szolgáltatások hálózati forgalmát a tűzfalon keresztül. Tegyük fel például, hogy engedélyezni kívánja a Windows Update hálózati forgalmát a tűzfalon keresztül. Létrehozhat egy alkalmazásszabályt, és hozzáadhatja a Windows Update címkéjét. A Windows Update hálózati forgalma ezután akadálytalanul áthaladhat a tűzfalon.

## <a name="service-tags"></a>Szolgáltatáscímkék

A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatási címkét, és nem adhatja meg, hogy mely IP-címek szerepeljenek a címkén belül. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

## <a name="threat-intelligence"></a>Fenyegetésészlelési intelligencia

A fenyegetésekkel kapcsolatos intelligencia-alapú szűrés engedélyezhető a tűzfal számára, hogy riasztást kapjon, és megtagadja a forgalmat az ismert kártékony IP-címekre és tartományokra. Az IP-címek és tartományok forrása a Microsoft Threat Intelligence-hírcsatorna.

## <a name="outbound-snat-support"></a>Kimenő SNAT-támogatás

A rendszer a kimenő virtuális hálózati forgalomhoz tartozó minden IP-címet lefordít az Azure Firewall nyilvános IP-címére (forráshálózati címfordítás, SNAT). Azonosíthatja és engedélyezheti a virtuális hálózatból a távoli internetes célhelyekre irányuló forgalmat. A Azure Firewall nem SNAT, ha a cél IP-cím egy [IANA RFC 1918-es](https://tools.ietf.org/html/rfc1918)magánhálózati IP-címtartomány. 

Ha a szervezete nyilvános IP-címtartományt használ a magánhálózatok számára, Azure Firewall a SNAT a AzureFirewallSubnet-ben lévő egyik tűzfal magánhálózati IP-címére irányítja át a forgalmat. A Azure Firewall konfigurálhatja úgy, hogy **ne** SNAT a nyilvános IP-címtartományt. További információ: [Azure Firewall SNAT magánhálózati IP-címtartományok](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Bejövő DNAT-támogatás

A tűzfal nyilvános IP-címére irányuló bejövő internetes hálózati forgalom le van fordítva (célként megadott hálózati címfordítás), és a virtuális hálózatok magánhálózati IP-címeire szűrve.

## <a name="multiple-public-ip-addresses"></a>Több nyilvános IP-cím

A tűzfallal több nyilvános IP-címet is hozzárendelhet (legfeljebb 100).

Ez a következő forgatókönyveket teszi lehetővé:

- **DNAT** – a háttér-kiszolgálókra több szabványos port-példányt is lefordíthat. Ha például két nyilvános IP-címmel rendelkezik, akkor mindkét IP-cím esetében lefordíthatja a 3389-es TCP-portot.
- **SNAT** – további portok érhetők el a kimenő SNAT-kapcsolatokhoz, ami csökkenti a SNAT-portok kimerülésének lehetséges lehetőségét. Ekkor Azure Firewall véletlenszerűen kiválasztja a forrás nyilvános IP-címét, amelyet a rendszer a kapcsolódáshoz használ. Ha a hálózaton bármilyen alsóbb rétegbeli szűrés van, engedélyeznie kell a tűzfalhoz társított összes nyilvános IP-címet.

## <a name="azure-monitor-logging"></a>Azure Monitor-naplózás

A rendszer minden eseményt integrál a Azure Monitorba, így lehetővé teszi a naplók archiválását egy Storage-fiókba, az események továbbítását az Event hub-ba, vagy elküldheti őket Azure Monitor naplókba.

## <a name="certifications"></a>Tanúsítványok

A Azure Firewall a Payment Card Industry (PCI), a Service Organization Controls (SOC), a Nemzetközi Szabványügyi Szervezet (ISO) és a ICSA Labs megfelelője. További információ: [Azure Firewall megfelelőségi tanúsítványok](compliance-certifications.md).


## <a name="known-issues"></a>Ismert problémák

Az Azure Firewall az alábbi ismert hibákkal rendelkezik:

|Probléma  |Leírás  |Kezelés  |
|---------|---------|---------|
A nem TCP/UDP-protokollokra (például ICMP) vonatkozó hálózati szűrési szabályok nem működnek az internetre irányuló forgalom esetében|A nem TCP/UDP-protokollokra vonatkozó hálózati szűrési szabályok nem működnek a nyilvános IP-címre vonatkozó forráshálózati címfordítással. A nem TCP/UDP-protokollok a küllők alhálózatai és a virtuális hálózatok között támogatottak.|Az Azure Firewall a Standard Load Balancert használja, [amely jelenleg nem támogatja a forráshálózati címfordítást az IP-protokollokon](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). A forgatókönyv egy későbbi kiadásban való támogatásának lehetőségeit vizsgálja.|
|A PowerShell és a CLI nem támogatja az ICMP-t|Az Azure PowerShell és a CLI nem támogatja az ICMP-t érvényes protokollként a hálózati szabályok között.|Az ICMP protokollt a Portálon és a REST API is használhatja protokollként. Hamarosan felvesszük az ICMP-t a PowerShellben és a CLI-ben.|
|Az FQDN-címkék protokoll: port megadását igénylik|Az FQDN-címkékkel rendelkező alkalmazás-szabályok port: protokoll-definíciót igényelnek.|A port:protokoll értékként használhat **https**-t. Azon dolgozunk, hogy ezt a mezőt nem kötelező megadni, ha FQDN-címkéket használunk.|
|A tűzfal más erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott|A tűzfal más erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott.|A funkció támogatása a közúti térképen is elérhető. Ahhoz, hogy egy tűzfalat áthelyezzen másik erőforráscsoportba vagy előfizetésbe, először törölnie kell az aktuális példányt, és újra létre kell hoznia az új erőforráscsoportban vagy előfizetésben.|
|A fenyegetések felderítésével kapcsolatos riasztások maszkolása is lehetséges|A kimenő szűréshez használt 80/443-as célként megadott hálózati szabályok a fenyegetések felderítésére vonatkozó riasztásokat észlelnek, ha csak riasztás módra vannak konfigurálva.|Hozzon létre kimenő szűrést az 80/443-hoz az alkalmazási szabályok használatával. Vagy módosítsa a fenyegetés intelligencia módot a **riasztás és a Megtagadás**értékre.|
|A Azure Firewall csak a névfeloldáshoz használja Azure DNS|Azure Firewall csak Azure DNS használatával oldja fel a teljes tartományneveket. Az egyéni DNS-kiszolgálók nem támogatottak. Más alhálózatokon nincs hatással a DNS-feloldásra.|Dolgozunk ennek a korlátozásnak a kihasználása érdekében.|
|Azure Firewall SNAT/DNAT nem működik a magánhálózati IP-címekhez|Azure Firewall SNAT/DNAT-támogatás az internetes kimenő/bejövő forgalomra korlátozódik. A SNAT/DNAT jelenleg nem működik a magánhálózati IP-címekhez. Tegyük fel például, hogy küllős volt.|Ez egy aktuális korlátozás.|
|Nem lehet eltávolítani az első nyilvános IP-konfigurációt|Minden Azure Firewall nyilvános IP-cím hozzá van rendelve egy *IP-konfigurációhoz*.  Az első IP-konfiguráció a tűzfal központi telepítése során lesz hozzárendelve, és általában a tűzfal alhálózatára mutató hivatkozást is tartalmaz (kivéve, ha explicit módon másképpen van konfigurálva a sablon központi telepítésen keresztül). Ezt az IP-konfigurációt nem lehet törölni, mert a tűzfal lefoglalása megtörtént. Továbbra is módosíthatja vagy eltávolíthatja az IP-konfigurációhoz társított nyilvános IP-címet, ha a tűzfalon legalább egy másik nyilvános IP-cím használható.|Ez az elvárt működés.|
|A rendelkezésre állási zónák konfigurálása csak az üzembe helyezés során lehetséges.|A rendelkezésre állási zónák konfigurálása csak az üzembe helyezés során lehetséges. A tűzfal telepítése után nem konfigurálható Availability Zones.|Ez az elvárt működés.|
|SNAT a bejövő kapcsolatokon|A DNAT kívül a tűzfal nyilvános IP-címén (bejövő) keresztül létesített kapcsolatok a címfordítást egyikéhez tartoznak. Ez a követelmény ma (aktív/aktív NVA esetén is) biztosítja a szimmetrikus útválasztást.|A HTTP/S eredeti forrásának megőrzése érdekében érdemes lehet [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) -fejléceket használni. Például olyan szolgáltatást használhat, mint például az [Azure bejárati ajtó](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) vagy az [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) a tűzfal előtt. A WAF az Azure bejárati ajtajának részeként is hozzáadhatja a tűzfalhoz.
|Az SQL FQDN szűrése csak proxy módban támogatott (1433-es port)|Azure SQL Database, Azure SQL Data Warehouse és Azure SQL felügyelt példány esetén:<br><br>Az előzetes verzióban az SQL FQDN-szűrés csak proxy módban támogatott (1433-es port).<br><br>Azure SQL-IaaS esetén:<br><br>Ha nem szabványos portokat használ, megadhatja ezeket a portokat az alkalmazási szabályokban.|Az SQL átirányítási módban, amely az alapértelmezett, ha az Azure-on keresztül csatlakozik, ehelyett a Azure Firewall hálózati szabályok részeként használhatja az SQL-szolgáltatás címkéjét.
|A 25-ös TCP-porton nem engedélyezett a kimenő forgalom| A 25-ös TCP-portot használó kimenő SMTP-kapcsolatok le vannak tiltva. A 25-ös port elsődlegesen a nem hitelesített e-mailek kézbesítéséhez használatos. Ez a virtuális gépek alapértelmezett platform-viselkedése. További információ: [a kimenő SMTP-kapcsolatokkal kapcsolatos problémák elhárítása az Azure-ban](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). A virtuális gépektől eltérően azonban jelenleg nem lehet engedélyezni ezt a funkciót Azure Firewallon.|Kövesse a javasolt módszert az e-mailek küldéséhez az SMTP hibaelhárítási cikkében leírtak szerint. Vagy zárja ki azt a virtuális gépet, amelynek a kimenő SMTP-hozzáférésre van szüksége az alapértelmezett útvonalról a tűzfalra, ehelyett konfigurálja a kimenő hozzáférést közvetlenül az internethez.
|Az aktív FTP nem támogatott|Az aktív FTP szolgáltatás le van tiltva Azure Firewall az FTP-PORT parancs használatával történő FTP-visszafordulási támadásokkal szembeni védelem érdekében.|Ehelyett használhatja a passzív FTP-t. A tűzfalon továbbra is explicit módon meg kell nyitnia a 20. és a 21. TCP-portot.

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon](tutorial-firewall-deploy-portal.md)
- [Azure Firewall üzembe helyezése sablon használatával](deploy-template.md)
- [Azure Firewall-tesztkörnyezet létrehozása](scripts/sample-create-firewall-test.md)
