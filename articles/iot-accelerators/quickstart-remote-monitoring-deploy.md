---
title: Próbálja ki a felhőalapú IoT távoli figyelési megoldás – Azure |} A Microsoft Docs
description: Ezzel a rövid útmutatóval üzembe helyezheti a távoli monitorozási Azure IoT megoldásgyorsítót, és bejelentkezhet a megoldás irányítópultjának használatához.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/25/2019
ms.author: dobett
ms.openlocfilehash: 32e2d3f9e8bbd63944188355774558ca5ea7bd9d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60890040"
---
# <a name="quickstart-try-a-cloud-based-remote-monitoring-solution"></a>Gyors útmutató: Próbálja ki a felhő alapú távoli figyelési megoldáshoz

Ez a rövid útmutató bemutatja az Azure IoT távoli monitorozási megoldásgyorsító üzembe helyezését. Ebben a felhőalapú megoldásban a **Dashboard** (Irányítópult) lap segítségével jelenítheti meg a szimulált eszközöket egy térképen, és a **Maintenance** (Karbantartás) lapon válaszolhat a szimulált hűtőeszköz nyomással kapcsolatos riasztásaira. A megoldásgyorsítót saját megvalósítása kiindulópontjaként vagy képzési eszközként is használhatja.

A kezdeti üzembe helyezés a Contoso nevű vállalat számára konfigurálja a megoldásgyorsítót. A Contoso operátoraként különféle fizikai környezetekben található, különböző típusú eszközöket (például hűtőeszközöket) felügyel. A hűtőeszköz a hőmérsékletre, a páratartalomra és a nyomásra vonatkozó telemetriai adatokat küld a távoli monitorozási megoldásgyorsítónak.

Ebben a rövid útmutatóban üzembe helyez egy **alapszintű** verzióját a megoldásgyorsító a tesztelésre és demonstrációs céllal, hogy minimalizálja a költségeket. A különböző verzióit telepítheti kapcsolatos további információkért lásd: [alap és normál központi telepítések](iot-accelerators-remote-monitoring-deploy-cli.md#basic-and-standard-deployments).

A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Amikor üzembe helyezi a megoldásgyorsítót az Azure-előfizetéshez, néhány konfigurációs beállítást meg kell adnia.

Jelentkezzen be az [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) webhelyen az Azure-fiók hitelesítő adataival.

Kattintson a **távoli megfigyelés** csempére. Az a **távoli megfigyelés** kattintson **próbálja ki most**:

![Távoli monitorozás kiválasztása](./media/quickstart-remote-monitoring-deploy/remotemonitoring.png)

Válasszon  **C# Mikroszolgáltatások** , a **központi telepítési beállítások**. A Java és C# megvalósításokhoz rendelkezik ugyanazokat a szolgáltatásokat.

Adjon meg egy egyedi **megoldásnevet** a távoli monitorozási megoldásgyorsítóhoz. Ebben a rövid útmutatóban a miénk hívjuk **contoso-rm**.

Válassza ki a megoldásgyorsító üzembe helyezéséhez használni kívánt **Előfizetést** és **Régiót**. Általában az Önhöz legközelebbi régiót érdemes választani. Ebben a rövid útmutatóban használjuk **USA keleti Régiójában**.
Választhat **Visual Studio Enterprise**, kell lennie, de egy [globális rendszergazda vagy felhasználó](iot-accelerators-permissions.md) ennek a végrehajtására.

A telepítés megkezdéséhez kattintson a **létrehozás**. A folyamat legalább öt percig tart:

![A távoli monitorozási megoldás részletei](./media/quickstart-remote-monitoring-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Bejelentkezés a megoldásba

Ha az üzembe helyezés befejeződött az Azure-előfizetésen, a megoldás csempéjén egy zöld pipa és a **Kész** felirat jelenik meg. Ezután bejelentkezhet a távoli monitorozási megoldásgyorsító irányítópultjába.

A **Kiépített megoldások** lapon kattintson az új távoli monitorozási megoldásgyorsítóra:

![Új megoldás kiválasztása](./media/quickstart-remote-monitoring-deploy/choosenew.png)

A megjelenő panelen megtekintheti a távoli monitorozási megoldásgyorsítóra vonatkozó információkat. Válasszon **nyissa meg a megoldásgyorsító** a távoli figyelési megoldásgyorsító megtekintése:

![Megoldás panel](./media/quickstart-remote-monitoring-deploy/solutionpanel.png)

Kattintson az **Elfogadás** gombra az engedélykérések elfogadásához. Ekkor a böngészőben megjelenik a távoli monitorozási megoldásgyorsító irányítópultja:

[![A megoldás irányítópultja](./media/quickstart-remote-monitoring-deploy/solutiondashboard-inline.png)](./media/quickstart-remote-monitoring-deploy/solutiondashboard-expanded.png#lightbox)

## <a name="view-your-devices"></a>Eszközök megtekintése

A megoldás irányítópultja a következő információkat jeleníti meg a Contoso szimulált eszközeiről:

* Az **Eszközstatisztika** panel a riasztások összegző információit és az eszközök teljes számát jeleníti meg. Az alapértelmezett üzemelő példányban a Contoso 10, különböző típusú szimulált eszközzel rendelkezik.

* Az **Eszközhelyek** panel az eszközök fizikai helyét jeleníti meg. A gombostű színe jelzi, ha egy eszköz riasztást váltott ki.

* A **Riasztások** panel az eszközöktől érkező riasztások részleteit tartalmazza.

* A **Telemetria** panel az eszközök telemetriai adatait jeleníti meg. A különböző telemetriastreameket a fent található telemetriatípusokra kattintva tekintheti meg.

* Az **Elemzések** panel az eszközöktől érkező riasztásokra vonatkozó egyesített információkat tartalmaz.

## <a name="respond-to-an-alert"></a>Válasz egy riasztásra

A Contoso operátoraként a megoldás irányítópultjáról monitorozhatja az eszközöket. Az **eszközök statisztikáinak** panelje azt mutatja, hogy volt néhány kritikus riasztás, a **riasztások** panelje pedig azt, hogy ezek nagy része egy hűtőeszköztől érkezett. A Contoso hűtőeszközei esetében a 250 PSI-t meghaladó belső nyomás azt jelzi, hogy az eszköz nem működik megfelelően.

### <a name="identify-the-issue"></a>A probléma azonosítása

Az **irányítópult** **Alerts** (Riasztások) paneljén a **Chiller pressure too high** (Hűtőeszköz nyomása túl magas) riasztás látható. A hűtő piros színű gombostűvel van jelölve a térképen (lehetséges, hogy pásztáznia és nagyítania kell a megjelenítéséhez):

[![Irányítópult nyomásriasztással és a térképen megjelenített eszközzel](./media/quickstart-remote-monitoring-deploy/dashboardalarm-inline.png)](./media/quickstart-remote-monitoring-deploy/dashboardalarm-expanded.png#lightbox)

A **riasztások** paneljén kattintson a **Chiller pressure too high** (Hűtőeszköz nyomása túl magas) szabály mellett található **vizsgálati** oszlop **...** gombjára. Ezzel továbblép a **karbantartási** lapra, ahol megtekintheti a riasztást kiváltó szabály részleteit.

A **Chiller pressure too high** (Hűtőeszköz nyomása túl magas) karbantartási lap megjeleníti a riasztást kiváltó szabály részleteit. A lap azt is megjeleníti, hogy mikor aktiválódtak a riasztások, és melyik eszköz váltotta ki őket:

[![A karbantartási lap az aktivált riasztások listájával](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-expanded.png#lightbox)

Ezzel sikeresen azonosította a riasztást kiváltó problémát és az érintett eszközt. Operátorként a következő lépés a riasztás nyugtázása és a hiba kijavítása.

### <a name="fix-the-issue"></a>A probléma megoldása

Ha jelezni szeretné más operátorok felé, hogy már foglalkozik a riasztással, válassza ki, és módosítsa az **Alert status** (Riasztási állapot) értékét **Acknowledged** (Nyugtázva) állapotra:

[![Riasztás kiválasztása és nyugtázása](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-expanded.png#lightbox)

Az állapotoszlop **Acknowledged** (Nyugtázva) értékre módosul.

Ha műveletet szeretne végrehajtani a hűtőeszközön, görgessen le a **Related information** (Kapcsolódó információk) menüponthoz, válassza ki a hűtőeszközt a **riasztott eszközök** listájából, majd válassza a **Jobs** (Feladatok) lehetőséget:

[![Eszköz kiválasztása és művelet ütemezése](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-expanded.png#lightbox)

A **feladatok** paneljén válassza a **Run method** (Metódus futtatása) elemet, majd az **EmergencyValveRelease** metódust. Adja hozzá a **ChillerPressureRelease** feladatnevet, és kattintson az **Apply** (Alkalmaz) elemre. Ezek a beállítások egy olyan feladatot hoznak létre, amelyet a rendszer azonnal végrehajt.

A feladat állapotának megtekintéséhez lépjen vissza a **karbantartási** lapra, és a **feladatok** nézetében tekintse meg a feladatok listáját. Szükség lehet Várjon néhány másodpercet, ahhoz, hogy a feladat futott:

[![A feladatok állapota a feladatok nézetében](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-expanded.png#lightbox)

### <a name="check-the-pressure-is-back-to-normal"></a>A határértéken belüli nyomásérték ellenőrzése

A hűtő nyomástelemetriájának megtekintéséhez lépjen az **irányítópultra**, válassza ki a **Pressure** (Nyomás) telemetriapanelt, és ellenőrizze, hogy a **chiller-02.0** nyomása visszatért-e az engedélyezett tartományba:

[![A nyomás határértéken belüli](./media/quickstart-remote-monitoring-deploy/pressurenormal-inline.png)](./media/quickstart-remote-monitoring-deploy/pressurenormal-expanded.png#lightbox)

Az incidens lezárásához lépjen a **karbantartási** lapra, válassza ki a riasztást, és állítsa **Closed** (Lezárva) értékre:

[![Riasztás kiválasztása és lezárása](./media/quickstart-remote-monitoring-deploy/maintenanceclose-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceclose-expanded.png#lightbox)

Az állapotoszlop **Closed** (Lezárva) értékre módosul.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább kíván lépni a további oktatóanyagokra, ne kapcsolja ki a távoli monitorozási megoldásgyorsítót.

Ha már nincs szüksége a megoldásgyorsítóra, törölje a [Kiépített megoldások](https://www.azureiotsolutions.com/Accelerators#dashboard) lapról. Ehhez jelölje ki, majd kattintson a **Megoldás törlése** gombra:

![Megoldás törlése](media/quickstart-remote-monitoring-deploy/deletesolution.png)


![Megoldás törlése](media/quickstart-remote-monitoring-deploy/deletesolution-page.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezte a távoli monitorozási megoldásgyorsítót, és elvégzett egy monitorozási feladatot az alapértelmezett Contoso üzemelő példányban szereplő szimulált eszközökön.

Ha többet szeretne megtudni a szimulált eszközöket használó megoldásgyorsítóról, lépjen tovább a következő oktatóanyagra.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az IoT-eszközök figyelése](iot-accelerators-remote-monitoring-monitor.md)