---
title: Adattárház visszaállítása geo-biztonsági mentésből
description: Útmutató egy SQL-készlet geo-visszaállításához.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 624c6665e70802907be8a41015b78d36cca7df1c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198368"
---
# <a name="geo-restore-for-sql-pool"></a>Geo-visszaállítás az SQL-készlethez

Ebből a cikkből megtudhatja, hogyan állíthatja vissza az SQL-készletet a Geo-biztonsági mentésből Azure Portal és a PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Ellenőrizze a DTU kapacitását.** Minden SQL-készletet egy SQL-kiszolgáló (például myserver.database.windows.net) üzemeltet, amely rendelkezik alapértelmezett DTU-kvótával. Ellenőrizze, hogy az SQL-kiszolgáló rendelkezik-e elegendő fennmaradó DTU-kvótával az adatbázis visszaállításához. A szükséges DTU kiszámításához, illetve további DTU igényléséhez lásd: [DTU-kvóta megváltoztatásának kérése](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Visszaállítás egy Azure földrajzi régióból a PowerShell használatával

A Geo biztonsági mentésből való visszaállításhoz használja a [Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) és a [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) parancsmagot.

> [!NOTE]
> A Gen2 a Geo-visszaállítást is elvégezheti. Ehhez meg kell adnia egy Gen2-ServiceObjectiveName (például DW1000**c**) választható paraméterként.
>

1. Mielőtt elkezdené, győződjön meg arról, hogy a [Azure PowerShell telepítését](https://docs.microsoft.com/powershell/azure/overview)végzi.
2. Nyissa meg a PowerShellt.
2. Kapcsolódjon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.
3. Válassza ki azt az előfizetést, amely a visszaállítani kívánt adatraktárat tartalmazza.
4. Szerezze be a helyreállítani kívánt adatraktárat.
5. Hozza létre az adatraktár helyreállítási kérelmét.
6. Ellenőrizze a Geo-helyreállított adattárház állapotát.
7. Ha az adattárházat a visszaállítás befejeződése után szeretné konfigurálni, tekintse meg [az adatbázis konfigurálása a helyreállítás után]( ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)című témakört.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

A helyreállított adatbázis TDE válik, ha a forrásadatbázis TDE engedélyezve van.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Visszaállítás egy Azure földrajzi régióból Azure Portal

Az SQL-készletek földrajzi biztonsági mentésből történő visszaállításához kövesse az alábbi lépéseket:

1. Jelentkezzen be [Azure Portal](https://portal.azure.com/) -fiókjába.
1. Kattintson **az + erőforrás létrehozása**elemre. 

![Új DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Kattintson az **adatbázisok** , majd a * * Azure szinapszis Analytics (korábbi NEVÉN SQL DW) * * elemre.

![Új DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Adja meg az **alapok** lapon kért információkat, majd kattintson a **Tovább gombra: további beállítások**.

![Alapvető beállítások](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. A **meglévő** Adatparaméter használata esetén válassza a **biztonsági mentés** lehetőséget, és válassza ki a megfelelő biztonsági mentést a görgetési beállítások közül. Kattintson a **felülvizsgálat + létrehozás**gombra.
 
![biztonsági mentés](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Az adattárház visszaállítása után győződjön meg arról, hogy az **állapot online állapotban** van.

## <a name="next-steps"></a>További lépések
- [Meglévő SQL-készlet visszaállítása](sql-data-warehouse-restore-active-paused-dw.md)
- [Törölt SQL-készlet visszaállítása](sql-data-warehouse-restore-deleted-dw.md)