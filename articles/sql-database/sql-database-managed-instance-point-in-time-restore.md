---
title: Felügyelt példány – időponthoz tartozó visszaállítás (PITR)
description: SQL-adatbázis visszaállítása felügyelt példányban egy korábbi időpontra.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268807"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>SQL-adatbázis visszaállítása felügyelt példányban egy korábbi időpontra

Az időponthoz tartozó visszaállítás (PITR) használatával létrehozhat egy adatbázist egy másik adatbázis másolatával egy korábbi időpontban. Ez a cikk azt ismerteti, hogyan végezhető el az adatbázis egy adott időpontban történő visszaállítása egy Azure SQL Database felügyelt példányban.

Az időponthoz való visszaállítás hasznos olyan helyreállítási helyzetekben, mint például a hibák által okozott incidensek, az adatok helytelen betöltése vagy a kritikus adatok törlése. Egyszerűen tesztelésre vagy naplózásra is használható. A biztonságimásolat-fájlok az adatbázis beállításaitól függően 7 – 35 napig tartanak.

Az időponthoz tartozó visszaállítás egy adatbázist állíthat vissza:

- egy meglévő adatbázisból.
- egy törölt adatbázisból.
- ugyanarra a felügyelt példányra, vagy egy másik felügyelt példányra. 

## <a name="limitations"></a>Korlátozások

A felügyelt példányra történő visszaállítási időpontra a következő korlátozások vonatkoznak:

- Ha egy felügyelt példányról egy másikra állítja vissza a visszaállítást, mindkét példánynak ugyanabban az előfizetésben és régióban kell lennie. A régiók közötti és az előfizetések közötti visszaállítás jelenleg nem támogatott.
- Egy teljes felügyelt példány időponthoz tartozó visszaállítása nem lehetséges. Ez a cikk a felügyelt példányokon üzemeltetett adatbázisok adott időpontban történő visszaállítását ismerteti.

> [!WARNING]
> Vegye figyelembe a felügyelt példány tárolási méretét. A visszaállítani kívánt adatmérettől függően elfogyhat a példányok tárolója. Ha nincs elég hely a visszaállított adatmennyiséghez, használjon más megközelítést.

A következő táblázat a felügyelt példányok időponthoz kapcsolódó visszaállítási forgatókönyveit mutatja be:

|           |Meglévő adatbázis visszaállítása azonos felügyelt példányra| Meglévő adatbázis visszaállítása egy másik felügyelt példányra|Az eldobott adatbázis visszaállítása ugyanarra a felügyelt példányra|Az eldobott adatbázis visszaállítása egy másik felügyelt példányra|
|:----------|:----------|:----------|:----------|:----------|
|**Azure Portalra**| Igen|Nem |Igen|Nem|
|**Azure CLI**|Igen |Igen |Nem|Nem|
|**PowerShell**| Igen|Igen |Igen|Igen|

## <a name="restore-an-existing-database"></a>Meglévő adatbázis visszaállítása

A Azure Portal, a PowerShell vagy az Azure CLI használatával állítson vissza egy meglévő adatbázist ugyanarra a példányra. Ha másik példányra szeretné visszaállítani az adatbázist, használja a PowerShellt vagy az Azure CLI-t, így megadhatja a cél felügyelt példány és az erőforráscsoport tulajdonságait. Ha nem határozza meg ezeket a paramétereket, a rendszer alapértelmezés szerint az adatbázist visszaállítja a meglévő példányra. A Azure Portal jelenleg nem támogatja a visszaállítást egy másik példányra.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). 
2. Lépjen a felügyelt példányra, és válassza ki a visszaállítani kívánt adatbázist.
3. Válassza a **visszaállítás** lehetőséget az adatbázis lapon:

    ![Adatbázis visszaállítása a Azure Portal használatával](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. A **visszaállítás** lapon válassza ki azt a dátumot és időpontot, amelyre vissza kívánja állítani az adatbázist.
5. Az adatbázis visszaállításához kattintson a **Confirm (megerősítés** ) gombra. Ez a művelet elindítja a visszaállítási folyamatot, amely létrehoz egy új adatbázist, és feltölti azt az eredeti adatbázisból származó adatokkal a megadott időpontban. A helyreállítási folyamattal kapcsolatos további információkért lásd: [helyreállítás ideje](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha még nincs Azure PowerShell telepítve, tekintse meg [a Azure PowerShell modul telepítését](https://docs.microsoft.com/powershell/azure/install-az-ps)ismertető témakört.

Az adatbázis PowerShell használatával történő visszaállításához adja meg a paraméterek értékét a következő parancsban. Ezután futtassa a parancsot:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Az adatbázis egy másik felügyelt példányra való visszaállításához adja meg a célként megadott erőforráscsoport és a célként felügyelt példány nevét is:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Részletekért lásd: [Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha még nem telepítette az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli?view=azure-cli-latest)ismertető témakört.

Az adatbázis Azure CLI használatával történő visszaállításához adja meg a paraméterek értékét a következő parancsban. Ezután futtassa a parancsot:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Az adatbázis egy másik felügyelt példányra való visszaállításához adja meg a cél erőforráscsoport és a felügyelt példány nevét is:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Az elérhető paraméterek részletes ismertetését lásd a [CLI dokumentációjában, amely egy adatbázis helyreállítását felügyelt példányban](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)végezheti el.

---

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása

A törölt adatbázisok visszaállítása a PowerShell vagy a Azure Portal használatával végezhető el. Ha egy törölt adatbázist ugyanarra a példányra kíván visszaállítani, használja a Azure Portal vagy a PowerShellt. A törölt adatbázisok másik példányra való visszaállításához használja a PowerShellt. 

### <a name="portal"></a>Portál 


Felügyelt adatbázis helyreállításához a Azure Portal segítségével nyissa meg a felügyelt példányok áttekintése lapot, és válassza a **törölt adatbázisok**lehetőséget. Válassza ki a visszaállítani kívánt törölt adatbázist, és írja be az új adatbázis nevét, amely a biztonsági másolatból visszaállított adatokkal lesz létrehozva.

  ![Képernyőkép a törölt Azure SQL-példány-adatbázis visszaállításáról](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Ha az adatbázist ugyanarra a példányra szeretné visszaállítani, frissítse a paramétereket, majd futtassa a következő PowerShell-parancsot: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Az adatbázis egy másik felügyelt példányra való visszaállításához adja meg a célként megadott erőforráscsoport és a célként felügyelt példány nevét is:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Meglévő adatbázis felülírása

Meglévő adatbázis felülírásához a következőket kell tennie:

1. Dobja el a felülírni kívánt meglévő adatbázist.
2. Nevezze át az időponthoz visszaállított adatbázist az eldobott adatbázis nevére.

### <a name="drop-the-original-database"></a>Az eredeti adatbázis eldobása

Az adatbázist a Azure Portal, a PowerShell vagy az Azure CLI használatával lehet eldobni.

Az adatbázist közvetlenül a felügyelt példányhoz való csatlakozással is elvégezheti, SQL Server Management Studio elindításával (SSMS), majd futtathatja a következő Transact-SQL (T-SQL) parancsot:

```sql
DROP DATABASE WorldWideImporters;
```

A következő módszerek egyikével csatlakozhat az adatbázishoz a felügyelt példányban:

- [SSMS/Azure Data Studio Azure-beli virtuális gépen keresztül](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Pont – hely kapcsolat](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Nyilvános végpont](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Portal](#tab/azure-portal)

A Azure Portal válassza ki az adatbázist a felügyelt példányból, majd válassza a **Törlés**lehetőséget.

   ![Adatbázis törlése a Azure Portal használatával](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A következő PowerShell-paranccsal elhúzhatja a felügyelt példányok egy meglévő adatbázisát:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A következő Azure CLI-paranccsal elhúzhatja a felügyelt példányokból származó meglévő adatbázist:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Módosítsa az új adatbázisnevet, hogy az megfeleljen az eredeti adatbázis nevének.

Kapcsolódjon közvetlenül a felügyelt példányhoz, és indítsa el SQL Server Management Studio. Ezután futtassa a következő Transact-SQL (T-SQL) lekérdezést. A lekérdezés módosítja a visszaállított adatbázis nevét a felülírni kívánt eldobott adatbázisra.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

A következő módszerek egyikével csatlakozhat az adatbázishoz a felügyelt példányban:

- [Azure-beli virtuális gép](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Pont – hely kapcsolat](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Nyilvános végpont](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Következő lépések

További információ az [automatizált biztonsági mentésekről](sql-database-automated-backups.md).
