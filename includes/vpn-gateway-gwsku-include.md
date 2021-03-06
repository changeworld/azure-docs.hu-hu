---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085245"
---
Egy virtuális hálózati átjáró létrehozásakor meg kell adni a használni kívánt termékváltozatot. Válassza ki a számítási feladatok, az átviteli sebesség, a funkciók és a szolgáltatói szerződés igényeinek megfelelő termékváltozatot. A Azure Availability Zones virtuális hálózati átjárói SKU-ban tekintse meg a [Azure Availability Zones Gateway SKU](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md)-ket.

###  <a name="benchmark"></a>Átjáró-termékváltozatok alagút, kapcsolat és átviteli sebesség szerint

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> A VpnGw SKU-ban (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 és VpnGw5AZ) csak a Resource Manager-alapú üzemi modellben támogatottak. A klasszikus virtuális hálózatok továbbra is a régi (örökölt) SKU-ket használják.
>  * További információ az örökölt átjárók (alapszintű, standard és HighPerformance) használatával kapcsolatban: [VPN Gateway SKU-EK (örökölt SKU-EK) használata](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
>  * A ExpressRoute átjárók esetében lásd: [Virtual Network átjárók a ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="feature"></a>Átjárók SKU-ként a szolgáltatáskészlet alapján

Az új VPN Gateway SKU-ra egyszerűsíti az átjárók által kínált szolgáltatáskészlet-készleteket:

| **Termékváltozat**| **Szolgáltatások**|
| ---    | ---         |
|**Alapszintű** (* *)   | **Útvonal-alapú VPN**: 10 alagút a S2S/kapcsolatok számára; a P2S nem rendelkezik RADIUS-hitelesítéssel; nincs IKEv2 a P2S<br>**Házirend alapú VPN**: (IKEv1): 1 S2S/kapcsolati alagút; nincs P2S|
| **Az összes Generation1 és Generation2 SKU, kivéve az alapszintű** | **Route-alapú VPN**: legfeljebb 30 alagút (*), P2S, BGP, aktív-aktív, egyéni IPSec/IKE-házirend, EXPRESSROUTE/VPN együttes létezése |
|        |             |

(*) A "PolicyBasedTrafficSelectors" konfigurálható úgy, hogy egy Route-alapú VPN-átjárót több helyszíni házirend-alapú tűzfallal csatlakoztasson. További részletekért tekintse meg a [VPN-átjárók több helyszíni házirendalapú VPN-eszközhöz való csatlakoztatása a PowerShellel](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) című cikket.

(\*\*) Az alapszintű SKU egy örökölt SKU-nak minősül. Az alapszintű SKU bizonyos szolgáltatási korlátozásokkal rendelkezik. Alapszintű SKU-t használó átjárót nem lehet átméretezni az új átjárók egyikére, ehelyett egy új SKU-ra kell váltania, amely magában foglalja a VPN-átjáró törlését és újbóli létrehozását.

###  <a name="workloads"></a>Gateway SKU-gyártás és fejlesztési-tesztelési feladatok

A SLA-kat és a szolgáltatáskészlet-készletekben mutatkozó különbségek miatt a következő SKU-ket ajánljuk a termeléshez és a dev-testhez:

| **Számítási feladat**                       | **Termékváltozatok**               |
| ---                                | ---                    |
| **Termelés, kritikus fontosságú számítási feladatok** | Az összes Generation1 és Generation2 SKU, kivéve az alapszintű |
| **Dev-test vagy a koncepció igazolása**   | Alapszintű (* *)                 |
|                                    |                        |

(\*\*) Az alapszintű SKU egy örökölt SKU-nak minősül, és szolgáltatási korlátozásokkal rendelkezik. Az alapszintű SKU használata előtt ellenőrizze, hogy az Ön által igényelt szolgáltatás támogatott-e.

Ha a régi SKU-t (örökölt) használja, a termelési SKU-javaslatok standard és HighPerformance. A régi SKU-ra vonatkozó információkkal és utasításokkal kapcsolatban lásd: [átjáró SKU-i (örökölt)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
