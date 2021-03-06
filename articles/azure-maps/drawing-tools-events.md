---
title: Rajzolási eszköztár hozzáadása térképhez | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá rajzolási eszköztárat térképekhez Microsoft Azure Maps web SDK használatával
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: cf9c79f608aa3ffd1137be41ff3348f62b890867
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198309"
---
# <a name="drawing-tool-events"></a>Rajzolási eszköz eseményei

Térképeken a rajzolási eszközök használatakor hasznos lehet reagálni bizonyos eseményekre, amikor a felhasználó a térképen rajzol. Ez a táblázat felsorolja a `DrawingManager` osztály által támogatott összes eseményt.

| Esemény | Leírás |
|-------|-------------|
| `drawingchanged` | Az alakzatban lévő bármely koordináta hozzáadása vagy módosítása történt. | 
| `drawingchanging` | Akkor aktiválódik, amikor megjelenik egy alakzat előnézeti koordinátái. Ez az esemény például többször is kigyullad, mivel a rendszer a koordinátákat húzza. | 
| `drawingcomplete` | Egy alakzat szerkesztési módból való befejezése vagy kilépésekor fellépett. |
| `drawingmodechanged` | A rajzolási mód módosult. Az új rajzolási mód átkerül az eseménykezelőbe. |
| `drawingstarted` | Akkor aktiválódik, amikor a felhasználó megkezdi az alakzat rajzolását, vagy szerkesztési módba helyezi az alakzatot.  |

Az alábbi kód azt mutatja be, hogyan működik a rajzolási eszközök moduljának eseményei. Rajzoljon alakzatokat a térképen, és tekintse meg az események tüzet.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzolási eszközök eseményei" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>rajzolási eszközeinek eseményeit</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Példák

Lássuk néhány olyan gyakori forgatókönyvet, amely a rajzolási eszközök eseményeit használja.

### <a name="select-points-in-polygon-area"></a>Pontok kijelölése a sokszög területen

Ez a kód azt mutatja be, hogyan lehet figyelni a felhasználói rajzok alakzatainak eseményeit. Ebben a példában a kód a sokszögek, a téglalapok és a körök alakzatait figyeli. Ezután meghatározza, hogy a térképen mely adatpontok vannak a rajzolt területen. A `drawingcomplete` eseményt a Select logika kiváltására használjuk. A válassza ki a logikát, a kód a térképen lévő összes adatponton keresztül hurkot. Ellenőrzi, hogy van-e metszéspontja a pontnak és a rajzolt alakzat területének. Ez a példa a nyílt forráskódú [Turf. js](https://turfjs.org/) függvénytár használatát teszi lehetővé a térbeli metszet számításának végrehajtásához.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A rajzolt sokszög területének kijelölése" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/XWJdeja'>rajzolt sokszög területen</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Rajzolás és keresés a sokszög területén

Ez a kód egy alakzat területén belüli érdeklődési pontokat keres, miután a felhasználó befejezte az alakzat rajzolását. A kód módosításához és végrehajtásához kattintson a keret jobb felső sarkában található "szerkesztés a kódban" elemre. A rendszer a `drawingcomplete` eseményt használja a keresési logika elindításához. Ha a felhasználó téglalapot vagy sokszöget rajzol, a rendszer a geometrián belüli keresést hajtja végre. Ha kört rajzol, a rendszer a sugár és a középpont pozícióját használja egy érdekes hely kereséséhez. A `drawingmodechanged` eseményt a rendszer annak meghatározására használja, hogy mikor váltson át a felhasználó a rajzolási módba, és ez az esemény törli a vásznat.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzolás és keresés a sokszög területén" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Rajzolás és keresés a sokszög területén</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Mérési eszköz létrehozása

Az alábbi kód azt mutatja be, hogy a rajzolási események hogyan használhatók mérési eszköz létrehozásához. A `drawingchanging` a rajzolt alakzat figyelésére szolgál. Ahogy a felhasználó áthelyezi az egeret, a rendszer kiszámítja az alakzat dimenzióit. A `drawingcomplete` eseményt a rendszer az alakzat végső számításának végrehajtásához használja a megrajzolása után. A `drawingmodechanged` eseményt a rendszer annak meghatározására használja, hogy mikor váltson át a felhasználó rajzolási módba. Emellett a `drawingmodechanged` esemény törli a rajzolási vászonat, és törli a régi mérési adatokat.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mérési eszköz" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>mérési eszközét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan használhatja a rajzolási eszközök modul további funkcióit:

> [!div class="nextstepaction"]
> [Alakzatadatok beolvasása](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Interakció típusa és billentyűparancsok](drawing-tools-interactions-keyboard-shortcuts.md)

További információ a Services modulról:

> [!div class="nextstepaction"]
> [Szolgáltatások modul](how-to-use-services-module.md)

További példák a kód megadására:

> [!div class="nextstepaction"]
> [Kód mintájának lapja](https://aka.ms/AzureMapsSamples)
