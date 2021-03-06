---
title: Oktatóanyag – Azure Service Fabric Mesh-erőforrások karbantartása
description: Ez a dokumentum az Azure Service Fabric Mesh-erőforrások eltávolítását ismerteti, hogy a már nem használt erőforrásokért ne kelljen fizetnie.
author: dkkapur
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: d5e33099d58c9cb4c110a78f2576d6eac83a7932
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351874"
---
# <a name="tutorial-remove-azure-resources"></a>Oktatóanyag: Azure-erőforrások eltávolítása

Ez az oktatóanyag egy sorozat ötödik része, amely azt mutatja be, hogyan törölheti az alkalmazásokat és azok erőforrásait, hogy ne kelljen értük fizetnie.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Az alkalmazások által használt erőforrások törlése a további költségek felmerülésének megelőzése érdekében.

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Service Fabric Mesh-alkalmazás létrehozása a Visual Studióban](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Egy, a helyi fejlesztési fürtön futó Service Fabric Mesh-alkalmazás hibakeresése](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás frissítése](service-fabric-mesh-tutorial-upgrade.md)
> * Service Fabric Mesh-erőforrások törlése

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem helyezte üzembe a teendőkezelő alkalmazást, kövesse a [Service Fabric Mesh-webalkalmazás közzétételét](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md) ismertető cikk utasításait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag vége. Amikor befejezte a létrehozott erőforrások használatát, törölje azokat, hogy a már nem használt erőforrásokért ne kelljen fizetnie. Ez amiatt is különösen fontos, mivel a háló egy kiszolgáló nélküli szolgáltatás, amelynek a számlázása másodpercalapon történik. A Mesh díjszabásával kapcsolatos további részletekért lásd: https://aka.ms/sfmeshpricing.

Az Azure által biztosított kényelmi szolgáltatások egyike, hogy az egy adott erőforráscsoportban létrehozott összes erőforrás törléséhez elegendő magát az erőforráscsoportot törölni, mivel ez az összes társított erőforrást is törli. Így azokat nem kell manuálisan egyenként törölnie.

Mivel a teendőkezelő alkalmazás létrehozásához új erőforráscsoportot hozott létre, nyugodtan törölheti azt, ami az összes társított erőforrást is törli.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Az **sfmeshTutorial1RG** erőforráscsoportot [a portálról is törölheti](../azure-resource-manager/management/manage-resource-groups-portal.md#delete-resource-groups). 

## <a name="next-steps"></a>Következő lépések

Most, hogy közzétette az Azure-ban a Service Fabric Mesh-alkalmazást, próbálkozzon a következőkkel:

* A szolgáltatások közötti kommunikáció további példájaként tekintse meg a [minta szavazóalkalmazást](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)
* A Service Fabric-erőforrásmodellel kapcsolatos további tudnivalókért lásd a [Service Fabric Mesh-erőforrásmodellt](service-fabric-mesh-service-fabric-resources.md) bemutató cikket.
* A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.
* Tájékozódjon a [Cloud Shellről](https://docs.microsoft.com/azure/cloud-shell/overview).