---
title: 'BGP és Azure VPN Gateway: áttekintés'
description: Ez a cikk bemutatja, hogyan használható a BGP az Azure VPN Gateway megoldással együtt.
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/25/2019
ms.author: yushwang
ms.openlocfilehash: 0b4bb7ed90225fcb52ea170c07be2b57f8afbafe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279324"
---
# <a name="about-bgp-with-azure-vpn-gateway"></a>A BGP és az Azure VPN Gateway
Ez a cikk az Azure VPN Gateway BGP (Border Gateway Protocol) támogatásának áttekintését tartalmazza.

A BGP az interneten gyakran használt szabványos útválasztási protokoll az útválasztási és elérhetőségi információcserére két vagy több hálózat között. Azure Virtual Network-környezetben a BGP teszi lehetővé az Azure VPN Gateway átjárók és a helyszíni VPN-eszközök (más néven BGP-társak vagy -szomszédok) számára az információcserét az „útvonalakat” illetően. Ezáltal mindkét átjáró ismerni fogja az érintett átjárókon és útválasztókon áthaladó előtagok rendelkezésre állási és elérhetőségi információit. A BGP lehetővé teszi a több hálózat közötti tranzit útválasztást azon útvonalak propagálásával az összes többi BGP-társ számára, amelyeket a BGP-átjáró az egyik BGP-társtól vesz át. 

## <a name="why"></a>Miért érdemes a BGP-t használni?
A BGP egy olyan opcionális szolgáltatás, amely az Azure útvonalalapú VPN-átjáróival együtt használható. A szolgáltatás engedélyezése előtt arról is meg kell győződni, hogy a helyszíni VPN-eszközök támogatják a BGP használatát. Az Azure VPN-átjáróit és a helyszíni VPN-eszközöket a BGP alkalmazása nélkül is tovább használhatja. Mindez egyenértékű a statikus (BGP nélküli) útvonalak dinamikus (BGP-vel történő) útválasztással *szembeni* alkalmazásával a hálózatok és az Azure között.

A BGP használata számos előnyt és új képességet biztosít:

### <a name="prefix"></a>Automatikus és rugalmas előtagok frissítéseinek támogatása
A BGP használatakor kizárólag egy minimális előtag megadására van szükség egy adott BGP-társ számára, az IPsec S2S VPN-alagúton keresztül. Lehet ez olyan kicsi is, mint a helyszíni VPN-eszközhöz tartozó BGP-társ IP-címének gazdaelőtagja (/32-es előtag). Szabályozhatja az Azure-on meghirdetett helyszíni hálózati előtagok körét az Azure Virtual Network hozzáférésének biztosításához.

Olyan nagyobb előtagokat is megadhat, amelyek tartalmazhatnak néhány VNet-címet, például egy nagy magánhálózati IP-címtartományt (például 10.0.0.0/8). Vegye figyelembe, hogy az előtagok nem lehetnek azonosak a VNet előtagjainak egyikével sem. A rendszer elutasítja a virtuális hálózat előtagjaival azonos útvonalakat.

### <a name="multitunnel"></a>Több alagút támogatása egy VNet és egy helyszíni hely között, automatikus feladatátvételsel a BGP alapján
Ugyanazon a helyen több kapcsolat is létesíthető az Azure virtuális hálózat és a helyszíni VPN-eszközök között. Ezen képesség révén több alagút (elérési út) létesíthető a két hálózat között, aktív-aktív konfiguráció formájában. Ha az egyik alagút le van választva, a rendszer visszavonja a megfelelő útvonalakat a BGP-n keresztül, és a forgalom automatikusan átvált a többi alagútra.

A következő ábra erre a magas rendelkezésre állású beállításra mutat egyszerű példát:

![Több aktív elérési út](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="transitrouting"></a>A helyszíni hálózatok és több Azure-virtuális hálózatok közötti tranzit Útválasztás támogatása
A BGP több átjáró számára biztosítja a különböző hálózatokról származó előtagok megtanulásának és propagálásának lehetőségét, és hogy azok közvetlen vagy közvetett módon csatlakoznak-e. Ezzel engedélyezhető az Azure VPN-átjárók használatával történő tranzit útválasztás a helyszínek között vagy több Azure virtuális hálózaton.

A következő ábra egy olyan, több elérési úttal rendelkező, többugrásos topológiára mutat példát, amely két helyszíni hálózat közötti tranzit adatátvitelre képes, Azure VPN-átjárókon keresztül, a Microsoft Networks keretein belül:

![Többugrásos átvitel](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="faq"></a>BGP – GYAKORI KÉRDÉSEK
[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="next-steps"></a>További lépések
A BGP helyek és virtuális hálózatok közötti kapcsolathoz történő konfigurálásának lépéseit [A BGP használatának első lépései Azure VPN-átjárókon](vpn-gateway-bgp-resource-manager-ps.md) című részben tekintheti meg.

