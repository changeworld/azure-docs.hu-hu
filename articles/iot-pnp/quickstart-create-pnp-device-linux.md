---
title: Azure IoT-Plug and Play előnézeti eszköz létrehozása (Linux) | Microsoft Docs
description: Eszköz-képesség modell használata az eszköz kódjának létrehozásához. Ezután futtassa az eszköz kódját, és tekintse meg az eszközt a IoT Hubhoz való kapcsolódáshoz.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d2cc440572d6f33480972c15f5c498cc384cb2e3
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550482"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Gyors útmutató: eszköz-képesség modell használata IoT Plug and Play Preview-eszköz (Linux) létrehozásához

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

Az _eszköz képességi modellje_ (DCM) ismerteti a IoT Plug and Play eszköz képességeit. A DCM gyakran társítva van egy termék SKU-hoz. A DCM-ben meghatározott képességek újrafelhasználható felületekbe vannak rendezve. A DCM-eszköz kódját létrehozhatja a DCM-ből. Ez a rövid útmutató bemutatja, hogyan használható a VS code on Ubuntu Linux egy IoT Plug and Play-eszköz a DCM használatával történő létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy a Ubuntu Linux asztali környezettel használja. Az oktatóanyag lépései az Ubuntu 18,04 használatával lettek tesztelve.

A rövid útmutató elvégzéséhez telepítenie kell a következő szoftvereket a helyi linuxos gépre:

* Telepítse a **GCC**, a **git**, a **CMAK**és az összes függőséget a `apt-get` parancs használatával:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Ellenőrizze, hogy a `cmake` verziója meghaladja-e a **2.8.12** , és a **GCC** verziója meghaladja a **4.4.7**-t.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Az Azure IoT-eszközök telepítése

Az alábbi lépéseket követve telepítheti a VS Code bővítmény-csomaghoz készült [Azure IoT-eszközöket](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) :

1. A VS Code-ban válassza a **kiterjesztések** lapot.
1. Keresse meg az **Azure IoT-eszközöket**.
1. Válassza az **Install** (Telepítés) lehetőséget.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>A vállalati modell adattárához tartozó kapcsolatok karakterláncának beolvasása

A _vállalati modell adattárának kapcsolati karakterláncát_ az [Azure Certified for IoT portál](https://preview.catalog.azureiotsolutions.com) portálon találja, ha Microsoft munkahelyi vagy iskolai fiókkal jelentkezik be, vagy ha rendelkezik Microsoft partner-azonosítóval. A bejelentkezést követően válassza a **vállalati tárház** , majd a **kapcsolatok karakterláncok**lehetőséget.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban a [Vcpkg](https://github.com/microsoft/vcpkg) Library Manager használatával telepítheti az Azure IoT C Device SDK-t a fejlesztői környezetbe.

Nyisson meg egy rendszerhéjat. Futtassa a következő parancsot a Vcpkg telepítéséhez:

```bash
cd ~
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
```

Ez a művelet várhatóan több percig is eltarthat.

## <a name="author-your-model"></a>A modell szerzője

Ebben a rövid útmutatóban egy meglévő minta-eszköz képesség modellt és társított csatolókat használ.

1. Hozzon létre egy `pnp_app` könyvtárat a helyi meghajtón. Ezt a mappát kell használnia az eszköz modell fájljaihoz és az eszköz kódjához.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Töltse le az eszköz képességeinek modelljét és a felületi mintákat a `pnp_app` mappába.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Nyissa meg `pnp_app` mappát a VS Code-ban. A fájlokat az IntelliSense használatával tekintheti meg:

    ![Eszköz képességeinek modellje](media/quickstart-create-pnp-device-linux/dcm.png)

1. A letöltött fájlokban cserélje le a `<YOUR_COMPANY_NAME_HERE>`t a `@id` és a `schema` mezőkbe egy egyedi értékkel. Csak az a – z, A-Z, 0-9 és aláhúzás karaktereket használja. További információ: [digitális kettős azonosító formátuma](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>A C kód kiváltása

Most, hogy már rendelkezik DCM-rel és a hozzá tartozó csatolókkal, létrehozhatja a modellt megvalósító eszköz kódját. A C-kód a (z) VS Code-ban való létrehozásához:

1. A VS Code-ban megnyitott `pnp_app` mappában a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza az **eszköz kódjának előállítása**lehetőséget.

    > [!NOTE]
    > Amikor első alkalommal használja a IoT Plug and Play Code Generator segédprogramot, az automatikusan letölti és telepíti a szolgáltatást.

1. Válassza ki az **SampleDevice. capabilitymodel. JSON** fájlt, amelyet az eszköz kódjának generálásához kíván használni.

1. Adja meg a projekt nevét **sample_device**. Ez lesz az eszköz-alkalmazás neve.

1. Válassza az **ANSI C** nyelvet.

1. Válassza a **IoT hub eszköz kapcsolatok karakterlánca** lehetőséget a csatlakoztatási módszerként.

1. A Project-sablon **alapján válassza a Linux** rendszerhez készült CMAK-projekt lehetőséget.

1. Válassza a **Vcpkg-on keresztül** lehetőséget az eszköz SDK-val való felvételéhez.

1. A rendszer egy **sample_device** nevű új mappát hoz létre a DCM-fájllal megegyező helyen, és ez a generált kódlap-fájlok. A VS Code egy új ablakot nyit meg, amely megjeleníti ezeket.
    ![eszköz kódja](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>A kód létrehozása és futtatása

Az eszköz SDK forráskódját a generált eszköz kódjának összeállításához használhatja. Az Ön által létrehozott alkalmazás szimulál egy olyan eszközt, amely egy IoT hubhoz csatlakozik. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

1. Hozzon létre egy **CMAK** -Build mappát a **sample_device** alkalmazáshoz:

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Futtassa a CMakt az alkalmazás SDK-val való létrehozásához. A következő parancs feltételezi, hogy telepítette a **vcpkg** a saját mappájába:

    ```bash
    cmake .. -DCMAKE_TOOLCHAIN_FILE=~/vcpkg/scripts/buildsystems/vcpkg.cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build .
    ```

1. A létrehozás sikeres befejezése után futtassa az alkalmazást a IoT hub Device-kapcsolatok karakterláncának paraméterként való átadásával.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. Az eszköz megkezdi az adatok küldését a IoT Hubba.

    ![Eszközön futó alkalmazás](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>A kód ellenőrzése

### <a name="publish-device-model-files-to-model-repository"></a>Eszköz-modell fájljainak közzététele a Model repositoryban

Az eszköz kódjának az **az parancssori** felülettel való ellenőrzéséhez közzé kell tennie a fájlokat a modell adattárában.

1. A VS Code-ban megnyitott `pnp_app` mappában a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a parancsot, majd válassza a **IoT Plug & Play: fájlok elküldése a modell adattárba**lehetőséget.

1. Válassza ki `SampleDevice.capabilitymodel.json` és `EnvironmentalSensor.interface.json` fájlokat.

1. Adja meg a vállalati modell adattárához tartozó kapcsolatok sztringjét.

    > [!NOTE]
    > A kapcsolati karakterláncra csak akkor van szükség, amikor először csatlakozik az adattárhoz.

1. A VS Code output ablakban és az értesítésben megtekintheti, hogy a fájlok közzététele sikeresen megtörtént-e.

    > [!NOTE]
    > Ha hibaüzenet jelenik meg az eszköz-modell fájljainak közzétételekor, akkor próbálja meg használni a **IoT Plug and Play: Jelentkezzen ki a modell tárházában** a kijelentkezéshez, és ismételje meg a lépéseket.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>A kód érvényesítése az Azure IoT CLI használatával

Az eszköz-ügyfél mintájának elindítása után megtekintheti, hogy az Azure CLI-vel dolgozik-e.

A következő parancs használatával megtekintheti a telemetria küldő eszközét. Előfordulhat, hogy várnia kell egy percet vagy kettőt, mielőtt bármilyen telemetria lát a kimenetben:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDeviceID>
```

A következő parancs használatával tekintheti meg az eszköz által eljuttatott összes tulajdonságot:

```azurecli-interactive
az iot dt list-properties --device-id <YourDeviceID> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre IoT Plug and Play-eszközt DCM használatával.

Ha többet szeretne megtudni a DCMs és a saját modelljeinek létrehozásáról, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Oktatóanyag: eszköz-képesség modell létrehozása és tesztelése a Visual Studio Code használatával](tutorial-pnp-visual-studio-code.md)
