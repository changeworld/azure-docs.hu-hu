---
title: Sablon exportálása Azure Portal
description: A Azure Portal használatával exportálhat egy Azure Resource Manager sablont az előfizetése erőforrásaiból.
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273734"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Egy-és többerőforrásos exportálás Azure Portal sablonba

Azure Resource Manager-sablonok létrehozásához segítséget nyújthat egy sablon exportálásához a meglévő erőforrásokból. Az exportált sablon segítségével megismerheti a JSON-szintaxist és az erőforrásokat telepítő tulajdonságokat. A jövőbeli központi telepítések automatizálásához kezdje az exportált sablonnal, és módosítsa a forgatókönyvét.

A Resource Manager lehetővé teszi, hogy egy vagy több erőforrást válasszon a sablonba való exportáláshoz. A sablonban pontosan a szükséges erőforrásokra koncentrálhat.

Ez a cikk bemutatja, hogyan exportálhat sablonokat a portálon keresztül. Használhatja az [Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates)-t, a [Azure PowerShellt](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)és a [REST API](/rest/api/resources/resourcegroups/exporttemplate)is.

## <a name="choose-the-right-export-option"></a>Válassza ki a megfelelő exportálási lehetőséget

Kétféleképpen exportálhat sablont:

* **Exportálás erőforráscsoporthoz vagy erőforrásból**. Ez a beállítás új sablont hoz létre a meglévő erőforrásokból. Az exportált sablon az erőforráscsoport aktuális állapotának "pillanatképe". A teljes erőforráscsoportot vagy az adott erőforráscsoporthoz tartozó erőforrásokat is exportálhatja.

* **Exportálás az üzembe helyezés előtt vagy az előzményekből**. Ez a beállítás az üzembe helyezéshez használt sablon pontos másolatát kérdezi le.

A választott lehetőségtől függően az exportált sablonok különböző tulajdonságokkal rendelkeznek.

| Erőforráscsoport vagy erőforrás alapján | Üzembe helyezés vagy előzmények előtt |
| --------------------- | ----------------- |
| A sablon az erőforrások aktuális állapotának pillanatképe. Az üzembe helyezés után végzett manuális módosításokat is tartalmazza. | A sablon csak az erőforrások állapotát jeleníti meg a telepítés időpontjában. Az üzembe helyezést követően elvégzett manuális módosítások nem szerepelnek. |
| Kiválaszthatja, hogy mely erőforrások legyenek exportálva az erőforráscsoporthoz. | A rendszer minden erőforrást tartalmaz egy adott központi telepítéshez. Ezekhez az erőforrásokhoz nem választhat részhalmazt, illetve nem adhat hozzá egy másik időpontban hozzáadott erőforrásokat. |
| A sablon az erőforrások összes tulajdonságát tartalmazza, beleértve az üzembe helyezés során általában nem beállított tulajdonságokat is. Előfordulhat, hogy a sablon újrafelhasználása előtt el kívánja távolítani vagy törölni szeretné ezeket a tulajdonságokat. | A sablon csak a központi telepítéshez szükséges tulajdonságokat tartalmazza. A sablon használatra kész. |
| A sablon valószínűleg nem tartalmazza az újrafelhasználáshoz szükséges összes paramétert. A legtöbb tulajdonság értéke rögzített a sablonban. A sablon más környezetekben való újbóli üzembe helyezéséhez olyan paramétereket kell hozzáadnia, amelyek növelik az erőforrások konfigurálásának lehetőségét.  Kiválaszthatja a **Paraméterek belefoglalása** lehetőséget is, így saját paramétereket hozhat létre. | A sablon olyan paramétereket tartalmaz, amelyek megkönnyítik a különböző környezetekben történő újraüzembe helyezést. |

Exportálja a sablont egy erőforráscsoporthoz vagy erőforrásból, ha:

* Rögzítenie kell az eredeti telepítés után létrehozott erőforrások módosításait.
* Ki szeretné választani, hogy mely erőforrások legyenek exportálva.

A sablon exportálása az üzembe helyezés előtt vagy az előzményekből a következő esetekben:

* Egy könnyen felhasználható sablont szeretne használni.
* Nem kell belefoglalnia az eredeti telepítés után elvégzett módosításokat.

## <a name="limitations"></a>Korlátozások

Egy erőforráscsoport vagy erőforrás exportálásakor az exportált sablon az egyes erőforrástípusok [közzétett sémái](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) alapján jön létre. Alkalmanként a séma nem rendelkezik az erőforrástípus legújabb verziójával. Ellenőrizze az exportált sablont, és győződjön meg róla, hogy tartalmazza a szükséges tulajdonságokat. Ha szükséges, szerkessze az exportált sablont a szükséges API-verzió használatára.

A sablon exportálása funkció nem támogatja Azure Data Factory erőforrások exportálását. A Data Factory-erőforrások exportálásával kapcsolatos további tudnivalókért lásd: az [adatfeldolgozó másolása vagy klónozása Azure Data Factory-ben](https://aka.ms/exportTemplateViaAdf).

A klasszikus üzemi modellel létrehozott erőforrások exportálásához [át kell telepítenie azokat a Resource Manager](https://aka.ms/migrateclassicresourcetoarm)-alapú üzemi modellbe.

## <a name="export-template-from-a-resource-group"></a>Sablon exportálása erőforráscsoporthoz

Egy vagy több erőforrás exportálása egy erőforráscsoporthoz:

1. Válassza ki az exportálni kívánt erőforrásokat tartalmazó erőforráscsoportot.

1. Válasszon ki egy vagy több erőforrást a jelölőnégyzetek bejelölésével.  Az összes kijelöléséhez jelölje be a **név**bal oldalán található jelölőnégyzetet. A **sablon exportálása** menüpont csak akkor válik elérhetővé, ha legalább egy erőforrást kiválasztott.

   ![Az összes erőforrás exportálása](./media/export-template-portal/select-all-resources.png)

    A képernyőképen csak a Storage-fiók van kiválasztva.
1. Válassza a **sablon exportálása**lehetőséget.

1. Megjelenik az exportált sablon, amely letölthető és telepíthető.

   ![Sablon megjelenítése](./media/export-template-portal/show-template.png)

   A **include paraméterek** alapértelmezés szerint ki vannak választva.  Ha be van jelölve, a sablon létrehozásakor minden sablon paraméter szerepelni fog. Ha saját paramétereket szeretne létrehozni, akkor a jelölőnégyzet bejelölésével ne vegye fel őket.

## <a name="export-template-from-a-resource"></a>Sablon exportálása erőforrásból

Egy erőforrás exportálása:

1. Válassza ki az exportálni kívánt erőforrást tartalmazó erőforráscsoportot.

1. Válassza ki az exportálni kívánt erőforrást az erőforrás megnyitásához.

1. Ehhez az erőforráshoz válassza a **sablon exportálása** lehetőséget a bal oldali ablaktáblán.

   ![Erőforrás exportálása](./media/export-template-portal/export-single-resource.png)

1. Megjelenik az exportált sablon, amely letölthető és telepíthető. A sablon csak egyetlen erőforrást tartalmaz. A **include paraméterek** alapértelmezés szerint ki vannak választva.  Ha be van jelölve, a sablon létrehozásakor minden sablon paraméter szerepelni fog. Ha saját paramétereket szeretne létrehozni, akkor a jelölőnégyzet bejelölésével ne vegye fel őket.

## <a name="export-template-before-deployment"></a>Sablon exportálása üzembe helyezés előtt

1. Válassza ki a telepíteni kívánt Azure-szolgáltatást.

1. Adja meg az új szolgáltatás értékeit.

1. Az érvényesítést követően, de az üzembe helyezés megkezdése előtt válassza **a sablon letöltése az automatizáláshoz**lehetőséget.

   ![Sablon letöltése](./media/export-template-portal/download-before-deployment.png)

1. Megjelenik a sablon, amely letölthető és telepíthető.


## <a name="export-template-after-deployment"></a>Sablon exportálása telepítés után

Exportálhatja a meglévő erőforrások üzembe helyezéséhez használt sablont. A kapott sablon pontosan az, amelyet az üzembe helyezéshez használt.

1. Válassza ki az exportálni kívánt erőforráscsoportot.

1. Válassza ki az üzemelő **példányok**alatt lévő hivatkozást.

   ![Telepítési előzmények kiválasztása](./media/export-template-portal/select-deployment-history.png)

1. Válasszon egyet az üzembe helyezési előzmények közül.

   ![Központi telepítés kiválasztása](./media/export-template-portal/select-details.png)

1. Válassza a **sablon**lehetőséget. Megjelenik az ehhez a központi telepítéshez használt sablon, amely letölthető.

   ![Sablon kiválasztása](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan exportálhat sablonokat az [Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), a [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)vagy a [REST API](/rest/api/resources/resourcegroups/exporttemplate)használatával.
- A Resource Manager-sablon szintaxisának megismeréséhez tekintse meg [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](template-syntax.md)című témakört.
- A sablonok fejlesztésének megismeréséhez tekintse meg az útmutató [lépésről lépésre szóló oktatóanyagokat](/azure/azure-resource-manager/).
- A Azure Resource Manager sablon sémáinak megtekintéséhez lásd: [sablon-hivatkozás](/azure/templates/).
