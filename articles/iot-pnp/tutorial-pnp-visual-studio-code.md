---
title: IoT-Plug and Play előnézeti eszköz létrehozása és tesztelése | Microsoft Docs
description: Az eszköz fejlesztőinek tekintse meg, hogyan lehet a VS Code használatával új eszköz-képesség modellt létrehozni és tesztelni egy IoT Plug and Play előnézeti eszközhöz.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719721"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Oktatóanyag: eszköz-képesség modell létrehozása és tesztelése a Visual Studio Code használatával

Ebből az oktatóanyagból megtudhatja, hogyan, mint az eszköz fejlesztői, hogyan hozhat létre egy _eszköz-képesség modellt_a Visual Studio Code használatával. A modellel létrehozhatja a csontváz kódját olyan eszközön való futtatáshoz, amely a felhőben egy Azure IoT Hub-példányhoz csatlakozik.

Az oktatóanyagban szereplő szakasz azt ismerteti, hogyan kell felépíteni a generált csontváz kódját, feltételezi, hogy a Windowst használja.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Eszköz-képesség modell létrehozása
> * Csontváz-eszköz kódjának készítése a modellből
> * A generált kódban szereplő Csonkok implementálása
> * A kód futtatásával tesztelheti az interakciókat egy IoT hub használatával

## <a name="prerequisites"></a>Előfeltételek

A jelen oktatóanyagban az eszköz képességeinek modelljével való együttműködéshez a következőkre lesz szüksége:

* [Visual Studio Code](https://code.visualstudio.com/download): a vs Code több platformon is elérhető
* [Azure IoT-eszközök a vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) bővítményi csomaghoz. A következő lépésekkel telepítheti a kiterjesztési csomagot a VS Code-ban:

    1. A VS Code-ban válassza a **kiterjesztések** lapot.
    1. Keresse meg az **Azure IoT-eszközöket**.
    1. Válassza az **Install** (Telepítés) lehetőséget.

A generált C kód a Windowsban való létrehozásához ebben az oktatóanyagban a következőkre lesz szüksége:

* [Hozzon létre eszközöket a Visual studióhoz](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) ,  **C++ és hozzon létre eszközöket** és **NuGet csomagkezelő összetevő** -számítási feladatokat. Ha már rendelkezik a [Visual Studióval (Közösség, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 vagy 2015-val, és ugyanazokat a számítási feladatokat telepítette.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

A jelen oktatóanyagban az eszköz kódjának teszteléséhez a következőkre lesz szüksége:

* Az [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases).
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Az eszköz modellezése

Az eszköz-képesség modell létrehozásához használja a _digitális kettős definíciós nyelvet_ . A modellek általában több _illesztőfelület_ -definíciós fájlból és egyetlen modellből állnak. A **vs Code-hoz készült Azure IoT Tools** olyan eszközöket tartalmaz, amelyek segítséget nyújtanak a JSON-fájlok létrehozásához és szerkesztéséhez.

### <a name="create-the-interface-file"></a>A csatoló fájljának létrehozása

A VS Code-ban a IoT-eszköz képességeit meghatározó illesztőfelület-fájl létrehozása:

1. Hozzon létre egy **devicemodel**nevű mappát.

1. Indítsa el a VS Code-ot, és használja a **CTRL + SHIFT + P** billentyűkombinációt a parancssor megnyitásához.

1. Adja meg **Plug and Play** , majd válassza ki a **IoT plug & Play: Create Interface** parancsot.

1. Keresse meg és válassza ki a létrehozott **devicemodel** mappát.

1. Ezután írja be a **EnvironmentalSensor** nevet az illesztő neveként, majd nyomja le az **ENTER**billentyűt. A VS Code egy **EnvironmentalSensor. Interface. JSON**nevű minta-illesztőfelület-fájlt hoz létre.

1. Cserélje le a fájl tartalmát a következő JSON-ra, és cserélje le a `{your name}` értéket a `@id` mezőben egyedi értékkel. Csak az a – z, A-Z, 0-9 és aláhúzás karaktereket használja. További információ: [digitális kettős azonosító formátuma](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). A csatoló AZONOSÍTÓjának egyedinek kell lennie, hogy mentse a felületet az adattárba:

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Ez az interfész definiálja az eszköz tulajdonságait, például az **ügyfél nevét**, a telemetria típusát, például a **hőmérsékletet**és a **turnon**parancsokat.

1. Adjon hozzá egy **Blink** nevű parancssori képességet a csatoló fájljának végén. Ügyeljen arra, hogy a parancs hozzáadása előtt adjon hozzá egy vesszőt. A definíció begépelésével megtekintheti, hogy az IntelliSense, az automatikus kiegészítés és az érvényesítés hogyan segíthet az illesztőfelület-definíciók szerkesztésében:

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Mentse a fájlt.

### <a name="create-the-model-file"></a>A modell fájljának létrehozása

A Model fájl határozza meg a IoT Plug and Play eszköz által megvalósított interfészeket. A modellben jellemzően legalább két interfész található – egy vagy több, amely meghatározza az eszköz adott képességeit, valamint egy olyan szabványos felületet, amelyet az összes IoT Plug and Play eszköznek végre kell hajtania.

A IoT Plug and Play-eszköz által megvalósított interfészeket megadó modell létrehozásához a VS Code-ban:

1. A Command paletta megnyitásához használja a **CTRL + SHIFT + P** billentyűkombinációt.

1. Adja meg **Plug and Play** , majd válassza ki a **IoT plug & Play: képesség modell létrehozása** parancsot. Ezután adja meg a **SensorboxModel** nevet a modell neveként. A VS Code egy **SensorboxModel. capabilitymodel. JSON**nevű minta-illesztőfelület-fájlt hoz létre.

1. Cserélje le a fájl tartalmát a következő JSON-ra, és cserélje le a `{your name}` a `@id` és a `EnvironmentalSensor` felületen a **EnvironmentalSensor. Interface. JSON** fájlban használt értékkel. A csatoló AZONOSÍTÓjának egyedinek kell lennie, hogy mentse a felületet az adattárba:

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    A modell olyan eszközt határoz meg, amely megvalósítja a **EnvironmentalSensor** felületet és a szabványos **DeviceInformation** felületet.

1. Mentse a fájlt.

### <a name="download-the-deviceinformation-interface"></a>A DeviceInformation felület letöltése

Mielőtt létrehozza a modellből a csontváz kódját, létre kell hoznia egy helyi másolatot a **DeviceInformation** a *nyilvános modell adattárból*. A nyilvános modell tárháza már tartalmazza a **DeviceInformation** felületet.

A **DeviceInformation** felület letöltése a nyilvános modell adattárból a vs Code használatával:

1. A Command paletta megnyitásához használja a **CTRL + SHIFT + P** billentyűkombinációt.

1. Adja meg a **Plug and Playt**, válassza ki a **modell megnyitása** parancsot, majd válassza a **nyilvános modell-adattár megnyitása**lehetőséget.

1. Válassza a **felületek**lehetőséget, majd válassza ki az eszköz információs felületét `urn:azureiot:DeviceManagement:DeviceInformation:1`azonosítóval, majd válassza a **Letöltés**lehetőséget.

Most már rendelkezik az eszköz képességeinek modelljét alkotó három fájllal:

* urn_azureiot_DeviceManagement_DeviceInformation_1. Interface. JSON
* EnvironmentalSensor. Interface. JSON
* SensorboxModel. capabilitymodel. JSON

## <a name="publish-the-model"></a>A modell közzététele

Ahhoz, hogy az Azure IoT Explorer eszköz beolvassa az eszköz képességeinek modelljét, közzé kell tennie a céges tárházban. A VS Code-ból való közzétételhez szüksége lesz a vállalati adattárhoz tartozó kapcsolódási karakterláncra:

1. Navigáljon az [Azure Certified for IoT portálra](https://aka.ms/ACFI).

1. A Microsoft _munkahelyi fiókjával_ jelentkezzen be a portálra.

1. Válassza a **vállalati tárház** , majd a **kapcsolatok karakterláncok**lehetőséget.

1. Másolja a kapcsolatok karakterláncát.

A vállalati adattár megnyitása a VS Code-ban:

1. A Command paletta megnyitásához használja a **CTRL + SHIFT + P** billentyűkombinációt.

1. Adja meg **Plug and Play** , majd válassza ki a **IoT plug & Play: Open Model adattár** parancsát.

1. Kattintson a **szervezeti modell megnyitása adattár** elemre, és illessze be a kapcsolatok sztringjét.

1. Nyomja le az **ENTER** billentyűt a vállalati adattár megnyitásához.

Az eszköz képességeinek modelljét és felületét a vállalati tárházban teheti közzé:

1. A Command paletta megnyitásához használja a **CTRL + SHIFT + P** billentyűkombinációt.

1. Adja meg **Plug and Play** , majd válassza ki a **IoT Plug & Play: Files küldése a Model adattárba** parancsot.

1. Válassza ki a **EnvironmentalSensor. Interface. JSON** és a **SensorboxModel. capabilitymodel. JSON** fájlt, majd kattintson **az OK gombra**.

A fájlok mostantól a céges tárházban tárolódnak.

## <a name="generate-code"></a>Kód létrehozása

A VS Code-hoz készült **Azure IoT Tools** használatával létrehozhat csontváz C-kódot a modellből. A csontváz kód előállítása a VS Code-ban:

1. A Command paletta megnyitásához használja a **CTRL + SHIFT + P** billentyűkombinációt.

1. Adja meg **Plug and Play** , majd válassza ki a **IoT Plug & Play: az eszköz Code helyettes** parancsának létrehozását.

1. Válassza ki a **SensorboxModel. capabilitymodel. JSON** képességi modellt tartalmazó fájlt.

1. Adja meg a **sensorbox_app** nevet a projekt neveként.

1. Válassza az **ANSI C** nyelvet.

1. A kapcsolódáshoz válassza a **IoT hub eszköz kapcsolati sztringje** lehetőséget.

1. Válassza **a Windows rendszerhez készült CMAK-projekt lehetőséget a** Project sablonként.

1. Válassza a **Vcpkg keresztül** lehetőséget az eszköz SDK-nak való felvételéhez.

A VS Code létrehozza a C-kód csontvázát, és a fájlokat a **modelcode** mappa **sensorbox_app** mappájába menti. A VS Code egy új ablakot nyit meg, amely a generált programkódokat tartalmazza.

## <a name="update-the-generated-code"></a>A generált kód frissítése

A kód létrehozása és futtatása előtt végre kell hajtania a stubbed tulajdonságait, a telemetria és a parancsokat.

A VS Code-ban a stubbed kód megvalósításának biztosítása:

1. Nyissa meg a **SensorboxModel_impl. c** fájlt.

1. Kód hozzáadása a stubbed függvények megvalósításához.

1. Mentse a módosításokat.

## <a name="build-the-code"></a>A kód létrehozása

Mielőtt futtatja a kódot a IoT Plug and Play eszköz Azure IoT hubhoz való teszteléséhez, le kell fordítania a kódot.

Kövesse a **sensorbox_app** mappában található **readme.MD** fájl utasításait a kód Windows rendszeren való létrehozásához és futtatásához. A következő szakasz útmutatást tartalmaz egy eszköz-kapcsolódási karakterlánc lekéréséhez, amely az eszköz kódjának futtatásakor használható.

## <a name="test-the-code"></a>A kód tesztelése

A kód futtatásakor a rendszer csatlakozik IoT Hubhoz, és elkezdi a minta-telemetria és-tulajdonságértékek küldését. Az eszköz a IoT Hub küldött parancsokra is válaszol. A viselkedés ellenőrzése:

1. IoT Hub létrehozása:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Adjon hozzá egy eszközt az IoT hubhoz, és kérje le a kapcsolati sztringet:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Jegyezze fel a kapcsolatok sztringjét.

1. A parancssorban navigáljon az **Azure-IOT-SDK-c** mappára, ahol létrehozta az SDK-t és a mintákat. Ezután navigáljon a **cmak\\sensorbox_app\\kiadás** mappájába.

1. Futtassa az alábbi parancsot:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Használja az Azure IoT Explorer eszközt az IoT hub-hoz csatlakoztatott IoT Plug and Play eszköz használatához. További információt az [Azure IoT Explorer telepítése és használata](./howto-install-iot-explorer.md)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

Most, hogy már létrehozott egy IoT Plug and Play minősítésre készen áll, Ismerje meg a következőket:

> [!div class="nextstepaction"]
> [Minősítésre kész eszköz létrehozása](tutorial-build-device-certification.md)
