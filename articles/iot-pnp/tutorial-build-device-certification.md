---
title: IoT-Plug and Play előnézeti eszköz létrehozása, amely készen áll a minősítésre | Microsoft Docs
description: Az eszköz fejlesztőinek megtudhatja, hogyan hozhat létre olyan IoT-Plug and Play előnézeti eszközt, amely készen áll a minősítésre.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: ce7d3ee8a0d05d837bc0049cba688cffe14d8a8c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721642"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>IoT Plug and Play előnézeti eszköz létrehozása, amely készen áll a minősítésre

Ebből az oktatóanyagból megtudhatja, hogyan fejlesztheti a IoT Plug and Play előnézeti eszközt, amely készen áll a minősítésre.

A minősítési tesztek a következőket ellenőrzi:

- A IoT Plug and Play eszköz kódja telepítve van az eszközön.
- A IoT Plug and Play eszköz kódját az Azure IoT SDK-val készítettük.
- Az eszköz kódja támogatja az [Azure-IoT hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
- Az eszköz kódja megvalósítja az eszköz információs felületét.
- A képesség modell és az eszköz kódja IoT Centralekkel működik.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- [Visual Studio Code](https://code.visualstudio.com/download)
- [Azure IoT-eszközök a vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Packhez

A Windows rendszerhez készült [eszköz-gyors útmutató létrehozásához az eszköz képességeinek modelljét](quickstart-create-pnp-device-windows.md) is be kell fejeznie. A rövid útmutató bemutatja, hogyan állíthatja be a fejlesztési környezetet a Vcpkg használatával, és hogyan hozhat létre egy minta projektet.

## <a name="store-a-capability-model-and-interfaces"></a>Képesség-modell és felületek tárolása

A IoT Plug and Play eszközökön olyan képességi modellt és interfészeket kell létrehoznia, amelyek az eszköznek a JSON-fájlként való használatát határozzák meg.

Ezeket a JSON-fájlokat három különböző helyen tárolhatja:

- A nyilvános modell tárháza.
- A vállalati modell tárháza.
- Az eszközön.

Jelenleg az eszköz hitelesítéséhez a fájlokat a vállalati modell adattárában vagy a nyilvános modell adattárában kell tárolni.

## <a name="include-the-required-interfaces"></a>A szükséges felületek belefoglalása

A minősítési folyamat átadásához be kell vonnia és végre kell hajtania az **eszköz adatai** felületet a képesség modellben. Ez az illesztőfelület a következő azonosítóval rendelkezik:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Ha elvégezte a gyors üzembe helyezést: eszköz- [képesség modell használata eszköz létrehozásához](quickstart-create-pnp-device-windows.md), a modellben már szerepel az **eszköz információi** felülete.

Az **eszköz adatai** felületének az eszköz modelljébe való felvételéhez adja hozzá az interfész azonosítóját a képesség modell `implements` tulajdonságához:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Az **eszköz információs** felületének megtekintése a vs Code-ban:

1. A Command paletta megnyitásához használja a **CTRL + SHIFT + P** billentyűkombinációt.

1. Adja meg **Plug and Play** , majd válassza ki a **IoT Plug and Play nyissa meg a Model repository** parancsot. Válassza a **nyilvános modell-adattár megnyitása**lehetőséget. A nyilvános modell tárháza a VS Code-ban nyílik meg.

1. A nyilvános modell adattárában válassza a **felületek** fület, válassza a szűrő ikont, majd írja be az **eszköz adatait** a szűrő mezőbe.

1. Az **eszköz adatai** felület helyi másolatának létrehozásához jelölje ki azt a szűrt listában, majd válassza a **Letöltés**lehetőséget. A VS Code megjeleníti a csatoló fájlját.

Az **eszköz információs** felületének megtekintése az Azure CLI használatával:

1. [Telepítse az Azure IOT CLI bővítményt](howto-install-pnp-cli.md).

1. Az alábbi Azure CLI-paranccsal megjelenítheti az eszköz információs felületének AZONOSÍTÓját tartalmazó felületet:

    ```cmd/sh
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

További információkért lásd: [Az Azure IoT bővítmény telepítése és használata az Azure CLI-hez](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Eszköz kódjának frissítése

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Eszköz kiépítés engedélyezése az Azure IoT Device kiépítési szolgáltatással (DPS)

Az eszköz hitelesítéséhez engedélyeznie kell az üzembe [helyezést az Azure IoT Device kiépítési szolgáltatás (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)használatával. Ha hozzá szeretné adni a DPS használatát, a C Code-t is létrehozhatja a VS Code-ban. Kövesse az alábbi lépéseket:

1. Nyissa meg a fájlt a VS Code-ban a DCM-fájllal, a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza az **eszköz kódjának előállítása**lehetőséget.

1. Válassza ki azt a DCM-fájlt, amelyet az eszköz kódjának létrehozásához használni kíván.

1. Adja meg a projekt nevét, például **sample_device**. Ez az eszköz-alkalmazás neve.

1. Válassza az **ANSI C** nyelvet.

1. Válasszon a következőn **keresztül: DPS (Device kiépítési szolgáltatás) szimmetrikus kulcs** , mint a kapcsolatok módszere.

1. Válassza a **Windows rendszerhez készült CMAK-projekt** lehetőséget a Project sablonként.

1. Válassza a **Vcpkg-on keresztül** lehetőséget az eszköz SDK-val való felvételéhez.

1. A VS Code egy új ablakot nyit meg a generált kódlap-fájlokkal.

## <a name="build-and-run-the-code"></a>A kód létrehozása és futtatása

A létrehozott Vcpkg-csomag használatával létrehozza a generált eszköz kódját. Az Ön által létrehozott alkalmazás szimulál egy olyan eszközt, amely egy IoT hubhoz csatlakozik. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

1. Hozzon létre egy `cmake` alkönyvtárat a `sample_device` mappában, és navigáljon a következő mappába:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a generált kód létrehozásához (a helyőrzőt cserélje le a Vcpkg-tárház könyvtárára):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > A Visual Studio 2017-es vagy a 2015-es verziójának használata esetén meg kell adnia a CMak-generátort az Ön által használt build-eszközök alapján:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Ha a Csatlakozáskezelő felügyeleti csomag nem C++ találja a fordítót, az előző parancs futtatásakor hibaüzeneteket kaphat. Ha ez történik, próbálja meg futtatni ezt a parancsot a [Visual Studio parancssorában](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. A létrehozás sikeres befejezése után adja meg a DPS hitelesítő adatait (DPS-**azonosító hatóköre**, **DPS szimmetrikus kulcs**, **eszköz azonosítója**) az alkalmazás paramétereinek megfelelően. A hitelesítő adatoknak a minősítési portálról való lekéréséhez tekintse [meg a IoT Plug and Play eszköz csatlakoztatása és tesztelése](tutorial-certification-test.md#connect-and-discover-interfaces)című témakört.

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Standard felületek implementálása

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>A modell információinak és az SDK-információs felületek implementálása

Az Azure IoT eszközoldali SDK implementálja a modell információit és az SDK információs felületeit. Ha a Code generálási függvényt használja a VS Code-ban, az eszköz kódja a IoT Plug and Play Device SDK-t használja.

Ha úgy döntött, hogy nem használja az Azure IoT eszközoldali SDK-t, a saját megvalósításához használhatja az SDK forráskódját is.

#### <a name="implement-the-device-information-interface"></a>Az eszköz információs felületének implementálása

Implementálja az eszköz **információs** felületét az eszközön, és adja meg az eszközre vonatkozó információkat az eszközről a futási időben.

Példaként használhatja a [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) rendszerhez készült **eszköz információi** felületének implementációját.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>A modellben definiált összes képesség megvalósítása

A minősítés során az eszköz programozott módon van tesztelve, hogy biztosítsa a felületén definiált képességek megvalósítását. Az 501-as HTTP-állapotkód használatával válaszoljon az írási és olvasási tulajdonságra, és ha az eszköz nem valósítja meg az eszközt, a parancs kéri.

## <a name="next-steps"></a>Következő lépések

Most, hogy már létrehozott egy IoT Plug and Play eszközre készen áll a minősítésre, a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan tanúsíthatja eszközét](tutorial-certification-test.md)
