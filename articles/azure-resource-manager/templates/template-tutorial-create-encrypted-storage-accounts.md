---
title: A sablonreferencia felhasználása
description: Használja az Azure Resource Manager-sablon hivatkozását egy sablon létrehozásához egy titkosított Storage-fiók telepítéséhez.
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 1bdd88d3f7dacdfe25645fda123076caadab5aec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472848"
---
# <a name="tutorial-utilize-the-azure-resource-manager-template-reference"></a>Oktatóanyag: az Azure Resource Manager-sablon dokumentációjának kihasználása

Megtudhatja, hogyan keresheti meg a sablonséma-információkat és használhatja fel őket Azure Resource Manager-sablonok létrehozására.

Ebben az oktatóanyagban egy alapszintű sablont fog használni az Azure-gyorssablonok közül. A sablon referenciadokumentációjával testreszabja a sablont egy titkosított tárfiók létrehozásához.

![Resource Manager-sablon referenciája titkosított Storage-fiók üzembe helyezése](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-tutorial-deploy-encrypted-storage-account.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon ismertetése
> * A sablonreferencia megkeresése
> * A sablon szerkesztése
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio Code a Resource Manager-eszközök bővítménnyel. További információ: [Azure Resource Manager sablonok létrehozása a Visual Studio Code használatával](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/) a Resource Manager-sablonok adattáraként szolgálnak. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. Az ebben a rövid útmutatóban használt sablon neve a következő: [Standard szintű tárfiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/). A sablon egy Azure Storage-fiókhoz tartozó erőforrást határoz meg.

1. A Visual Studio Code-ban válassza a **File** (Fájl) >**Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A **Fájl**>**Mentés másként** elem kiválasztásával mentse a fájlt a helyi számítógépre **azuredeploy.json** néven.

## <a name="understand-the-schema"></a>A séma bemutatása

1. A VS Code-ban csukja össze a sablont a gyökérszintig. Az ekkor előálló legegyszerűbb struktúra a következő elemeket tartalmazza:

    ![Resource Manager-sablon – Legegyszerűbb struktúra](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema**: adja meg a sablon nyelvének verziószámát tartalmazó JSON-sémafájl helyét.
    * **contentVersion**: adjon meg egy tetszőleges értéket ehhez az elemhez a sablon lényeges módosításainak dokumentálásához.
    * **parameters**: adja meg az üzembe helyezéskor beállított értékeket az erőforrás üzembe helyezésének testreszabásához.
    * **variables**: adja meg a sablonban JSON-töredékekként használt értékeket a sablonnyelvi kifejezések leegyszerűsítéséhez.
    * **resources**: adja meg az erőforráscsoportban üzembe helyezett vagy frissített erőforrástípusokat.
    * **outputs**: adja meg az üzembe helyezés után visszaadott értékeket.

2. Bontsa ki a **resources** elemet. Itt `Microsoft.Storage/storageAccounts` nevű erőforrás van meghatározva. A sablon egy nem titkosított tárfiókot hoz létre.

    ![Resource Manager-sablon, tárfiók-definíció](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>A sablonreferencia megkeresése

1. Tallózással keresse meg az [Azure-sablonok referenciáját](https://docs.microsoft.com/azure/templates/).
2. A **szűrés cím szerint** mezőben adja meg a **Storage-fiókokat**.
3. Válassza a **hivatkozás/sablon hivatkozás/Storage/&lt;verzió >/Storage-fiókok** lehetőséget az alábbi képernyőképen látható módon:

    ![Resource Manager-sablonreferencia – tárfiók](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    Ha nem tudja, hogy melyik verziót szeretné kiválasztani, használja a legújabb verziót.

4. Keresse meg a titkosítással kapcsolatos definíció információit.

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    Ugyanezen a webhelyen az alábbi leírás megerősíti, hogy az `encryption` objektum használatával történik a tárfiók létrehozása.

    ![Resource Manager-sablonreferencia, tárfiók titkosítása](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    A titkosítási kulcs kétféleképpen kezelhető. Használhat a Microsoft által felügyelt titkosítási kulcsokat a Storage Service Encryptionnel, vagy használhatja a saját titkosítási kulcsait. Az oktatóanyag egyszerűsége kedvéért válassza a `Microsoft.Storage` lehetőséget, hogy ne kelljen létrehoznia egy Azure Key Vaultot.

    ![Resource Manager-sablonreferencia, tárfiók-titkosítási objektum](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    A titkosítási objektumnak a következőképpen kell kinéznie:

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>A sablon szerkesztése

A Visual Studio Code-ban módosítsa úgy a sablont, hogy a resources elem a következőképpen nézzen ki:

![Resource Manager-sablon – Titkosított tárfiók – Erőforrások](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A Visual Studio Code üzembehelyezési eljárásról szóló rövid útmutatójában tekintse meg [A sablon üzembe helyezése](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) című szakaszt.

Az alábbi képernyőképen látható az újonnan létrehozott tárfiókok listázására szolgáló parancssori felületi parancs, amely azt jelzi, hogy a Blob Storage-titkosítás engedélyezve lett.

![Azure Resource Manager – Titkosított tárfiók](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag azt ismertette, hogyan használhatja a sablonreferenciát egy létező sablon testreszabására. Több tárfiókpéldány létrehozása:

> [!div class="nextstepaction"]
> [Több példány létrehozása](./template-tutorial-create-multiple-instances.md)
