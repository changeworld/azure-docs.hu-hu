---
title: Bevezetés a biztonsági csoport nézetbe az Azure Network Watcherban | Microsoft Docs
description: Ez az oldal áttekintést nyújt a Network Watcher biztonsági nézet funkcióról
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 18619cc0f7a21a39d0802575c484c68c5fa1a624
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840757"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Az Azure Network Watcher hatékony biztonsági szabályok nézetének bemutatása

A hálózati biztonsági csoportok alhálózatok vagy hálózati ADAPTERek szintjén vannak társítva. Ha az alhálózat szintjén van társítva, az az alhálózat összes virtuálisgép-példányára vonatkozik. A hatályos biztonsági szabályok nézet az összes olyan konfigurált NSG és szabályt visszaadja, amely a virtuális gép hálózati ADAPTERéhez és alhálózatának szintjén van társítva, és betekintést nyújt a konfigurációba. Emellett az érvényes biztonsági szabályok a virtuális gépek egyes hálózati adapterei esetében is visszakerülnek. A hatékony biztonsági szabályok nézet használatával olyan hálózati biztonsági réseket is felhasználhat, mint a nyitott portok. Azt is ellenőrizheti, hogy a hálózati biztonsági csoport a várt módon működik [-e a konfigurált és a jóváhagyott biztonsági szabályok összehasonlítása](network-watcher-nsg-auditing-powershell.md)alapján.

A kiterjesztett használati eset a biztonsági megfelelőség és a naplózás. Megadhatja a biztonsági szabályok előírásos készletét a szervezet biztonsági irányításának modelljéként. Az időszakos megfelelőségi audit programozott módon valósítható meg, ha összehasonlítja az előírási szabályokat a hálózatban lévő virtuális gépekre érvényes szabályokkal.

A portál szabályai megjelennek az egyes hálózati adapterekhez, és a bejövő és kimenő csoportok szerint vannak csoportosítva. Ez egyszerű áttekintést nyújt a virtuális gépekre alkalmazott szabályokról. A letöltés gombra kattintva egyszerűen letöltheti az összes biztonsági szabályt, függetlenül attól, hogy a TAB egy CSV-fájlba esik-e.

![biztonsági csoport nézet][1]

A szabályok kiválaszthatók, és egy új panel nyílik meg a hálózati biztonsági csoport, valamint a forrás és a cél előtagjainak megjelenítéséhez. Ebből a panelből közvetlenül a hálózati biztonsági csoport erőforrására lehet navigálni.

![részletezés][2]

### <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan naplózhatja a hálózati biztonsági csoport beállításait a [hálózati biztonsági csoport beállításainak naplózása a PowerShell](network-watcher-nsg-auditing-powershell.md) használatával

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









