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
ms.openlocfilehash: 5b9e036816aa532d32b1b4305ef6ae646ae05bae
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179008"
---
A következő táblázat felsorolja a házirendalapú és Útvonalalapú VPN-átjárók követelményei. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes. A klasszikus modellt a házirendalapú VPN gatewayek ugyanazok, mint statikus átjárókhoz, és az útvonalalapú átjárók ugyanazok, mint a dinamikus átjárók.

|  | **Házirendalapú alapszintű VPN Gateway** | **Alapszintű Útvonalalapú VPN-átjáró** | **Standard Útvonalalapú VPN-átjáró** | **Útvonalalapú nagy teljesítményű VPN Gateway** |
| --- | --- | --- | --- | --- |
| **Helyek közötti kapcsolat (S2S)** |Házirendalapú VPN-konfiguráció |Útvonalalapú VPN-konfiguráció |Útvonalalapú VPN-konfiguráció |Útvonalalapú VPN-konfiguráció |
| **Pont–hely típusú kapcsolat (P2S**) |Nem támogatott |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |
| **Hitelesítési módszer** |Előre megosztott kulcs |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |
| **S2S-kapcsolatok maximális száma** |1 |10 |10 |30 |
| **P2S-kapcsolatok maximális száma** |Nem támogatott |128 |128 |128 |
| **Aktív útválasztás-támogatás (BGP)** |Nem támogatott |Nem támogatott |Támogatott |Támogatott |
