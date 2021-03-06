---
title: Jegyzetfüzetek engedélyezése a Azure Cosmos DB fiókban (előzetes verzió)
description: A Azure Cosmos DB beépített jegyzetfüzetei lehetővé teszik az adatok elemzését és megjelenítését a portálon belülről. Ez a cikk bemutatja, hogyan engedélyezheti ezt a funkciót a Cosmos-fiókokhoz.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768022"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Jegyzetfüzetek engedélyezése Azure Cosmos DB-fiókokhoz (előzetes verzió)

> [!IMPORTANT]
> A Azure Cosmos DB beépített jegyzetfüzetei jelenleg a következő Azure-régiókban érhetők el: Kelet-Ausztrália, USA keleti régiója, USA 2. keleti régiója, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Nyugat-Európa és az USA 2. nyugati régiója. Jegyzetfüzetek használatához [hozzon létre egy új fiókot jegyzetfüzetekkel](#enable-notebooks-in-a-new-cosmos-account) , vagy [engedélyezze a jegyzetfüzeteket egy meglévő fiókban](#enable-notebooks-in-an-existing-cosmos-account) az egyik régióban.

A Azure Cosmos DB beépített Jupyter notebookok lehetővé teszik az adatok elemzését és megjelenítését a Azure Portal. Ez a cikk leírja, hogyan lehet engedélyezni ezt a funkciót az Azure Cosmos DB-fiókhoz.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Jegyzetfüzetek engedélyezése új Cosmos-fiókban
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Kattintson az **Erőforrás létrehozása** > **Adatbázisok** > **Azure Cosmos DB** elemre.
1. A **Azure Cosmos db fiók létrehozása** lapon válassza a **jegyzetfüzetek**lehetőséget. 
 
    ![Válassza a jegyzetfüzetek lehetőséget a Azure Cosmos DB létrehozás panelen](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Válassza az **Áttekintés + létrehozás** lehetőséget. Kihagyhatja a **hálózat** és a **címkék** lehetőséget. 
1. Tekintse át a Fiókbeállítások beállítást, majd kattintson a **Létrehozás**gombra. A fiók létrehozása néhány percet vesz igénybe. Várjon, amíg befejeződik a portál oldalának megjelenítése a **központi telepítés befejezéséhez**. 

    ![Az Azure Portal Értesítések panelje](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa a Azure Cosmos db fiók lapot. 

    ![A Azure Cosmos DB fiók lapja](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Navigáljon a **adatkezelő** panelre. Ekkor látnia kell a jegyzetfüzetek munkaterületét.

    ![Új Azure Cosmos DB notebookok munkaterülete](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Jegyzetfüzetek engedélyezése egy meglévő Cosmos-fiókban
A meglévő fiókokon is engedélyezheti a jegyzetfüzeteket. Ezt a lépést fiókkal csak egyszer kell elvégezni.

1. Navigáljon a Cosmos-fiók **adatkezelő** ablaktáblájához.
1. Válassza a **jegyzetfüzetek engedélyezése**lehetőséget.

    ![Új jegyzetfüzetek munkaterületének létrehozása Adatkezelő](media/enable-notebooks/enable-notebooks-workspace.png)
1. Ez a művelet megkéri, hogy hozzon létre egy új jegyzetfüzet-munkaterületet. Válassza a **telepítés befejezése lehetőséget.**
1. A fiókja már engedélyezve van a jegyzetfüzetek használatához.

## <a name="create-and-run-your-first-notebook"></a>Az első jegyzetfüzet létrehozása és futtatása

Annak ellenőrzéséhez, hogy használhatók-e jegyzetfüzetek, válassza ki az egyik jegyzetfüzetet a minta jegyzetfüzetek területen. Ezzel a művelettel a jegyzetfüzet egy másolatát menti a munkaterületre, és megnyithatja.

Ebben a példában a **bemutatása GettingStarted. ipynb**-t fogjuk használni. 

![Bemutatása GettingStarted. ipynb jegyzetfüzet megtekintése](media/enable-notebooks/select-getting-started-notebook.png)

A jegyzetfüzet futtatása:
1. Válassza ki a Python-kódot tartalmazó első kódlapot. 
1. Válassza a **Futtatás** lehetőséget a cella futtatásához. A cella futtatásához a **SHIFT + ENTER** billentyűkombinációt is használhatja.
1. Frissítse az erőforrás-ablaktáblát, és tekintse meg a létrehozott adatbázist és tárolót.

    ![Az első lépések jegyzetfüzet futtatása](media/enable-notebooks/run-first-notebook-cell.png)

Az **új jegyzetfüzet** lehetőség kiválasztásával új jegyzetfüzetet hozhat létre, vagy feltöltheti a meglévő jegyzetfüzeteket (. ipynb) a **saját jegyzetfüzetek** menüjének **fájl feltöltése** parancsával. 

![Új jegyzetfüzet létrehozása vagy feltöltése](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [Azure Cosmos db Jupyter notebookok](cosmosdb-jupyter-notebooks.md) előnyeit
