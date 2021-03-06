---
title: Törölt SQL-készlet visszaállítása
description: Útmutató a törölt SQL-készlet visszaállításához.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 34851203432b7e2daf44e840e45275de76bc3b3a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196626"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Törölt SQL-készlet visszaállítása az Azure szinapszis Analytics használatával

Ebből a cikkből megtudhatja, hogyan állíthatja vissza az SQL-t a Azure Portal vagy a PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Ellenőrizze a DTU kapacitását.** Minden SQL-készletet egy SQL-kiszolgáló (például myserver.database.windows.net) üzemeltet, amely rendelkezik alapértelmezett DTU-kvótával.  Ellenőrizze, hogy az SQL-kiszolgáló rendelkezik-e elegendő fennmaradó DTU-kvótával az adatbázis visszaállításához. A szükséges DTU kiszámításához, illetve további DTU igényléséhez lásd: [DTU-kvóta megváltoztatásának kérése](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Törölt adattárház visszaállítása a PowerShell-lel

Egy törölt SQL-készlet visszaállításához használja a [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) parancsmagot. Ha a megfelelő logikai kiszolgáló törölve lett, az adatraktár nem állítható vissza.

1. Mielőtt elkezdené, győződjön meg arról, hogy a [Azure PowerShell telepítését](https://docs.microsoft.com/powershell/azure/overview)végzi.
2. Nyissa meg a PowerShellt.
3. Kapcsolódjon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.
4. Válassza ki azt az előfizetést, amely a visszaállítani kívánt törölt adattárházat tartalmazza.
5. A megadott törölt adattárház beolvasása.
6. A törölt adattárház visszaállítása
    1. Ha egy másik logikai kiszolgálóra szeretné visszaállítani a törölt SQL Data Warehouse, akkor ügyeljen arra, hogy a másik logikai kiszolgáló nevét adja meg.  Ez a logikai kiszolgáló más erőforráscsoport és régió is lehet.
    1. Másik előfizetésre való visszaállításhoz használja az [Áthelyezés](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal) gombot a logikai kiszolgáló másik előfizetésre való áthelyezéséhez.
1. Ellenőrizze, hogy a visszaállított adattárház online állapotban van-e.
1. A visszaállítás befejezése után a helyreállított adattárházat a [helyreállítás után az adatbázis konfigurálása](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)után állíthatja be.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>" 
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Törölt adatbázis visszaállítása a Azure Portal használatával

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2. Navigáljon ahhoz az SQL Server-kiszolgálóhoz, amelyen a törölt adattárház található.
3. Válassza a **törölt adatbázisok** ikont a tartalomjegyzékben.

    ![Törölt adatbázisok](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Válassza ki a visszaállítani kívánt törölt SQL Data Warehouse.

    ![Törölt adatbázisok kiválasztása](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Adja meg az új **adatbázis nevét** , majd kattintson **az OK** gombra.

    ![Adja meg az adatbázis nevét](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>További lépések
- [Meglévő SQL-készlet visszaállítása](sql-data-warehouse-restore-active-paused-dw.md)
- [Visszaállítás egy földrajzi biztonsági mentési SQL-készletből](sql-data-warehouse-restore-from-geo-backup.md)