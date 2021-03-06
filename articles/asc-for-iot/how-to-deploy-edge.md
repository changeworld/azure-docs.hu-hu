---
title: Azure Security Center üzembe helyezése IoT Edge modulhoz | Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy Azure Security Center a IoT biztonsági ügynökön a IoT Edgeon.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: b2af392dc4dc848a099b8297bb58e7d4a7104fa6
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964039"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Biztonsági modul üzembe helyezése a IoT Edge eszközön


A **IoT modul Azure Security Center** átfogó biztonsági megoldást nyújt a IoT Edge-eszközökhöz.
A biztonsági modul az operációs rendszer és a tárolórendszer nyers biztonsági adatainak gyűjtését, összesítését és elemzését végezheti el a gyakorlatban használható biztonsági javaslatokkal és riasztásokkal.
További információ: [IoT Edge biztonsági modulja](security-edge-architecture.md).

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy biztonsági modult a IoT Edge eszközön.

## <a name="deploy-security-module"></a>Biztonsági modul telepítése

A következő lépésekkel telepítheti a IoT Edge IoT biztonsági moduljának Azure Security Center.

### <a name="prerequisites"></a>Előfeltételek

1. A IoT Hub ellenőrizze, hogy az eszköz IoT Edge- [eszközként](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)van-e regisztrálva.

1. A IoT Edge modul Azure Security Center a [naplózott keretrendszer](https://linux.die.net/man/8/auditd) telepítése szükséges a IoT Edge eszközön.

    - A keretrendszer telepítéséhez futtassa a következő parancsot a IoT Edge eszközön:
   
    `sudo apt-get install auditd audispd-plugins`

    - A naplózott ellenőrzés aktív a következő parancs futtatásával: 
   
    `sudo systemctl status auditd`<br>
    - A várt válasz: `active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Üzembe helyezés Azure Portal használatával

1. A Azure Portal nyissa meg a **Marketplace**-t.

1. Válassza a **eszközök internetes hálózata**lehetőséget, majd keresse meg a **IoT Azure Security Center** , és válassza ki.

   ![IoT Azure Security Center kiválasztása](media/howto/edge-onboarding-8.png)

1. A telepítés konfigurálásához kattintson a **Létrehozás** gombra. 

1. Válassza ki a IoT Hub Azure- **előfizetését** , majd válassza ki a **IoT hub**.<br>Válassza a **telepítés egy eszközre** lehetőséget egyetlen eszköz kiválasztásához, vagy válassza a nagy **méretű üzembe helyezés** lehetőséget a több eszköz megcélzásához, majd kattintson a **Létrehozás**gombra. További információ a nagy léptékű üzembe helyezésről: a [központi](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)telepítés. 

    >[!Note] 
    >Ha a **központi telepítés méretezése**lehetőséget választotta, adja hozzá az eszköz nevét és részleteit, mielőtt továbblép a **modulok hozzáadása** lapra az alábbi utasításokban.     

Hajtsa végre az egyes lépéseket a Azure Security Center IoT való IoT Edge telepítésének befejezéséhez. 

#### <a name="step-1-modules"></a>1\. lépés: modulok

1. Válassza ki a **AzureSecurityCenterforIoT** modult.
1. A **modul beállításai** lapon módosítsa a **nevet** a **azureiotsecurity**értékre.
1. A **környezeti változók** lapon adjon hozzá egy változót, ha szükséges (például hibakeresési szint).
1. A **tároló létrehozása beállítások** lapon adja hozzá a következő konfigurációt:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
    
1. A **modul Twin beállítások** lapján adja hozzá a következő konfigurációt:
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{}
    ```

1. Válassza a **frissítés**lehetőséget.

#### <a name="step-2-runtime-settings"></a>2\. lépés: futtatókörnyezet beállításai

1. Válassza a **futtatókörnyezet beállításait**.
1. Az **Edge hub**alatt módosítsa a **képet** **MCR.microsoft.com/azureiotedge-hub:1.0.8.3**.
1. A **létrehozási beállítások** ellenőrzése a következő konfigurációra van beállítva: 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
    
1. Kattintson a **Mentés** gombra.
   
1. Kattintson a **Tovább** gombra.

#### <a name="step-3-specify-routes"></a>3\. lépés: útvonalak meghatározása 

1. Az **útvonalak meghatározása** lapon győződjön meg arról, hogy van olyan útvonala (explicit vagy implicit), amely továbbítja az üzeneteket a **azureiotsecurity** modulból az alábbi példáknak megfelelően **$upstream** . Csak ha az útvonal van érvényben, válassza a **tovább**lehetőséget.

   Példa útvonalak:

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. Kattintson a **Tovább** gombra.

#### <a name="step-4-review-deployment"></a>4\. lépés: az üzembe helyezés áttekintése

- A központi telepítés **áttekintése** lapon tekintse át a központi telepítési adatokat, majd válassza a **Létrehozás** lehetőséget a telepítés befejezéséhez.

## <a name="diagnostic-steps"></a>Diagnosztikai lépések

Ha problémába ütközik, a tároló naplói a legjobb módszer a IoT Edge biztonsági modul eszközének állapotának megismerésére. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Ellenőrizze, hogy a szükséges tárolók telepítve vannak-e, és megfelelően működnek-e.

1. Futtassa a következő parancsot a IoT Edge eszközön:
    
    `sudo docker ps`
   
1. Ellenőrizze, hogy a következő tárolók futnak-e:
   
   | Name (Név) | LEMEZKÉP |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   Ha a minimálisan szükséges tárolók nem jelennek meg, ellenőrizze, hogy a IoT Edge központi telepítési jegyzékfájlja az ajánlott beállításokkal van-e igazítva. További információ: [IoT Edge modul üzembe helyezése](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>A modul naplófájljainak vizsgálata hibák esetén
   
1. Futtassa a következő parancsot a IoT Edge eszközön:

   `sudo docker logs azureiotsecurity`
   
1. A részletes naplókhoz adja hozzá a következő környezeti változót a **azureiotsecurity** modul telepítéséhez: `logLevel=Debug`.

## <a name="next-steps"></a>Következő lépések

A konfigurációs beállításokkal kapcsolatos további információkért folytassa a modul konfigurálásának útmutatójában. 
> [!div class="nextstepaction"]
> [Útmutató a modul konfigurálásához](./how-to-agent-configuration.md)
