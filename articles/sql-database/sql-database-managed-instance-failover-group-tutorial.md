---
title: 'Oktatóanyag: felügyelt példány hozzáadása feladatátvételi csoporthoz'
description: Megtudhatja, hogyan konfigurálhat egy feladatátvételi csoportot a Azure SQL Database felügyelt példányához.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: bf83155e971061f22e5f5fc33d216b58621c9249
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462649"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Oktatóanyag: SQL Database felügyelt példány hozzáadása feladatátvételi csoporthoz

SQL Database felügyelt példány hozzáadása egy feladatátvételi csoporthoz. Ez a cikk azt ismerteti, hogyan lehet:

> [!div class="checklist"]
> - Elsődleges felügyelt példány létrehozása
> - Hozzon létre egy másodlagos felügyelt példányt egy [feladatátvételi csoport](sql-database-auto-failover-group.md)részeként. 
> - Feladatátvétel tesztelése

  > [!NOTE]
  > - Ha ezt az oktatóanyagot hajtja végre, győződjön meg arról, hogy az erőforrásokat a [felügyelt példányhoz tartozó feladatátvételi csoportok beállításának előfeltételei](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)szerint konfigurálja. 
  > - A felügyelt példányok létrehozása jelentős időt vehet igénybe. Ennek eredményeképpen ez az oktatóanyag több órát is igénybe vehet. További információ a kiépítési időszakokról: [felügyelt példányok kezelési műveletei](sql-database-managed-instance.md#managed-instance-management-operations). 
  > - A feladatátvételi csoportban részt vevő felügyelt példányokhoz [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) vagy két csatlakoztatott VPN-átjáró szükséges. Ez az oktatóanyag a VPN-átjárók létrehozásának és csatlakoztatásának lépéseit ismerteti. Hagyja ki ezeket a lépéseket, ha már konfigurálta a ExpressRoute. 


## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portal](#tab/azure-portal)
Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel: 

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 – erőforráscsoport és elsődleges felügyelt példány létrehozása
Ebben a lépésben létrehozza az erőforráscsoportot és a feladatátvételi csoport elsődleges felügyelt példányát a Azure Portal vagy a PowerShell használatával. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Hozza létre az erőforráscsoportot és az elsődleges felügyelt példányt a Azure Portal használatával. 

1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A különböző adatbázisokkal kapcsolatos további információkat az adatbázisok csempén a Részletek megjelenítése lehetőség kiválasztásával tekintheti meg.
1. Válassza a **Létrehozás** lehetőséget a **felügyelt SQL-példányok** csempén. 

    ![Felügyelt példány kiválasztása](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. A **Azure SQL Database felügyelt példány létrehozása** lap **alapok** lapján
    1. A **Project Details (projekt részletei**) területen válassza ki az **előfizetését** a legördülő menüből, majd válassza az **Új erőforráscsoport létrehozása** lehetőséget. Írja be az erőforráscsoport nevét, például `myResourceGroup`. 
    1. A **felügyelt példány részletei**területen adja meg a felügyelt példány nevét, valamint azt a régiót, ahol a felügyelt példányt telepíteni szeretné. Hagyja meg a számítási és a **tárolási** értékeket az alapértelmezett értékeken. 
    1. A **rendszergazdai fiók**területen adjon meg egy rendszergazdai bejelentkezési azonosítót, például `azureuser`és egy összetett rendszergazdai jelszót. 

    ![Elsődleges MI létrehozása](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Hagyja meg a többi beállítást az alapértelmezett értékeken, majd válassza a **felülvizsgálat + létrehozás** lehetőséget a felügyelt példányok beállításainak áttekintéséhez. 
1. Válassza a **Létrehozás** lehetőséget az elsődleges felügyelt példány létrehozásához. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre az erőforráscsoportot és az elsődleges felügyelt példányt a PowerShell használatával. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary managed instance created successfully."
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot.  |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt rendel egy virtuális hálózathoz. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Beolvas egy alhálózatot egy virtuális hálózatban. | 
| [Új – AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Egy hálózati biztonsági csoportot hoz létre. | 
| [Új – AzRouteTable](/powershell/module/az.network/new-azroutetable) | Létrehoz egy útválasztási táblázatot. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Egy virtuális hálózat alhálózati konfigurációjának frissítése.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hálózati biztonsági csoport beolvasása. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Egy hálózati biztonsági csoport frissítése.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Útvonalat helyez el egy útválasztási táblához. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Frissíti az útválasztási táblázatot.  |
| [Új – AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Létrehoz egy Azure SQL Database felügyelt példányt.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 – másodlagos virtuális hálózat létrehozása
Ha a Azure Portal a felügyelt példány létrehozásához használja, külön kell létrehoznia a virtuális hálózatot, mivel követelmény, hogy az elsődleges és a másodlagos felügyelt példány alhálózata ne rendelkezzen átfedésben lévő tartományokkal. Ha a PowerShell segítségével konfigurálja a felügyelt példányt, ugorjon a 3. lépésre. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 
Az elsődleges virtuális hálózat alhálózati tartományának ellenőrzéséhez kövesse az alábbi lépéseket:
1. A [Azure Portal](https://portal.azure.com)navigáljon az erőforráscsoporthoz, és válassza ki az elsődleges példány virtuális hálózatát. 
1. Válassza ki az **alhálózatok** lehetőséget a **Beállítások** területen, és jegyezze fel a **címtartományt**. A másodlagos felügyelt példányhoz tartozó virtuális hálózat alhálózati címtartomány nem fedi át ezt a tartományt. 


   ![Elsődleges alhálózat](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Virtuális hálózat létrehozásához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforrás létrehozása** és a *virtuális hálózat*keresése lehetőséget. 
1. Válassza ki a Microsoft által közzétett **Virtual Network** lehetőséget, majd válassza a **Létrehozás** elemet a következő oldalon. 
1. Adja meg a szükséges mezőket a másodlagos felügyelt példány virtuális hálózatának konfigurálásához, majd válassza a **Létrehozás**lehetőséget. 

   A következő táblázat a másodlagos virtuális hálózathoz szükséges értékeket mutatja be:

    | **Mező** | Érték |
    | --- | --- |
    | **Name (Név)** |  A másodlagos felügyelt példány által használandó virtuális hálózat neve, például `vnet-sql-mi-secondary`. |
    | **Címtér** | A virtuális hálózat (például `10.128.0.0/16`) címterület. | 
    | **Előfizetés** | Az az előfizetés, amelyben az elsődleges felügyelt példány és az erőforráscsoport található. |
    | **Régió** | Az a hely, ahová a másodlagos felügyelt példányt telepíteni fogja. |
    | **Alhálózat** | Az alhálózat neve. Alapértelmezés szerint a `default` biztosítjuk. |
    | **Címtartomány**| Az alhálózat címtartomány. Ennek eltérőnek kell lennie, mint az elsődleges felügyelt példány virtuális hálózata által használt alhálózat-címtartomány, például `10.128.0.0/24`.  |
    | &nbsp; | &nbsp; |

    ![Másodlagos virtuális hálózati értékek](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ez a lépés csak akkor szükséges, ha a Azure Portal használatával telepíti a felügyelt példányt. Ugorjon a 3. lépésre, ha a PowerShellt használja. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 – másodlagos felügyelt példány létrehozása
Ebben a lépésben egy másodlagos felügyelt példányt fog létrehozni a Azure Portalban, amely a két felügyelt példány közötti hálózatkezelést is konfigurálja. 

A második felügyelt példánynak a következőket kell tennie:
- Üresnek kell lennie. 
- Az elsődleges felügyelt példánytól eltérő alhálózattal és IP-tartománnyal rendelkezik. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Hozza létre a másodlagos felügyelt példányt a Azure Portal használatával. 

1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A különböző adatbázisokkal kapcsolatos további információkat az adatbázisok csempén a Részletek megjelenítése lehetőség kiválasztásával tekintheti meg.
1. Válassza a **Létrehozás** lehetőséget a **felügyelt SQL-példányok** csempén. 

    ![Felügyelt példány kiválasztása](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. A **Azure SQL Database felügyelt példány létrehozása** lap **alapok** lapján töltse ki a szükséges mezőket a másodlagos felügyelt példány konfigurálásához. 

   A következő táblázat a másodlagos felügyelt példányhoz szükséges értékeket tartalmazza:
 
    | **Mező** | Érték |
    | --- | --- |
    | **Előfizetés** |  Az az előfizetés, amelyben az elsődleges felügyelt példánya. |
    | **Erőforráscsoport**| Az az erőforráscsoport, amelyben az elsődleges felügyelt példánya. |
    | **Felügyelt példány neve** | Az új másodlagos felügyelt példány neve, például `sql-mi-secondary`  | 
    | **Régió**| A másodlagos felügyelt példány helye.  |
    | **Felügyelt példány rendszergazdai bejelentkezési neve** | Az új másodlagos felügyelt példányhoz használni kívánt bejelentkezési azonosító, például `azureuser`. |
    | **Jelszó** | Összetett jelszó, amelyet az új másodlagos felügyelt példányhoz tartozó rendszergazdai bejelentkezés fog használni.  |
    | &nbsp; | &nbsp; |

1. A **hálózatkezelés** lap **Virtual Network**területén válassza ki a másodlagos felügyelt példányhoz létrehozott virtuális hálózatot a legördülő menüből.

   ![Másodlagos MI hálózatkezelés](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. A **További beállítások** lap **geo-replikáció**területén válassza az **Igen** lehetőséget a _feladatátvétel másodlagosként való használathoz_. Válassza ki az elsődleges felügyelt példányt a legördülő menüből. 
    1. Ügyeljen arra, hogy a rendezés és az időzóna megfeleljen az elsődleges felügyelt példánynak. Az oktatóanyagban létrehozott elsődleges felügyelt példány az alapértelmezett `SQL_Latin1_General_CP1_CI_AS` rendezést és a `(UTC) Coordinated Universal Time` időzónát használta. 

   ![Másodlagos MI hálózatkezelés](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a másodlagos felügyelt példány beállításainak áttekintéséhez. 
1. Válassza a **Létrehozás** lehetőséget a másodlagos felügyelt példány létrehozásához. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a másodlagos felügyelt példányt a PowerShell használatával. 

   ```powershell-interactive
   # Configure secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary managed instance created successfully."
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot.  |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt rendel egy virtuális hálózathoz. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Beolvas egy alhálózatot egy virtuális hálózatban. | 
| [Új – AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Egy hálózati biztonsági csoportot hoz létre. | 
| [Új – AzRouteTable](/powershell/module/az.network/new-azroutetable) | Létrehoz egy útválasztási táblázatot. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Egy virtuális hálózat alhálózati konfigurációjának frissítése.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hálózati biztonsági csoport beolvasása. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Egy hálózati biztonsági csoport frissítése.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Útvonalat helyez el egy útválasztási táblához. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Frissíti az útválasztási táblázatot.  |
| [Új – AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Létrehoz egy Azure SQL Database felügyelt példányt.  |

---

## <a name="4---create-primary-gateway"></a>4 – elsődleges átjáró létrehozása 
Két felügyelt példánynak a feladatátvételi csoportban való részvételhez ExpressRoute vagy átjárót kell konfigurálnia a két felügyelt példány virtuális hálózatai között, hogy engedélyezze a hálózati kommunikációt. Ha úgy dönt, hogy [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) konfigurálja a két VPN-átjáró csatlakoztatása helyett, ugorjon a [7. lépésre](#7---create-a-failover-group).  

Ez a cikk a két VPN-átjáró létrehozásának lépéseit és azok összekapcsolását ismerteti, de a feladatátvételi csoport létrehozásához a ExpressRoute konfigurálásakor ugorjon előre. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Hozza létre az elsődleges felügyelt példány virtuális hálózatának átjáróját a Azure Portal használatával. 


1. A [Azure Portal](https://portal.azure.com)nyissa meg az erőforráscsoportot, és válassza ki a **virtuális hálózati** erőforrást az elsődleges felügyelt példányhoz. 
1. Válassza ki az **alhálózatok** lehetőséget a **Beállítások** területen, majd válassza a lehetőséget egy új **átjáró-alhálózat**hozzáadásához. Hagyja meg az alapértelmezett értékeket. 

   ![Átjáró hozzáadása az elsődleges felügyelt példányhoz](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Az alhálózat-átjáró létrehozása után válassza az **erőforrás létrehozása** lehetőséget a bal oldali navigációs ablaktáblán, majd írja be a `Virtual network gateway` kifejezést a keresőmezőbe. Válassza ki a **Microsoft**által közzétett **Virtual Network Gateway** -erőforrást. 

   ![Új virtuális hálózati átjáró létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Töltse ki a kötelező mezőket az elsődleges felügyelt példány átjárójának konfigurálásához. 

   Az alábbi táblázat az elsődleges felügyelt példány átjárója számára szükséges értékeket tartalmazza:
 
    | **Mező** | Érték |
    | --- | --- |
    | **Előfizetés** |  Az az előfizetés, amelyben az elsődleges felügyelt példánya. |
    | **Name (Név)** | A virtuális hálózati átjáró neve, például `primary-mi-gateway`. | 
    | **Régió** | Az a régió, amelyben a másodlagos felügyelt példány található. |
    | **Átjáró típusa** | Válassza a **VPN**lehetőséget. |
    | **VPN-típus** | **Útvonal-alapú** |
    | **Termékváltozat**| `VpnGw1`alapértelmezett értékének meghagyása. |
    | **Hely**| Az elsődleges felügyelt példány és az elsődleges virtuális hálózat helye.   |
    | **Virtuális hálózat**| Válassza ki a 2. szakaszban létrehozott virtuális hálózatot, például `vnet-sql-mi-primary`. |
    | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
    | **Nyilvános IP-cím neve**| Adja meg az IP-cím nevét, például `primary-gateway-IP`. |
    | &nbsp; | &nbsp; |

1. Hagyja meg a többi értéket alapértelmezettként, majd válassza a **felülvizsgálat + létrehozás** lehetőséget a virtuális hálózati átjáró beállításainak áttekintéséhez.

   ![Elsődleges átjáró beállításai](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Válassza a **Létrehozás** lehetőséget az új virtuális hálózati átjáró létrehozásához. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre az elsődleges felügyelt példány virtuális hálózatának átjáróját a PowerShell használatával. 

   ```powershell-interactive
   # Create primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt rendel egy virtuális hálózathoz. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Beolvas egy alhálózatot egy virtuális hálózatban. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre.  | 
| [Új – AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Egy Virtual Network átjáró IP-konfigurációjának létrehozása |
| [Új – AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Virtual Network átjáró létrehozása |


---


## <a name="5---create-secondary-gateway"></a>5 – másodlagos átjáró létrehozása 
Ebben a lépésben létrehozza a másodlagos felügyelt példány virtuális hálózatának átjáróját a Azure Portal használatával. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

A Azure Portal használatával ismételje meg az előző szakaszban leírt lépéseket a virtuális hálózati alhálózat és átjáró létrehozásához a másodlagos felügyelt példányhoz. Adja meg a szükséges mezőket a másodlagos felügyelt példány átjárójának konfigurálásához. 

   A következő táblázat a másodlagos felügyelt példány átjárója számára szükséges értékeket tartalmazza:

   | **Mező** | Érték |
   | --- | --- |
   | **Előfizetés** |  Az előfizetés, amelyben a másodlagos felügyelt példánya van. |
   | **Name (Név)** | A virtuális hálózati átjáró neve, például `secondary-mi-gateway`. | 
   | **Régió** | Az a régió, amelyben a másodlagos felügyelt példány található. |
   | **Átjáró típusa** | Válassza a **VPN**lehetőséget. |
   | **VPN-típus** | **Útvonal-alapú** |
   | **Termékváltozat**| `VpnGw1`alapértelmezett értékének meghagyása. |
   | **Hely**| A másodlagos felügyelt példány és a másodlagos virtuális hálózat helye.   |
   | **Virtuális hálózat**| Válassza ki a 2. szakaszban létrehozott virtuális hálózatot, például `vnet-sql-mi-secondary`. |
   | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
   | **Nyilvános IP-cím neve**| Adja meg az IP-cím nevét, például `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Másodlagos átjáró beállításai](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a másodlagos felügyelt példány virtuális hálózatának átjáróját a PowerShell használatával. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt rendel egy virtuális hálózathoz. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Beolvas egy alhálózatot egy virtuális hálózatban. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre.  | 
| [Új – AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Egy Virtual Network átjáró IP-konfigurációjának létrehozása |
| [Új – AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Virtual Network átjáró létrehozása |

---


## <a name="6---connect-the-gateways"></a>6 – az átjárók összekötése
Ebben a lépésben hozzon létre kétirányú kapcsolatot a két virtuális hálózat két átjárója között. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Kapcsolja össze a két átjárót a Azure Portal használatával. 


1. Válassza az **erőforrás létrehozása** lehetőséget a [Azure Portal](https://portal.azure.com).
1. Írja be a `connection` kifejezést a keresőmezőbe, majd nyomja le az ENTER billentyűt a kereséshez, amely a Microsoft által közzétett **kapcsolódási** erőforrásra viszi.
1. Válassza a **Létrehozás** lehetőséget a kapcsolódás létrehozásához. 
1. Az **alapvető beállítások** lapon válassza ki a következő értékeket, majd kattintson az **OK gombra**. 
    1. Válassza ki a **kapcsolattípus**`VNet-to-VNet`. 
    1. Válassza ki előfizetését a legördülő listából. 
    1. Válassza ki a felügyelt példányhoz tartozó erőforráscsoportot a legördülő menüből. 
    1. Válassza ki az elsődleges felügyelt példány helyét a legördülő menüből. 
1. A **Beállítások** lapon válassza ki vagy adja meg a következő értékeket, majd kattintson az **OK gombra**:
    1. Válassza ki az **első virtuális hálózati átjáró**elsődleges hálózati átjáróját, például `Primary-Gateway`.  
    1. Válassza ki a **második virtuális hálózati átjáró**másodlagos hálózati átjáróját, például `Secondary-Gateway`. 
    1. Jelölje be a **kétirányú kapcsolat létesítése**melletti jelölőnégyzetet. 
    1. Hagyja meg az alapértelmezett elsődleges kapcsolódási nevet, vagy nevezze át tetszőleges értékre. 
    1. Adjon meg egy **megosztott kulcsot (PSK)** a kapcsolatban, például `mi1m2psk`. 

   ![Átjáró-kapcsolatok létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. Az **Összefoglalás** lapon tekintse át a kétirányú kapcsolatok beállításait, majd kattintson **az OK** gombra a kapcsolódás létrehozásához. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A két átjáró összekapcsolásához használja a PowerShellt. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagot használja:

| Parancs | Megjegyzések |
|---|---|
| [Új – AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Kapcsolatot hoz létre a két virtuális hálózati átjáró között.   |

---


## <a name="7---create-a-failover-group"></a>7 – feladatátvételi csoport létrehozása
Ebben a lépésben létrehozza a feladatátvételi csoportot, és hozzáadja a felügyelt példányokat is. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Hozza létre a feladatátvételi csoportot a Azure Portal használatával. 


1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza ki az első szakaszban létrehozott elsődleges felügyelt példányt, például `sql-mi-primary`. 
1. A **Beállítások**területen navigáljon a **példány feladatátvételi csoportok** elemre, majd válassza a **Csoport hozzáadása** lehetőséget a **példány feladatátvételi csoport** lap megnyitásához. 

   ![Feladatátvételi csoport hozzáadása](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. A **példány feladatátvételi csoport** lapján írja be a feladatátvételi csoport nevét, például `failovergrouptutorial`, majd válassza ki a másodlagos felügyelt példányt, például `sql-mi-secondary` a legördülő listából. Válassza a **Létrehozás** lehetőséget a feladatátvételi csoport létrehozásához. 

   ![Feladatátvételi csoport létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. A feladatátvételi csoport központi telepítésének befejezése után vissza fog térni a **feladatátvételi csoport** lapra. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Hozza létre a feladatátvételi csoportot a PowerShell használatával. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagot használja:

| Parancs | Megjegyzések |
|---|---|
| [Új – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Létrehoz egy új Azure SQL Database felügyelt példány feladatátvételi csoportját.  |


---


## <a name="8---test-failover"></a>8 – feladatátvételi teszt
Ebben a lépésben a feladatátvételi csoportot a másodlagos kiszolgálóra fogja felvenni, majd a Azure Portal használatával hajtja végre a feladatokat. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Feladatátvételi teszt a Azure Portal használatával. 


1. Navigáljon a _másodlagos_ felügyelt példányhoz a [Azure Portalon](https://portal.azure.com) belül, és válassza a **példányok feladatátvételi csoportok** lehetőséget a beállítások területen. 
1. Tekintse át, hogy melyik felügyelt példány az elsődleges, és melyik felügyelt példány a másodlagos. 
1. Válassza a **feladatátvétel** lehetőséget, majd válassza az **Igen** lehetőséget a TDS-munkamenetek leválasztására vonatkozó figyelmeztetésben. 

   ![Feladatátvételi csoport feladatátvétele](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Tekintse át az elsődleges és a másodlagos példányt. Ha a feladatátvétel sikeres volt, a két példánynak átváltott szerepkörökkel kell rendelkeznie. 

   ![A felügyelt példányok a feladatátvételt követően váltanak át szerepköröket](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Nyissa meg az új _másodlagos_ felügyelt példányt, és kattintson ismét a **feladatátvétel** lehetőségre, hogy az elsődleges példány vissza legyen hajtva az elsődleges szerepkörre. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Feladatátvételi teszt a PowerShell használatával. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


A feladatátvételi csoport visszaállítása az elsődleges kiszolgálóra:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Lekérdezi vagy felsorolja a felügyelt példányok feladatátvételi csoportjait.| 
| [Kapcsoló – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Végrehajtja a felügyelt példányok feladatátvételi csoportjának feladatátvételét. | 

---



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az erőforrások tisztításához először törölje a felügyelt példányt, majd a virtuális fürtöt, majd a többi erőforrást, végül pedig az erőforráscsoportot. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navigáljon az erőforráscsoporthoz a [Azure Portal](https://portal.azure.com). 
1. Válassza ki a felügyelt példány (oka) t, majd válassza a **Törlés**lehetőséget. Írja be `yes` a szövegmezőbe annak megerősítéséhez, hogy törölni kívánja az erőforrást, majd válassza a **Törlés**lehetőséget. Ez a folyamat hosszabb időt is igénybe vehet a háttérben, és amíg el nem végzi a műveletet, nem fogja tudni törölni a *virtuális fürtöt* vagy bármely más függő erőforrást. Figyelje meg a törlést a tevékenység lapon a felügyelt példány törlésének megerősítéséhez. 
1. A felügyelt példány törlése után törölje a *virtuális fürtöt* úgy, hogy kiválasztja az erőforráscsoportot, majd a **Törlés**lehetőséget választja. Írja be `yes` a szövegmezőbe annak megerősítéséhez, hogy törölni kívánja az erőforrást, majd válassza a **Törlés**lehetőséget. 
1. Törölje a többi erőforrást. Írja be `yes` a szövegmezőbe annak megerősítéséhez, hogy törölni kívánja az erőforrást, majd válassza a **Törlés**lehetőséget. 
1. Törölje az erőforráscsoportot az **erőforráscsoport törlése**elem kiválasztásával, írja be az erőforráscsoport nevét, `myResourceGroup`, majd válassza a **Törlés**lehetőséget. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az erőforráscsoportot kétszer kell eltávolítania. Az erőforráscsoport eltávolításakor a rendszer először eltávolítja a felügyelt példányt és a virtuális fürtöket, de a hibaüzenettel `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Futtassa a Remove-AzResourceGroup parancsot egy második alkalommal, hogy eltávolítsa a fennmaradó erőforrásokat és az erőforráscsoportot.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

Az oktatóanyag ezen része a következő PowerShell-parancsmagot használja:

| Parancs | Megjegyzések |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot. |

---

## <a name="full-script"></a>Teljes szkript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot.  |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt rendel egy virtuális hálózathoz. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Beolvas egy alhálózatot egy virtuális hálózatban. | 
| [Új – AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Egy hálózati biztonsági csoportot hoz létre. | 
| [Új – AzRouteTable](/powershell/module/az.network/new-azroutetable) | Létrehoz egy útválasztási táblázatot. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Egy virtuális hálózat alhálózati konfigurációjának frissítése.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hálózati biztonsági csoport beolvasása. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Egy hálózati biztonsági csoport frissítése.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Útvonalat helyez el egy útválasztási táblához. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Frissíti az útválasztási táblázatot.  |
| [Új – AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Létrehoz egy Azure SQL Database felügyelt példányt.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Az Azure SQL felügyelt adatbázis példányával kapcsolatos információkat ad vissza. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre.  | 
| [Új – AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Egy Virtual Network átjáró IP-konfigurációjának létrehozása |
| [Új – AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Virtual Network átjáró létrehozása |
| [Új – AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Kapcsolatot hoz létre a két virtuális hálózati átjáró között.   |
| [Új – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Létrehoz egy új Azure SQL Database felügyelt példány feladatátvételi csoportját.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Lekérdezi vagy felsorolja a felügyelt példányok feladatátvételi csoportjait.| 
| [Kapcsoló – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Végrehajtja a felügyelt példányok feladatátvételi csoportjának feladatátvételét. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot. | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Nincsenek elérhető parancsfájlok a Azure Portal számára.

---

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy feladatátvételi csoportot konfigurált két felügyelt példány között. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Elsődleges felügyelt példány létrehozása
> - Hozzon létre egy másodlagos felügyelt példányt egy [feladatátvételi csoport](sql-database-auto-failover-group.md)részeként. 
> - Feladatátvétel tesztelése

Folytassa a következő rövid útmutatóval, hogyan csatlakozhat a felügyelt példányhoz, és hogyan állíthatja vissza az adatbázist a felügyelt példányra: 

> [!div class="nextstepaction"]
> [Kapcsolódás a felügyelt példányhoz](sql-database-managed-instance-configure-vm.md)
> [adatbázis visszaállítása felügyelt példányra](sql-database-managed-instance-get-started-restore.md)


