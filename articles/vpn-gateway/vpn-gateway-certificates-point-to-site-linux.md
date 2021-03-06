---
title: 'Tanúsítványok előállítása és exportálása pont – hely kapcsolatokhoz: Linux: parancssori felület'
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot, és hozzon létre ügyféltanúsítványt a Linux (alapú strongswan) parancssori felület használatával.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: a0f996ff2805da4dd5af400642eef2506c228d33
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779756"
---
# <a name="generate-and-export-certificates"></a>Tanúsítványok előállítása és exportálása

A pont – hely kapcsolatok tanúsítványokat használnak a hitelesítéshez. Ebből a cikkből megtudhatja, hogyan hozhat létre önaláírt főtanúsítványokat, és hogyan hozhat létre ügyféltanúsítványt a linuxos parancssori felület és a alapú strongswan használatával. Ha más tanúsítványokra vonatkozó utasításokat keres, tekintse meg a [PowerShell](vpn-gateway-certificates-point-to-site.md) -vagy [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) -cikkeket. További információ a alapú strongswan a parancssori felület helyett a GUI használatával történő telepítéséről: az [ügyfél-konfigurációval](point-to-site-vpn-client-configuration-azure-cert.md#install) kapcsolatos cikk lépései.

## <a name="install-strongswan"></a>A alapú strongswan telepítése

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Tanúsítványok előállítása és exportálása

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Következő lépések

Folytassa a pont – hely konfigurációval a VPN- [ügyfél konfigurációs fájljainak létrehozásához és telepítéséhez](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
