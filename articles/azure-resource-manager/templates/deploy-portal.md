---
title: Erőforrások üzembe helyezése Azure Portal
description: A Azure Portal és az Azure Erőforrás-kezelés használatával az erőforrásokat üzembe helyezheti az előfizetésében található erőforráscsoporthoz.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 32c807b4881bc59b6bec0d26ab3664abdb200628
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274358"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Portallal

Ismerje meg, hogyan helyezhet üzembe az Azure-erőforrásokat a [Azure Portal](https://portal.azure.com) és a [Azure Resource Manager](overview.md) használatával. Az erőforrások kezelésével kapcsolatos további információkért lásd: [Az Azure-erőforrások kezelése a Azure Portal használatával](../management/manage-resources-portal.md).

Az Azure-erőforrások a Azure Portal használatával történő üzembe helyezése általában két lépést foglal magában:

- Hozzon létre egy erőforráscsoportot.
- Erőforrások üzembe helyezése az erőforráscsoporthoz.

Emellett Azure Resource Manager sablont is üzembe helyezhet az Azure-erőforrások létrehozásához.

Ez a cikk mindkét módszert mutatja be.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

1. Új erőforráscsoport létrehozásához válassza az **erőforráscsoportok** lehetőséget a [Azure Portal](https://portal.azure.com).

   ![Erőforráscsoportok kiválasztása](./media/deploy-portal/select-resource-groups.png)

1. Az erőforráscsoportok területen válassza a **Hozzáadás**lehetőséget.

   ![Erőforráscsoport hozzáadása](./media/deploy-portal/add-resource-group.png)

1. Válassza ki vagy adja meg a következő tulajdonságértékek értékét:

    - **Előfizetés**: válasszon ki egy Azure-előfizetést.
    - **Erőforráscsoport**: adja meg az erőforráscsoport nevét.
    - **Régió**: válasszon egy Azure-helyet. Ez az a hely, ahol az erőforráscsoport az erőforrásokkal kapcsolatos metaadatokat tárolja. Megfelelőségi okokból érdemes megadnia a metaadatok tárolási helyét. Általánosságban elmondható, hogy olyan helyet kell megadnia, ahol az erőforrások többsége található. Ha ugyanazt a helyet használja, egyszerűbbé teheti a sablon használatát.

   ![Csoportok értékeinek beállítása](./media/deploy-portal/set-group-properties.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Tekintse át az értékeket, majd válassza a **Létrehozás**lehetőséget.
1. Válassza a **frissítés** lehetőséget, mielőtt megtekinti az új erőforráscsoportot a listában.

## <a name="deploy-resources-to-a-resource-group"></a>Erőforrások üzembe helyezése erőforráscsoporthoz

Az erőforráscsoport létrehozása után erőforrásokat telepíthet a csoportba a piactéren. A piactér előre definiált megoldásokat kínál a gyakori forgatókönyvekhez.

1. A központi telepítés elindításához válassza az **erőforrás létrehozása** lehetőséget a [Azure Portal](https://portal.azure.com).

   ![Új erőforrás](./media/deploy-portal/new-resources.png)

1. Keresse meg a telepíteni kívánt erőforrás típusát. Az erőforrások kategóriákba vannak rendezve. Ha nem látja a központilag telepíteni kívánt megoldást, megkeresheti a piactéren. Az alábbi képernyőfelvételen látható, hogy az Ubuntu-kiszolgáló van kiválasztva.

   ![Erőforrás típusának kiválasztása](./media/deploy-portal/select-resource-type.png)

1. A kiválasztott erőforrás típusától függően az üzembe helyezés előtt meg kell adni a megfelelő tulajdonságok gyűjteményét. Minden típushoz ki kell választania egy cél erőforráscsoportot. Az alábbi képen megtudhatja, hogyan hozhat létre Linux rendszerű virtuális gépet, és hogyan telepítheti azt a létrehozott erőforráscsoporthoz.

   ![Erőforráscsoport létrehozása](./media/deploy-portal/select-existing-group.png)

   Azt is megteheti, hogy létrehoz egy erőforráscsoportot az erőforrások üzembe helyezése során. Válassza az **új létrehozása** elemet, és adja meg az erőforráscsoport nevét.

1. A telepítés megkezdődik. Az üzembe helyezés több percet is igénybe vehet. Egyes erőforrások hosszabb időt vehetnek igénybe, mint a többi erőforrás. Ha a telepítés befejeződött, egy értesítés jelenik meg. Válassza **az erőforrás** megnyitása a megnyitáshoz lehetőséget.

   ![Értesítés megtekintése](./media/deploy-portal/view-notification.png)

1. Az erőforrások üzembe helyezése után további erőforrásokat adhat hozzá az erőforráscsoporthoz a **Hozzáadás gombra**kattintva.

   ![Erőforrás hozzáadása](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Erőforrások üzembe helyezése egyéni sablonból

Ha egy központi telepítést kíván végrehajtani, de nem használja a piactér egyik sablonját sem, létrehozhat egy testreszabott sablont, amely meghatározza a megoldás infrastruktúráját. További információ a sablonok létrehozásáról: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](template-syntax.md).

> [!NOTE]
> A portál felülete nem támogatja a [titkos kulcsra hivatkozó Key Vault](key-vault-parameter.md). Ehelyett a [PowerShell](deploy-powershell.md) vagy az [Azure CLI](deploy-cli.md) használatával helyezheti üzembe a SABLONT helyileg vagy külső URI-ból.

1. Testreszabott sablon a portálon keresztüli üzembe helyezéséhez válassza az **erőforrás létrehozása**, majd a **sablon**keresése lehetőséget. majd válassza a **template Deployment**lehetőséget.

   ![Sablon központi telepítésének keresése](./media/deploy-portal/search-template.png)

1. Kattintson a **Létrehozás** gombra.
1. A sablonok létrehozásához több lehetőség is megjelenik:

    - **Saját sablon készítése a szerkesztőben**: sablon létrehozása a portál sablon szerkesztőjével.  A szerkesztő képes az erőforrás-sablon sémájának hozzáadására.
    - **Gyakori sablonok**: a linuxos virtuális gépek, a Windows-alapú virtuális gépek, a webalkalmazások és az Azure SQL Database-adatbázisok létrehozásához négy gyakori sablon áll rendelkezésre.
    - **GitHub-gyorsindítási sablon betöltése**: meglévő rövid útmutató- [sablonok](https://azure.microsoft.com/resources/templates/)használata.

   ![Beállítások megtekintése](./media/deploy-portal/see-options.png)

    Ez az oktatóanyag a gyors üzembe helyezési sablon betöltéséhez nyújt útmutatást.

1. A GitHub gyors üzembe helyezése **sablon betöltéséhez**írja be vagy válassza a **101-Storage-Account-Create**lehetőséget.

    Erre két lehetősége van:

    - **Sablon kiválasztása**: a sablon üzembe helyezése.
    - **Sablon szerkesztése**: az üzembe helyezés előtt szerkessze a rövid útmutató sablont.

1. Válassza a **Sablon szerkesztése** lehetőséget a portál sablon szerkesztőjének megismeréséhez. A sablon betöltődik a szerkesztőbe. Figyelje meg, hogy van két paraméter: **tárfióktípus** és **Location**.

   ![Sablon létrehozása](./media/deploy-portal/show-json.png)

1. Végezze el a sablon kisebb módosítását. Frissítse például a **storageAccountName** változót a következőre:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Kattintson a **Mentés** gombra. Ekkor megjelenik a portál sablonjának telepítési felülete. Figyelje meg a sablonban megadott két paramétert.
1. Adja meg vagy válassza ki a tulajdonságértékek értékét:

    - **Előfizetés**: válasszon ki egy Azure-előfizetést.
    - **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, és adjon meg egy nevet.
    - **Hely**: válasszon ki egy Azure-helyet.
    - **Storage-fiók típusa**: használja az alapértelmezett értéket.
    - **Hely**: használja az alapértelmezett értéket.
    - **Elfogadom a fenti feltételeket és kikötéseket**: (kiválasztás)

1. Válassza a **Beszerzés** lehetőséget.

## <a name="next-steps"></a>További lépések

- A naplók megtekintéséhez lásd: [műveletek naplózása a Resource Managerrel](../management/view-activity-logs.md).
- Az üzembe helyezési hibák elhárításáról lásd: [telepítési műveletek megtekintése](deployment-history.md).
- Ha a sablont egy központi telepítésből vagy erőforráscsoporthoz szeretné exportálni, tekintse meg a [Azure Resource Manager-sablonok exportálása](export-template-portal.md)című témakört.
- A szolgáltatás több régióban való biztonságos kihelyezéséhez lásd: [Azure Telepítéskezelő](deployment-manager-overview.md).
