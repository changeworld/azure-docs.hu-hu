---
title: Eszköz-hozzáférés, energiaellátás és kapcsolódási mód Azure Data Box Gateway
description: Útmutató az adatok Azure-ba való átvitelét segítő Azure Data Box Gateway eszköz hozzáférésének, teljesítményének és csatlakozási módjának kezeléséhez
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: e2e82c551117f2ae96e026b39c08ccf56e7ce8e2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265453"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>A Azure Data Box Gateway hozzáférésének, teljesítményének és kapcsolati módjának kezelése

Ez a cikk azt ismerteti, hogyan kezelhető a Azure Data Box Gateway hozzáférési, energiagazdálkodási és kapcsolódási módja. Ezek a műveletek a helyi webes felületen vagy a Azure Portalon keresztül hajthatók végre.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eszközök hozzáférésének kezelése
> * Kapcsolati mód kezelése
> * A Power kezelése

## <a name="manage-device-access"></a>Eszközök hozzáférésének kezelése

A Data Box Gateway eszköz hozzáférését az eszköz jelszavának használatával szabályozhatja. A jelszó a helyi webes felületen keresztül módosítható. Alaphelyzetbe állíthatja az eszköz jelszavát is a Azure Portalban.

### <a name="change-device-password"></a>Az eszköz jelszavának módosítása

Az eszköz jelszavának módosításához kövesse az alábbi lépéseket a helyi felhasználói felületen.

1. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > jelszavának módosítása**elemre.
2. Adja meg az aktuális jelszót, majd az új jelszót. A megadott jelszónak 8 és 16 karakter közöttinek kell lennie. A jelszónak legalább hármat tartalmaznia kell a következő karakterek közül: nagybetűk, kisbetűk, számok és speciális karakterek. Erősítse meg az új jelszót.

    ![Jelszó módosítása](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Kattintson a **jelszó módosítása**gombra.
 
### <a name="reset-device-password"></a>Eszköz jelszavának alaphelyzetbe állítása

A munkafolyamat alaphelyzetbe állítása nem igényli, hogy a felhasználó felidézze a régi jelszót, és akkor is hasznos, ha a jelszó elvész. Ezt a munkafolyamatot a Azure Portal hajtja végre.

1. A Azure Portal lépjen az **áttekintés > rendszergazdai jelszó visszaállítása**elemre.

    ![Új jelszó létrehozása](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Adja meg az új jelszót, majd erősítse meg. A megadott jelszónak 8 és 16 karakter közöttinek kell lennie. A jelszónak legalább hármat tartalmaznia kell a következő karakterek közül: nagybetűk, kisbetűk, számok és speciális karakterek. Kattintson az **Alaphelyzetbe állítás**gombra.

    ![Új jelszó létrehozása](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Erőforrás-hozzáférés kezelése

A Data Box Edge/Data Box Gateway, a IoT Hub és az Azure Storage-erőforrás létrehozásához jogosultsággal kell rendelkeznie közreműködői vagy magasabb szintű erőforrás-csoport szintjén. Szükség van a megfelelő erőforrás-szolgáltatók regisztrálására is. Az aktiválási kulcsot és a hitelesítő adatokat is tartalmazó műveletek esetében a Azure Active Directory Graph APIra vonatkozó engedélyek is szükségesek. Ezeket a következő szakaszokban ismertetjük.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Microsoft Azure Active Directory Graph API-engedélyek kezelése

A Data Box Edge eszköz aktiválási kulcsának létrehozásakor vagy a hitelesítő adatokat igénylő műveletek elvégzéséhez engedélyekkel kell rendelkeznie Azure Active Directory Graph API. A hitelesítő adatokat igénylő műveletek a következők lehetnek:

-  Megosztás létrehozása társított Storage-fiókkal.
-  Hozzon létre egy felhasználót, aki hozzáfér az eszközön található megosztásokhoz.

Active Directory bérlőhöz `User` hozzáféréssel kell rendelkeznie, ahogy `Read all directory objects`. Nem lehet vendég felhasználó, mert nem rendelkezik a `Read all directory objects`hez szükséges engedélyekkel. Ha Ön vendég, akkor az aktiválási kulcs létrehozása, a megosztás létrehozása a Data Box Edge-eszközön, a felhasználó létrehozása és az ehhez hasonló műveletek sikertelenek lesznek.

A felhasználók Azure Active Directory Graph API való hozzáférésének biztosításával kapcsolatos további információkért lásd: [az alapértelmezett hozzáférés a rendszergazdák, a felhasználók és a vendég felhasználók](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)számára.

### <a name="register-resource-providers"></a>Erőforrás-szolgáltató regisztrálása

Ha erőforrást szeretne kiépíteni az Azure-ban (a Azure Resource Manager modellben), szüksége lesz egy erőforrás-szolgáltatóra, amely támogatja az adott erőforrás létrehozását. Ha például egy virtuális gépet szeretne kiépíteni, az előfizetésben elérhető "Microsoft. számítási" erőforrás-szolgáltatónak kell lennie.
 
Az erőforrás-szolgáltatók az előfizetés-szinten vannak regisztrálva. Alapértelmezés szerint minden új Azure-előfizetés előre regisztrálva valamelyik gyakran használt erőforrás-szolgáltatónál. A "Microsoft. DataBoxEdge" erőforrás-szolgáltatója nem szerepel a listán.

Nem kell hozzáférési engedélyeket megadnia az előfizetési szintre ahhoz, hogy a felhasználók olyan erőforrásokat hozzanak létre, mint például a "Microsoft. DataBoxEdge" azon erőforráscsoportok, amelyeken a tulajdonos jogosultságai vannak, feltéve, hogy az erőforrás-szolgáltató már létezik regisztrált.

Mielőtt megpróbál létrehozni egy erőforrást, győződjön meg arról, hogy az erőforrás-szolgáltató regisztrálva van az előfizetésben. Ha az erőforrás-szolgáltató nincs regisztrálva, meg kell győződnie arról, hogy az új erőforrást létrehozó felhasználónak elegendő jogosultsága van ahhoz, hogy regisztrálja a szükséges erőforrás-szolgáltatót az előfizetési szinten. Ha még nem tette meg, akkor a következő hibaüzenet jelenik meg:

*Az előfizetés \<előfizetés neve > nem rendelkezik engedéllyel az erőforrás-szolgáltató (k) regisztrálásához: Microsoft. DataBoxEdge.*


A jelenlegi előfizetésben regisztrált erőforrás-szolgáltatók listájának lekéréséhez futtassa a következő parancsot:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Data Box Edge eszköz esetében regisztrálni kell a `Microsoft.DataBoxEdge`. `Microsoft.DataBoxEdge`regisztrálásához az előfizetés rendszergazdájának a következő parancsot kell futtatnia:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Az erőforrás-szolgáltatók regisztrálásával kapcsolatos további információkért lásd: [hibák elhárítása az erőforrás-szolgáltató regisztrálásához](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Kapcsolati mód kezelése

Az alapértelmezett normál mód mellett az eszköz részlegesen leválasztott vagy leválasztott módban is futhat. Az alábbi módok mindegyikét az alábbiak szerint írja le:

- **Részben leválasztva** – ebben a módban az eszköz nem tudja feltölteni a megosztásokra vonatkozó összes adatfeltöltést, azonban a Azure Portal használatával felügyelhető.

    Ezt a módot általában a mért műhold-hálózaton használják, és a cél a hálózati sávszélesség-fogyasztás csökkentése. Az eszköz-figyelési műveletek esetében a minimális hálózati fogyasztás továbbra is előfordulhat.

- **Leválasztva** – ebben a módban az eszköz teljesen le van választva a felhőből, és a Felhőbeli feltöltések és a letöltések is le vannak tiltva. Az eszközt csak a helyi webes felületen keresztül lehet felügyelni.

    Ez a mód általában akkor használatos, ha az eszközt offline állapotba kívánja helyezni.

Az eszköz üzemmódjának módosításához kövesse az alábbi lépéseket:

1. Az eszköz helyi webes FELÜLETén nyissa meg a **konfigurációs > a felhő beállításait**.
2. Tiltsa le a **Felhőbeli feltöltést és letöltést**.
3. Az eszköz részlegesen leválasztott módban való futtatásához engedélyezze **Azure Portal felügyeletet**.

    ![Kapcsolati mód](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Az eszköz leválasztott módban való futtatásához tiltsa le a **Azure Portal felügyeletet**. Most az eszközt csak a helyi webes felületen keresztül lehet felügyelni.

    ![Kapcsolati mód](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>A Power kezelése

Leállíthatja vagy újraindíthatja a virtuális eszközt a helyi webes felhasználói felület használatával. Az újraindítás előtt ajánlott offline állapotba állítani a gazdagépen található megosztásokat, majd magát az eszközt is. Ez a művelet lekicsinyíti az adatsérülés lehetőségét.

1. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > energiagazdálkodási beállítások**elemre.
2. A kívánt művelettől függően kattintson a **Leállítás** vagy az **Újraindítás** lehetőségre.

    ![Energiaellátási beállítások](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz.

> [!NOTE]
> Ha leállítja a virtuális eszközt, az eszközt a hypervisor-felügyeleten keresztül kell elindítania.
