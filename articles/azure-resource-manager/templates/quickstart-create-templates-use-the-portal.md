---
title: Sablon üzembe helyezése – Azure Portal
description: Megismerheti, hogyan hozhatja létre első Azure Resource Manager-sablonját az Azure Portalon, és hogyan helyezheti üzembe.
author: mumian
ms.date: 06/12/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 66f730cae654c6c740e4224cfbb2ba1ae41d8df5
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689725"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Rövid útmutató: Azure Resource Manager-sablon létrehozása és üzembe helyezése az Azure Portalon

Útmutató a Resource Manager-sablonok létrehozásához a Azure Portal használatával, valamint a sablon szerkesztésének és telepítésének folyamata a portálról. A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. Az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmak megismeréséhez tekintse meg a [sablonok üzembe helyezésének áttekintése](overview.md)című témakört.

![Resource Manager-sablon – rövid útmutató portál](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Az oktatóanyag befejezése után üzembe kell helyeznie egy Azure Storage-fiókot. Ugyanez a folyamat használható más Azure-erőforrások üzembe helyezésére is.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="generate-a-template-using-the-portal"></a>Sablon generálása az Azure Portal használatával

A teljesen új Resource Manager-sablon létrehozása nem egyszerű feladat, különösen akkor, ha még nem ismeri az Azure-beli üzembe helyezést, és nem ismeri a JSON-formátumot. A Azure Portal használatával beállíthat egy erőforrást, például egy Azure Storage-fiókot. Az erőforrás üzembe helyezése előtt exportálhatja a konfigurációt egy Resource Manager-sablonba. A sablont későbbi használatra mentheti is.

Számos tapasztalt sablon-fejlesztő ezt a módszert használja a sablonok létrehozásához, amikor olyan Azure-erőforrásokat próbálnak üzembe helyezni, amelyekről nem ismeri őket. A sablonok a portál használatával történő exportálásával kapcsolatos további információkért lásd: [erőforráscsoportok exportálása sablonokba](../management/manage-resource-groups-portal.md#export-resource-groups-to-templates). A munkasablon megkeresésének másik módja az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/)származik.

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be.
1. A Azure Portal menüben válassza az **erőforrás létrehozása**lehetőséget.

    ![Válassza az erőforrás létrehozása lehetőséget Azure Portal menüből](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

1. Válassza a **Tárolás** > **Tárfiók** lehetőséget.

    ![Azure Storage-fiók létrehozása](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
1. Adja meg a következő információkat:

    |Név|Value (Díj)|
    |----|----|
    |**Erőforráscsoport**|Válassza az **új létrehozása**lehetőséget, és adjon meg egy tetszőleges erőforráscsoport-nevet. A képernyőképen az erőforráscsoport neve *mystorage1016rg*. Az erőforráscsoport az Azure-erőforrások tárolója. Az erőforráscsoport megkönnyíti az Azure-erőforrások kezelését. |
    |**Name (Név)**|Adjon egyedi nevet a Storage-fióknak. A Storage-fiók nevének egyedinek kell lennie az összes Azure-ban, és csak kisbetűket és számokat tartalmaz. A névnek 3 – 24 karakter hosszúnak kell lennie. Ha hibaüzenet jelenik meg, amely szerint a "mystorage1016" nevű Storage-fiók neve már használatban van ", próbálja meg használni **a&lt;nevét > storage&lt;a mai dátumot a MMDD >** , például **johndolestorage1016**. További információ: [elnevezési szabályok és korlátozások](/azure/architecture/best-practices/resource-naming).|

    A többi tulajdonság esetén használhatja az alapértelmezett értékeket.

    ![Azure-tárfiók konfigurációjának létrehozása az Azure Portalon](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Üzembe helyezés előtt egyes exportált sablonokat szerkeszteni szükséges.

1. Válassza a képernyő alján a **Felülvizsgálat + létrehozás** lehetőséget. A következő lépésben ne válassza a **Létrehozás** lehetőséget.
1. Válassza az **Automatizációs sablon letöltése** lehetőséget a képernyő alján. A portál megjeleníti a létrehozott sablont:

    ![Sablon generálása a Portal használatával](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    A sablon a főoldalon látható. Ez egy olyan JSON-fájl, amely hat legfelső szintű elemmel rendelkezik – `schema`, `contentVersion`, `parameters`, `variables`, `resources`és `output`. További információt az [Azure Resource Manager-sablonok struktúrája és szintaxisa](./template-syntax.md) című témakörben talál.

    Hat paraméter van definiálva. Az egyikük neve **storageAccountName**. Az előző képernyőképen a második kiemelt rész bemutatja, hogyan hivatkozhat erre a paraméterre a sablonban. A következő szakaszban úgy szerkeszti a sablont, hogy létrehozott nevet használjon a tárfiók neveként.

    A sablonban egy Azure-erőforrás van definiálva. A típus `Microsoft.Storage/storageAccounts`. Tekintse át az erőforrás definiálásának módját és a definíciós struktúrát.
1. Válassza a **Letöltés** lehetőséget a képernyő tetején.
1. Nyissa meg a letöltött zip-fájlt, majd mentse a **template. JSON** fájlt a számítógépre. A következő szakaszban egy üzembehelyezési sablon eszközzel fogja szerkeszteni a sablont.
1. A **Paraméterek** lapon tekintheti meg a paraméterekhez megadott értékeket. Jegyezze fel ezeket az értékeket, mivel a következő szakaszban, a sablon üzembe helyezésekor szükség lesz rájuk.

    ![Sablon generálása a Portal használatával](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    A sablonfájl és a Parameters fájl használatával létrehozhat egy erőforrást, ebben az oktatóanyagban egy Azure Storage-fiókot.

## <a name="edit-and-deploy-the-template"></a>Sablon szerkesztése és üzembe helyezése

Az Azure Portal is használható egyes alapvető szerkesztési műveletekhez. Ebben a rövid útmutatóban a portál *Template deployment* nevű eszközét használjuk. A *sablon központi telepítése* ebben az oktatóanyagban van használatban, így a teljes oktatóanyagot elvégezheti egy felület használatával – a Azure Portal. Összetettebb sablon szerkesztéséhez érdemes lehet használni a [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)-ot, amely sokoldalú szerkesztési funkciókat biztosít.

> [!IMPORTANT]
> A sablon üzembe helyezése felületet biztosít az egyszerű sablonok teszteléséhez. A szolgáltatás éles környezetben való használata nem ajánlott. Ehelyett tárolja a sablonokat egy Azure Storage-fiókban vagy egy forráskód-tárházban, például a GitHubon.

Az Azure megköveteli, hogy minden Azure-szolgáltatás egyedi névvel rendelkezzen. A központi telepítés meghiúsulhat, ha olyan nevű Storage-fióknevet adott meg, amely már létezik. A probléma elkerüléséhez módosítsa a sablont úgy, hogy a sablon függvény hívása `uniquestring()` egy egyedi Storage-fióknév létrehozásához.

1. A Azure Portal menüben vagy a **Kezdőlap** lapon válassza az **erőforrás létrehozása**lehetőséget.
1. A **Keresés a Marketplace-en** mezőbe írja be a **template deployment** kifejezést, majd nyomja le az **ENTER** billentyűt.
1. Válassza a **Template deployment** lehetőséget.

    ![Azure Resource Manager-sablonkönyvtár](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
1. Kattintson a **Létrehozás** gombra.
1. Válassza a **Saját sablon készítése a szerkesztőben** lehetőséget.
1. Válassza a **Fájl betöltése** lehetőséget, majd az útmutatásokat követve töltse be az előző szakaszban letöltött template.json fájlt.
1. Végezze el a következő három módosítást a sablonon:

    ![Azure Resource Manager-sablonok](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Távolítsa el a **storageAccountName** paramétert az előző képernyőképen látható módon.
   - Vegyen fel egy **storageAccountName** nevű változót az előző képernyőképen látható módon:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       A következő két sablon függvényt használja a rendszer: `concat()` és `uniqueString()`.
   - Frissítse a **Microsoft.Storage/storageAccounts** erőforrás név elemét, és a paraméter helyett használja az újonnan definiált változót:

       ```json
       "name": "[variables('storageAccountName')]",
       ```

     A végső sablon így fog kinézni:

     ```json
     {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "location": {
               "type": "string"
           },
           "accountType": {
               "type": "string"
           },
           "kind": {
               "type": "string"
           },
           "accessTier": {
               "type": "string"
           },
           "supportsHttpsTrafficOnly": {
               "type": "bool"
           }
       },
       "variables": {
           "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       },
       "resources": [
           {
               "name": "[variables('storageAccountName')]",
               "type": "Microsoft.Storage/storageAccounts",
               "apiVersion": "2018-07-01",
               "location": "[parameters('location')]",
               "properties": {
                   "accessTier": "[parameters('accessTier')]",
                   "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
               },
               "dependsOn": [],
               "sku": {
                   "name": "[parameters('accountType')]"
               },
               "kind": "[parameters('kind')]"
           }
       ],
       "outputs": {}
     }
     ```
1. Kattintson a **Mentés** gombra.
1. Írja be a következő értékeket:

    |Név|Value (Díj)|
    |----|----|
    |**Erőforráscsoport**|Válassza ki az utolsó szakaszban létrehozott erőforráscsoport-nevet. |
    |**Hely**|Válassza ki a Storage-fiók helyét. Például az **USA középső**régiója. |
    |**Fiók típusa**|Adja meg **Standard_LRS** ehhez a rövid útmutatóhoz. |
    |**Típusú**|Adja meg a rövid útmutató **StorageV2** . |
    |**Hozzáférési szintek**|Adja meg a **gyors** üzembe helyezési útmutatót. |
    |**Https-forgalom engedélyezve**| Ennél a rövid útmutatónál válassza a **true** (igaz) értéket. |
    |**Elfogadom a fenti feltételeket és kikötéseket**|Válassza|

    Az alábbi képen egy minta üzembe helyezés látható:

    ![Azure Resource Manager-sablonok üzembe helyezése](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

1. Válassza a **Beszerzés** lehetőséget.
1. Az üzembe helyezés állapotát úgy nézheti meg, ha a képernyő felső részén kiválasztja a harang (értesítések) ikont. Ekkor **a telepítés folyamatban**van. Várjon, amíg az üzembe helyezés befejeződik.

    ![Azure Resource Manager-sablonok üzembehelyezési értesítése](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

1. Válassza az **Ugrás az erőforráscsoportra** lehetőséget az értesítési panelen. A következőhöz hasonló képernyő jelenik meg:

    ![Azure Resource Manager-sablonok üzembehelyezési erőforráscsoportja](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Láthatja, hogy az üzembe helyezés állapota sikeres, és csak egyetlen tárfiók található az erőforráscsoportban. A tárfiók neve egy, a sablon által létrehozott egyedi sztring. Az Azure-tárfiókokkal kapcsolatos további információkért lásd: [Rövid útmutató: blobok feltöltése, letöltése és listázása az Azure Portal használatával](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal menüben.
1. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
1. Válassza ki az erőforráscsoport nevét.  Az erőforráscsoportban megjelenik a tárfiók.
1. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan generálható sablon az Azure Portalon, és hogyan helyezhető üzembe a sablon a Portal használatával. Ebben a rövid útmutatóban egy egyszerű sablont hoztunk létre, amelyben egyetlen Azure-erőforrás szerepel. Ha összetettebb sablonnal dolgozik, egyszerűbb a Visual Studio Code-ot vagy a Visual Studiót használni a sablon létrehozásához. A sablonok fejlesztésével kapcsolatos további tudnivalókért tekintse meg az új kezdő oktatóanyag-sorozatot:

> [!div class="nextstepaction"]
> [Kezdő oktatóanyagok](./template-tutorial-create-first-template.md)