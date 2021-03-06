---
title: Azure IoT Edge és az Azure IoT Central | Microsoft Docs
description: Ismerje meg, hogyan használhatja a Azure IoT Edget egy IoT Central alkalmazással.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 69660152458de26e9dbcbf1f50db6ce6824351d0
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027068"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Azure IoT Edge eszközök csatlakoztatása Azure IoT Central-alkalmazáshoz

A IoT Edge három összetevőből áll:

* **IoT Edge modulok** az Azure-szolgáltatásokat, a partneri szolgáltatásokat vagy a saját kódokat futtató tárolók. A modulok IoT Edge eszközökön vannak telepítve, és helyileg futnak ezeken az eszközökön.
* A **IoT Edge futtatókörnyezet** minden IoT Edge eszközön fut, és felügyeli az egyes eszközökön üzembe helyezett modulokat.
* A **felhőalapú felület** lehetővé teszi IoT Edge-eszközök távoli figyelését és kezelését. IoT Central a felhő felülete.

Egy **Azure IoT Edge** eszköz lehet átjáró-eszköz, amely a IoT Edge eszközhöz csatlakozó alsóbb rétegbeli eszközökkel rendelkezik. Ez a cikk további információkat oszt meg az alsóbb rétegbeli eszközök kapcsolódási szokásairól.

Az **eszköz-sablonok** határozzák meg az eszköz képességeit és IoT Edge modulokat. A képességek közé tartozik a modul által küldött telemetria, a modul tulajdonságai, valamint az a parancs, amelyet a modul válaszol.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Alsóbb rétegbeli eszközök kapcsolatai átjáróval és modulokkal

Az alsóbb rétegbeli eszközök a `$edgeHub` modulon keresztül kapcsolódhatnak egy IoT Edge átjáró eszközhöz. Ez az IoT Edge-eszköz egy transzparens átjáró lesz ebben a forgatókönyvben.

![Transzparens átjáró diagramja](./media/concepts-iot-edge/gateway-transparent.png)

Az alárendelt eszközök egy egyéni modulon keresztül is csatlakozhatnak egy IoT Edge átjáró eszközéhez. A következő forgatókönyvben az alsóbb rétegbeli eszközök egy Modbus egyéni modulon keresztül csatlakoznak.

![Egyéni modul-kapcsolatok diagramja](./media/concepts-iot-edge/gateway-module.png)

Az alábbi ábrán egy IoT Edge átjáró-eszközhöz való kapcsolódás látható mindkét típusú modulon keresztül (egyéni és `$edgeHub`).  

![A csatlakozás diagramja mindkét kapcsolati modulon keresztül](./media/concepts-iot-edge/gateway-module-transparent.png)

Végül az alsóbb rétegbeli eszközök több egyéni modulon keresztül csatlakozhatnak egy IoT Edge átjáróhoz. Az alábbi ábrán egy Modbus egyéni modul, egy egyedi modul és a `$edgeHub` modul segítségével csatlakozó alsóbb rétegbeli eszközök láthatók. 

![Több egyéni modulon keresztüli csatlakozás diagramja](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Üzembe helyezési jegyzékek és eszközök sablonjai

IoT Edge az üzleti logikát modulok formájában helyezheti üzembe és kezelheti. A IoT Edge modulok a IoT Edge által kezelt számítási egységek legkisebb egységei, és tartalmazhatnak Azure-szolgáltatásokat (például Azure Stream Analytics) vagy a saját megoldásra vonatkozó kódokat. A modulok fejlesztésének, üzembe helyezésének és karbantartásának megismeréséhez tekintse meg [IoT Edge modulokat](../../iot-edge/iot-edge-modules.md).

Az üzembe helyezési jegyzék magas szinten az olyan modulok listája, amelyek a kívánt tulajdonságokkal vannak konfigurálva. Az üzembe helyezési jegyzék egy IoT Edge eszközt (vagy az eszközök egy csoportját) adja meg, mely modulokat kell telepíteni és konfigurálni. Az üzembe helyezési jegyzékek tartalmazzák a különálló modulok kívánt tulajdonságait. IoT Edge az eszközök jelentést készítenek az egyes modulok jelentett tulajdonságairól.

A Visual Studio Code használatával hozzon létre egy üzembe helyezési jegyzéket. További információk: [Azure IoT Edge a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)-hoz.

Az Azure IoT Central-ban importálhat egy üzembe helyezési jegyzéket egy eszköz sablon létrehozásához. A következő folyamatábra egy üzembe helyezési jegyzékfájl életciklusát mutatja IoT Centralban.

![Üzembe helyezési jegyzékfájl életciklusának folyamatábrája](./media/concepts-iot-edge/dmflow.png)

A IoT Plug and Play (előzetes verzió) a következőképpen modellezi a IoT Edge eszközt:

* Minden IoT Edge eszköz-sablonhoz tartozik egy eszköz-képesség modell.
* Az üzembe helyezési jegyzékben felsorolt összes egyéni modulhoz létrejön egy modul-képesség modell.
* Létrejön egy kapcsolat az egyes modulok képességeinek modellje és az eszköz képességeinek modellje között.
* A modul-képesség modell modul-illesztőfelületeket valósít meg.
* Mindegyik modul felülete telemetria, tulajdonságokat és parancsokat tartalmaz.

![IoT Edge modellezés diagramja](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>Átjáró-eszközök IoT Edge

Ha IoT Edge eszközt jelölt ki egy átjáró eszközként, az eszközhöz csatlakozni kívánó eszközökhöz hozzáadhat alsóbb rétegbeli kapcsolatokat az eszköz képességeinek modelljeihez.

## <a name="next-steps"></a>Következő lépések

Most, hogy tudja, mi IoT Central alkalmazás-sablonok, első lépésként [hozzon létre egy IoT Central alkalmazást](quick-deploy-iot-central.md).