---
title: Valós felhasználómérés a Visual Studio Mobile centerrel – Azure Traffic Manager
description: A Visual Studio Mobile Center használatával fejlesztett Mobile-alkalmazás beállítása valós felhasználómérés küldéséhez Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 3106334e1fb3e3000cbd09e00e413b34a1b55e54
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939187"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>valós felhasználómérés küldése a Traffic Managernak a Visual Studio Mobile centerrel

A Visual Studio Mobile Center használatával kifejlesztheti a Mobile Application alkalmazást, hogy a következő lépések végrehajtásával elküldje valós felhasználómérés a Traffic Managernak:

>[!NOTE]
> Jelenleg a valós felhasználómérés küldése a Traffic Managerhez csak Android rendszer esetén támogatott.

Valós felhasználómérés konfigurálásához be kell szereznie egy kulcsot, és ki kell alakítania az alkalmazást a RUM-csomaggal.

## <a name="step-1-obtain-a-key"></a>1\. lépés: kulcs beszerzése
    
Az ügyfélalkalmazás által Traffic Manager elvégezhető méréseket a szolgáltatás a valós felhasználómérés (RUM) kulcsnak nevezett egyedi karakterlánc használatával azonosítja. Egy RUM-kulcsot a Azure Portal, egy REST API vagy a PowerShell/CLI felületek használatával szerezhet be.

A RUM-kulcs Azure Portal használatával történő beszerzéséhez a következő eljárással:
1. Egy böngészőből jelentkezzen be a Azure Portalba. Ha még nem rendelkezik fiókkal, regisztrálhat egy hónapos ingyenes próbaverzióra.
2. A portálon keresse meg a módosítani kívánt Traffic Manager profil nevét, majd kattintson a Traffic Manager profilra a megjelenített eredmények között.
3. A Traffic Manager profil lapon kattintson a **valós felhasználómérés** elemre a **Beállítások**területen.
4. Kattintson a **kulcs létrehozása** lehetőségre egy új rum-kulcs létrehozásához.
        
   ![valós felhasználómérés kulcs előállítása](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **1. ábra: valós felhasználómérés kulcs létrehozása**

5. A lap megjeleníti a generált RUM-kulcsot, valamint egy JavaScript-kódrészletet, amelyet be kell ágyazni a HTML-lapra.
 
   ![valós felhasználómérés kulcs JavaScript-kódja](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **2. ábra: valós felhasználómérés kulcs-és mérési JavaScript**
 
6. A RUM kulcs másolásához kattintson a **Másolás** gombra. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>2\. lépés: eszköz alkalmazása a Mobile Center SDK RUM-csomagjával

Ha most ismerkedik a Visual Studio Mobile centerrel, látogasson el a [webhelyére](https://mobile.azure.com). Az SDK-integrációval kapcsolatos részletes utasításokért lásd: [első lépések az Android SDK-val](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

A valós felhasználómérés használatához hajtsa végre a következő eljárást:

1.  Az SDK hozzáadása a projekthez

    Az ATM RUM SDK előzetes verziójában explicit módon hivatkozni kell a csomag adattárára.

    Az **app/Build. gradle** fájlban adja hozzá a következő sorokat:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    Az **app/Build. gradle** fájlban adja hozzá a következő sorokat:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Az SDK elindítása

    Nyissa meg az alkalmazás fő tevékenységi osztályát, és adja hozzá a következő importálási utasításokat:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Keresse meg a `onCreate` visszahívást ugyanabban a fájlban, és adja hozzá a következő kódot:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Következő lépések
- További információ a [valós felhasználómérés](traffic-manager-rum-overview.md)
- Tudnivalók a [Traffic Manager működéséről](traffic-manager-overview.md)
- További információ a [Mobile Centerről](https://docs.microsoft.com/mobile-center/)
- [Regisztráció](https://mobile.azure.com) a Mobile Centerre
- További információ a Traffic Manager által támogatott [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)
- Megtudhatja, hogyan [hozhat létre Traffic Manager-profilt](traffic-manager-create-profile.md)

