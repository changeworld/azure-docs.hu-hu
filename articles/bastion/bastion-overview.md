---
title: Azure-megerősített | Microsoft Docs
description: Az Azure Bastion megismerése
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 01/31/2020
ms.author: cherylmc
ms.openlocfilehash: e995cba1c2ba06333d7bee507182693002cf4bbf
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989491"
---
# <a name="what-is-azure-bastion"></a>Mi az az Azure Bastion?

Az Azure Bastion szolgáltatás egy új, teljes körűen felügyelt, a virtuális hálózaton belül kiépített, teljesen platform által felügyelt Pásti szolgáltatás. Biztonságos és zökkenőmentes RDP/SSH-kapcsolatot biztosít a virtuális gépekhez közvetlenül a Azure Portal SSL-en keresztül. Az Azure Bastion használatával történő csatlakozásokhoz nincs szükség nyilvános IP-címekre.

A Bastion biztonságos RDP-és SSH-kapcsolatot biztosít annak a virtuális hálózatnak az összes virtuális géphez, amelyben kiépítve van. Az Azure Bastion használatával megvédheti a virtuális gépeket az RDP/SSH-portok a külvilág felé való kitéve, miközben továbbra is biztonságos hozzáférést biztosít az RDP/SSH használatával. Az Azure Bastion használatával közvetlenül a Azure Portal csatlakozhat a virtuális géphez. Nincs szüksége további ügyfélre, ügynökre vagy szoftverre.

## <a name="architecture"></a>Architektúra

Az Azure Bastion üzembe helyezése virtuális hálózatonként történik, nem pedig előfizetés/fiók vagy virtuális gép esetében. Miután kiépített egy Azure-beli megerősített szolgáltatást a virtuális hálózatában, az RDP/SSH-élmény az azonos virtuális hálózatban lévő összes virtuális gép számára elérhető.

Az RDP és az SSH egy olyan alapvető eszköz, amellyel az Azure-ban futó számítási feladatokhoz kapcsolódhat. Az RDP/SSH-portok az interneten keresztül nem kívánatosak, és jelentős veszélyforrásnak tekinthetők. Ez gyakran a protokollok biztonsági rései miatt fordul elő. Ennek a veszélyforrásnak a felszínének a megtalálása érdekében a peremhálózaton nyilvános oldalán a megerősített gazdagépeket (más néven ugrás-kiszolgálókat) is üzembe helyezheti. A megerősített gazdagép-kiszolgálók úgy lettek kialakítva és konfigurálva, hogy ellenálljanak a támadásoknak. A megerősített kiszolgálók RDP-és SSH-kapcsolatot is biztosítanak a megerősített munkaterhelésekhez, valamint a hálózaton belül.

![architektúra](./media/bastion-overview/architecture.png)

Ez az ábra egy Azure-alapú központi telepítés architektúráját mutatja be. Ebben a diagramban:

* A megerősített gazdagép üzembe helyezése a virtuális hálózaton történik.
* A felhasználó bármely HTML5 böngésző használatával csatlakozik a Azure Portalhoz.
* A felhasználó kiválasztja azt a virtuális gépet, amelyhez csatlakozni szeretne.
* Egyetlen kattintással megnyílik az RDP/SSH-munkamenet a böngészőben.
* Nem szükséges nyilvános IP-cím az Azure-beli virtuális gépen.

## <a name="key-features"></a>Fő funkciók

A következő szolgáltatások érhetők el:

* **Az RDP és az SSH közvetlenül a Azure Portalban:** Az RDP-és SSH-munkamenetet közvetlenül a Azure Portal közvetlenül az egyetlen kattintással elérhető zökkenőmentes felülettel érheti el.
* **Távoli munkamenet SSL-en keresztül és tűzfal-átjárás RDP/SSH-hoz:** Az Azure Bastion egy HTML5-alapú webes ügyfélprogramot használ, amelyet a rendszer automatikusan továbbít a helyi eszközre, így a 443-as porton lévő RDP/SSH-munkamenetet az SSL protokollon keresztül érheti el, amely lehetővé teszi a vállalati tűzfalak biztonságos átjárását.
* **Nem szükséges nyilvános IP-cím az Azure-beli virtuális gépen:** Az Azure Bastion a virtuális gépen lévő magánhálózati IP-címmel nyitja meg az RDP/SSH-kapcsolatokat az Azure-beli virtuális géppel. Nincs szüksége nyilvános IP-címekre a virtuális gépen.
* **Nem kell bajlódnia a NSG kezeléséhez:** Az Azure Bastion egy teljes körűen felügyelt Azure-szolgáltatás, amelyet belsőleg erősítenek, hogy biztonságos RDP/SSH-kapcsolatot biztosítson. Nem kell NSG alkalmaznia az Azure megerősített alhálózaton. Mivel az Azure Bastion magánhálózati IP-címekkel csatlakozik a virtuális gépekhez, a NSG úgy is beállíthatja, hogy csak az Azure-megerősített szolgáltatásból engedélyezze az RDP/SSH-t. Ezzel a megoldással a virtuális gépekhez való biztonságos kapcsolódáshoz szükséges minden alkalommal eltávolítja a NSG kezelését.
* A **portok vizsgálatával szembeni védelem:** Mivel a virtuális gépeket nem kell nyilvános internetre kiadnia, a virtuális gépek a virtuális hálózatán kívüli rosszindulatú felhasználók által a portok vizsgálatával védve vannak.
* **Védelem a nulla napi kihasználat ellen. Csak egy helyen kell megerősíteni:** az Azure Bastion egy teljes körűen felügyelt Pásti-szolgáltatás. Mivel a virtuális hálózat peremén helyezkedik el, nem kell aggódnia a virtuális hálózatban lévő virtuális gépek megerősítésével kapcsolatban. Az Azure-platform az Azure-ban megerősített és mindig naprakészen tartja a napi zéró kihasználást.

## <a name="faq"></a>Gyakori kérdések

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Következő lépések

* [Hozzon létre egy Azure Bastion Host-erőforrást](bastion-create-host-portal.md).
* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.
