---
title: Csempe réteg hozzáadása Android-térképekhez | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg egy csempe rétegét a térképeken a Microsoft Azure Maps Android SDK használatával.
author: farah-alyasari
ms.author: v-faalya
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8e1a77ae83783b2841a2600654a9775e9ceb6ada
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209936"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Csempe réteg hozzáadása térképhez a Azure Maps Android SDK használatával

Ebből a cikkből megtudhatja, hogyan jelenítheti meg a csempéket a térképeken a Azure Maps Android SDK használatával. A csempe rétegek lehetővé teszik, hogy az alapszintű Térkép csempék fölé írja a képeket Azure Maps. Azure Maps csempe rendszerével kapcsolatos további információkért tekintse meg a [nagyítási szintek és a csempék rácsának](zoom-levels-and-tile-grid.md) dokumentációját.

Egy csempe réteg tölti be a csempéket egy kiszolgálóról. Ezeket a képeket előre megjelenítheti és tárolhatja, mint bármely más rendszerkép egy kiszolgálón, a csempe rétegének értelmezési elnevezési konvenció használatával. Ezeket a képeket olyan dinamikus szolgáltatással is megjelenítheti, amely a képeket közel valós időben hozza létre. Az Azure Maps TileLayer osztály három különböző csempe-szolgáltatási elnevezési konvenciót támogat:

* X, Y, nagyítási jelölés – a nagyítási szint alapján az x az oszlop, az Y pedig a csempén lévő csempe sor pozíciója.
* Quadkey jelölés – x, y és nagyítási információ egyetlen karakterlánc-értékre, amely egy csempe egyedi azonosítója.
* A határoló mezőhöz kötött koordináták a következő formátumban adhatók meg: `{west},{south},{east},{north}` amelyet általában a [web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms)használ.

> [!TIP]
> A TileLayer nagyszerű lehetőséget mutat a nagyméretű adathalmazok megjelenítésére a térképen. Nem csak a csempe réteg hozható létre egy képből, de a vektoros adatok csempe rétegként is megjeleníthető. A vektoros adattároló rétegként való megjelenítésével a Térkép vezérlőelemnek csak be kell töltenie a csempéket, ami sokkal kisebb lehet a fájlméretnél, mint az általuk képviselt adatmennyiség. Ezt a technikát sokan használják, akiknek több millió sornyi adatsort kell megjeleníteniük a térképen.

A csempe rétegbe átadott csempe URL-címének HTTP/HTTPS URL-címnek kell lennie egy TileJSON-erőforráshoz vagy egy csempe URL-sablonhoz, amely a következő paramétereket használja: 

* a csempe `{x}`-X pozíciója `{y}` és `{z}`is szükséges.
* a csempe `{y}` – Y pozíciója `{x}` és `{z}`is szükséges.
* `{z}` – a csempe nagyítási szintje. `{x}` és `{y}`is szükséges.
* `{quadkey}` csempe quadkey azonosítóját a Bing Maps csempe rendszer-elnevezési konvenciója alapján.
* `{bbox-epsg-3857}` – egy, a EPSG 3857 térbeli hivatkozási rendszerében `{west},{south},{east},{north}` formátumú határolókeret-karakterlánc.
* `{subdomain}` – a altartomány értékeinek helyőrzője, ha meg van adva az altartomány értéke.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő folyamat elvégzéséhez telepítenie kell [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) -t egy Térkép betöltéséhez.


## <a name="add-a-tile-layer-to-the-map"></a>Csempe réteg hozzáadása a térképhez

 Ez a minta bemutatja, hogyan hozhat létre csempéket tartalmazó csempe réteget. Ezek a csempék az "x, y, zoom" csemperendszer használatát használják. Ennek a csempe rétegnek a forrása az [Iowa Állami Egyetem Iowa környezeti Mesonet](https://mesonet.agron.iastate.edu/ogc/)származó időjárási radar. 

Az alábbi lépéseket követve hozzáadhat egy csempe réteget a térképhez.

1. Szerkessze a **res > elrendezést > activity_main. xml fájlt** úgy, hogy a következőképpen néz ki:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >
    
        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Másolja az alábbi kódrészletet a `MainActivity.java` osztály **onCreate ()** metódusára.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    A fenti kódrészlet először a **onReady ()** callback metódus használatával szerzi be Azure Maps Térkép vezérlőelem-példányát. Ezután létrehoz egy `TileLayer` objektumot, és átadja a formázott **XYZ** csempe URL-címét a `tileUrl` lehetőségnek. A réteg opacitása `0.8`re van állítva, és mivel a csempe szolgáltatásból származó csempék 256 képpont csempék, a rendszer ezeket az információkat átadja a `tileSize` lehetőségnek. Ezután a csempe réteget a Maps Layer Manager továbbítja.

    Miután hozzáadta a kódrészletet a fenti kódrészlethez, a `MainActivity.java`nek az alábbihoz hasonlóan kell kinéznie:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
    public class MainActivity extends AppCompatActivity {
    
        static{
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
    
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }    
    }
    ```

Ha most futtatja az alkalmazást, látnia kell egy vonalat a térképen az alábbi képen látható módon:

<center>

![androidos térképes vonal](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Következő lépések

A Térkép stílusainak beállításával kapcsolatos további tudnivalókért tekintse meg a következő cikket.

> [!div class="nextstepaction"]
> [Térkép stílusainak módosítása Android-térképeken](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)