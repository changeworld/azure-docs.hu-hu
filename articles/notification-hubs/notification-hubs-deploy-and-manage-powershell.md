---
title: Notification Hubs üzembe helyezése és kezelése a PowerShell használatával
description: Notification Hubs létrehozása és kezelése a PowerShell használatával automatizáláshoz
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 863fdb445cce41f0fe4cbee63a3d6198c0a79339
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264644"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Értesítési központok üzembe helyezése és kezelése a PowerShell-lel

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan használható az Azure Notification Hubs létrehozása és kezelése a PowerShell használatával. Ebben a cikkben a következő általános automatizálási feladatok jelennek meg.

- Értesítési központ létrehozása
- Hitelesítő adatok beállítása

Ha új Service Bus-névteret is létre kell hoznia az értesítési központok számára, olvassa el a [Service Bus kezelése a PowerShell használatával](../service-bus-messaging/service-bus-powershell-how-to-provision.md)című témakört.

Az értesítések központjának felügyeletét nem támogatja közvetlenül a Azure PowerShellhoz tartozó parancsmagok. A PowerShell legjobb megközelítése a Microsoft. Azure. NotificationHubs. dll szerelvényre való hivatkozás. A szerelvény elosztása a [Microsoft Azure Notification Hubs NuGet csomaggal](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)történik.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Az Azure egy előfizetés-alapú platform. Az előfizetés beszerzésével kapcsolatos további információkért lásd a [Vásárlási lehetőségek], a [Tag ajánlatok]vagy az [Ingyenes próbaverzió].
- Azure PowerShell-t futtató számítógép. Útmutatásért lásd: [Telepítse és konfigurálja az Azure PowerShellt].
- A PowerShell-parancsfájlok, a NuGet-csomagok és a .NET-keretrendszer általános ismerete.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Többek között a Service Bus .NET-szerelvényre mutató hivatkozás

Az Azure Notification Hubs kezelése még nem része a Azure PowerShell PowerShell-parancsmagjai. Az értesítési központok kiépítéséhez használhatja a [Microsoft Azure Notification Hubs NuGet csomagban](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)megadott .net-ügyfelet.

Először is győződjön meg arról, hogy a parancsfájl megkeresi a **Microsoft. Azure. NotificationHubs. dll** szerelvényt, amely NuGet-csomagként van telepítve egy Visual Studio-projektben. Ahhoz, hogy rugalmas legyen, a parancsfájl a következő lépéseket hajtja végre:

1. Meghatározza a meghívásának elérési útját.
2. Áthalad az elérési úton, amíg meg nem találja a `packages`nevű mappát. Ez a mappa akkor jön létre, amikor NuGet-csomagokat telepít a Visual Studio-projektekhez.
3. Rekurzív módon megkeresi a `packages` mappát `Microsoft.Azure.NotificationHubs.dll`nevű szerelvényhez.
4. A szerelvényre hivatkozik, hogy a típusok később is elérhetők legyenek.

A lépéseket egy PowerShell-parancsfájlban hajtja végre:

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>A `NamespaceManager` osztály létrehozása

Notification Hubs kiépítéséhez hozza létre a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager?view=azure-dotnet) osztály egy példányát az SDK-ból.

A Azure PowerShellhoz mellékelt [Get-AzureSBAuthorizationRule] parancsmaggal kérhet le egy olyan engedélyezési szabályt, amely a kapcsolódási karakterlánc biztosítására szolgál. A `NamespaceManager` példányra mutató hivatkozás a `$NamespaceManager` változóban van tárolva. `$NamespaceManager` az értesítési központ kiépítésére szolgál.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Új értesítési központ kiépítés

Új értesítési központ létrehozásához használja a .NET API-t a [.NET API a Notification Hubshoz].

A szkript ezen részében négy helyi változót kell beállítania.

1. `$Namespace`: állítsa be annak a névtérnek a nevére, amelyben az értesítési központot létre kívánja hozni.
2. `$Path`: ezt az elérési utat állítsa az új értesítési központ nevére.  Például: "MyHub".
3. `$WnsPackageSid`: a Windows [fejlesztői központból](https://developer.microsoft.com/en-us/windows)állítsa be a Windows-alkalmazás CSOMAGJÁNAK biztonsági azonosítóját.
4. `$WnsSecretkey`: a Windows [fejlesztői központból](https://developer.microsoft.com/en-us/windows)állítsa be a Windows-alkalmazás titkos kulcsát.

Ezek a változók a névtérhez való kapcsolódáshoz és egy új értesítési központ létrehozásához vannak konfigurálva, amely a Windows Notification Services (WNS) WNS hitelesítő adataival rendelkező Windows-alkalmazások kezelésére szolgál. További információ a csomag biztonsági azonosítójának és titkos kulcsának beszerzéséről: [Első lépések Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) oktatóanyaggal.

- A szkript a `NamespaceManager` objektum használatával ellenőrizze, hogy a `$Path` által azonosított értesítési központ létezik-e.
- Ha nem létezik, a szkript létrehozza `NotificationHubDescription` WNS hitelesítő adatokkal, és átadja azt a `NamespaceManager` Class `CreateNotificationHub` metódusnak.

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>További források

- [A Service Bus kezelése a PowerShell használatával](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Service Bus várólisták, témakörök és előfizetések létrehozása PowerShell-parancsfájl használatával](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Service Bus névtér és az Event hub létrehozása PowerShell-parancsfájl használatával](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Néhány előre elkészített szkript letöltése is elérhető:

- [PowerShell-parancsfájlok Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Vásárlási lehetőségek]: https://azure.microsoft.com/pricing/purchase-options/
[Tag ajánlatok]: https://azure.microsoft.com/pricing/member-offers/
[Ingyenes próbaverzió]: https://azure.microsoft.com/pricing/free-trial/
[Telepítse és konfigurálja az Azure PowerShellt]: /powershell/azureps-cmdlets-docs
[.NET API a Notification Hubshoz]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
