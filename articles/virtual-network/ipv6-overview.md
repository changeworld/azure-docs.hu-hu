---
title: Az IPv6 for Azure Virtual Network (előzetes verzió) áttekintése
titlesuffix: Azure Virtual Network
description: IPv6-végpontok és adatelérési útvonalak IPv6-leírása egy Azure-beli virtuális hálózaton.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/19/2019
ms.author: kumud
ms.openlocfilehash: 9214886f468a4a052328a99289845361a059b650
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780079"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Mi az az IPv6 for Azure Virtual Network? (Előzetes verzió)

Az IPv6 for Azure Virtual Network (VNet) lehetővé teszi, hogy az Azure-ban IPv6-és IPv4-kapcsolaton keresztül is üzemeltetheti az alkalmazásokat a virtuális hálózaton belül és az internetről. A nyilvános IPv4-címek kimerítése miatt az új mobilitási és eszközök internetes hálózatai hálózatok (IoT) gyakran az IPv6-ra épülnek. Még a hosszú ideig létesített ISP-és mobil hálózatok is át lettek alakítva IPv6-ra. Az IPv4-alapú szolgáltatások a meglévő és a feltörekvő piacokon is valós hátrányban találhatják magukat. A Dual stack IPv4/IPv6-kapcsolat lehetővé teszi, hogy az Azure által üzemeltetett szolgáltatások belépjék ezt a technológiai rést olyan globálisan elérhető, kettős halmozású szolgáltatásokkal, amelyek könnyedén csatlakozhatnak a meglévő IPv4-hez és az új IPv6-eszközökhöz és hálózatokhoz.

Az Azure eredeti IPv6-kapcsolata megkönnyíti az Azure-ban üzemeltetett alkalmazások számára a kettős verem (IPv4/IPv6) internetkapcsolatának megadását. Lehetővé teszi a virtuális gépek egyszerű üzembe helyezését elosztott terhelésű IPv6-kapcsolattal a bejövő és a kimenő kezdeményezett kapcsolatokhoz. Ez a funkció továbbra is elérhető, és további információk [itt](../load-balancer/load-balancer-ipv6-overview.md)érhetők el.
Az IPv6 az Azure Virtual networkhez sokkal teljesebb funkcionalitással rendelkezik, így teljes körű IPv6-megoldási architektúrák helyezhetők üzembe az Azure-ban.

> [!Important]
> Az Azure Virtual Networkhez készült IPv6 jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az alábbi ábrán egy egyszerű, kettős verem (IPv4/IPv6) üzemelő példány látható az Azure-ban:

![IPv6 hálózati telepítési diagram](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Előnyök

IPv6 az Azure VNET előnyei:

- Segít kiterjeszteni az Azure által üzemeltetett alkalmazások elérhetőségét a növekvő mobil-és eszközök internetes hálózata piacokon.
- A kettős halmozott IPv4-/IPv6-alapú virtuális gépek maximális rugalmasságot biztosítanak a szolgáltatás számára. Egyetlen szolgáltatási példány is csatlakozhat IPv4-és IPv6-kompatibilis internetes ügyfelekhez.
- Hosszú távú, stabil Azure-alapú virtuális gépek közötti IPv6-kapcsolaton alapul.
- Alapértelmezés szerint biztonságos, mivel az internethez való IPv6-kapcsolat csak akkor áll fenn, ha explicit módon megkéri azt a központi telepítésben.

## <a name="capabilities"></a>Képességek

Az IPv6 for Azure VNet a következő képességeket tartalmazza:

- Az Azure-ügyfelek meghatározhatják saját IPv6-alapú virtuális hálózati címüket, hogy megfeleljenek alkalmazásaik, ügyfeleik igényeinek, vagy zökkenőmentesen integrálhatók a helyszíni IP-helyükbe.
- A Dual stack alhálózatokkal rendelkező kettős verem (IPv4 és IPv6) virtuális hálózatok lehetővé teszik, hogy az alkalmazások IPv4-és IPv6-erőforrásokkal csatlakozhassanak a virtuális hálózatban vagy az interneten.
    > [!IMPORTANT]
    > Az IPv6-alhálózatok méretének pontosan/64-nek kell lennie.  Ez biztosítja a jövőbeli kompatibilitást, ha úgy dönt, hogy engedélyezi az alhálózat útválasztását egy helyszíni hálózatra, mivel egyes útválasztók csak a/64 IPv6-útvonalakat fogadják el.  
- Az erőforrások védelme a hálózati biztonsági csoportokra vonatkozó IPv6-szabályokkal.
    - Az Azure platform elosztott szolgáltatásmegtagadási (DDoS) védelmi szolgáltatásai kiterjeszthetők az internetre irányuló nyilvános IP-címekre
- Testre szabhatja az IPv6-forgalom útválasztását a virtuális hálózaton a felhasználó által megadott útvonalakkal – különösen a hálózati virtuális berendezések kihasználása az alkalmazások bővítéséhez.
- A Linux és a Windows Virtual Machines egyaránt használhatja az IPv6-ot az Azure VNET
- [Standard IPv6 nyilvános Load Balancer](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) támogatás rugalmas, méretezhető alkalmazások létrehozásához, amelyek többek között a következők:
    - Opcionális IPv6-alapú állapot-mintavétel, amely meghatározza, hogy mely háttérbeli készlet-példányok állapota, így az új folyamatok fogadására is képes.
    - Opcionális kimenő szabályok, amelyek teljes körű deklaratív vezérlést biztosítanak a kimenő kapcsolaton keresztül az adott igényeknek megfelelően méretezhető és hangolható módon.
    - Nem kötelező több előtér-konfiguráció, amely lehetővé teszi, hogy egyetlen terheléselosztó több IPv6 nyilvános IP-címet használjon – ugyanezt a frontend-protokollt és portot újra felhasználhatja a frontend-címek között.
    - A választható IPv6-portok újra felhasználhatók a háttérbeli példányokon a terheléselosztási szabályok *lebegőpontos IP-* funkciójának használatával 
- [Standard IPv6 belső Load Balancer](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) támogatás rugalmas többrétegű alkalmazások létrehozásához az Azure virtuális hálózatok-n belül.  
- Alapszintű IPv6 nyilvános Load Balancer támogatása az örökölt központi telepítésekkel való kompatibilitáshoz
- A [fenntartott IPv6 nyilvános IP-címek és címtartományok](ipv6-public-ip-address-prefix.md) stabil és kiszámítható IPv6-címeket biztosítanak, amelyek megkönnyítik az Azure által üzemeltetett alkalmazások engedélyezését vállalata és ügyfelei számára.
- Példányszintű nyilvános IP IPv6-alapú internetkapcsolatot biztosít közvetlenül az egyes virtuális gépek számára.
- [IPv6 hozzáadása meglévő IPv4-alapú központi telepítésekhez](ipv6-add-to-existing-vnet-powershell.md)– ez a funkció lehetővé teszi, hogy a központi telepítések újbóli létrehozása nélkül egyszerűen hozzá lehessen adni IPv6-kapcsolatot a meglévő IPv4-alapú telepítésekhez.  A folyamat során az IPv4-alapú hálózati forgalom nem befolyásolja, hogy az alkalmazástól és az operációs rendszertől függően akár élő szolgáltatásokhoz is hozzáadhat IPv6-t.    
- Lehetővé teszi, hogy az internetes ügyfelek zökkenőmentesen férjenek hozzá a kettős stack-alkalmazásokhoz, és a Azure DNS IPv6-(AAAA-) rekordokat támogatják. 
- Hozzon létre olyan kettős stack-alkalmazásokat, amelyek automatikusan méretezhetők a terheléshez az IPv6-os virtuálisgép-méretezési csoportokkal.
- [Virtual Network (VNET)](virtual-network-peering-overview.md) – mind a regionális, mind a globális együttműködésben – lehetővé teszi a kettős stack-virtuális hálózatok összekapcsolását – az IPv4-és IPv6-végpontokat a seemlessly lévő virtuális gépeken is kommunikálni tudnak egymással. Akár két, akár csak IPv4-alapú virtuális hálózatok is használhat, miközben az üzemelő példányokat kettős verembe helyezi át. 
- Az IPv6-hibaelhárítás és-diagnosztika elérhető terheléselosztási metrikákkal/riasztásokkal és olyan Network Watcher szolgáltatásokkal, mint a csomagok rögzítése, a NSG, a kapcsolat hibaelhárítása és a kapcsolatok figyelése.   

## <a name="scope"></a>Hatókör
Az IPv6 for Azure VNET egy alapszintű szolgáltatáskészlet, amely lehetővé teszi, hogy az ügyfelek Dual stack-(IPv4-és IPv6-) alkalmazásokat működtessenek az Azure-ban.  További IPv6-támogatást kívánunk hozzáadni több Azure hálózati szolgáltatáshoz az idő múlásával, és végül az Azure Pásti szolgáltatásainak kettős stack verzióit kínáljuk, de addig az összes Azure Pásti-szolgáltatás a kettős verem Virtual Machines IPv4-végpontján keresztül érhető el.   

## <a name="limitations"></a>Korlátozások
Az Azure Virtual Network jelenlegi IPv6-kiadása a következő korlátozásokkal rendelkezik:
- Az Azure Virtual Network (előzetes verzió) IPv6 az összes globális Azure-régióban elérhető, de csak a globális Azure-ban – a kormányzati felhőkben még nem.
- A ExpressRoute és a VPN-átjárók nem használhatók olyan VNET, amelyeken engedélyezve van az IPv6, vagy közvetlenül a "UseRemoteGateway" kapcsolattal. 
- Az Azure platform (ak stb.) nem támogatja az IPv6-alapú kommunikációt a tárolók esetében.  

## <a name="pricing"></a>Díjszabás

Az IPv6-os Azure-erőforrások és a sávszélesség az IPv4-vel megegyező díjszabás szerint történik. Az IPv6 esetében nincs további vagy eltérő díj. A [nyilvános IP-címekre](https://azure.microsoft.com/pricing/details/ip-addresses/), a [hálózati sávszélességre](https://azure.microsoft.com/pricing/details/bandwidth/)vagy a [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)díjszabására vonatkozó részletekért tekintse meg a következőt:.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [helyezhet üzembe egy IPv6-alapú kettős verem-alkalmazást Azure PowerShell használatával](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Ismerje meg, hogyan [helyezhet üzembe egy IPv6-alapú kettős verem-alkalmazást az Azure CLI használatával](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Ismerje meg, hogyan [helyezhet üzembe egy IPv6-alapú kettős verem-alkalmazást Resource Manager-sablonokkal (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
