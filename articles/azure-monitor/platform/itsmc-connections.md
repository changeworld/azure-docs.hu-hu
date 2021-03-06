---
title: Az Azure Log Analytics IT-szolgáltatásmenedzsmenti csatoló támogatott kapcsolatai | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan csatlakoztathatók a ITSM-termékek/szolgáltatások a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) segítségével Azure Monitor a ITSM-munkaelemek központi monitorozásához és kezeléséhez.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: c6cad29b6cc392746a2e56323302521302835b2f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274553"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM-termékek és-szolgáltatások összekötése IT-szolgáltatásmenedzsmenti csatoló
Ez a cikk azt ismerteti, hogyan konfigurálható a ITSM terméke/szolgáltatása és a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) közötti kapcsolat a munkaelemek központilag felügyelhető Log Analyticsban. További információ a ITSMC: [Áttekintés](../../azure-monitor/platform/itsmc-overview.md).

A következő ITSM-termékek/szolgáltatások támogatottak. Válassza ki a terméket a termék ITSMC való összekapcsolásával kapcsolatos részletes információk megtekintéséhez.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Megjelenő](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> ITSM-csatoló csak felhőalapú ServiceNow-példányokhoz tud csatlakozni. A helyszíni ServiceNow-példányok jelenleg nem támogatottak.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>System Center Service Manager összekötése az Azure-ban IT-szolgáltatásmenedzsmenti csatoló

A következő szakaszokban részletesen ismertetjük, hogyan csatlakoztathatók a System Center Service Manager-termékek a ITSMC az Azure-ban.

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

- A ITSMC telepítve van. További információ: [a it-szolgáltatásmenedzsmenti csatoló megoldás hozzáadása](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- A Service Manager webalkalmazás (Web App) telepítése és konfigurálása történik. A webalkalmazással kapcsolatos információk [itt](#create-and-deploy-service-manager-web-app-service)találhatóak.
- A hibrid kapcsolatok létrehozása és konfigurálása megtörtént. További információ: [a hibrid kapcsolatok konfigurálása](#configure-the-hybrid-connection).
- A Service Manager támogatott verziói: 2012 R2 vagy 2016.
- Felhasználói szerepkör: [speciális kezelő](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Csatlakoztatási eljárás

A System Center Service Manager-példány ITSMC való összekapcsolásához kövesse az alábbi eljárást:

1. A Azure Portalban lépjen a **minden erőforrás** elemre, és keresse meg a **ügyfélszolgálati (YourWorkspaceName)**

2.  A **munkaterület-ADATforrások** területen kattintson az **ITSM-kapcsolatok**elemre.

    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején kattintson a **Hozzáadás**gombra.

4. Adja meg az adatokat az alábbi táblázatban leírtak szerint, majd kattintson az **OK** gombra a kapcsolódás létrehozásához.

> [!NOTE]
> 
> Ezeket a paramétereket kötelező megadni.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be annak a System Center Service Manager-példánynak a nevét, amelyhez csatlakozni szeretne a ITSMC.  Ezt a nevet később, a munkaelemek ebben a példányban való konfigurálásakor, illetve a részletes log Analytics megtekintésekor kell használni. |
| **Partner típusa**   | Válassza a **System Center Service Manager**lehetőséget. |
| **Kiszolgáló URL-címe**   | Írja be a Service Manager webalkalmazás URL-címét. Service Manager webalkalmazással kapcsolatos további információkért [tekintse meg a](#create-and-deploy-service-manager-web-app-service)következőt:.
| **Ügyfél-azonosító**   | Írja be a generált ügyfél-azonosítót (az automatikus parancsfájl használatával) a webalkalmazás hitelesítéséhez. Az automatizált parancsfájlról további információt [itt talál.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Ügyfél titka**   | Adja meg az AZONOSÍTÓhoz generált ügyfél-titkot.   |
| **Adatszinkronizálás**   | Válassza ki azokat a Service Manager munkaelemeket, amelyeket szinkronizálni szeretne a ITSMC-on keresztül.  Ezeket a munkaelemeket a rendszer a Log Analyticsba importálja. **Beállítások:**  Incidensek, módosítási kérelmek.|
| **Adatszinkronizálási hatókör** | Adja meg az elmúlt napok számát, amelyből az adatok származnak. **Maximális korlát**: 120 nap. |
| **Új konfigurációs elemek létrehozása a ITSM-megoldásban** | Akkor válassza ezt a lehetőséget, ha a ITSM termékben szeretné létrehozni a konfigurációs elemeket. Ha bejelöli ezt a beállítást, a Log Analytics az érintett CIs-t konfigurációs elemekként (nem létező CIs esetén) hozza létre a támogatott ITSM-rendszeren. **Alapértelmezett**: letiltva. |

![Service Manager-kapcsolatok](media/itsmc-connections/service-manager-connection.png)

**Sikeres csatlakozás és szinkronizálás**:

- A Service Managerból kiválasztott munkaelemek importálása az Azure **log Analyticsba történik.** A munkaelemek összegzését a IT-szolgáltatásmenedzsmenti csatoló csempén tekintheti meg.

- Létrehozhat incidenseket Log Analytics riasztásokból, illetve a naplófájlokból, illetve az Azure-riasztásokból ebben a Service Manager-példányban.


További információ: [ITSM-munkaelemek létrehozása az Azure-riasztásokból](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Service Manager Web App Service létrehozása és üzembe helyezése

A helyszíni Service Manager Azure-beli ITSMC való összekapcsolásához a Microsoft létrehozott egy Service Manager webalkalmazást a GitHubon.

A Service Manager ITSM-webalkalmazásának beállításához tegye a következőket:

- **Telepítse a webalkalmazást** – telepítse a webalkalmazást, állítsa be a tulajdonságokat, és végezze el a hitelesítést az Azure ad szolgáltatásban. A webalkalmazást a Microsoft által biztosított [automatikus parancsfájl](../../azure-monitor/platform/itsmc-service-manager-script.md) használatával helyezheti üzembe.
- **Konfigurálja a hibrid kapcsolatokat** - [konfigurálja](#configure-the-hybrid-connection)kézzel a kapcsolatokat.

#### <a name="deploy-the-web-app"></a>A webalkalmazás üzembe helyezése
Az automatizált [parancsfájllal](../../azure-monitor/platform/itsmc-service-manager-script.md) telepítse a webalkalmazást, állítsa be a tulajdonságokat, és végezze el a hitelesítést az Azure ad-vel.

Futtassa a szkriptet a következő szükséges részletek megadásával:

- Azure-előfizetés részletei
- Erőforráscsoport neve
- Hely
- Service Manager kiszolgáló adatai (kiszolgáló neve, tartomány, Felhasználónév és jelszó)
- A webalkalmazás helynév-előtagja
- ServiceBus-névtér.

A parancsfájl a megadott névvel hozza létre a webalkalmazást (néhány további sztringtel együtt, hogy egyedivé tegye azt). Létrehozza a **webalkalmazás URL-címét**, az **ügyfél-azonosítót** és az **ügyfél titkos kulcsát**.

Mentse az értékeket, amikor ITSMC-vel létesített kapcsolatokat hoz létre.

**A webalkalmazás telepítésének megkeresése**

1. Nyissa meg **Azure Portal** > **erőforrásait**.
2. Válassza ki a webalkalmazást, majd kattintson a **beállítások** > **Alkalmazásbeállítások**elemre.
3. Erősítse meg az alkalmazás parancsfájl használatával történő üzembe helyezésének időpontjában megadott Service Manager-példány adatait.

### <a name="configure-the-hybrid-connection"></a>A hibrid kapcsolatok konfigurálása

A következő eljárással konfigurálhatja a hibrid kapcsolatot, amely összekapcsolja a Service Manager példányt a ITSMC az Azure-ban.

1. Keresse meg az Service Manager webalkalmazást az **Azure-erőforrások**területen.
2. Kattintson a **beállítások** > **hálózatkezelés**elemre.
3. A **hibrid kapcsolatok**alatt kattintson **a hibrid kapcsolatok végpontok konfigurálása**elemre.

    ![Hibrid kapcsolat hálózatkezelése](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. A **hibrid kapcsolatok** panelen kattintson a **hibrid kapcsolatok hozzáadása**lehetőségre.

    ![Hibrid kapcsolatok hozzáadása](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Az **hibrid kapcsolatok hozzáadása** panelen kattintson az **új hibrid kapcsolatok létrehozása**lehetőségre.

    ![Új hibrid kapcsolatok](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Írja be a következő értékeket:

   - **Végpont neve**: adja meg az új hibrid kapcsolatok nevét.
   - **Végpont-gazdagép**: a Service Manager felügyeleti kiszolgáló teljes tartományneve.
   - **Végpont portja**: Type 5724
   - **Servicebus névtér**: használjon meglévő Servicebus-névteret, vagy hozzon létre egy újat.
   - **Hely**: válassza ki a helyet.
   - **Név**: adjon meg egy nevet a servicebus, ha létrehozza azt.

     ![Hibrid kapcsolatok értékei](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Kattintson az **OK** gombra a **hibrid kapcsolatok létrehozása** panel bezárásához és a hibrid kapcsolatok létrehozásának megkezdéséhez.

    A hibrid kapcsolatok létrehozása után az megjelenik a panelen.

7. A hibrid kapcsolatok létrehozása után válassza ki a kapcsolatokat, és kattintson a **kiválasztott hibrid kapcsolatok hozzáadása**lehetőségre.

    ![Új hibrid kapcsolatok](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>A figyelő beállításának konfigurálása

A következő eljárással konfigurálhatja a figyelő telepítőjét a hibrid kapcsolatok esetében.

1. A **hibrid kapcsolatok** panelen kattintson a **Csatlakozáskezelő letöltése** elemre, és telepítse azt a gépre, amelyen System Center Service Manager példány fut.

    Miután a telepítés befejeződött, **hibridkapcsolat-kezelő felhasználói felület** lehetőség elérhető a **Start** menüben.

2. Kattintson **hibridkapcsolat-kezelő felhasználói felületre** , a rendszer kérni fogja az Azure-beli hitelesítő adatait.

3. Jelentkezzen be az Azure-beli hitelesítő adataival, és válassza ki az előfizetését, ahol a hibrid kapcsolatok létrejöttek.

4. Kattintson a **Save** (Mentés) gombra.

A hibrid kapcsolat sikeresen csatlakoztatva van.

![sikeres hibrid kapcsolatok](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> A hibrid kapcsolat létrehozása után ellenőrizze és tesztelje a kapcsolatát az üzembe helyezett Service Manager webalkalmazás meglátogatásával. Győződjön meg arról, hogy a kapcsolat sikeres, mielőtt megpróbál csatlakozni a ITSMC az Azure-ban.

A következő minta képe a sikeres kapcsolat részleteit mutatja be:

![Hibrid kapcsolatok tesztelése](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>ServiceNow-IT-szolgáltatásmenedzsmenti csatoló összekötése az Azure-ban

A következő szakaszokban részletesen ismertetjük, hogyan csatlakoztathatók a ServiceNow-termékek az Azure-beli ITSMC.

### <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:
- A ITSMC telepítve van. További információ: [a it-szolgáltatásmenedzsmenti csatoló megoldás hozzáadása](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ServiceNow támogatott verziók: New York, Madrid, London, Kingston, Jakarta, Isztambul, Helsinki, Genf.

A **ServiceNow-rendszergazdáknak a következőket kell tenniük a ServiceNow-példányban**:
- Ügyfél-azonosító és ügyfél-titkos kód előállítása a ServiceNow termékhez. Az ügyfél-azonosító és a titkos kulcs létrehozásával kapcsolatos információkért tekintse meg a szükséges információkat a következő információk alapján:

    - [A New York-i OAuth beállítása](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth beállítása Madridhoz](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [A OAuth beállítása Londonban](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [A OAuth beállítása a Kingston számára](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth beállítása a Jakarta számára](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Az OAuth beállítása Isztambulhoz](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [A OAuth beállítása Helsinkihez](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [A OAuth beállítása Genfben](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Telepítse a Microsoft Log Analytics Integration (ServiceNow app) felhasználói alkalmazását. [További információk](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Integrációs felhasználói szerepkör létrehozása a telepített felhasználói alkalmazáshoz. Az integrációs felhasználói szerepkör létrehozásával kapcsolatos információkat [itt](#create-integration-user-role-in-servicenow-app)találja.

### <a name="connection-procedure"></a>**Csatlakoztatási eljárás**
ServiceNow-kapcsolatok létrehozásához kövesse az alábbi eljárást:


1. A Azure Portalban lépjen a **minden erőforrás** elemre, és keresse meg a **ügyfélszolgálati (YourWorkspaceName)**

2.  A **munkaterület-ADATforrások** területen kattintson az **ITSM-kapcsolatok**elemre.
    ![új kapcsolatok](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején kattintson a **Hozzáadás**gombra.

4. Adja meg az adatokat az alábbi táblázatban leírtak szerint, majd kattintson az **OK** gombra a kapcsolódás létrehozásához.


> [!NOTE]
> Ezeket a paramétereket kötelező megadni.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be annak a ServiceNow-példánynak a nevét, amelyhez csatlakozni kíván a ITSMC.  Ezt a nevet később Log Analytics, ha munkaelemeket konfigurál ebben a ITSM/részletes log Analytics nézetben. |
| **Partner típusa**   | Válassza a **ServiceNow**lehetőséget. |
| **Felhasználónév**   | Írja be a ServiceNow alkalmazásban létrehozott integrációs felhasználónevet, hogy támogassa a ITSMC való kapcsolódást. További információ: [ServiceNow-alkalmazás felhasználói szerepkör létrehozása](#create-integration-user-role-in-servicenow-app).|
| **Jelszó**   | Írja be a felhasználónévhez tartozó jelszót. **Megjegyzés**: a felhasználónevet és a jelszót csak a hitelesítési jogkivonatok létrehozásához használja a rendszer, és a ITSMC szolgáltatásban bárhol tárolja őket.  |
| **Kiszolgáló URL-címe**   | Írja be annak az ServiceNow-példánynak az URL-címét, amelyhez csatlakozni szeretne a ITSMC. |
| **Ügyfél-azonosító**   | Írja be a korábban létrehozott OAuth2-hitelesítéshez használni kívánt ügyfél-azonosítót.  További információ az ügyfél-azonosító és a titkos kulcs létrehozásáról: [OAuth Setup](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Ügyfél titka**   | Adja meg az AZONOSÍTÓhoz generált ügyfél-titkot.   |
| **Adatszinkronizálási hatókör**   | Válassza ki azokat a ServiceNow munkaelemeket, amelyeket szinkronizálni szeretne az Azure Log Analytics a ITSMC keresztül.  A kiválasztott értékeket a rendszer a log analyticsbe importálja.   **Beállítások:**  Incidensek és módosítási kérelmek.|
| **Adatszinkronizálás** | Adja meg az elmúlt napok számát, amelyből az adatok származnak. **Maximális korlát**: 120 nap. |
| **Új konfigurációs elemek létrehozása a ITSM-megoldásban** | Akkor válassza ezt a lehetőséget, ha a ITSM termékben szeretné létrehozni a konfigurációs elemeket. Ha be van jelölve, a ITSMC létrehozza az érintett CIs-t konfigurációs elemekként (nem létező CIs esetén) a támogatott ITSM-rendszeren. **Alapértelmezett**: letiltva. |

![ServiceNow-kapcsolatok](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Sikeres csatlakozás és szinkronizálás**:

- A ServiceNow-példány kiválasztott munkaelemeit az Azure **log Analyticsba** importálja a rendszer. A munkaelemek összegzését a IT-szolgáltatásmenedzsmenti csatoló csempén tekintheti meg.

- Incidenseket hozhat létre Log Analytics riasztásokból, illetve a naplóbejegyzésekből vagy az Azure-riasztásokból ebben az ServiceNow-példányban.

További információ: [ITSM-munkaelemek létrehozása az Azure-riasztásokból](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Integrációs felhasználói szerepkör létrehozása a ServiceNow alkalmazásban

A felhasználó az alábbi eljárást:

1. Látogasson el a [ServiceNow áruházba](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) , és telepítse a **ServiceNow és a Microsoft OMS-integrációs felhasználói alkalmazást** a ServiceNow-példányba.
   
   >[!NOTE]
   >A Microsoft Operations Management Suite (OMS) és a Azure Monitor közötti folyamatos áttérés részeként a OMS-et már Log Analyticsnak nevezzük.     
2. A telepítést követően nyissa meg a ServiceNow-példány bal oldali navigációs sávját, keresse meg és válassza a Microsoft OMS-integrátor elemet.  
3. Kattintson a **telepítési ellenőrzőlista**elemre.

   Ha a felhasználói szerepkör még létre van hozva, az állapot **nem fejeződött be** .

4. Az **integrációs felhasználó létrehozása**elem melletti szövegmezőbe írja be annak a felhasználónak a felhasználónevét, aki CSATLAKOZHAT a ITSMC az Azure-ban.
5. Adja meg a felhasználó jelszavát, majd kattintson **az OK**gombra.  

> [!NOTE]
> 
> Ezeket a hitelesítő adatokat használja a ServiceNow-kapcsolatok az Azure-ban való létrehozásához.

Az újonnan létrehozott felhasználó megjelenik a hozzárendelt alapértelmezett szerepkörökkel.

**Alapértelmezett szerepkörök**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   ITIL
-   template_editor
-   view_changer

A felhasználó sikeres létrehozása után a **telepítési ellenőrzőlista** állapotának állapota befejeződött, amely felsorolja az alkalmazáshoz létrehozott felhasználói szerepkör részleteit.

> [!NOTE]
> 
> A ITSM-csatoló a ServiceNow-példányra telepített más modulok nélkül is küldhet incidenseket a ServiceNow. Ha a ServiceNow-példányban EventManagement-modult használ, és az összekötő használatával szeretne eseményeket vagy riasztásokat létrehozni a ServiceNow-ben, adja hozzá a következő szerepköröket az integrációs felhasználóhoz:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>IT-szolgáltatásmenedzsmenti csatoló az Azure-ban való kapcsolódáshoz

A következő szakaszokban részletesen ismertetjük, hogyan csatlakoztatható a ITSMC az Azure-ban.


### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:


- A ITSMC telepítve van. További információ: [a it-szolgáltatásmenedzsmenti csatoló megoldás hozzáadása](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Az Azure AD-ben regisztrálni kell az alkalmazást, és az ügyfél-azonosítót is elérhetővé kell tenni. Részletes információk: [az Active Directory-hitelesítés konfigurálása](../../app-service/configure-authentication-provider-aad.md).

- Felhasználói szerepkör: rendszergazda.

### <a name="connection-procedure"></a>Csatlakoztatási eljárás

A következő eljárással hozhat létre egy elővance-kapcsolatokat:

1. A Azure Portalban lépjen a **minden erőforrás** elemre, és keresse meg a **ügyfélszolgálati (YourWorkspaceName)**

2.  A **munkaterület-ADATforrások** területen kattintson az **ITSM-kapcsolatok**elemre.
    ![új kapcsolatok](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején kattintson a **Hozzáadás**gombra.

4. Adja meg az adatokat az alábbi táblázatban leírtak szerint, majd kattintson az **OK** gombra a kapcsolódás létrehozásához.

> [!NOTE]
> 
> Ezeket a paramétereket kötelező megadni.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Adja meg a ITSMC-vel összekapcsoláshoz használni kívánt elővance-példány nevét.  Ezt a nevet később is használhatja, ha munkaelemeket konfigurál ebben a ITSM/részletes log Analytics nézetben. |
| **Partner típusa**   | Válassza az **elővance**elemet. |
| **Felhasználónév**   | Írja be azt a felhasználónevet, amely csatlakozhat a ITSMC.    |
| **Jelszó**   | Írja be a felhasználónévhez tartozó jelszót. **Megjegyzés:** A rendszer csak a hitelesítési jogkivonatok létrehozásához használja a felhasználónevet és a jelszót, és a ITSMC szolgáltatásban bárhol nem tárolja őket. _|
| **Kiszolgáló URL-címe**   | Írja be a ITSMC-hez csatlakozni kívánt Előa-példány URL-címét. |
| **Ügyfél-azonosító**   | Adja meg az ügyfél-azonosítót a-kiszolgáló hitelesítéséhez, amelyet a elővizsgálati példányban hozott létre.  További információ az ügyfél-AZONOSÍTÓról: [Active Directory-hitelesítés konfigurálása](../../app-service/configure-authentication-provider-aad.md). |
| **Adatszinkronizálási hatókör**   | Válassza ki a ITSMC-on keresztül szinkronizálni kívánt munkaelemeket az Azure Log Analyticshoz.  Ezeket a munkaelemeket a rendszer a log analyticsbe importálja.   **Beállítások:**   Incidensek, módosítási kérelmek.|
| **Adatszinkronizálás** | Adja meg az elmúlt napok számát, amelyből az adatok származnak. **Maximális korlát**: 120 nap. |
| **Új konfigurációs elemek létrehozása a ITSM-megoldásban** | Akkor válassza ezt a lehetőséget, ha a ITSM termékben szeretné létrehozni a konfigurációs elemeket. Ha be van jelölve, a ITSMC létrehozza az érintett CIs-t konfigurációs elemekként (nem létező CIs esetén) a támogatott ITSM-rendszeren. **Alapértelmezett**: letiltva.|

![Megjelenő kapcsolatok](media/itsmc-connections/itsm-connections-provance-latest.png)

**Sikeres csatlakozás és szinkronizálás**:

- A rendszer az Azure **log Analyticsba** importálja a jelen Előa-példány kiválasztott munkaelemeit. A munkaelemek összegzését a IT-szolgáltatásmenedzsmenti csatoló csempén tekintheti meg.

- Az incidenseket Log Analytics riasztásokból, illetve a naplófájlokból, illetve az Azure-riasztásokból is létrehozhatja ebben a megtekintő példányban.

További információ: [ITSM-munkaelemek létrehozása az Azure-riasztásokból](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Cherwell-IT-szolgáltatásmenedzsmenti csatoló összekötése az Azure-ban

A következő szakaszokban részletesen ismertetjük, hogyan csatlakoztathatók a Cherwell-termékek az Azure-beli ITSMC.

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

- A ITSMC telepítve van. További információ: [a it-szolgáltatásmenedzsmenti csatoló megoldás hozzáadása](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Ügyfél-azonosító létrehozva. További információ: [a Cherwell ügyfél-azonosítójának előállítása](#generate-client-id-for-cherwell).
- Felhasználói szerepkör: rendszergazda.

### <a name="connection-procedure"></a>Csatlakoztatási eljárás

A következő eljárással hozhat létre egy elővance-kapcsolatokat:

1. A Azure Portalban lépjen a **minden erőforrás** elemre, és keresse meg a **ügyfélszolgálati (YourWorkspaceName)**

2.  A **munkaterület-ADATforrások** területen kattintson az **ITSM-kapcsolatok**elemre.
    ![új kapcsolatok](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején kattintson a **Hozzáadás**gombra.

4. Adja meg az adatokat az alábbi táblázatban leírtak szerint, majd kattintson az **OK** gombra a kapcsolódás létrehozásához.

> [!NOTE]
> 
> Ezeket a paramétereket kötelező megadni.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be annak a Cherwell-példánynak a nevét, amelyhez csatlakozni szeretne a ITSMC.  Ezt a nevet később is használhatja, ha munkaelemeket konfigurál ebben a ITSM/részletes log Analytics nézetben. |
| **Partner típusa**   | Válassza a **Cherwell lehetőséget.** |
| **Felhasználónév**   | Írja be azt a Cherwell-felhasználónevet, amely tud csatlakozni a ITSMC. |
| **Jelszó**   | Írja be a felhasználónévhez tartozó jelszót. **Megjegyzés:** A rendszer csak a hitelesítési jogkivonatok létrehozásához használja a felhasználónevet és a jelszót, és a ITSMC szolgáltatásban bárhol nem tárolja őket.|
| **Kiszolgáló URL-címe**   | Írja be annak a Cherwell-példánynak az URL-címét, amelyhez csatlakozni szeretne a ITSMC. |
| **Ügyfél-azonosító**   | Adja meg az ügyfél-azonosítót a Cherwell-példányban létrehozott, a kapcsolatok hitelesítéséhez.   |
| **Adatszinkronizálási hatókör**   | Válassza ki azokat a Cherwell munkaelemeket, amelyeket szinkronizálni szeretne a ITSMC-on keresztül.  Ezeket a munkaelemeket a rendszer a log analyticsbe importálja.   **Beállítások:**  Incidensek, módosítási kérelmek. |
| **Adatszinkronizálás** | Adja meg az elmúlt napok számát, amelyből az adatok származnak. **Maximális korlát**: 120 nap. |
| **Új konfigurációs elemek létrehozása a ITSM-megoldásban** | Akkor válassza ezt a lehetőséget, ha a ITSM termékben szeretné létrehozni a konfigurációs elemeket. Ha be van jelölve, a ITSMC létrehozza az érintett CIs-t konfigurációs elemekként (nem létező CIs esetén) a támogatott ITSM-rendszeren. **Alapértelmezett**: letiltva. |


![Megjelenő kapcsolatok](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Sikeres csatlakozás és szinkronizálás**:

- A Cherwell-példány kiválasztott munkaelemeit az Azure **log Analyticsba** importálja a rendszer. A munkaelemek összegzését a IT-szolgáltatásmenedzsmenti csatoló csempén tekintheti meg.

- Incidenseket hozhat létre Log Analytics riasztásokból, illetve a naplóbejegyzésekből vagy az Azure-riasztásokból ebben az Cherwell-példányban.

További információ: [ITSM-munkaelemek létrehozása az Azure-riasztásokból](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Ügyfél-azonosító előállítása a Cherwell

Az ügyfél-azonosító/-kulcs Cherwell való létrehozásához kövesse az alábbi eljárást:

1. Jelentkezzen be a Cherwell-példányba rendszergazdaként.
2. Kattintson a **biztonság** > **REST API ügyfélbeállítások szerkesztése**elemre.
3. Válassza az **új ügyfél létrehozása** > **ügyfél titka**lehetőséget.

    ![Cherwell-felhasználói azonosító](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>További lépések
 - [ITSM-munkaelemek létrehozása az Azure-riasztásokból](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
