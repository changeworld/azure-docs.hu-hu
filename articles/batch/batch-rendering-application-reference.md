---
title: Renderelési alkalmazások használata – Azure Batch
description: Renderelési alkalmazások használata Azure Batch használatával. Ez a cikk az egyes renderelési alkalmazások futtatásának rövid leírását tartalmazza.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: dc0ce23c90a4ba6575ba26b37d97f94ba8fa1f63
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390485"
---
# <a name="rendering-applications"></a>Alkalmazások renderelése

A renderelési alkalmazások a Batch-feladatok és-feladatok létrehozásával használhatók. A feladat parancssori tulajdonsága megadja a megfelelő parancssort és paramétereket.  A feladatok feladatainak létrehozásához a legegyszerűbb módszer a Batch Explorer-sablonok használata az [ebben a cikkben](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer)megadott módon.  A sablonok megtekinthetők és módosíthatók, ha szükségesek.

Ez a cikk az egyes renderelési alkalmazások futtatásának rövid leírását tartalmazza.

## <a name="rendering-with-autodesk-3ds-max"></a>Renderelés az Autodesk 3ds Max-val

### <a name="renderer-support"></a>Megjelenítő támogatása

A 3ds Max-ban beépített renderelő elemek mellett a következő megjelenítők is elérhetők a renderelési virtuálisgép-lemezképeken, és a 3ds Max Scene fájl hivatkozhat rájuk:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Feladat parancssora

Hívja meg a `3dsmaxcmdio.exe` alkalmazást a parancssori renderelés végrehajtásához egy készlet-csomóponton.  Ez az alkalmazás a feladat futtatásakor megadott elérési úton található. A `3dsmaxcmdio.exe` alkalmazás ugyanazokkal az elérhető paraméterekkel rendelkezik, mint a `3dsmaxcmd.exe` alkalmazás, amely a [3ds Max Súgó dokumentációjában](https://help.autodesk.com/view/3DSMAX/2018/ENU/) található (renderelés | Parancssori renderelés szakasza).

Példa:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Megjegyzések:

* Az adategység fájljainak megtalálása érdekében nagy gonddal kell eljárni.  Győződjön meg arról, hogy az elérési utak helyesek és relatívak az **eszköz követési** ablakán keresztül, vagy használja a `-bitmapPath` paramétert a parancssorban.
* Ellenőrizze, hogy van-e probléma a rendereléssel, például ha nem szeretné, hogy az eszközök megtalálják a 3ds Max által írt `stdout.txt` fájlt a feladat futtatásakor.

### <a name="batch-explorer-templates"></a>Batch Explorer sablonok

A készlet **és a feladatok sablonjai a Batch Explorer** katalógusában érhetők el.  A sablon forrásfájljait a [GitHub batch Explorer adattárában](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax)érheti el.

## <a name="rendering-with-autodesk-maya"></a>Megjelenítés Autodesk Maya

### <a name="renderer-support"></a>Megjelenítő támogatása

A Maya beépített renderelő eszközökön kívül a következő megjelenítők is elérhetők a renderelési virtuálisgép-lemezképeken, és a 3ds Max Scene fájl hivatkozhat rájuk:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Feladat parancssora

A `renderer.exe` parancssori megjelenítő a feladat parancssorában van használatban. A parancssori megjelenítő dokumentációját a [Maya súgója ismerteti](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

A következő példában egy feladat-előkészítési tevékenység használható a jelenet fájljainak és eszközeinek a feladat-előkészítési munkakönyvtárba való másolására, a renderelési kép tárolására szolgáló kimeneti mappa, a 10. keret pedig megjelenítésre kerül.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

A V-Ray megjelenítéshez a Maya Scene fájl általában a renderer-ként fogja megadni a V-Ray-t.  A parancssorban is megadható:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Az Arnold rendering esetében a Maya Scene fájl általában az Arnold-t jeleníti meg megjelenítőként.  A parancssorban is megadható:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Batch Explorer sablonok

A készlet **és a feladatok sablonjai a Batch Explorer** katalógusában érhetők el.  A sablon forrásfájljait a [GitHub batch Explorer adattárában](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya)érheti el.

## <a name="next-steps"></a>Következő lépések

Használja a készletet és a feladatsort a [GitHub adattárházában](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) batch Explorer használatával.  Ha szükséges, hozzon létre új sablonokat, vagy módosítsa a megadott sablonok egyikét.