---
title: Azure IoT-eszközkezelés Azure IoT-eszközökkel a VSCode-hez
description: A Visual Studio Code-hoz készült Azure IoT Tools for Azure IoT Hub eszközkezelés, valamint a közvetlen metódusok és a Twin kívánt tulajdonságok felügyeleti lehetőségei.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 9d4d82472664900c96b77b31740573d0463465b8
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911910"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>A Visual Studio Code-hoz készült Azure IoT Tools használata az Azure IoT Hub-eszközök felügyeletéhez

![Végpontok közötti diagram](media/iot-hub-get-started-e2e-diagram/2.png)

Az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) egy hasznos Visual Studio Code-bővítmény, amely megkönnyíti a IoT hub felügyeletét és a IoT alkalmazás-fejlesztését. Olyan felügyeleti lehetőségeket tartalmaz, amelyek különböző feladatok elvégzésére használhatók.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Felügyeleti lehetőség          | Tevékenység                    |
|----------------------------|--------------------------------|
| Közvetlen metódusok             | Hajtson végre olyan eszközt, amely elindítja vagy leállítja az üzenetek küldését vagy az eszköz újraindítását.                                        |
| Írásvédett eszköz – Twin           | Egy eszköz jelentett állapotának beolvasása. Az eszköz például azt jelenti, hogy a LED azonnal villog.                                    |
| Eszköz dupla frissítése         | Helyezzen egy eszközt bizonyos állapotba, például állítsa be a LED-et zöldre, vagy állítsa a telemetria küldési intervallumát 30 percre.         |
| Felhőből az eszközre irányuló üzenetek   | Értesítések küldése egy eszközre. Például: "nagyon valószínű, hogy még ma is esik az eső. Ne felejtsen el egy esernyőt bevinni. "              |

További információ az ilyen beállításokkal kapcsolatos különbségekről és útmutatásról: az [eszközről a felhőbe irányuló kommunikációs útmutató](iot-hub-devguide-d2c-guidance.md) és a [felhőből az eszközre irányuló kommunikációs útmutató](iot-hub-devguide-c2d-guidance.md).

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. A IoT Hub minden olyan eszközön megtartja a különálló eszközt, amely csatlakozik hozzá. További információ az eszközök Twins-ról: Ismerkedés [az eszközök ikrekkel](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti a Visual Studio Code-hoz készült Azure IoT Tools használatát a fejlesztői számítógép különböző felügyeleti lehetőségeivel.

## <a name="what-you-do"></a>Teendők

A Visual Studio Code-hoz készült Azure IoT Tools futtatása különböző felügyeleti lehetőségekkel.

## <a name="what-you-need"></a>Mi szükséges

* Aktív Azure-előfizetés.
* Az előfizetéshez tartozó Azure IoT hub.
* [Visual Studio Code](https://code.visualstudio.com/)
* A VS Code-hoz készült [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) vagy a [hivatkozás megnyitása a Visual Studio Code-ban](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

1. A VS Code **Explorer** nézetében bontsa ki az **Azure IoT hub-eszközök** szakaszt a bal alsó sarokban.

2. Kattintson a helyi menü **IoT hub kiválasztása** elemére.

3. A jobb alsó sarokban megjelenik egy előugró ablak, amely lehetővé teszi az Azure-ba való bejelentkezést az első alkalommal.

4. A bejelentkezést követően megjelenik az Azure-előfizetések listája, majd válassza az Azure-előfizetés és a IoT Hub lehetőséget.

5. Az eszközök listája az **Azure IoT hub-eszközök** lapon, néhány másodperc alatt jelenik meg.

   > [!Note]
   > A telepítést elvégezheti a **Set IoT Hub Connection String** (IoT Hub kapcsolati sztring beállítása) elemre kattintva is. Adja meg annak a IoT hub- **iothubowner** a házirend-kapcsolati karakterláncát, amelyhez a IoT-eszköz csatlakozik az előugró ablakban.

## <a name="direct-methods"></a>Közvetlen metódusok

1. Kattintson a jobb gombbal az eszközre, és válassza a **közvetlen metódus meghívása**lehetőséget. 

2. Adja meg a metódus nevét és a hasznos adatokat a beviteli mezőben.

3. Az eredmények megjelennek a **kimenet** > **Azure IoT hub** nézetben.

## <a name="read-device-twin"></a>Írásvédett eszköz – Twin

1. Kattintson a jobb gombbal az eszközre, és válassza az **eszközök dupla szerkesztése**lehetőséget. 

2. Az **Azure-IOT-Device-Twin. JSON** fájl megnyitható az eszköz Twin tartalmával.

## <a name="update-device-twin"></a>Eszköz dupla frissítése

1. Végezze el a **címkék** vagy a **Tulajdonságok. kívánt** mező szerkesztését.

2. Kattintson a jobb gombbal az **Azure-IOT-Device-Twin. JSON** fájlra.

3. Válassza az **eszköz Twin frissítése** lehetőséget az eszköz dupla frissítéséhez.

## <a name="send-cloud-to-device-messages"></a>Üzenetek küldése a felhőből eszközökre

Az alábbi lépéseket követve küldhet üzenetet az IoT hub-ról az eszközre:
 
1. Kattintson a jobb gombbal az eszközre, és válassza az **C2D-üzenet küldése az eszközre**lehetőséget. 

2. Adja meg az üzenetet a beviteli mezőben.

3. Az eredmények megjelennek a **kimenet** > **Azure IoT hub** nézetben.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan használhatja az Azure IoT Tools bővítményt a Visual Studio Code-hoz különböző felügyeleti lehetőségekkel.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
