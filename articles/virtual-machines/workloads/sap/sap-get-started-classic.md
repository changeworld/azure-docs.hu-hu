---
title: SAP használata Linux rendszerű virtuális gépeken | Microsoft Docs
description: Ismerje meg az SAP használatát Linux virtuális gépeken (VM) a Microsoft Azure megoldásban
services: virtual-machines-linux,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: gwallace
editor: ''
tags: azure-service-management
keywords: ''
ms.assetid: f9cd93dc-71ad-48a4-8778-4e48aec484a6
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
ms.openlocfilehash: 9525f339861b5de8dc22da753f7c36dcc6eede8a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078985"
---
# <a name="using-sap-on-linux-virtual-machines-in-azure"></a>Az SAP használata az Azure-beli Linux rendszerű virtuális gépeken
A felhőalapú számítástechnika széles körben használt fogalom, amely egyre fontosabbá válik az informatikai iparágban, a kisvállalatok esetében ugyanúgy, mint a nagy, nemzetközi vállalatok számára. A Microsoft Azure a Microsoft új lehetőségek széles skáláját nyújtó felhőszolgáltatás-platformja. Az ügyfelek most már gyorsan építhetnek ki és vonhatnak ki az üzemből felhőszolgáltatásként rendelkezésre bocsájtott alkalmazásokat, ami azt jelenti, hogy többé nem érvényesülnek a technikai vagy költségvetési korlátozások. A vállalatoknak nem kell időt és forrásokat befektetni a hardverinfrastruktúrába: ehelyett az alkalmazásra, az üzleti folyamatokra és az ügyfeleknek és felhasználóknak nyújtott értékre koncentrálhatnak.

A Microsoft Azure Virtual Machines szolgáltatással a Microsoft átfogó infrastruktúra-szolgáltatási (IaaS) platformot kínál. Az Azure virtuális gépek támogatják az SAP NetWeaver-alapú alkalmazásokat (IaaS). Az alábbi tanulmányok azt írják le, hogyan tervezhet és implementálhat SAP NetWeaver-alapú alkalmazásokat az Azure-beli Windows rendszerű virtuális gépeken. Emellett SAP NetWeaver-alapú alkalmazásokat is alkalmazhat [Windows rendszerű virtuális gépeken](../../virtual-machines-windows-classic-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure-suse-linux-virtual-machines"></a>SAP NetWeaver az Azure SUSE Linux Virtual Machines
Cím Az SAP NetWeaver tesztelése Microsoft Azure-beli SUSE Linux-alapú virtuális gépeken

Összefoglalás: Az SAP NetWeaver Azure Linux rendszerű virtuális gépeken való futtatásához jelenleg nincs hivatalos SAP-támogatás. Ennek ellenére előfordulhat, hogy az ügyfelek valamilyen tesztelést végeznek, vagy fontolóra veszik az SAP-bemutató vagy a betanítási rendszerek futtatását az Azure Linux virtuális gépeken, ha nincs szükség az SAP-támogatással való kapcsolatfelvételre. Ez a cikk segítséget nyújt az Azure SUSE Linux rendszerű virtuális gépek beállításához az SAP futtatásához, és néhány alapvető tudnivalót biztosít a lehetséges buktatók elkerüléséhez.

Frissítve: December 2015

[Ez a cikk itt található](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

