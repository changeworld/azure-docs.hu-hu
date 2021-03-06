---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964113"
---
## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A Visual Studio Code integrálva van [Azure functions Core Tools](../articles/azure-functions/functions-run-local.md) , hogy lehetővé tegye a projekt futtatását a helyi fejlesztési számítógépen, mielőtt közzéteszi az Azure-ban.

1. A függvény meghívásához nyomja le az F5 billentyűt a Function app projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

1. Ha még nem telepítette Azure Functions Core Tools, válassza a **telepítés** lehetőséget a parancssorban. A központi eszközök telepítésekor az alkalmazás elindul a **terminál** panelen. Megtekintheti a helyileg futtatott HTTP-triggerű függvény URL-végpontját. 

    ![Az Azure helyi kimenete](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. A-t futtató alapvető eszközökkel navigáljon a következő URL-címre egy GET kérelem végrehajtásához, amely `?name=Functions` lekérdezési karakterláncot tartalmaz.

    <http://localhost:7071/api/HttpExample?name=Functions>

1. A rendszer egy választ ad vissza, amely a következőhöz hasonlóan néz ki egy böngészőben:

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. A kérésre vonatkozó információk a **terminál** panelen jelennek meg.

    ![Függvény végrehajtása a terminál panelen](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Nyomja le a CTRL + C billentyűkombinációt az alapvető eszközök leállításához és a hibakereső leválasztásához.
