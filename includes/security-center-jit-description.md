---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597940"
---
## <a name="attack-scenario"></a>Támadási forgatókönyv

A találgatásos támadások általában a felügyeleti portok megcélzására szolgálnak, hogy hozzáférjenek a virtuális gépekhez. Ha ez sikeres, a támadó átveheti a vezérlést a virtuális gépen, és megadhat egy lábát a környezetében.

A találgatásos támadásoknak való kitettség csökkentése az egyik lehetőség, hogy korlátozza a portok megnyitásának időtartamát. A felügyeleti portokat nem kell mindig megnyitnia. Csak akkor kell megnyitnia őket, amikor csatlakozik a virtuális géphez, például felügyeleti vagy karbantartási feladatok elvégzéséhez. Ha az igény szerinti hozzáférés engedélyezve van, a Security Center a [hálózati biztonsági csoport](../articles/virtual-network/security-overview.md#security-rules) (NSG) és a Azure Firewall szabályok használatával korlátozza a felügyeleti portok elérését, hogy a támadók ne tudják megcélozni azokat.

![Igény szerinti forgatókönyv](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Hogyan működik az JIT-hozzáférés?

Ha engedélyezve van az igény szerinti idő, Security Center az Azure-beli virtuális gépek felé irányuló bejövő forgalmat egy NSG-szabály létrehozásával zárolja. Válassza ki a virtuális gépen azokat a portokat, amelyeken a bejövő forgalom le lesz zárva. Ezeket a portokat az igény szerinti megoldás vezérli.

Amikor egy felhasználó hozzáférést kér egy virtuális géphez, Security Center ellenőrzi, hogy a felhasználó rendelkezik [-e szerepköralapú Access Control (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) engedélyekkel az adott virtuális géphez. Ha a kérést jóváhagyják, Security Center automatikusan konfigurálja a hálózati biztonsági csoportokat (NSG), és Azure Firewall, hogy engedélyezze a bejövő forgalmat a kiválasztott portokra és a kért forrás IP-címekre vagy tartományokra vonatkozóan a megadott időtartamig. Az idő lejárta után Security Center visszaállítja a NSG az előző állapotokra. A már létrehozott kapcsolatok azonban nem szakadnak meg.

 > [!NOTE]
 > Ha egy Azure Firewall mögötti virtuális géphez jóváhagy egy JIT hozzáférési kérelmet, akkor Security Center automatikusan módosítja a NSG és a tűzfal házirend-szabályait is. A megadott időtartamra vonatkozóan a szabályok engedélyezik a bejövő forgalmat a kiválasztott portokra és a kért forrás IP-címekre vagy tartományokra. Az idő elteltével Security Center visszaállítja a tűzfal és a NSG szabályait az előző állapotba.


## <a name="permissions-needed-to-configure-and-use-jit"></a>A JIT konfigurálásához és használatához szükséges engedélyek

| A felhasználók engedélyezése: | Beállított engedélyek|
| --- | --- |
| Egy virtuális gép JIT-szabályzatának konfigurálása vagy szerkesztése | *Rendelje hozzá ezeket a műveleteket a szerepkörhöz:*  <ul><li>A virtuális géphez társított előfizetés vagy erőforráscsoport hatókörén:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Egy előfizetés vagy egy virtuális gép erőforráscsoport hatókörén: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|JIT-hozzáférés kérése egy virtuális géphez | *Rendelje hozzá ezeket a műveleteket a felhasználóhoz:*  <ul><li>A virtuális géphez társított előfizetés vagy erőforráscsoport hatókörén:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>A virtuális géphez társított előfizetés vagy erőforráscsoport hatókörén:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Az előfizetés vagy az erőforráscsoport vagy a virtuális gép hatókörén:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Az előfizetés vagy az erőforráscsoport vagy a virtuális gép hatókörén:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|