---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b050d0cd5f6a21757685f9bc0991f8ce0a971e3c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178941"
---
Ha probléma adódik egy virtuális gép VPN-kapcsolaton keresztüli csatlakoztatása során, ellenőrizze az alábbiakat:

- Ellenőrizze, hogy a VPN-kapcsolat sikeresen létrejött-e.
- Ellenőrizze, hogy a virtuális gép magánhálózati IP-címéhez csatlakozik-e.
- Ha tud csatlakozni a virtuális géphez a magánhálózati IP-címmel, de a számítógép nevével nem, ellenőrizze, hogy a DNS-konfiguráció megfelelő-e. A virtuális gépek névfeloldásának működésével kapcsolatos további információkért lásd [a virtuális gépek névfeloldásával](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) foglakozó cikket.

Ha pont–hely kapcsolattal csatlakozik, ellenőrizze az alábbi elemeket is:

- Az „ipconfig” használatával ellenőrizze annak a számítógépnek az Ethernet-adapteréhez hozzárendelt IPv4-címet, amelyről a kapcsolatot létesíti. Ha az IP-cím azon virtuális hálózat tartományában található, amelyhez csatlakozni kíván, vagy a VPN-ügyfél címkészletének címtartományában, akkor átfedő címtérről beszélünk. Ilyen átfedés esetén a hálózati forgalom nem éri el az Azure-t, és a helyi hálózaton marad.
- Ellenőrizze, hogy létrejött-e a VPN-ügyfél konfigurációs csomagja azután, hogy a DNS-kiszolgáló IP-címei meg lettek adva a virtuális hálózathoz. Ha frissítette a DNS-kiszolgáló IP-címeit, hozzon létre és telepítsen egy új VPN-ügyfélkonfigurációs csomagot.

Az RDP-kapcsolatok hibaelhárításával kapcsolatos további információkért lásd [a virtuális gép távoli asztali kapcsolatainak hibaelhárításával](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md) foglalkozó cikket.