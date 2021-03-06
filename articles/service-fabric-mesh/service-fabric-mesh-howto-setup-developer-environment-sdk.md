---
title: Windows fejlesztői környezet beállítása Service Fabric Mesh számára
description: Kialakíthatja úgy a Windows fejlesztési környezetet, hogy Service Fabric Mesh-alkalmazást hozhasson létre, és üzembe helyezhesse azt az Azure Service Fabric Mesh-ben.
author: dkkapur
ms.author: dekapur
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: a674047722d4deca02d8f4d38a0826e479065037
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259200"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>A Windows fejlesztési környezet kialakítása Service Fabric Mesh-alkalmazások létrehozásához

Az Azure Service Fabric Mesh-alkalmazások Windows-fejlesztői gépen való létrehozásához és futtatásához a következőkre lesz szüksége:

* Docker
* Visual Studio 2017 vagy újabb
* Service Fabric Mesh futtatókörnyezet
* Service Fabric Mesh SDK és eszközök.

Valamint a Windows következő verzióinak egyike:

* Windows 10 (Enterprise, Professional vagy Education) verziók 1709 (Fall Creators Update) vagy 1803 (Windows 10 április 2018 Update)
* Windows Server 1709-es verzió
* Windows Server 1803-es verzió

A következő utasítások segítséget nyújtanak a futtatott Windows-verzió alapján végzett minden telepítéshez.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

Service Fabric Mesh-alkalmazások telepítéséhez a Visual Studio 2017-es vagy újabb verziójára van szükség. [Telepítse a 15.6.0][download-visual-studio] vagy újabb verziót, és engedélyezze a következő számítási feladatokat:

* ASP.NET és webfejlesztés
* Azure-fejlesztés

## <a name="install-docker"></a>A Docker telepítése

Ha a Docker már telepítve van, győződjön meg arról, hogy a legújabb verzióval rendelkezik. A Docker kérheti, hogy ha egy új verzió ki van-e, de manuálisan is ellenőrizheti, hogy a legújabb verzióval rendelkezik-e.

#### <a name="install-docker-on-windows-10"></a>A Docker telepítése Windows 10 rendszeren

Töltse le és telepítse a [Windowshoz készült Docker Community Edition][download-docker] legújabb verzióját, hogy támogassa a Service Fabric Mesh által használt tároló Service Fabric alkalmazásokat.

Amikor a telepítés során a rendszer kéri, válassza a **Windows-tárolók használatát Linux-tárolók helyett**.

Ha a Hyper-V nincs engedélyezve a gépen, akkor a Docker telepítője felajánlja az engedélyezését. Ehhez kattintson az **OK** gombra, ha a rendszer arra kéri.

#### <a name="install-docker-on-windows-server-2016"></a>A Docker telepítése Windows Server 2016 rendszerre

Ha nincs engedélyezve a Hyper-V szerepkör, nyissa meg a PowerShellt rendszergazdaként, futtassa a következő parancsot a Hyper-V engedélyezéséhez, majd indítsa újra a számítógépet. További információ: [a Windows Serverhez készült Docker Enterprise Edition][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Indítsa újra a gépet.

Nyissa meg a PowerShellt rendszergazdaként, majd futtassa a következő parancsokat a Docker telepítéséhez:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK és eszközök

Telepítse a Service Fabric Mesh-futtatókörnyezetet, az SDK-t és az eszközöket a következő sorrendben.

1. Telepítse a [Service Fabric Mesh SDK][download-sdkmesh] -t a webplatform-telepítő használatával. Ez a Microsoft Azure Service Fabric SDK-t és a futtatókörnyezetet is telepíti.
2. Telepítse a [Visual studio Service Fabric Mesh Tools (előzetes verzió) bővítményt][download-tools] a Visual Studio Piactérről.

## <a name="build-a-cluster"></a>Fürt létrehozása

> [!IMPORTANT]
> Egy fürt létrehozása előtt a Dockernek futnia **kell**.
> A Docker futásának ellenőrzéséhez nyisson meg egy terminálablakot, majd a `docker ps` parancs futtatásával ellenőrizze, hogy történik-e hiba. Ha a válasz nem jelez hibát, akkor a Docker fut, és készen áll a fürt létrehozására.

> [!Note]
> Ha a Windows Fall Creators Update (1709-es verzió) gépen fejleszt, csak a Windows Version 1709 Docker-rendszerképeket használhatja.
> Ha a Windows 10 április 2018 Update (1803-es verzió) gépen dolgozik, a Windows-verzió 1709-es vagy 1803-es Docker-rendszerképeket is használhat.

Ha a Visual studiót használja, akkor kihagyhatja ezt a szakaszt, mert a Visual Studio létrehoz egy helyi fürtöt, ha még nem rendelkezik ilyennel.

Ha egyszerre csak egyetlen Service Fabric alkalmazást hoz létre és futtat, hozzon létre egy egycsomópontos helyi fejlesztési fürtöt a legjobb hibakeresési teljesítményhez. Ha egyszerre több alkalmazást futtat, hozzon létre egy öt csomópontos helyi fejlesztési fürtöt. Ha Service Fabric Mesh-projektet telepít vagy hibakeresést végez, a fürtnek futnia kell.

Miután telepítette a futtatókörnyezetet, az SDK-kat, a Visual Studio-eszközöket, a Docker-t és a Docker szolgáltatást, hozzon létre egy fejlesztési fürtöt.

1. Zárja be a PowerShell-ablakot.
2. Nyisson meg egy új, emelt szintű PowerShell-ablakot rendszergazdaként. Ez a lépés a nemrégiben telepített Service Fabric-modulok betöltéséhez szükséges.
3. Futtassa az alábbi PowerShell-parancsot egy fejlesztési fürt létrehozásához:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. A helyi fürtkezelő eszköz elindításához futtassa a következő PowerShell-parancsot:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Miután a Service cluster Manager eszköz fut (megjelenik a rendszertálcán), kattintson rá a jobb gombbal, majd kattintson a **helyi fürt indítása**lehetőségre.

![1\. ábra – a helyi fürt elindítása](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Készen áll a Service Fabric Mesh-alkalmazások létrehozására!

## <a name="next-steps"></a>További lépések

Olvassa át az [Azure Service Fabric-alkalmazás létrehozásáról](service-fabric-mesh-tutorial-create-dotnetcore.md) szóló szakaszt.

Választ kaphat a [gyakori kérdésekre és az ismert problémákra](service-fabric-mesh-faq.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/