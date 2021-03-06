---
title: 'Rövid útmutató: az első portál lekérdezése'
description: Ebben a rövid útmutatóban a következő lépésekkel futtathatja első lekérdezését Azure Portal az Azure Resource Graph Explorer használatával.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: 5cf355e78ad51e06d7ba27d48dd352f35b4c0740
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406799"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Rövid útmutató: az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerrel

Az Azure Resource Graph hatékonysága közvetlenül az Azure Resource Graph Explorerben Azure Portal érhető el. Az erőforrás-Graph Explorer böngészhető információt nyújt a lekérdezhető Azure Resource Manager erőforrástípusok és tulajdonságok közül. A Resource Graph Explorer egy tiszta felületet is biztosít a több lekérdezéssel való munkához, az eredmények kiértékeléséhez, valamint néhány lekérdezés eredményének konvertálásához egy olyan diagramra, amely rögzíthető egy Azure-irányítópulton.

Ennek a rövid útmutatónak a végén a Azure Portal és az Resource Graph Explorer használatával futtatta az első Resource Graph-lekérdezést, és az eredményeket egy irányítópultra rögzítette.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Az első Resource Graph-lekérdezés futtatásához a következő lépésekkel keresse meg a [Azure Portal](https://portal.azure.com) , és használja az Resource Graph Explorert:

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **Resource Graph Explorert**.

1. Az ablak **lekérdezés 1** részében adja meg a lekérdezési `Resources | project name, type | limit 5`, és válassza a **lekérdezés futtatása**lehetőséget.

   > [!NOTE]
   > Mivel ez a lekérdezési példa nem tartalmaz olyan rendezési módosítót, mint például a `order by`, a lekérdezés többszöri futtatása valószínűleg különböző erőforrás-készletet eredményez.

1. Tekintse át a lekérdezési választ az **eredmények** lapon. a lekérdezés részleteinek megtekintéséhez válassza az **üzenetek** fület, beleértve az eredmények számát és a lekérdezés időtartamát. A hibák (ha vannak) a lapon jelennek meg.

1. Frissítse a lekérdezést, hogy `order by` a **Name** tulajdonságot: `Resources | project name, type | limit 5 | order by name asc`. Ezután válassza a **lekérdezés futtatása**lehetőséget.

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Így először korlátozza a lekérdezés eredményeit, majd rendezi őket.

1. Frissítse a lekérdezést úgy, hogy először `order by` a **Name** tulajdonságot, majd `limit` az első öt találatra: `Resources | project name, type | order by name asc | limit 5`. Ezután válassza a **lekérdezés futtatása**lehetőséget.

Ha a végső lekérdezés többször is fut, feltételezve, hogy a környezetében semmi sem változik, a visszaadott eredmények konzisztensek és a várt módon jelennek meg – a **Name** tulajdonság szerint rendezve, de továbbra is az első öt találatra korlátozódik.

### <a name="schema-browser"></a>Séma böngésző

A séma böngészője a Resource Graph Explorer bal oldali ablaktábláján található. Ezen erőforrások listája az Azure Resource Graph által támogatott Azure-erőforrások összes olyan _erőforrás-típusát_ megjeleníti, amelyek az Ön számára elérhető bérlőben találhatók. Az erőforrás-típus vagy az altulajdonságok kibővítésével megjelenítheti az erőforrás-gráf-lekérdezések létrehozásához használható alárendelt tulajdonságokat.

Válassza ki az erőforrástípus `where type =="<resource type>"` a lekérdezés mezőbe. A gyermek tulajdonságok egyikének kiválasztásakor a `where <propertyName> == "INSERT_VALUE_HERE"` a lekérdezés mezőbe kerül.
A sémakezelő böngésző nagyszerű lehetőséget nyújt a lekérdezésekben használható tulajdonságok felderítésére. Ügyeljen arra, hogy az _INSERT\_value\_itt_ a saját értékét cserélje le, a feltételekkel, operátorokkal és függvényekkel állítsa be a lekérdezéseket a kívánt eredmények eléréséhez.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Diagram létrehozása az erőforrás-gráf lekérdezésből

A fenti utolsó lekérdezés futtatása után a **diagramok** lapon megjelenik egy üzenet arról, hogy "az eredményhalmaz nem kompatibilis a tortadiagram vizualizációval." A lista eredményeit tartalmazó lekérdezések nem hozhatók létre diagramba, de az erőforrásokat tartalmazó lekérdezések is megadhatók. Hozzon létre egy vizualizációt az erőforrás-gráf lekérdezésből, és használja a [minta lekérdezési számú virtuális gépet az operációs rendszer típusa alapján](./samples/starter.md#count-virtual-machines-by-os-type).

1. Az ablak **lekérdezés 1** részében adja meg a következő lekérdezést, és válassza a **lekérdezés futtatása**lehetőséget.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Válassza az **eredmények** fület, és figyelje meg, hogy a lekérdezésre adott válasz a következőt adja meg:.

1. Válassza a **diagramok** lapot. A lekérdezés most a vizualizációkat eredményezi. Módosítsa a típust a _diagram típusának kiválasztása..._ lehetőségre a _diagram vagy a_ _fánk diagram_ kiválasztásához, hogy kísérletezzen a rendelkezésre álló vizualizációs beállításokkal.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>A lekérdezés vizualizációjának rögzítése egy irányítópulton

Ha egy olyan lekérdezés eredményeiből áll, amely láthatóvá teheti, az adatvizualizáció az egyik irányítópultra rögzíthető. A fenti lekérdezés futtatása után kövesse az alábbi lépéseket:

1. Válassza a **Save (Mentés** ) lehetőséget, és adja meg a "virtuális gépek az operációs rendszer típusa szerint" nevet. Ezután kattintson a **Mentés** gombra a jobb oldali ablaktábla alján.

1. A lekérdezés **futtatása** gombra kattintva futtassa újra a lekérdezést, hogy az már mentve legyen.

1. A **diagramok** lapon válasszon ki egy adatvizualizációt. Ezután válassza **a rögzítés az irányítópulton**lehetőséget.

1. Válassza ki a portálon megjelenő értesítést, vagy válassza ki az **irányítópultot** a bal oldali ablaktáblán.

A lekérdezés mostantól elérhető az irányítópulton a lekérdezés nevével egyező csempe címével. Ha a lekérdezés nem mentette meg a rögzített értéket, a neve "Query 1".

A lekérdezés és az eredményül kapott adatvizualizáció futtatása és frissítése minden alkalommal, amikor az irányítópult betöltődik, valós idejű és dinamikus elemzéseket biztosít az Azure-környezethez közvetlenül a munkafolyamatban.

> [!NOTE]
> A listákat eredményező lekérdezések az irányítópulton is rögzíthető. A szolgáltatás nem korlátozódik a lekérdezések adatvizualizációra.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Példa Resource Graph Explorer-irányítópultok importálására

A Resource Graph-lekérdezések példáinak megadásához, valamint arról, hogyan használható az erőforrás-Graph Explorer a Azure Portal-munkafolyamatok fejlesztéséhez, próbálja ki ezeket a példa-irányítópultokat.

- [Resource Graph Explorer – minta irányítópult #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  [![példa a minta-irányítópultra #1](./media/arge-sample1-small.png)](./media/arge-sample1-large.png#lightbox)

- [Resource Graph Explorer – minta irányítópult #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  [![példa a minta-irányítópultra #2](./media/arge-sample2-small.png)](./media/arge-sample2-large.png#lightbox)

> [!NOTE]
> A fenti példában az irányítópult képernyőképei az Azure-környezettől függően eltérőek lesznek.

1. Válassza ki és töltse le a kiértékelni kívánt minta irányítópultot.

1. Azure Portal a bal oldali ablaktáblán válassza az **irányítópult** lehetőséget.

1. Válassza a **feltöltés**lehetőséget, majd keresse meg és válassza ki a letöltött minta irányítópult-fájlt. Ezután válassza a **Megnyitás**lehetőséget.

Az importált Irányítópult automatikusan megjelenik. Mivel már létezik a Azure Portalban, szükség szerint feltárhatja és módosíthatja a módosításokat, vagy létrehozhat új irányítópultokat a példából, hogy megossza a csapatával. További információ az irányítópultok használatáról: [irányítópultok létrehozása és megosztása a Azure Portalban](../../azure-portal/azure-portal-dashboards.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a minta erőforrás-gráf irányítópultokat a Azure Portal-környezetből, ezt a következő lépésekkel végezheti el:

1. Válassza ki az **irányítópultot** a bal oldali panelen.

1. Az irányítópult legördülő listából válassza ki a törölni kívánt minta erőforrás-gráf irányítópultot.

1. Válassza a **Törlés** lehetőséget az irányítópult tetején található irányítópult menüjében, majd kattintson **az OK gombra** a megerősítéshez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az Azure Resource Graph Explorer használatával futtatta az első lekérdezést, és megvizsgálta az irányítópulton megjelenő példákat. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)