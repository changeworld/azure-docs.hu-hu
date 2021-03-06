---
title: Az Azure Database Migration Service előfeltételei
description: Ismerje meg, hogyan tekintheti át az előfeltételeket az adatbázis-áttelepítés végrehajtásához a Azure Database Migration Service használatával.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 89cb63630e3dbe953ed3f4fd8796d01ba0d36067
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651491"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>A Azure Database Migration Service használatának előfeltételeinek áttekintése

Több előfeltétel szükséges annak biztosításához, hogy Azure Database Migration Service zökkenőmentesen fusson az adatbázisok áttelepítésekor. Néhány előfeltétel a szolgáltatás által támogatott összes forgatókönyv (forrás-cél párok) esetében érvényes, míg más előfeltételek egyediek egy adott forgatókönyv esetében.

A Azure Database Migration Service használatával kapcsolatos előfeltételek a következő részekben találhatók.

## <a name="prerequisites-common-across-migration-scenarios"></a>Gyakori előfeltételek az áttelepítési forgatókönyvek között

Azure Database Migration Service az összes támogatott áttelepítési forgatókönyv esetében gyakori előfeltételek közé tartozik a következők szükségesek:

* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Service számára a Azure Resource Manager üzemi modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával.
* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport (NSG) szabályai nem gátolják meg a következő kommunikációs portokat: 443, 53, 9354, 445, 12000. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)című cikket.
* Ha a forrásadatbázis (ok) előtt tűzfal-berendezést használ, előfordulhat, hogy olyan tűzfalszabályok hozzáadására van szükség, amelyek lehetővé teszik a Azure Database Migration Service számára a forrás-adatbázis (ok) elérését az áttelepítéshez.
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Engedélyezze a TCP/IP protokollt, amely az SQL Server Express telepítése során alapértelmezés szerint le van tiltva – kövesse a [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) cikk utasításait.

    > [!IMPORTANT]
    > Azure Database Migration Service egy példányának létrehozása olyan virtuális hálózati beállításokhoz való hozzáférést igényel, amelyek általában nem azonos erőforráscsoporthoz tartoznak. Ennek eredményeképpen a DMS-példányt létrehozó felhasználónak engedélyre van szüksége az előfizetés szintjén. A szükséges szerepkörök létrehozásához, amelyeket igény szerint hozzárendelhet, futtassa a következő parancsfájlt:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>SQL Server áttelepítésének előfeltételei a Azure SQL Database

A Azure Database Migration Service az összes áttelepítési forgatókönyvre vonatkozó előfeltételeket is, amelyek az egyes forgatókönyvekre vagy egy másikra is érvényesek.

Ha a Azure Database Migration Service használatával hajtja végre SQL Server Azure SQL Database áttelepítéseket, az összes áttelepítési forgatókönyvhöz közös előfeltételek mellett ügyeljen arra, hogy a következő további előfeltételeket kövesse:

* Hozzon létre egy Azure SQL Database-példányt az [Azure SQL Database létrehozása az Azure Portalon](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) cikk lépéseit követve.
* Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3-as vagy újabb verzióját.
* Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
* Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
* Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure SQL-adatbáziskiszolgáló számára, hogy az Azure SQL Database Migration Service hozzáférhessen a céladatbázisokhoz. Adja meg a Azure Database Migration Servicehoz használt virtuális hálózat alhálózati tartományát.
* Gondoskodjon róla, hogy a forrásként szolgáló SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyekkel.
* Gondoskodjon róla, hogy a forrásként szolgáló Azure SQL Database-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek CONTROL DATABASE engedéllyel a célként szolgáló Azure SQL adatbázisokban.

   > [!NOTE]
   > A Azure Database Migration Service SQL Serverról Azure SQL Databasere való áttelepítésének végrehajtásához szükséges előfeltételek teljes listáját az oktatóanyag [SQL Server áttelepítése Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)című cikkben tekintheti meg.
   >

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>SQL Server áttelepítésének előfeltételei egy Azure SQL Database felügyelt példányra

* Hozzon létre egy Azure SQL Database felügyelt példányt a [Azure Portal Azure SQL Database felügyelt példány létrehozása](https://aka.ms/sqldbmi)című cikkben ismertetett részletességgel.
* Nyissa meg a tűzfalakat, hogy engedélyezze az SMB-forgalmat az 445-as porton a Azure Database Migration Service IP-cím vagy alhálózat-tartomány számára.
* Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
* Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
* Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példány és a célként szolgáló felügyelt példány összekapcsolásához használt bejelentkezési adatok mind a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
* Hozzon létre egy hálózati megosztást, amelyet az Azure Database Migration Service használhat a forrásként szolgáló adatbázis biztonsági mentésére.
* Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
* Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. A Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat a biztonságimásolat-fájlok Azure Storage-tárolóba való feltöltéséhez a visszaállítási művelethez.
* Hozzon létre egy BLOB-tárolót, és kérje le az SAS URI- [t az Azure Blob Storage-erőforrások kezelése Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)használatával című cikk lépéseit követve. A SAS URI létrehozásakor ügyeljen arra, hogy a házirend ablakban válassza az összes engedély (olvasás, írás, törlés, Listázás) lehetőséget.

   > [!NOTE]
   > A SQL Serverról Azure SQL Database felügyelt példányra történő áttelepítések végrehajtásához szükséges előfeltételek teljes listáját az oktatóanyag [SQL Server áttelepítése Azure SQL Database felügyelt példányra](https://aka.ms/migratetomiusingdms)című Azure Database Migration Service.

## <a name="next-steps"></a>Következő lépések

A Azure Database Migration Service és a regionális elérhetőség áttekintését lásd: [Mi a Azure Database Migration Service](dms-overview.md).
