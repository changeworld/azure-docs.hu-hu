---
title: Többértékű forgalom útválasztásának konfigurálása – Azure Traffic Manager
description: Ez a cikk azt ismerteti, hogyan konfigurálható Traffic Manager, hogy a forgalmat egy/AAAA-végpontra irányítsa.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: rohink
ms.openlocfilehash: daf7d09916d276130e337f7acea738228ee23707
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938784"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Többértékű útválasztási módszer konfigurálása Traffic Manager

Ez a cikk azt ismerteti, hogyan konfigurálható a többtényezős forgalom – útválasztási módszer. A többértékű forgalom útválasztási módszere lehetővé teszi több kifogástalan állapotú végpont visszaadását, és segít az alkalmazás megbízhatóságának növelésében, **mivel az ügyfelek** több lehetőség közül választhatnak az újrapróbálkozáshoz anélkül, hogy más DNS-keresést kellene végezniük A többértékű útválasztás csak olyan profilok esetében engedélyezett, amelyekben IPv4-vagy IPv6-címekkel megadott végpontok vannak megadva. Ha egy lekérdezés érkezik ehhez a profilhoz, a rendszer az összes kifogástalan állapotú végpontot a megadott konfigurálható maximális visszaadott szám alapján adja vissza. 

>[!NOTE]
> A végpontok IPv4-vagy IPv6-címekkel való hozzáadása jelenleg csak **külső** típusú végpontok esetén támogatott, így a többértékű útválasztás csak az ilyen végpontok esetében támogatott.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy erőforráscsoportot a Traffic Manager profilhoz.
1. A Azure Portal bal oldali ablaktábláján válassza az **erőforráscsoportok**lehetőséget.
2. Az **erőforráscsoportok**lapon a lap tetején válassza a **Hozzáadás**lehetőséget.
3. Az **erőforráscsoport neve**mezőbe írja be a *myResourceGroupTM1*nevet. Az **erőforráscsoport helye**területen válassza az **USA keleti**régiója lehetőséget, majd kattintson **az OK gombra**.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása
Hozzon létre egy Traffic Manager-profilt, amely a legalacsonyabb késleltetésű végpontra küldi a felhasználói forgalmat.

1. A képernyő bal felső részén válassza az **Erőforrás létrehozása** > **Hálózat** > **Traffic Manager-profil** > **Létrehozás** elemet.
2. A **Traffic Manager profil létrehozása**lapon adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **Létrehozás**lehetőséget:
    
    | Beállítás                 | Value (Díj)                                              |
    | ---                     | ---                                                |
    | Name (Név)                   | Ennek a névnek egyedinek kell lennie a trafficmanager.net zónában, és a trafficmanager.net DNS-nevet eredményezi, amellyel elérhető a Traffic Manager-profil.                                   |
    | Útválasztási metódus          | Válassza ki **a** többértékű útválasztási módszert.                                       |
    | Előfizetést            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza a *myResourceGroupTM1*lehetőséget. |
    | Földrajzi egység                | Ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a globálisan üzembe helyezendő Traffic Manager-profilra.                              |
   |        |           | 
  
   ![Traffic Manager-profil létrehozása](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Vegyen fel két IP-címet külső végpontként az előző lépésben létrehozott többértékű Traffic Manager profilba.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére, majd válassza ki a profilt a megjelenített eredmények között.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában kattintson a **Végpontok**, majd a **Hozzáadás** elemre.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    | Beállítás                 | Value (Díj)                                              |
    | ---                     | ---                                                |
    | Type (Típus)                    | Külső végpont                                   |
    | Name (Név)           | myEndpoint1                                        |
    | Teljes tartománynév (FQDN) vagy IP-cím           | Írja be annak a végpontnak a nyilvános IP-címét, amelyet hozzá szeretne adni a Traffic Manager profilhoz                         |
    |        |           |

4. Ismételje meg a 2. és a 3. lépést egy *myEndpoint2*nevű másik végpont hozzáadásához a **teljes tartománynév (FQDN) vagy az IP-** cím mezőben adja meg a második végpont nyilvános IP-címét.
5. Miután mindkét végpontot hozzáadta, azok megjelennek a **Traffic Manager-profil** panelen, **Online** figyelési állapottal.

   ![Traffic Manager-végpont hozzáadása](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Következő lépések

- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- További információ az [elsődleges útválasztási metódusról](traffic-manager-configure-priority-routing-method.md).
- További információ a [teljesítmény-útválasztási módszerről](traffic-manager-configure-performance-routing-method.md)
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).


