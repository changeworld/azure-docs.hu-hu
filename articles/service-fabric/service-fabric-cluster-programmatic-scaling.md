---
title: Azure Service Fabric programozott skálázás
description: Azure Service Fabric-fürt méretezése programozott módon, egyéni eseményindítók szerint
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: ffe07960c6d32bea8ec31b1fe8248b6abc2b63af
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458289"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Service Fabric-fürt programozott méretezése 

Az Azure-ban futó Service Fabric fürtök a virtuálisgép-méretezési csoportokra épülnek.  A [fürt skálázása](./service-fabric-cluster-scale-up-down.md) azt ismerteti, hogyan méretezhető Service Fabric fürtök manuálisan vagy automatikus méretezési szabályokkal. Ez a cikk bemutatja, hogyan kezelheti a hitelesítő adatokat, és hogyan méretezheti be vagy ki a fürtöt a Fluent Azure számítási SDK-val, ami egy fejlettebb forgatókönyv. Áttekintést az [Azure skálázási műveleteinek koordinálására szolgáló programozott módszerekről](service-fabric-cluster-scaling.md#programmatic-scaling)olvashat. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Hitelesítő adatok kezelése
A szolgáltatásnak a skálázás kezelésére való írásának egyik kihívása, hogy a szolgáltatásnak képesnek kell lennie a virtuálisgép-méretezési csoport erőforrásainak interaktív bejelentkezés nélküli elérésére. Ha a skálázási szolgáltatás módosítja a saját Service Fabric alkalmazást, a Service Fabric-fürt elérése egyszerűen megtörténik, de a méretezési csoport eléréséhez hitelesítő adatokra van szükség. A bejelentkezéshez használhatja az [Azure CLI](https://github.com/azure/azure-cli)-vel létrehozott [egyszerű szolgáltatásnevet](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) .

Egy egyszerű szolgáltatásnév a következő lépésekkel hozható létre:

1. Jelentkezzen be az Azure CLI-be (`az login`) olyan felhasználóként, aki hozzáfér a virtuálisgép-méretezési csoporthoz
2. Egyszerű szolgáltatásnév létrehozása `az ad sp create-for-rbac`
    1. Jegyezze fel a appId (más néven "ügyfél-azonosító"), a nevet, a jelszót és a bérlőt későbbi használatra.
    2. Szüksége lesz az előfizetés-AZONOSÍTÓra is, amely megtekinthető `az account list`

A Fluent számítási függvénytár a következőképpen tud bejelentkezni ezekkel a hitelesítő adatokkal (vegye figyelembe, hogy a [Microsoft. Azure. Management. Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) csomagban találhatók az Azure-beli Core-típusok (például `IAzure`):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

A bejelentkezést követően a méretezési csoport példányainak száma `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`keresztül kérdezhető le.

## <a name="scaling-out"></a>Méretezés
A Fluent Azure számítási SDK használatával a példányok csak néhány hívással adhatók hozzá a virtuálisgép-méretezési csoportokhoz.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Másik lehetőségként a virtuálisgép-méretezési csoport mérete is kezelhetők PowerShell-parancsmagokkal. [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) lekérheti a virtuálisgép-méretezési Csoport objektumát. Az aktuális kapacitás a `.sku.capacity` tulajdonságon keresztül érhető el. Miután módosította a kapacitást a kívánt értékre, az Azure-ban található virtuálisgép-méretezési csoport a [`Update-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) paranccsal frissíthető.

Amikor manuálisan ad hozzá egy csomópontot, a méretezési csoport példányainak hozzáadásához minden szükséges, ami egy új Service Fabric csomópont elindításához szükséges, mivel a méretezési csoport sablonja olyan bővítményeket tartalmaz, amelyek automatikusan csatlakoznak az új példányokhoz a Service Fabric-fürthöz. 

## <a name="scaling-in"></a>Skálázás folyamatban

A méretezése hasonló a horizontális felskálázáshoz. A virtuálisgép-méretezési csoport tényleges változásai gyakorlatilag azonosak. De ahogy korábban már említettük, Service Fabric csak az arany vagy ezüst tartósságával törli az eltávolított csomópontokat. Tehát a bronz-tartóssági méretezés esetében szükség van a Service Fabric-fürttel való interakcióra, hogy leállítsa a csomópontot, majd távolítsa el az állapotát.

A csomópont leállításra való előkészítése magában foglalja az eltávolítandó csomópont (a legutóbb hozzáadott virtuálisgép-méretezési csoport példányának) megkeresését és a deaktiválását. A virtuálisgép-méretezési csoport példányai a hozzáadásuk sorrendjében vannak számozva, így az újabb csomópontok a csomópontok neveiben található szám utótag összehasonlításával (amelyek megfelelnek a virtuálisgép-méretezési csoport példányainak). 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

A csomópont eltávolítását követően inaktiválható és eltávolítható ugyanazzal a `FabricClient` példánnyal és a `IAzure` példánnyal.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

A horizontális felskálázáshoz hasonlóan a virtuálisgép-méretezési csoport kapacitásának módosítására szolgáló PowerShell-parancsmagok is használhatók itt, ha a parancsfájl-kezelési módszer előnyös. A virtuálisgép-példány eltávolítása után Service Fabric csomópont-állapotot lehet eltávolítani.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Következő lépések

A saját automatikus méretezési logikája megvalósításának megkezdéséhez Ismerkedjen meg a következő fogalmakkal és hasznos API-kkal:

- [Méretezés manuálisan vagy automatikus méretezési szabályokkal](./service-fabric-cluster-scale-up-down.md)
- [Fluent Azure felügyeleti kódtárak a .net-hez](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (a Service Fabric-fürt mögöttes virtuálisgép-méretezési csoportjaival való interakcióhoz hasznos)
- [System. Fabric. FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (Service Fabric-fürttel és annak csomópontjaival való interakcióhoz hasznos)
