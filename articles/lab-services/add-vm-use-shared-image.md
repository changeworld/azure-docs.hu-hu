---
title: Virtuális gép hozzáadása megosztott rendszerkép használatával Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá virtuális gépet (VM) a csatolt megosztott rendszerkép-katalógus rendszerképének használatával Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775590"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Virtuális gép hozzáadása rendszerkép használatával a csatolt megosztott rendszerkép-gyűjteményből
Azure DevTest Labs lehetővé teszi, hogy megosztott képtárat csatoljon a laborhoz, majd a galériában lévő lemezképeket a laborban létrehozott virtuális gépek alapjaként használja. Ha meg szeretné tudni, hogyan csatolhat megosztott képtárat a laborhoz, tekintse meg a [megosztott képgyűjtemény konfigurálása](configure-shared-image-gallery.md)című témakört. Ebből a cikkből megtudhatja, hogyan adhat hozzá egy virtuális gépet a laborhoz a csatolt megosztott rendszerkép-katalógusból származó rendszerképek használatával. 

## <a name="azure-portal"></a>Azure Portal
Ebből a szakaszból megtudhatja, hogyan adhat hozzá egy virtuális gépet a laborhoz a Azure Portal használatával a csatolt megosztott képkatalógusból származó rendszerkép alapján. Ez a szakasz részletes útmutatást nyújt a virtuális gépek Azure Portal használatával történő létrehozásához. Ezeket a részleteket lásd: [virtuális gép létrehozása Azure Portal](devtest-lab-add-vm.md). Ez csak azokat a lépéseket mutatja be, amelyekben kijelöl egy rendszerképet a csatolt megosztott rendszerkép-katalógusból, és kiválasztja a használni kívánt rendszerkép verzióját. 

Ha a laborhoz ad hozzá egy virtuális gépet, kiválaszthat egy rendszerképet a csatolt megosztott rendszerkép-katalógusból alaprendszerképként: 

![Válasszon egy megosztott rendszerképet az alaphoz](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Ezután a **labor erőforrás létrehozása** lap **Speciális beállítások** lapján kiválaszthatja az alaprendszerképként használni kívánt rendszerkép verzióját:

![Rendszerkép verziójának kiválasztása](./media/add-vm-use-shared-image/select-version-shared-image.png)

A virtuális gép létrehozása után átválthat a rendszerkép egy másik verziójára. 

## <a name="resource-manager-template"></a>Resource Manager-sablon
Ha Azure Resource Manager sablont használ egy virtuális gép megosztott képtárat használó rendszerkép használatával történő létrehozásához, akkor a **Tulajdonságok** szakaszban a **sharedImageId** értékét kell megadnia. Lásd a következő példát: 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

A Resource Manager-sablonok teljes példáját lásd: [virtuális gép létrehozása megosztott](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) képkatalógus képtára használatával a GitHub-tárházban. 

## <a name="rest-api"></a>REST API

1. Először le kell kérnie a rendszerkép AZONOSÍTÓját a megosztott rendszerkép-katalógusban. Az egyik módszer az, hogy az alábbi GET paranccsal listázza az összes rendszerképet a csatolt megosztott Képtárban. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Hívja meg a PUT metódust a virtuális gépeken úgy, hogy átadja az előző hívásból a `properties.SharedImageId`felé kapott megosztott rendszerkép azonosítóját.

## <a name="next-steps"></a>További lépések
Ha szeretné megtudni, hogyan csatolhat egy megosztott képtárat egy laborhoz, és hogyan konfigurálhatja azt, tekintse meg a [megosztott](configure-shared-image-gallery.md)képtárat.