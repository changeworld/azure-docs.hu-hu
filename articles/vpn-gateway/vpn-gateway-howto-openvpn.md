---
title: 'Az OpenVPN konfigurálása az Azure VPN Gatewayban: PowerShell'
description: Az OpenVPN Azure VPN Gateway konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 7505420cc31fe751ecc0c114a89fea0734cbc6cf
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162407"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Az OpenVPN konfigurálása az Azure pont – hely kapcsolathoz VPN Gateway

Ebből a cikkből megtudhatja, hogyan állíthatja be az **OpenVPN® protokollt** az Azure VPN Gateway-on. A cikk feltételezi, hogy már rendelkezik egy működő pont – hely környezettel. Ha nem, az 1. lépésben szereplő utasításokat követve hozzon létre egy pont – hely típusú VPN-t.



## <a name="vnet"></a>1. pont – hely típusú VPN létrehozása

Ha még nem rendelkezik működő pont – hely környezettel, kövesse az utasításokat, és hozzon létre egyet. Lásd: [pont – hely típusú VPN létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md) és konfigurálása egy pont – hely típusú VPN-átjáróhoz natív Azure-Tanúsítványos hitelesítéssel. 

> [!IMPORTANT]
> Az OpenVPN esetében az alapszintű SKU nem támogatott.

## <a name="enable"></a>2. az OpenVPN engedélyezése az átjárón

Az OpenVPN engedélyezése az átjárón. Az alábbi parancsok futtatása előtt győződjön meg arról, hogy az átjáró már konfigurálva van a pont – hely (IKEv2 vagy SSTP) számára:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Következő lépések

Az OpenVPN-ügyfelek konfigurálásával kapcsolatban lásd: az [OpenVPN-ügyfelek konfigurálása](vpn-gateway-howto-openvpn-clients.md).

**Az "OpenVPN" az OpenVPN Inc védjegye.**
