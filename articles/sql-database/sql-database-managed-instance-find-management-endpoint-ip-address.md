---
title: Felügyelt példányok felügyeleti végpontjának felderítése
description: Ismerje meg, hogyan kérhet Azure SQL Database felügyelt példányok felügyeleti végpontjának nyilvános IP-címét, és ellenőrizheti a beépített tűzfal-védelmet
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825713"
---
# <a name="determine-the-management-endpoint-ip-address"></a>A felügyeleti végpont IP-címének meghatározása

A Azure SQL Database felügyelt példány virtuális fürt felügyeleti végpontot tartalmaz, amelyet a Microsoft a felügyeleti műveletekhez használ. A felügyeleti végpontot a rendszer egy beépített tűzfallal védi a hálózati szinten, valamint a kölcsönös tanúsítvány-ellenőrzést az alkalmazás szintjén. Meghatározhatja a felügyeleti végpont IP-címét, de ehhez a végponthoz nem férhet hozzá.

A felügyeleti IP-cím meghatározásához hajtson végre DNS-lekérdezést a felügyelt példány teljes tartománynevén: `mi-name.zone_id.database.windows.net`. Ez egy olyan DNS-bejegyzést ad vissza, amely hasonló `trx.region-a.worker.vnet.database.windows.net`. Ezt követően a ". vnet" nevű DNS-lekérdezést törölheti a teljes tartománynévre. Ekkor a rendszer visszaküldi a felügyeleti IP-címet. 

Ez a PowerShell mindent megtesz, ha lecseréli \<MI FQDN-\> a felügyelt példány DNS-bejegyzésére: `mi-name.zone_id.database.windows.net`:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

A felügyelt példányokkal és kapcsolatokkal kapcsolatos további információkért lásd: [Azure SQL Database felügyelt példány kapcsolati architektúrája](sql-database-managed-instance-connectivity-architecture.md).
