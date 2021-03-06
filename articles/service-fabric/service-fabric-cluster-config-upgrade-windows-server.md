---
title: Önálló fürt konfigurációjának frissítése
description: Ismerje meg, hogyan frissítheti az önálló Service Fabric fürtöt futtató konfigurációt.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8e7e01dac29cb9ba91c83270dac4e46c73b2089e
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610122"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Önálló fürt konfigurációjának frissítése 

Bármely modern rendszer esetében a frissítés a termék hosszú távú sikerességének kulcsa. Az Azure Service Fabric-fürt egy saját erőforrás. Ez a cikk az önálló Service Fabric-fürt konfigurációs beállításainak frissítését ismerteti.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>A fürtkonfiguráció testreszabása a ClusterConfig. JSON fájlban
Az önálló fürtök konfigurálása a *ClusterConfig. JSON* fájlon keresztül történik. További információ a különböző beállításokról: [önálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md).

A *ClusterConfig. JSON* [fürt tulajdonságai](./service-fabric-cluster-manifest.md#cluster-properties) szakaszának `fabricSettings` szakaszában adhat hozzá, frissíthet vagy eltávolíthat beállításokat. 

A következő JSON például egy új *MaxDiskQuotaInMB* -beállítást hoz létre a *diagnosztika* szakaszhoz a `fabricSettings`alatt:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Miután módosította a beállításokat a ClusterConfig. JSON fájlban, [tesztelje a fürtöt](#test-the-cluster-configuration) , majd [frissítse a fürtöt](#upgrade-the-cluster-configuration) , hogy alkalmazza a beállításokat a fürtön. 

## <a name="test-the-cluster-configuration"></a>A fürt konfigurációjának tesztelése
A konfiguráció frissítésének megkezdése előtt tesztelheti az új fürtkonfiguráció JSON-t a következő PowerShell-szkript futtatásával az önálló csomagban:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Vagy használja a következő parancsfájlt:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Egyes konfigurációk nem frissíthetők, például végpontok, fürt neve, csomópont IP-címe stb. Az új fürtkonfiguráció JSON a régivel van tesztelve, és hibák léptek fel a PowerShell-ablakban, ha probléma merül fel.

## <a name="upgrade-the-cluster-configuration"></a>A fürtkonfiguráció frissítése
A fürt konfigurációjának frissítéséhez futtassa a [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)parancsot. A konfiguráció frissítését a frissítési tartomány dolgozza fel.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Fürtkonfiguráció konfigurációjának frissítése
A fürtcsomópontok közötti hitelesítéshez a rendszer a fürt tanúsítványát használja. A tanúsítvány-átváltást óvatosan kell elvégezni, mert a hiba blokkolja a fürtcsomópontok közötti kommunikációt.

Négy lehetőség támogatott:  

* Egyetlen tanúsítvány frissítése: a frissítési útvonal az A tanúsítvány (elsődleges) – > B tanúsítvány (elsődleges) – > tanúsítvány C (elsődleges) – >....

* Dupla tanúsítvány frissítése: a frissítési útvonal az a tanúsítvány (elsődleges) – > tanúsítvány A (elsődleges) és B (másodlagos) – > B tanúsítvány (elsődleges) – > B (elsődleges) és C (másodlagos) – > C (elsődleges) tanúsítvány, >....

* Tanúsítvány típusának frissítése: ujjlenyomat-alapú tanúsítvány-konfiguráció < – > Köznapinév-alapú tanúsítvány-konfiguráció. Például: A Tanúsítvány ujjlenyomata A (elsődleges) és A B ujjlenyomat (másodlagos) – > a C Köznapinév tanúsítvány.

* Tanúsítvány kiállítói ujjlenyomatának frissítése: a frissítési útvonal a következő tanúsítvány: CN = A, IssuerThumbprint = IT1 (elsődleges)-> tanúsítvány CN = A, IssuerThumbprint = IT1, IT2 (elsődleges)-> tanúsítvány CN = A, IssuerThumbprint = IT2 (elsődleges).


## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan szabhatja testre a [Service Fabric fürtkonfiguráció beállításait](service-fabric-cluster-fabric-settings.md).
* Ismerje meg, hogyan [méretezheti a fürtöt a és a](service-fabric-cluster-scale-up-down.md)szolgáltatásban.
* Az [alkalmazások frissítéseinek](service-fabric-application-upgrade.md)megismerése.

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
