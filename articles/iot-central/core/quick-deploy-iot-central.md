---
title: Azure IoT Central-alkalmazás létrehozása | Microsoft Docs
description: Új Azure IoT Central-alkalmazás létrehozása. Hozza létre az alkalmazást az ingyenes díjszabási csomag vagy az egyik standard díjszabási csomag használatával.
author: viv-liu
ms.author: viviali
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: d69a761df8066b4a84312c0c3ae8be5a79490960
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169438"
---
# <a name="create-an-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure IoT Central alkalmazást.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Navigáljon az [Azure IoT Central Build](https://aka.ms/iotcentral) webhelyére. Ezután jelentkezzen be a Microsoft személyes, munkahelyi vagy iskolai fiókjával.

Hozzon létre egy új alkalmazást az iparághoz kapcsolódó IoT Central sablonok listájából, amely segítséget nyújt a gyors kezdéshez, vagy az **egyéni alkalmazások** sablonjának használatával. Ebben a rövid útmutatóban az **egyéni alkalmazás** sablont használja.

Új Azure IoT Central-alkalmazás létrehozása az **egyéni alkalmazás** sablonból:

1. Navigáljon a **Build** oldalra:

    ![A IoT-alkalmazás oldalának összeállítása](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. Válassza az **egyéni alkalmazások** lehetőséget, és győződjön meg arról, hogy az **egyéni alkalmazás** sablon van kiválasztva.

1. Az Azure IoT Central automatikusan javaslatot tesz az **alkalmazás nevére** a kiválasztott alkalmazás sablonja alapján. Használhatja ezt a nevet, vagy megadhatja a saját felhasználóbarát alkalmazásának nevét.

1. Az Azure IoT Central az alkalmazás neve alapján egyedi **URL-** előtagot is létrehoz az Ön számára. Ezt az URL-címet használja az alkalmazás eléréséhez. Módosítsa ezt az URL-előtagot valami emlékezetre, ha szeretné.

    ![Azure IoT Central alkalmazás létrehozása lap](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Azure IoT Central számlázási adatok](media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png)

    > [!NOTE]
    > Ha az előző oldalon az **egyéni alkalmazás** lehetőséget választotta, megjelenik egy **alkalmazás-sablon** legördülő lista. Innen válthat az egyéni és a régi sablonok között. Előfordulhat, hogy a szervezete számára elérhetővé tett egyéb sablonokat is láthat.

1. Válassza ezt az alkalmazást a 7 napos ingyenes próbaverzió díjszabási csomagjának használatával, vagy a standard díjszabási csomagok valamelyikével:

    - Az *ingyenes* csomag használatával létrehozott alkalmazások hét napig ingyenesen használhatók, és legfeljebb öt eszközt támogatnak. Egy standard díjszabási csomag használatára a lejárat előtt bármikor átalakítható.
    - A *standard* csomag használatával létrehozott alkalmazások számlázása eszközönként történik, a **standard 1** vagy **Standard 2** díjszabási csomaggal pedig az első két eszköz ingyenesen használható. Az ingyenes és standard díjszabási csomagokról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/)tájékozódhat. Ha standard díjszabási csomag használatával hoz létre alkalmazást, ki kell választania a *címtárat*, az *Azure-előfizetést*és a *helyet*:
        - A *könyvtár* az a Azure Active Directory, amelyben létrehozza az alkalmazást. A Azure Active Directory felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti adatokat tartalmaz. Ha nincs Azure Active Directory, akkor létrejön egy Azure-előfizetés létrehozásakor.
        - Az *Azure-előfizetéssel* Azure-szolgáltatások példányait hozhatja létre. IoT Central az előfizetéshez tartozó erőforrásokat. Ha nem rendelkezik Azure-előfizetéssel, az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription)ingyenesen létrehozhat egyet. Az Azure-előfizetés létrehozása után váltson vissza az **új alkalmazás** lapra. Az új előfizetés mostantól megjelenik az **Azure-előfizetés** legördülő menüjében.
        - A hely az a [földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) *hely* , ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában ki kell választania az eszközökhöz legközelebb eső helyet. Ha kiválasztott egy helyet, később nem helyezheti át az alkalmazást egy másik helyre.

1. Tekintse át a használati feltételeket, majd válassza a **Létrehozás** elemet az oldal alján. Néhány perc elteltével IoT Central alkalmazás készen áll a használatra:

    ![Azure IoT Central-alkalmazás](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy IoT Central-alkalmazást. A következő javasolt lépés:

> [!div class="nextstepaction"]
> [Szimulált eszköz hozzáadása a IoT Central alkalmazáshoz](./quick-create-pnp-device.md)
