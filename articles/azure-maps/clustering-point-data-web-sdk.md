---
title: A csoportosítási pontra vonatkozó adatelemek | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan teheti a fürtöket, és hogyan jelenítheti meg egy térképen a Microsoft Azure Maps web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: e65681aefc047ba540d4ad0d91ef6e4d2af5f3ca
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190261"
---
# <a name="clustering-point-data"></a>Fürtözési pontra vonatkozó adatértékek

Ha sok adatpontot jelenít meg a térképen, az adatpontok átfedésben lehetnek egymással. Az átfedés miatt előfordulhat, hogy a Térkép olvashatatlan és nehezen használható. A fürtözési pontra vonatkozó adatgyűjtési folyamat a pontok egymáshoz közel lévő és a térképen való megjelenítését jelenti egyetlen fürtözött adatpontként. Ahogy a felhasználó nagyítja a térképet, a fürtök az egyes adatpontokon kívülre kerülnek. Ha nagy számú adatpontot használ, a fürtözési folyamatokkal növelheti felhasználói élményét.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Az adatforrások fürtözésének engedélyezése

Engedélyezze a fürtözést a `DataSource` osztályban úgy, hogy a `cluster` beállítást igaz értékre állítja. A `ClusterRadius` beállítása a közeli pontok kiválasztására és a fürthöz való egyesítésére. `ClusterRadius` értéke képpontban megadva. A `clusterMaxZoom` használatával megadnia azt a nagyítási szintet, amelynél le szeretné tiltani a fürtszolgáltatási logikát. Íme egy példa arra, hogyan engedélyezhető a fürtözés egy adatforrásban.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Ha két adatpont van a helyszínen, akkor lehetséges, hogy a fürt soha nem szakad meg egymástól, attól függetlenül, hogy a felhasználó milyen mértékben nagyítja a szolgáltatást. Ennek megoldásához beállíthatja a `clusterMaxZoom` lehetőséget, hogy letiltsa a fürtszolgáltatási logikát, és egyszerűen megjelenítse a mindent.

Az alábbiakban további módszereket talál, amelyeket a `DataSource` osztály biztosít a fürtözéshez:

| Módszer | Visszatérési típus | Leírás |
|--------|-------------|-------------|
| getClusterChildren (clusterId: szám) | Megígéri, hogy&lt;Array&lt;&lt;geometriát, bármely&gt; \| alakzatot&gt;&gt; | A következő nagyítási szinten kéri le a megadott fürt gyermekeit. Ezek a gyerekek az alakzatok és alfürtek kombinációja lehet. Az alfürtek a ClusteredProperties megfelelő tulajdonságokkal rendelkező funkciók lesznek. |
| getClusterExpansionZoom (clusterId: szám) | Ígéret&lt;szám&gt; | Kiszámítja azt a nagyítási szintet, amelynél a fürt megkezdi a kibővítését vagy szétbontását. |
| getClusterLeaves (clusterId: szám, korlát: szám, eltolás: szám) | Megígéri, hogy&lt;Array&lt;&lt;geometriát, bármely&gt; \| alakzatot&gt;&gt; | Egy fürt összes pontjának lekérése. Állítsa be úgy a `limit`, hogy a pontok egy részhalmazát adja vissza, és használja a `offset` a pontokon keresztül. |

## <a name="display-clusters-using-a-bubble-layer"></a>Fürtök megjelenítése buborék réteg használatával

A buborék réteg a fürtözött pontok renderelésének nagyszerű módja. Használjon kifejezéseket a sugár méretezésére és a szín módosítására a fürtben lévő pontok száma alapján. Ha buborék réteggel jeleníti meg a fürtöket, akkor egy különálló réteget kell használnia a nem fürtözött adatpontok megjelenítéséhez.

A buborék tetején található fürt méretének megjelenítéséhez használjon egy szöveget tartalmazó szimbólum réteget, és ne használjon ikont.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alapszintű buborékdiagram-fürtözés" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>alapszintű buborék rétegének fürtözését</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Fürtök megjelenítése egy szimbólum réteg használatával

Az adatpontok megjelenítésekor a szimbólum réteg automatikusan elrejti az egymással átfedésben lévő szimbólumokat a tisztább felhasználói felület biztosításához. Ez az alapértelmezett viselkedés nem kívánatos lehet, ha az adatpontok sűrűségét szeretné megjeleníteni a térképen. Ezek a beállítások azonban megváltoztathatók. Az összes szimbólum megjelenítéséhez állítsa a Symbol Layers `iconOptions` tulajdonság `allowOverlap` lehetőségét a következőre: `true`. 

A fürtözés használatával jelenítheti meg az adatpontok sűrűségét a tiszta felhasználói felület megőrzése mellett. Az alábbi minta bemutatja, hogyan adhat hozzá egyéni szimbólumokat, és hogyan jelölheti ki a fürtöket és az egyes adatpontokat a szimbólum réteg használatával.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürtözött szimbólum réteg" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>fürtözött szimbólum réteget</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Fürtözés és a Heat Maps réteg

A Heat Maps nagyszerű lehetőséget mutat a térképen lévő adatsűrűség megjelenítésére. Ez a vizualizációs módszer nagy számú adatpontot képes kezelni. Ha az adatpontok fürtözöttek, és a rendszer a fürt méretét használja a Heat Map súlyozásához, akkor a Heat Map még több adattal is képes kezelni. Ennek a beállításnak a megadásához állítsa `['get', 'point_count']`re a Heat Térkép réteg `weight` lehetőségét. Ha a fürt sugara kisméretű, a Heat Térkép a nem fürtözött adatpontok használatával nagyjából megegyezően fog megjelenni, de sokkal jobban fog végezni. Azonban minél kisebb a fürt sugara, annál pontosabbak lesznek a hőenergia-leképezések, de a teljesítményük kevesebb előnnyel jár.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A fürt súlyozott felmelegedési térképe" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>fürt súlyozott Heat térképét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Az egér eseményei a fürtözött adatpontokon

Ha az egér eseményei olyan rétegen történnek, amely fürtözött adatpontokat tartalmaz, a fürtözött adatpont GeoJSON pont szolgáltatás objektumként tér vissza az eseményre. Ennek a pontnak a funkciója a következő tulajdonságokkal fog rendelkezni:

| Tulajdonság neve             | Típus    | Leírás   |
|---------------------------|---------|---------------|
| `cluster`                 | logikai | Azt jelzi, hogy a szolgáltatás egy fürtöt jelöl-e. |
| `cluster_id`              | sztring  | A fürt egyedi azonosítója, amely a DataSource `getClusterExpansionZoom`, `getClusterChildren`és `getClusterLeaves` metódusokkal használható. |
| `point_count`             | szám  | A fürt által tartalmazott pontok száma.  |
| `point_count_abbreviated` | sztring  | Egy karakterlánc, amely a `point_count` értéket rövidíti, ha hosszú. (például 4 000-es lesz 4K)  |

Ez a példa egy buborék réteget hoz létre, amely megjeleníti a fürtöket, és hozzáadja a click (kattintás) eseményt. Ha az esemény-Eseményindítók elemre kattint, a kód kiszámítja és nagyítja a térképet a következő nagyítási szintre, amelyen a fürt elszakad. Ez a funkció a `DataSource` osztály `getClusterExpansionZoom` metódusával és a fürtözött adatpontra kattintva `cluster_id` tulajdonsággal valósítható meg.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürt getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/moZWeV/'>fürt getClusterExpansionZoom</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>A fürt környékének megjelenítése 

A fürt által reprezentált pont-adat egy adott terület felett van elosztva. Ebben a példában, amikor az egérmutatót egy fürt fölé viszi, két fő viselkedés fordul elő. Először a fürtben lévő egyes adatpontokat fogjuk használni a domború hajótest kiszámításához. Ezután a domború hajótest megjelenik a térképen egy terület megjelenítéséhez.  A domború hajótest egy olyan sokszög, amely egy rugalmas szalaghoz hasonló pontokat helyez el, és a `atlas.math.getConvexHull` metódussal számítható ki. A fürtben található összes pont a `getClusterLeaves` metódus használatával kérhető le az adatforrásból.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Fürt domború hajótest" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>fürt domború hajótestét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Fürtökben lévő adatösszesítés

A fürtök gyakran jelképezik a fürtön belüli pontok számát jelző szimbólumot. Előfordulhat azonban, hogy a fürtök stílusát további mérőszámokkal szeretné testre szabni. A fürtök összesítései esetében egyéni tulajdonságok hozhatók létre és tölthetők fel [összesítő kifejezés](data-driven-style-expressions-web-sdk.md#aggregate-expression) számításával.  A fürtök összesítései meghatározhatók a `DataSource``clusterProperties` lehetőségében.

Az alábbi minta összesítő kifejezést használ. A kód a fürt minden adatpontjának entitás típusa tulajdonsága alapján számítja ki a darabszámot. Amikor egy felhasználó rákattint egy fürtre, a felugró ablak további információkat jelenít meg a fürtről.

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürt összesítései" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
A tollas <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>fürtök összesítéseit</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján tekintheti meg a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [DataSource osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas. Math névtér](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Az alkalmazás funkcióinak hozzáadásához tekintse meg a kód példáit:

> [!div class="nextstepaction"]
> [Buborék réteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Heat Map-réteg hozzáadása](map-add-heat-map-layer.md)
