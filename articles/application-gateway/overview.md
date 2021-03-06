---
title: Mi az Azure Application Gateway?
description: Megtudhatja, hogyan kezelheti az alkalmazása webes forgalmát az Azure Application Gateway segítségével.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4a4395801218409fe77d1081689ba80b495fcfad
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302576"
---
# <a name="what-is-azure-application-gateway"></a>Mi az Azure Application Gateway?

Az Azure Application Gateway egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. A hagyományos terheléselosztók az átviteli rétegen működnek (OSI 4. réteg – TCP és UDP), és a forrás IP-címe és portja alapján irányítják a forgalmat a célállomás IP-címére és portjára.

Application Gateway a HTTP-kérések további attribútumain alapuló útválasztási döntéseket hozhat létre, például az URI elérési út vagy a gazdagép fejléceit. A forgalmat például a bejövő URL-cím alapján irányíthatja. Tehát ha a bejövő URL-cím a `/images` kifejezést tartalmazza, a forgalmat adott, képekre konfigurált kiszolgálókra irányíthatja (azaz egy készletbe). Ha `/video` szerepel az URL-címben, a rendszer átirányítja a forgalmat egy másik, videókra optimalizált készletbe.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Ezt a fajta útválasztást alkalmazásrétegbeli (OSI 7. réteg) terheléselosztásnak nevezzük. Az Azure Application Gateway URL-alapú és egyéb útválasztásra is képes.

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. Ha nagy teljesítményű, kis késleltetésű, 4. rétegbeli terheléselosztásra van szüksége, tekintse meg [a mi az Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Ha globális DNS-terheléselosztást keres, tekintse meg a [Mi az Traffic Manager?](../traffic-manager/traffic-manager-overview.md) A végpontok közötti forgatókönyvek a megoldások kombinálásával is hasznosak lehetnek.
>
> Az Azure terheléselosztási lehetőségeinek összehasonlítását lásd: [Az Azure terheléselosztási lehetőségeinek áttekintése](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="features"></a>Szolgáltatások

Application Gateway szolgáltatásokkal kapcsolatos további tudnivalókért lásd az [Azure Application Gateway funkcióit](features.md)ismertető témakört.

## <a name="pricing-and-sla"></a>Díjszabás és SLA

Application Gateway díjszabási információkért lásd: [Application Gateway díjszabása](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway SLA-ra vonatkozó információkért lásd: [Application Gateway SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="next-steps"></a>Következő lépések

A követelményektől és a környezettől függően létrehozhat egy tesztelési Application Gateway a Azure Portal, a Azure PowerShell vagy az Azure CLI használatával.

- [Gyors útmutató: webes forgalom közvetlen továbbítása az Azure Application Gateway-Azure Portal](quick-create-portal.md)
- [Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure PowerShell](quick-create-powershell.md)
- [Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure CLI](quick-create-cli.md)
