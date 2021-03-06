---
title: Színséma észlelése – Computer Vision
titleSuffix: Azure Cognitive Services
description: A rendszerképek színsémájának észlelésével kapcsolatos fogalmak a Computer Vision API használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e0fa85b8a90ea57d9b81bd2eeaa6d080b7582acd
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945278"
---
# <a name="detect-color-schemes-in-images"></a>Képek színsémáinak észlelése

Computer Vision elemzi a képen látható színeket, hogy három különböző attribútumot adjon meg: a domináns előtéri színt, a domináns háttérszínt, valamint a kép domináns színeit egészként. A visszaadott színek a készlethez tartoznak: fekete, kék, barna, szürke, zöld, narancssárga, rózsaszín, lila, piros, kékeszöld, fehér és sárga. 

A Computer Vision egy ékezetes színt is Kinyer, amely a képen a legélénkebb színt jelöli, a domináns színek és a telítettség kombinációja alapján. A rendszer a kiejtés színét hexadecimális HTML színkóddal adja vissza. 

A Computer Vision egy logikai értéket ad vissza, amely azt jelzi, hogy a képek fekete-fehérben vannak-e.

## <a name="color-scheme-detection-examples"></a>Példák a színséma észlelésére

Az alábbi példa a Computer Vision által visszaadott JSON-választ mutatja be, amikor észleli a képsémát a képen. Ebben az esetben a kép nem fekete-fehér kép, de a domináns előtér-és háttérszínek fekete színűek, a kép domináns színei pedig fekete és fehérek.

![Kültéri hegyi naplemente, egy személy sziluettje](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Domináns szín példák

A következő táblázat az egyes mintaképek előtérbeli, háttérszínét és képének színét mutatja be.

| Image | Domináns színek |
|-------|-----------------|
|![Fehér virág Zöld háttérrel](./Images/flower.png)| Előtér Fekete<br/>Háttér Fehér<br/>Színek Fekete, fehér, zöld|
![Állomáson futó vonat](./Images/train_station.png) | Előtér Fekete<br/>Háttér Fekete<br/>Színek Fekete |

### <a name="accent-color-examples"></a>Kiejtési színek példái

 A következő táblázat a visszaadott hangsúlyozási színt jeleníti meg hexadecimális HTML színértékként az egyes képképeknél.

| Image | Kiegészítő szín |
|-------|--------------|
|![A napnyugtakor egy hegyi sziklán álló személy](./Images/mountain_vista.png) | #BB6D10 |
|![Fehér virág Zöld háttérrel](./Images/flower.png) | #C6A205 |
|![Állomáson futó vonat](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Fekete & White észlelési példák

A következő táblázat Computer Vision fekete-fehér kiértékelését mutatja a minta lemezképekben.

| Image | Fekete & fehér? |
|-------|----------------|
|![A Manhattanben található épületek fekete-fehér képe](./Images/bw_buildings.png) | true |
|![Egy kék ház és az első udvar](./Images/house_yard.png) | false |

## <a name="next-steps"></a>További lépések

A képtípusok [észlelésével](concept-detecting-image-types.md)kapcsolatos fogalmak megismerése.
