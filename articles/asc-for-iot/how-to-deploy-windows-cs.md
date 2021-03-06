---
title: Azure Security Center Windows-telepítése a IoT-ügynökhöz | Microsoft Docs
description: Ismerkedjen meg a 32 bites vagy 64-bites Windows-eszközökön a IoT-ügynök Azure Security Center telepítésével.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: acc99f260931de7fd8c7566a3ff6daf43f34c5ef
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597214"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Azure Security Center telepítése IoT C#-alapú biztonsági ügynökhöz Windows rendszeren

Ez az útmutató ismerteti, hogyan telepítheti a IoT C#-alapú biztonsági ügynök Azure Security Center a Windows rendszeren.

Ebből az útmutatóból a következőket tanulhatja meg: 
> [!div class="checklist"]
> * Telepítés
> * Az üzembe helyezés ellenőrzése
> * Az ügynök eltávolítása
> * Hibaelhárítás 

## <a name="prerequisites"></a>Előfeltételek

Más platformokon és ügynöki Ízeknél tekintse meg [a megfelelő biztonsági ügynök kiválasztása](how-to-deploy-agent.md)című témakört.

1. Helyi rendszergazdai jogosultságok azon a gépen, amelyre telepíteni kívánja. 

1. [Hozzon létre egy biztonsági modult](quickstart-create-security-twin.md) az eszközhöz.

## <a name="installation"></a>Telepítés 

A biztonsági ügynök telepítéséhez használja a következő munkafolyamatot:

1. Telepítse a IoT Windows C# -ügynök Azure Security centerét az eszközön. Töltse le a legújabb verziót a gépére a IoT [GitHub-adattár](https://github.com/Azure/Azure-IoT-Security-Agent-CS)Azure Security Center.

1. Bontsa ki a csomag tartalmát, és navigáljon a/install mappára.

1. Nyissa meg a Windows PowerShellt rendszergazdaként. 
1. A következő parancs futtatásával adja hozzá a futó engedélyeket a InstallSecurityAgent parancsfájlhoz:<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    Ezután futtassa a parancsot:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Példa:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    További információ a hitelesítési paraméterekről: [a hitelesítés konfigurálása](concept-security-agent-authentication-methods.md).

A szkript a következő műveleteket végzi el:

- Telepíti az előfeltételeket.

- Szolgáltatásbeli felhasználó (interaktív bejelentkezéssel letiltva) hozzáadásával.

- Telepíti az ügynököt rendszerszolgáltatásként.

- A megadott hitelesítési paraméterekkel konfigurálja az ügynököt.


További segítségért használja a Get-Help parancsot a PowerShellben <br>Get-Help példa:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Központi telepítési állapot ellenőrzése

- Az ügynök központi telepítési állapotának ellenőrzéséhez futtassa a következőket:<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Az ügynök eltávolítása

Az ügynök eltávolítása:

1. Futtassa a következő PowerShell-parancsfájlt a **-Mode** paraméterrel az **Eltávolítás**értékre.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az ügynök nem indul el, kapcsolja be a naplózást (a naplózás alapértelmezés szerint *ki van kapcsolva* ) további információk eléréséhez.

A naplózás bekapcsolása:

1. Nyissa meg a konfigurációs fájlt (General. config) a szerkesztéshez egy szabványos fájlkezelő használatával.

1. Szerkessze a következő értékeket:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > A hibaelhárítás befejezése után javasoljuk a naplózás kikapcsolását. A naplózás bekapcsolásával megnő **a** naplófájl mérete és az adatfelhasználás. 

1. Indítsa újra az ügynököt a következő PowerShell vagy parancssor futtatásával:

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   vagy

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. A hibával kapcsolatos további információkért tekintse át a naplófájlt.

   Naplófájl helye:`%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>További lépések
- A IoT-szolgáltatás áttekintésének [](overview.md) Azure Security Center olvasása
- További információ a IoT- [architektúra](architecture.md) Azure Security Center
- A [szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- A [GYIK](resources-frequently-asked-questions.md) áttekintése
- A [riasztások](concept-security-alerts.md) ismertetése