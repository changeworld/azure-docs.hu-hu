---
title: Modulok üzembe helyezése az Azure CLI parancssorból – Azure IoT Edge
description: Az Azure CLI és az Azure IoT bővítmény használatával leküldheti az IoT Edge modult a IoT Hubról a IoT Edge eszközre, amelyet a telepítési jegyzék konfigurál.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e93360d4045f9c97d45abe2af489804a4c3c85f0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673502"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Az Azure CLI-vel az Azure IoT Edge-modulok telepítése

Miután létrehozott egy IoT Edge modulok az üzleti logikával rendelkező, érdemes őket az eszközökre telepíteni kívánt megfelelően működjenek a peremhálózaton. Ha több modulokat, amelyek együttműködve gyűjtenek és dolgoznak fel adatokat, és egyszerre telepítheti őket, és deklarálja az útválasztási szabályokat, amelyek csatlakoztathatja őket.

Az [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forráskódú, többplatformos parancssori eszköz az Azure-erőforrások, például a IoT Edge kezelésére. Lehetővé teszi, hogy az Azure IoT Hub-erőforrások, eszközregisztrációs szolgáltatáspéldányok és csatolt központok beépített kezelése. Az új IoT-bővítmény Azure CLI-vel bővíti, például az Eszközfelügyelet és teljes körű IoT Edge-képességek.

Ez a cikk bemutatja, hogyan hozzon létre egy JSON-manifest nasazení, majd küldje le az üzembe helyezés IoT Edge-eszköz fájl használatával. További információ a megosztott címkék alapján több eszközt célzó központi telepítés létrehozásáról: [IoT Edge modulok üzembe helyezése és figyelése nagy léptékben](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében.
* [IoT Edge-eszköz](how-to-register-device.md#register-with-the-azure-cli) , amelyen telepítve van a IoT Edge futtatókörnyezet.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI-verziójának 2.0.70 vagy újabbnak kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.
* Az [Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. Az üzembe helyezési jegyzékek működésével és létrehozásával kapcsolatos további információkért lásd: [IoT Edge modulok használatának, konfigurálásának és](module-composition.md)újbóli használatának ismertetése.

Az Azure CLI-vel modulok üzembe helyezéséhez egy .JSON kiterjesztésű fájlt mentse helyileg manifest nasazení. A fájl elérési útja fogja használni a következő szakaszban, amikor futtatja a parancsot a alkalmazni a konfigurációt az eszközre.

Íme egy modult az alapszintű üzemelő példányhoz jegyzék példaként:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

A modul információkkal konfigurált manifest nasazení alkalmazása modulok üzembe az eszközre.

Módosítsa a könyvtárakat a mappát, ahol a manifest nasazení van. Ha a VS Code IoT Edge-sablonok valamelyikét használta, használja a `deployment.json` fájlt a megoldás könyvtárának **konfigurációs** mappájába, és ne a `deployment.template.json` fájlt.

A következő parancsot használja a alkalmazni a konfigurációt egy IoT Edge-eszközön:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

A Device ID paraméter megkülönbözteti a kis-és nagybetűket. A tartalom paraméter mutat az üzembe helyezés manifest mentett fájlt.

   ![az iot edge set-modul kimenete](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Modulok megjelenítése az eszközön

Miután telepítette a modulokat az eszközön, megtekintheti azokat a következő paranccsal:

A modulok megtekintése az IoT Edge-eszközön:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

A Device ID paraméter megkülönbözteti a kis-és nagybetűket.

   ![az iot hub modul-identity list kimeneti](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [helyezhet üzembe és figyelheti IoT Edge modulokat a skálán](how-to-deploy-monitor.md)
