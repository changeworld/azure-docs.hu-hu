---
title: Az Azure Red Hat OpenShift támogatott erőforrásai
description: Megtudhatja, hogy mely Azure-régiók és virtuálisgép-méretek támogatottak Microsoft Azure Red Hat-OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243665"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure Red Hat OpenShift-erőforrások

Ez a témakör a Microsoft Azure Red Hat OpenShift szolgáltatás által támogatott Azure-régiókat és virtuálisgép-méreteket sorolja fel.

## <a name="azure-regions"></a>Azure-régiók

Az Azure Red Hat OpenShift-fürtök üzembe helyezéséhez tekintse meg a régiók [által elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) listáját.

## <a name="virtual-machine-sizes"></a>Virtuálisgép-méretek

Az alábbiakban az Azure Red Hat OpenShift-fürt számítási csomópontjaihoz megadható támogatott virtuálisgép-méretek láthatók.

> [!Important]
> Minden virtuális gép különböző számú meghajtót tud csatolni. Előfordulhat, hogy a memória vagy a CPU mérete nem lehet azonnal egyértelmű.
> Nem minden virtuálisgép-méret érhető el minden régióban. Ha az API támogatja a megadott méretet, akkor hibaüzenet jelenik meg, ha a méret nem érhető el a megadott régióban.
> További információkért tekintse meg régiónként a támogatott virtuálisgép- [méretek aktuális listáját](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) .

## <a name="compute-node-sizes"></a>Számítási csomópontok méretei

Az Azure Red Hat OpenShift REST API a következő számítási csomópont-méreteket támogatja:

|Méret|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standard F8s v2|8|16 GB|
|Standard F16s v2|16|32 GB|
|Standard F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Fő csomópontok méretei

Az Azure Red Hat OpenShift REST API a következő fő/infrastrukturális csomópontok méreteit támogatja:

|Méret|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>További lépések

Próbálja ki az [Azure Red Hat OpenShift-fürt létrehozásával](tutorial-create-cluster.md) foglalkozó oktatóanyagot.
