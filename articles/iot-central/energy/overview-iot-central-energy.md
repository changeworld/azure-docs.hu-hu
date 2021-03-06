---
title: Energetikai megoldások létrehozása IoT Centralokkal | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre energiát az Azure IoT Central alkalmazás-sablonok használatával.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 13b626945743c17bad8df58fba77e1c960e90ef3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016562"
---
# <a name="build-energy-solutions-with-iot-central"></a>Energiaipari megoldások létrehozása az IoT Centrallal 




Az intelligens mérőszámok és a napelemes panelek fontos szerepet játszanak az energetikai átalakításban. Az intelligens mérőszámok további szabályozást és valós idejű elemzéseket biztosítanak az energiafogyasztással és a napelemek növekedésével kapcsolatban a megújuló energia-létrehozásban. Az intelligens fogyasztásmérő és a napelemek figyelésére szolgáló alkalmazások példák a különböző funkciók megjelenítésére. A partnerek kihasználhatják ezeket a sablonokat úgy, hogy az adott igényeknek megfelelően IoT Central energiát hozzanak létre. Nincsenek új kódolások, és az alkalmazások telepítéséhez és használatához nincs szükség további díjakra. További információ az energetikai alkalmazások sablonjairól és képességeiről.


## <a name="what-is-the-smart-meter-monitoring-application"></a>Mi az intelligens mérőműszer-figyelő alkalmazás?
 Az intelligens mérőszámok nem csak az automatizált számlázást teszik lehetővé, hanem a speciális mérési használati eseteket is, például a valós idejű olvasást és a kétirányú kommunikációt. Az intelligens fogyasztásmérő alkalmazás sablonja lehetővé teszi a segédprogramok és partnerek számára az Intelligens Fogyasztásmérők állapotának és adatkezelésének figyelését, riasztások és értesítések definiálását. Minta parancsokat biztosít, például a leválasztási mérőszámot és a szoftverek frissítését. A mérési adatait beállíthatja más üzleti alkalmazásokhoz való kilépésre és egyéni megoldások fejlesztésére. 

Az alkalmazás legfontosabb funkciói: 

* Mérőműszer minta eszköz modellje 
* Mérési adatok és élő állapot 
* Mérési mérések, például energia, energiaellátás és feszültségek
* A mérő parancs mintái 
* Beépített vizualizációk és irányítópultok
* Bővíthetőség az egyéni megoldások fejlesztéséhez

Az [intelligens fogyasztásmérőt figyelő alkalmazást](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring) Azure-előfizetés nélkül, díjmentesen kipróbálhatja.


Az alkalmazás üzembe helyezése után a szimulált mérési adat az irányítópulton látható, az alábbi ábrán látható módon. Ez a sablon egy minta alkalmazás, amellyel könnyedén bővíthetők és testreszabhatók a konkrét használati esetek.

> [!div class="mx-imgBorder"]
> ![intelligens fogyasztásmérő alkalmazás irányítópultja](media/overview-iot-central-energy/smart-meter-app-dashboard.png)


## <a name="what-is-the-solar-panel-monitoring-application"></a>Mi a napelemes figyelő alkalmazás?
A napelemes figyelési alkalmazás lehetővé teszi a segédprogramok és partnerek számára a napelemek, például az energiatermelés és a kapcsolati állapot figyelését közel valós időben. A megadott küszöbértékek alapján küldhet értesítéseket. Minta parancsokat biztosít, például a belső vezérlőprogram frissítését és egyéb tulajdonságokat. A napelemek adatait beállíthatja más üzleti alkalmazásokhoz és egyéni megoldások fejlesztéséhez. 

Az alkalmazás legfontosabb funkciói: 

* Napelem minta eszköz modellje 
* Napelemek adatai és élő állapota
* Napenergiás energiatermelés és egyéb beolvasás
* Parancs-és vezérlési minták
* Beépített vizualizációk és irányítópultok
* Bővíthetőség az egyéni megoldások fejlesztéséhez

Az Azure-előfizetés és az egyéb kötelezettségvállalások nélkül is ingyenesen kipróbálhatja a [Solar panel monitoring alkalmazást](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring) .

Az alkalmazás üzembe helyezése után az alábbi irányítópulton látható, 1-2 percen belül megjelenik a szimulált napelem-információ. Ez a sablon egy minta alkalmazás, amellyel könnyedén bővíthetők és testreszabhatók a konkrét használati esetek. 

> [!div class="mx-imgBorder"]
> ![napelemes alkalmazás irányítópultja](media/overview-iot-central-energy/solar-panel-app-dashboard.png)


## <a name="next-steps"></a>Következő lépések
Az energetikai megoldások létrehozásának megkezdéséhez:
* Ingyenes alkalmazás-sablonok létrehozása: [intelligens fogyasztásmérő alkalmazás](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring), [napelemes](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring) alkalmazás
* További tudnivalók az [intelligens fogyasztásmérő monitorozási alkalmazásával kapcsolatos fogalmakról](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* Tudnivalók a [napelemek monitorozási alkalmazásával kapcsolatos fogalmakról](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Tudnivalók a [IoT Central platformról](https://docs.microsoft.com/azure/iot-central/)
