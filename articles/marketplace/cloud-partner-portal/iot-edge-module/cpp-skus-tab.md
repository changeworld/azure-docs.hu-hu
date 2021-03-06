---
title: Azure IoT Edge modul SKU-i | Azure piactér
description: Hozzon létre SKU-t egy IoT Edge modulhoz.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 230f3d6438d44c4e1e1721c0cb1453c85958e282
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813847"
---
# <a name="iot-edge-module-skus-tab"></a>IoT Edge modul SKU-i lapja

Az **új ajánlat** oldal **SKU** -i lapján létrehozhat egy vagy több SKU-t, és hozzárendelheti őket az új ajánlathoz.  Különböző SKU-ket használhat a megoldások a szolgáltatások, a számlázási modellek vagy más jellemzők megkülönböztetésére.


## <a name="sku-settings"></a>SKU-beállítások

Új ajánlat létrehozásának megkezdése esetén nincs az ajánlathoz társított SKU. Új SKU létrehozásához kövesse az alábbi lépéseket:

- A **IoT Edge-modulok > új ajánlat** lapon válassza a **SKUs** fület.
- Az SKU alatt válassza az **+ új SKU** elemet a párbeszédpanel megnyitásához.

  ![Új SKU gomb a IoT Edge-modulok új ajánlat lapján](./media/iot-edge-module-skus-tab-new-sku.png)

- Az **új SKU** párbeszédpanelen adja meg az SKU azonosítóját, majd kattintson az **OK gombra**.
(Az alábbi táblázat az azonosító elnevezési konvenciókat tartalmazza.)

A rendszer frissíti a **SKUs** fület, és MEGJELENÍTI az SKU konfigurálásához használt mezőket. A mezőhöz hozzáfűzni kívánt csillag (*) azt jelzi, hogy a név megadása kötelező.

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU-azonosító\***       | Az SKU azonosítója. Ez a név legfeljebb 50 karakterből állhat, amely kisbetűs alfanumerikus karaktereket vagy kötőjelet (-) tartalmaz, de nem végződhet kötőjeltel. **Megjegyzés:** Ez a név nem módosítható az ajánlat közzétételét követően. A név nyilvánosan látható a termék URL-címeiben. |
|  |  |


## <a name="sku-details"></a>SKU részletei

Az **SKU adatainak** konfigurálásával meghatározhatja, hogyan jelenjen meg az SKU az Azure piactéren és az Azure Portal webhelyein.

![IoT Edge modul SKU-metaadatai](media/iot-edge-module-skus-tab-metadata.png)

Az alábbi táblázat a mezők célját, tartalmát és formázását írja le az **SKU-részletek**területen. A kötelező mezőket csillag (*) alapján vádoljuk.

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Cím\***        | A SKU címe. Legfeljebb 50 karakter hosszú lehet. <br/> Ez az Azure Portalon jelenik meg, és alapértelmezett modulként lesz használva (szóközök és speciális karakterek nélkül), ha üzembe van helyezve. Az alábbi képekből megtudhatja, hogy pontosan hol jelennek meg a mező.|
| **Összefoglalás\***      | Az SKU rövid összefoglalása. Legfeljebb 100 karakter hosszú lehet. Ne **foglalja össze** az ajánlatot, csak az SKU-t.  Ez az összefoglalás az Azure piactéren jelenik meg. Az alábbi képekből megtudhatja, hogy pontosan hol jelennek meg a mező.|
| **Leírás\***  | Az SKU rövid leírása. Legfeljebb 3000 karakter hosszú lehet. Ne írja le az ajánlatot, hanem csak ezt az SKU-t. Az Azure Marketplace-en és a Azure Portal is megjelenik. A Azure Portal a piactér leírásában a Marketplace (Piactér) lapon definiált ajánlat leírása jelenik meg.  Ez lehet ugyanaz, mint az SKU összegzése. Az alábbi képekből megtudhatja, hogy pontosan hol jelennek meg a mező.|
| **Az SKU elrejtése\*** | Tartsa meg az alapértelmezett beállítást, amely **nem**. |
|  |  |


### <a name="sku-example"></a>SKU-példa

 Az alábbi példák bemutatják, hogyan jelennek meg a SKU **title**, az **Summary**és a **descripting** mezők különböző nézetekben.
 

#### <a name="on-the-azure-marketplace-website"></a>Az Azure Marketplace webhelyén:

- Az SKU részleteinek megtekintésekor:

    ![Hogyan jelennek meg a SKU-ban az Azure Marketplace webhelyén](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>Az Azure Portal webhelyen:

- Az SKU-ra való böngészéskor:

    ![Hogyan jelenik meg IoT Edge modul a Azure Portal böngészése során #1](media/iot-edge-module-portal-browse.png)

    ![Hogyan jelenik meg IoT Edge modul a Azure Portal böngészése során #2](media/iot-edge-module-portal-product-picker.png)

- SKU-ra való kereséskor:

    ![Hogyan jelenik meg IoT Edge modul a Azure Portal keresésekor](media/iot-edge-module-portal-search.png)

- Az SKU részleteinek megtekintésekor:

    ![Hogyan jeleníti meg IoT Edge modul a termék részleteit a portálon](./media/iot-edge-module-portal-pdp.png)

- A modul telepítésekor:
    
    ![IoT Edge modul megjelenítése az üzembe helyezéskor](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>SKU-tartalom

Az **Edge-modul lemezképei**területen adja meg a IoT Edge modul feltöltéséhez szükséges adatokat.

Adja meg nekünk a IoT Edge modul képét tartalmazó [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) elérését, hogy fel lehessen tölteni és hitelesíteni lehessen. A közzétételt követően a rendszer a IoT Edge modult az Azure piactéren üzemeltetett nyilvános tároló-beállításjegyzék használatával másolja és terjeszti.

Több platformot is megcélozhat, és a címkékkel több verziót is megadhat. További információ a [címkékről és a verziószámozásról: "a IoT Edge modul technikai eszközeinek előkészítése"](./cpp-create-technical-assets.md).

![IoT Edge modul rendszerképei](./media/iot-edge-module-skus-tab-acr.png)

A következő táblázat a mezők célját, tartalmát és formázását ismerteti a szakasz **képtára részleteit** és a **rendszerkép verzióját**.  A kötelező mezőket csillag (*) alapján vádoljuk.


|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Rendszerkép-tárház részletei***    |
| **Előfizetés-azonosító\***        | Az ACR Azure-előfizetés azonosítója.|
| **Erőforráscsoport neve\***      | Az ACR erőforráscsoport-neve.|
| **Beállításjegyzék neve\***  | Az ACR-beállításjegyzék neve. Csak másolja a beállításjegyzék nevét, ne a bejelentkezési kiszolgáló nevét (például a `azurecr.io`nélkül) |
| **Adattár neve\***  | Az IoT Edge modult tartalmazó ACR adattárának neve. **Megjegyzés:** A név beállítása után később nem módosítható. Adjon meg egy egyedi nevet annak biztosítására, hogy a fiókban ne legyen azonos nevű másik ajánlat. |
| **Felhasználónév\*** | Az ACR-hez társított Felhasználónév (rendszergazdai Felhasználónév). |
| **Jelszó\*** | Az ACR-hez társított jelszó. |
|    |  ***Rendszerkép verziója***   |
| **Képcímke vagy kivonatoló\*** | Tartalmaznia kell legalább egy `latest` címkét és egy Version címkét (például `xx.xx.xx-`, ahol XX egy szám). A több platform megcélzásához meg kell jelennie a [címkéknek](https://github.com/estesp/manifest-tool) . A jegyzékfájl által hivatkozott összes címkét is fel kell venni, hogy fel lehessen tölteni őket. Címkék használatával hozzáadhat egy IoT Edge modul több verzióját is. Az összes manifest-címkének (kivéve `latest`) `X.Y-` vagy `X.Y.Z-` kell kezdődnie, ahol az X, az Y, a Z egész számok. További információ a [címkékről és a verziószámozásról: "a IoT Edge modul technikai eszközeinek előkészítése"](./cpp-create-technical-assets.md). <br/> Ha például egy `latest` címke erre a pontra mutat `1.0.1-linux-x64`, `1.0.1-linux-arm32`, és `1.0.1-windows-arm32`re, akkor ezeket a 6 címkéket itt fel kell venni. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Az ügyfelek használatának segítése az alapértelmezett beállításokkal

Adja meg a IoT Edge modul üzembe helyezésének leggyakoribb beállításait. Az ügyfelek központi telepítésének optimalizálása azáltal, hogy elindítják a IoT Edge modult az alapértelmezett beállításokkal.

![IoT Edge modul alapértelmezett beállításai az üzembe helyezéskor](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

Az alábbi táblázat ismerteti az **alapértelmezett útvonalak**mezőinek, tartalmának és formázásának módját, az **alapértelmezett dupla kívánt tulajdonságokat**, az **alapértelmezett környezeti változókat**és az **alapértelmezett CreateOptions**.

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Alapértelmezett útvonalak**        | Az alapértelmezett útvonal nevének és értékének 512 karakternél rövidebbnek kell lennie. Legfeljebb 5 alapértelmezett útvonalat adhat meg. Ügyeljen arra, hogy a megfelelő [útvonal-szintaxist](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) használja az útvonal értékeként. A modulra való hivatkozáshoz használja az alapértelmezett moduljának nevét, amely az **SKU címét** szóközök és speciális karakterek nélkül fogja használni. Ha más, még nem ismert modulokra hivatkozik, használja az `<FROM_MODULE_NAME>` konvenciót, hogy tájékoztassa ügyfeleit arról, hogy frissíteniük kell ezt az információt. További információ a [IoT Edge útvonalakról](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Ha például a modul `ContosoModule` figyeli a `ContosoInput` és a kimeneti adatok bemeneteit a `ContosoOutput`-on, érdemes megadnia a következő két alapértelmezett útvonalat:<br/>-Name #1: `ToContosoModule`<br/>-Value #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>-Name #2: `FromContosoModuleToCloud`<br/>-Value #2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Alapértelmezett Twin kívánt tulajdonságok**      | Az alapértelmezett dupla kívánt tulajdonságok nevének és értékének 512 karakternél rövidebbnek kell lennie. Legfeljebb 5 név/érték Twin kívánt tulajdonságot adhat meg. A Twin kívánt tulajdonságok értékének érvényes JSON-nek, nem kimaradt, tömb nélkülinek és 4-es, legfeljebb 4 beágyazott hierarchiának kell lennie. További információ a [Twin kívánt tulajdonságokról](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Ha például egy modul támogatja a dinamikusan konfigurálható frissítési sebességet a kívánt tulajdonságok alapján, érdemes megadnia a következő alapértelmezett dupla kívánt tulajdonságot:<br/> -Name #1: `RefreshRate`<br/>-Value #1: `60`|
| **Alapértelmezett környezeti változók**  | Az alapértelmezett környezeti változók nevének és értékének 512 karakternél rövidebbnek kell lennie. Legfeljebb 5 név/érték környezeti változót adhat meg. <br/>Ha például egy modulnak el kell fogadnia a használati feltételeket az indítás előtt, megadhatja a következő környezeti változót:<br/> -Name #1: `ACCEPT_EULA`<br/>-Value #1: `Y`|
| **Alapértelmezett createOptions**  | A createOptions 512 karakternél rövidebbnek kell lennie. Érvényes JSON-nek kell lennie, nem Escape-nek. További információ a [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Ha például egy modulhoz kötés szükséges egy porthoz, akkor a következő createOptions határozhatja meg:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Válassza a **Mentés** lehetőséget az SKU-beállítások mentéséhez. 


## <a name="next-steps"></a>További lépések

A [piactér lapon](./cpp-marketplace-tab.md) létrehozhat egy piactér-leírást az ajánlathoz.
