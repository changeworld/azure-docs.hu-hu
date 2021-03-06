---
title: Modulok üzembe helyezése Azure Portal-Azure IoT Edge
description: A Azure Portal IoT Hub segítségével leküldheti a IoT Edge modult a IoT Hubról a IoT Edge eszközre, amelyet a telepítési jegyzék konfigurál.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271446"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Az Azure Portalról az Azure IoT Edge-modulok telepítése

Miután létrehozott egy IoT Edge modulok az üzleti logikával rendelkező, érdemes őket az eszközökre telepíteni kívánt megfelelően működjenek a peremhálózaton. Ha több modulokat, amelyek együttműködve gyűjtenek és dolgoznak fel adatokat, és egyszerre telepítheti őket, és deklarálja az útválasztási szabályokat, amelyek csatlakoztathatja őket.

Ez a cikk bemutatja, hogyan az Azure Portalon végigvezeti egy manifest nasazení létrehozása és leküldése az üzembe helyezés az IoT Edge-eszköz. További információ a megosztott címkék alapján több eszközt célzó központi telepítés létrehozásáról: [IoT Edge modulok üzembe helyezése és figyelése nagy léptékben](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében.
* [IoT Edge-eszköz](how-to-register-device.md#register-in-the-azure-portal) , amelyen telepítve van a IoT Edge futtatókörnyezet.

## <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. Az üzembe helyezési jegyzékek működésével és létrehozásával kapcsolatos további információkért lásd: [IoT Edge modulok használatának, konfigurálásának és](module-composition.md)újbóli használatának ismertetése.

Az Azure Portalon rendelkezik egy varázslót, amely manifest nasazení, a JSON-dokumentum manuálisan semmint létrehozásának módját ismerteti. Három lépésből áll: **modulok hozzáadása**, **útvonalak megadása**és az **üzembe helyezés áttekintése**.

### <a name="select-device-and-add-modules"></a>Eszköz kiválasztása és modulok hozzáadása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.
1. A bal oldali panelen válassza a **IoT Edge** lehetőséget a menüből.
1. Kattintson az eszközök a listából a célként megadott eszköz Azonosítóját.
1. A felső sávon válassza a **modulok beállítása**lehetőséget.
1. A lap **Container Registry beállítások** szakaszában adja meg a modul lemezképeit tartalmazó privát tároló-nyilvántartók eléréséhez szükséges hitelesítő adatokat.
1. A lap **IoT Edge modulok** szakaszában válassza a **Hozzáadás**lehetőséget.
1. Tekintse meg a modulok típusát a legördülő menüből:

   * **IoT Edge modul** – megadja a modul nevét és a tároló rendszerképének URI-ját. Például a minta SimulatedTemperatureSensor-modul képuri-ja `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. Ha a modul rendszerképét egy privát tároló beállításjegyzékében tárolja, adja hozzá a hitelesítő adatokat ezen a lapon a rendszerkép eléréséhez.
   * **Piactér modul** – az Azure piactéren üzemeltetett modulok. Egyes piactér-modulok további konfigurálást igényelnek, ezért tekintse át a modul részleteit az [Azure marketplace IoT Edge modulok](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) listájában.
   * **Azure stream Analytics modul** – Azure stream Analytics munkaterhelés által generált modulok.

1. A modul hozzáadása után válassza ki a modul nevét a listából a modul beállításainak megnyitásához. Töltse ki az opcionális mezőket, ha szükséges. További információ a tároló létrehozási lehetőségeiről, az újraindítási szabályzatról és a kívánt állapotról: [EdgeAgent kívánt tulajdonságai](module-edgeagent-edgehub.md#edgeagent-desired-properties). További információ a modul Twin-ről: a [kívánt tulajdonságok meghatározása vagy frissítése](module-composition.md#define-or-update-desired-properties).
1. Ha szükséges, ismételje meg az 5 – 8. lépést további modulok hozzáadásához a központi telepítéshez.
1. Válassza a **Tovább: útvonalak** szakaszt az útvonalak szakaszhoz.

### <a name="specify-routes"></a>Útvonalak megadása

Az **útvonalak** lapon megadhatja, hogyan adja át az üzeneteket a modulok és a IoT hub között. Az üzenetek név/érték párokkal vannak kiépítve. Alapértelmezés szerint az útvonalat a rendszer **route (útvonal** ) néven adja meg, és a **/messages/\* a $upstreamba**, ami azt jelenti, hogy minden modulból kimenetet küld az IoT hubhoz.  

Adja hozzá vagy frissítse az útvonalakat a [deklarált útvonalak](module-composition.md#declare-routes)információi közül, majd válassza a **Tovább: felülvizsgálat + létrehozás** lehetőséget a varázsló következő lépésének folytatásához.

### <a name="review-deployment"></a>Tekintse át a központi telepítés

A tekintse át a szakasz azt mutatja be, a JSON-telepítésben manifest létrehozott az előző két szakaszokban alapján. Vegye figyelembe, hogy két modul deklarálta azt, hogy nem adta hozzá a következőket: **$edgeAgent** és **$edgeHub**. Ez a két modul hozza létre a [IoT Edge futtatókörnyezetet](iot-edge-runtime.md) , és minden központi telepítés esetében kötelező alapértelmezett érték.

Tekintse át az üzembe helyezési adatokat, majd kattintson a **Létrehozás**gombra.

## <a name="view-modules-on-your-device"></a>Modulok megjelenítése az eszközön

Miután telepítette a modulokat az eszközön, megtekintheti az összeset a IoT Hub eszköz részletei lapján. Ezen a lapon minden egyes telepített modul, valamint a hasznos információk, például a központi telepítési állapot és a kilépési kód nevét jeleníti meg.

## <a name="deploy-modules-from-azure-marketplace"></a>Modulok üzembe helyezése az Azure Marketplace-en

Az [Azure Marketplace](https://azuremarketplace.microsoft.com/) egy online alkalmazások és szolgáltatások piaca, ahol az Azure-on futó Nagyvállalati alkalmazások és megoldások széles körét böngészheti, beleértve a [IoT Edge modulokat](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)is.

IoT Edge modult az Azure piactéren és a IoT Hub is üzembe helyezhet.

### <a name="deploy-from-azure-marketplace"></a>Üzembe helyezés az Azure piactéren

Beolvassa a IoT Edge modulokat a piactéren, és ha megtalálta, hogy melyikre van szüksége, a **Létrehozás** **vagy a letöltés lehetőség**kiválasztásával telepítheti. Folytassa a központi telepítési varázsló lépéseit, amelyek a kiválasztott IoT Edge modultól függően változhatnak:

1. A **Folytatás**gombra kattintva nyugtázza a szolgáltató használati feltételeit és adatvédelmi szabályzatát. Előfordulhat, hogy először meg kell adnia a kapcsolattartási adatokat.
1. Válassza ki az előfizetését és azt a IoT Hub, amelyhez a céleszköz csatolva van.
1. Válassza **a telepítés eszközre**lehetőséget.
1. Adja meg az eszköz nevét, vagy válassza az **eszköz keresése** lehetőséget a hub-ban regisztrált eszközök között a tallózáshoz.
1. Válassza a **Létrehozás** lehetőséget a telepítési jegyzék konfigurálásának szabványos folyamatának folytatásához, beleértve a további modulok hozzáadását, ha szükséges. Az új modul adatai, például a rendszerkép URI-ja, a létrehozási beállítások és a kívánt tulajdonságok előre definiálva vannak, de módosíthatók.

Ellenőrizze, hogy a modul telepítve van-e a IoT Hub a Azure Portalban. Válassza ki az eszközt, válassza a **modulok beállítása** elemet, és a modult a **IoT Edge modulok** szakaszban kell megadnia.

### <a name="deploy-from-azure-iot-hub"></a>Üzembe helyezés az Azure IoT Hub

Az Azure Marketplace-en gyorsan üzembe helyezhet egy modult az eszközön a Azure Portal IoT Hub.

1. A Azure Portal navigáljon a IoT Hub.
1. A bal oldali ablaktábla **automatikus eszközkezelés**területén válassza a **IoT Edge**lehetőséget.
1. Válassza ki azt a IoT Edge eszközt, amely az üzemelő példány fogadására szolgál.
1. A felső sávon válassza a **modulok beállítása**lehetőséget.
1. A **IoT Edge-modulok** szakaszban kattintson a **Hozzáadás**gombra, és válassza ki a **piactér modult** a legördülő menüből.

![Modul hozzáadása a IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Válasszon egy modult a **IoT Edge modul Marketplace** oldaláról. A kiválasztott modul automatikusan konfigurálva van az előfizetéshez, az erőforráscsoporthoz és az eszközhöz. Ezután megjelenik a IoT Edge-modulok listájában. Egyes modulok további konfigurálást igényelhetnek.

> [!TIP]
> Az Azure IoT Hub IoT Edge moduljaival kapcsolatos információk korlátozottak. Először tájékozódhat az Azure Marketplace [IoT Edge moduljairól](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .

Válassza a Next (tovább) lehetőséget **: útvonalakat** , és folytassa az üzembe helyezést a jelen cikk [útvonalak megadása](#specify-routes) és [üzembe helyezés áttekintése](#review-deployment) című szakaszában leírtak szerint.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [helyezhet üzembe és figyelheti IoT Edge modulokat a skálán](how-to-deploy-monitor.md)
