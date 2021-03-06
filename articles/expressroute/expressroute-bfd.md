---
title: 'Azure-ExpressRoute: a BFD konfigurálása'
description: Ez a cikk a privát társviszony-létesítési ExpressRoute-Kapcsolatcsoportok keresztül BFD (kétirányú továbbítás észlelési) konfigurálása utasításokat tartalmaz.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 608b5e0011d4ed656ff61fec84a23f2fb22373b3
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080801"
---
# <a name="configure-bfd-over-expressroute"></a>BFD konfigurálása expressroute-on keresztül

A ExpressRoute támogatja a kétirányú továbbítási észlelést (BFD) a magán-és a Microsoft-partnereknél. A BFD ExpressRoute-en keresztüli engedélyezésével felgyorsíthatja a Microsoft Enterprise Edge-(MSEE-) eszközök és a ExpressRoute-áramkör (CE/PE) leállítására szolgáló útválasztók közötti kapcsolódási hibák észlelését. Ügyfélperemhálózat útválaszt eszközöknek vagy Partner útválasztási peremeszközökre ExpressRoute leállíthatja, (Ha a hiba történt a felügyelt 3. rétegbeli kapcsolatot szolgáltatással). Ez a dokumentum végigvezeti BFD és expressroute-on keresztül BFD engedélyezése szükséges.

## <a name="need-for-bfd"></a>BFD szükség

Az alábbi ábra bemutatja az ExpressRoute-kapcsolatcsoporton keresztül BFD engedélyezése előnyeit: [ ![1]][1]

Engedélyezheti az ExpressRoute-kapcsolatcsoport vagy a 2. rétegbeli kapcsolatokat vagy felügyelt 3. rétegbeli kapcsolatokat. Mindkét esetben ha az ExpressRoute kapcsolati útvonal egy vagy több 2. rétegbeli eszközök felelőssége minden olyan hivatkozás hibák észlelése az elérési út rejlik a felsőbb szintű BGP-t.

A MSEE eszközökön BGP életben tartási és fenntartási idő általában határozzák 60 és 180 másodperc jelölik. Ezért az akár időbe hivatkozás hiba után 3 percenként, majd felderítéséhez hivatkozásra a hiba, és váltson a forgalmat a másodlagos kapcsolathoz.

A BGP időzítők alacsonyabb BGP életben tartási és fenntartási-idő az az ügyfél társviszony-létesítési edge-eszköz konfigurálásával szabályozhatja. Ha a BGP időzítők nem egyeznek a két társviszony-létesítési eszközök között, a BGP-munkamenetben az egyenrangú társak közötti alacsonyabb időzítő értékét használja. A BGP életben tartási akár három másodperc alatt, és a visszatartás idő több tíz másodpercet sorrendjében is beállítható. A BGP-időzítők agresszív beállítása azonban kevésbé előnyösebb, mert a protokoll intenzív folyamat.

Ebben a forgatókönyvben BFD segítségével. BFD biztosít alacsony terheléssel járó hivatkozás kiszűrésénél az adott subsecond idő alatt. 


## <a name="enabling-bfd"></a>BFD engedélyezése

BFD alapértelmezés szerint minden újonnan létrehozott ExpressRoute privát társviszony-létesítési illesztői az msee-k van konfigurálva. Ezért a BFD engedélyezéséhez egyszerűen konfigurálnia kell a BFD-t a CEs/PEs-ben (mindkettő az elsődleges és a másodlagos eszközön egyaránt). A BFD konfigurálása kétlépéses folyamat: be kell állítania a BFD a csatolón, majd csatolni kell a BGP-munkamenethez.

Alább látható például a CE/PE (Cisco IOS XE használatával) konfiguráció. 

    interface TenGigabitEthernet2/0/0.150
      description private peering to Azure
      encapsulation dot1Q 15 second-dot1q 150
      ip vrf forwarding 15
      ip address 192.168.15.17 255.255.255.252
      bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
      address-family ipv4 vrf 15
        network 10.1.15.0 mask 255.255.255.128
        neighbor 192.168.15.18 remote-as 12076
        neighbor 192.168.15.18 fall-over bfd
        neighbor 192.168.15.18 activate
        neighbor 192.168.15.18 soft-reconfiguration inbound
      exit-address-family

>[!NOTE]
>Alapján egy már meglévő magánhálózati társviszony-létesítés; BFD engedélyezése a társviszony-létesítés alaphelyzetbe kell. Lásd: ExpressRoute-társítások [alaphelyzetbe állítása][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD időzítő egyeztetése

Közötti BFD kapcsolatba szaktársakkal a két társaknak lassabb határozza meg, az átviteli sebességet. Msee BFD átviteli/fogadása időközök 300 ezredmásodpercben vannak állítva. Bizonyos esetekben az időköz: 750 ezredmásodperc magasabb értékét. A magasabb értékek beállításával kényszerítheti a hosszabb; intervallumok de nem rövidebb.

>[!NOTE]
>Ha a Geo-redundáns ExpressRoute-áramköröket konfigurálta, vagy a helyek közötti IPSec VPN-kapcsolatot használja biztonsági mentésként; a BFD engedélyezése a ExpressRoute kapcsolódási hibája után gyorsabban segít a feladatátvételben. 
>

## <a name="next-steps"></a>További lépések

További információ vagy a Súgó tekintse meg az alábbi hivatkozásokat:

- [Az ExpressRoute-kapcsolatcsoport létrehozása és módosítása][CreateCircuit]
- [ExpressRoute-áramkör útválasztásának létrehozása és módosítása][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD felgyorsítja hiba levonása kötésidő"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






