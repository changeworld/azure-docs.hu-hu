---
title: Exchange-üzenetek Azure Service Bus
description: Automatikus feladatok és munkafolyamatok létrehozása, amelyek üzeneteket küldenek és fogadnak a Azure Logic Apps Azure Service Bus használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 1b38b8508dbe17d42bf191149410f5db638cf834
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261619"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Exchange-üzenetek a felhőben Azure Logic Apps és Azure Service Bus használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és az [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) -összekötővel automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek adatátvitelt, például értékesítési és beszerzési rendeléseket, naplókat és leltározási mozgásokat végeznek a szervezete alkalmazásaiban. Az összekötő nem csak az üzenetek figyelését, küldését és kezelését végzi, hanem a várólistákkal, munkamenetekkel, témakörökkel, előfizetésekkel és egyéb műveletekkel is végrehajtja a műveleteket, például:

* A várólisták, témakörök és témakör-előfizetések fogadásának figyelése, amikor az üzenetek érkeznek (automatikusan befejeződik) vagy érkeznek (betekintési zárolás).
* Üzenetek küldése.
* Témakör-előfizetések létrehozása és törlése.
* Kezelheti a várólisták és a témakör-előfizetések üzeneteit, például a Get, a késleltetett, a teljes, a késleltetés, a lemondás és a kézbesítetlen levelek.
* Üzenetek és munkamenetek zárolásának megújítása a várólistákban és a témakör-előfizetésekben.
* Munkamenetek lezárása a várólistákban és témakörökben.

Olyan eseményindítókat is használhat, amelyek Service Bus válaszokat kapnak, és elérhetővé teszik a kimenetet a logikai alkalmazások más műveletei számára. Más műveletek is használhatók Service Bus műveletek kimenetének használatával. Ha most ismerkedik Service Bus és Logic Apps, tekintse át a [Mi az Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) és [Mi az Azure Logic apps](../logic-apps/logic-apps-overview.md)?

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy Service Bus névtér és üzenetküldési entitás, például egy üzenetsor. Ezeknek az elemeknek és a logikai alkalmazásnak ugyanazt az Azure-előfizetést kell használnia. Ha nem rendelkezik ezekkel az elemekkel, megtudhatja, hogyan [hozhatja létre a Service Bus névteret és a várólistát](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyben a Service Bus névteret és az üzenetküldési entitást használja. A logikai alkalmazásnak és a Service Bus-nek ugyanazt az Azure-előfizetést kell használnia. Ha Service Bus triggerrel szeretné elindítani a munkafolyamatot, [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha Service Bus műveletet szeretne használni a munkafolyamatban, indítsa el a logikai alkalmazást egy másik eseményindítóval, például az [ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Engedélyek keresése

Győződjön meg arról, hogy a logikai alkalmazás rendelkezik a Service Bus névtér eléréséhez szükséges engedélyekkel.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Lépjen a Service Bus *névtérhez*. A névtér lap **Beállítások**területén válassza a **megosztott elérési házirendek**elemet. A **jogcímek**területen győződjön meg arról, hogy rendelkezik az adott névtérhez **tartozó jogosultságokkal** .

   ![Service Bus névtér engedélyeinek kezelése](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. A Service Bus névtérhez tartozó kapcsolatok karakterláncának beolvasása. Erre a karakterláncra akkor van szükség, amikor megadja a kapcsolódási adatokat a logikai alkalmazásban.

   1. A **megosztott hozzáférési házirendek** panelen válassza a **RootManageSharedAccessKey**lehetőséget.
   
   1. Az elsődleges kapcsolódási karakterlánc mellett kattintson a Másolás gombra. Mentse a kapcsolatok karakterláncát későbbi használatra.

      ![Service Bus névtérbeli kapcsolatok karakterláncának másolása](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Annak ellenőrzéséhez, hogy a kapcsolódási karakterlánc társítva van-e a Service Bus névteréhez vagy egy üzenetküldési entitáshoz, például egy várólistához, keresse meg a `EntityPath` paraméterhez tartozó kapcsolódási karakterláncot. Ha megtalálta ezt a paramétert, a kapcsolódási karakterlánc egy adott entitásra vonatkozik, és nem a megfelelő karakterláncot használja a logikai alkalmazáshoz.

## <a name="add-service-bus-trigger"></a>Service Bus trigger hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A keresőmezőbe írja be szűrőként az "Azure Service Bus" kifejezést. Az eseményindítók listából válassza ki a kívánt eseményindítót.

   Például a logikai alkalmazás aktiválásához, amikor új elem érkezik egy Service Bus várólistára, jelölje be az **üzenet fogadása egy várólistában (automatikus Befejezés)** triggert.

   ![Service Bus trigger kiválasztása](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Az összes Service Bus eseményindító *hosszú lekérdezési* eseményindítók. Ez a Leírás azt jelenti, hogy az eseményindító indításakor az eseményindító feldolgozza az összes üzenetet, majd 30 másodpercet vár, hogy további üzenetek jelenjenek meg a várólista vagy a témakör előfizetésében. Ha 30 másodpercen belül nem jelenik meg üzenet, a rendszer kihagyja a trigger futtatását. Ellenkező esetben az trigger folytatja az üzenetek olvasását, amíg a várólista vagy a témakör-előfizetés üres. A következő eseményindító-lekérdezés az eseményindító tulajdonságaiban megadott ismétlődési intervallumon alapul.

   Egyes eseményindítók, például **Ha egy vagy több üzenet érkezik egy várólistába (automatikusan befejeződött)** , egy vagy több üzenetet adhat vissza. Ha ezek az eseményindítók tüzet adnak vissza, a rendszer az eseményindítók **maximális** száma tulajdonsága által megadott számú üzenetet adja vissza.

1. Ha az trigger első alkalommal csatlakozik a Service Bus-névtérhez, kövesse az alábbi lépéseket, amikor a Logic app Designer kéri a kapcsolódási adatok megadását.

   1. Adja meg a kapcsolatok nevét, és válassza ki a Service Bus névteret.

      ![Service Bus kapcsolat létrehozása, 1. rész](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Ha ehelyett manuálisan szeretné megadni a kapcsolódási karakterláncot, válassza a **kapcsolódási adatok manuális megadása**lehetőséget. Ha nem rendelkezik a kapcsolódási karakterlánccal, Ismerje meg, [hogyan keresheti meg a kapcsolódási karakterláncot](#permissions-connection-string).

   1. Válassza ki a Service Bus szabályzatot, majd válassza a **Létrehozás**lehetőséget.

      ![Service Bus kapcsolat létrehozása, 2. rész](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Válassza ki a kívánt üzenetküldési entitást, például egy várólistát vagy egy témakört. Ebben a példában válassza ki a Service Bus várólistát.
   
      ![Service Bus üzenetsor kiválasztása](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Adja meg a szükséges információkat a kiválasztott triggerhez. Ha más elérhető tulajdonságokat szeretne hozzáadni a művelethez, nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt tulajdonságokat.

   Ehhez a példához tartozó trigger esetében válassza ki a lekérdezési időközt és a várólista ellenőrzésének gyakoriságát.

   ![Lekérdezési időköz beállítása](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Az elérhető eseményindítókkal és tulajdonságokkal kapcsolatos további információkért tekintse meg az összekötő [hivatkozási oldalát](/connectors/servicebus/).

1. A kívánt műveletek hozzáadásával folytassa a logikai alkalmazás összeállítását.

   Hozzáadhat például egy olyan műveletet, amely e-mailt küld, amikor új üzenet érkezik. Amikor a trigger ellenőrzi a várólistát, és új üzenetet talál, a logikai alkalmazás futtatja az észlelt üzenethez kiválasztott műveleteket.

## <a name="add-service-bus-action"></a>Service Bus művelet hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Válassza ki azt a lépést, amelyben a műveletet hozzá szeretné adni, majd kattintson az **új lépés**gombra.

   Vagy a lépések közötti művelet hozzáadásához vigye a mutatót a fenti lépések között látható nyíl fölé. Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet**területen a keresőmezőbe írja be szűrőként az "Azure Service Bus" kifejezést. A műveletek listából válassza ki a kívánt műveletet. 

   Ebben a példában válassza az **üzenet küldése** műveletet.

   ![Service Bus művelet kiválasztása](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Ha a művelet első alkalommal csatlakozik a Service Bus-névtérhez, kövesse az alábbi lépéseket, amikor a Logic app Designer kéri a kapcsolati információk megadását.

   1. Adja meg a kapcsolatok nevét, és válassza ki a Service Bus névteret.

      ![Service Bus kapcsolat létrehozása, 1. rész](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Ha ehelyett manuálisan szeretné megadni a kapcsolódási karakterláncot, válassza a **kapcsolódási adatok manuális megadása**lehetőséget. Ha nem rendelkezik a kapcsolódási karakterlánccal, Ismerje meg, [hogyan keresheti meg a kapcsolódási karakterláncot](#permissions-connection-string).

   1. Válassza ki a Service Bus szabályzatot, majd válassza a **Létrehozás**lehetőséget.

      ![Service Bus kapcsolat létrehozása, 2. rész](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Válassza ki a kívánt üzenetküldési entitást, például egy várólistát vagy egy témakört. Ebben a példában válassza ki a Service Bus várólistát.

      ![Service Bus üzenetsor kiválasztása](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Adja meg a kiválasztott művelethez szükséges adatokat. Ha más elérhető tulajdonságokat szeretne hozzáadni a művelethez, nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt tulajdonságokat.

   Válassza ki például a **tartalom** és a **tartalomtípus** tulajdonságokat, hogy hozzáadja őket a művelethez. Ezután adja meg az elküldeni kívánt üzenet tartalmát.

   ![Üzenet tartalmának és részleteinek megadása](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Az elérhető műveletekkel és azok tulajdonságaival kapcsolatos további információkért tekintse meg az összekötő [hivatkozási oldalát](/connectors/servicebus/).

1. Folytassa a logikai alkalmazás kiépítése a kívánt egyéb műveletek hozzáadásával.

   Hozzáadhat például egy olyan műveletet, amely e-mailt küld, hogy erősítse meg az üzenet elküldését.

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

## <a name="connector-reference"></a>Összekötő-referencia

A Service Bus-összekötő egyszerre akár 1 500 egyedi munkamenetet is megtakaríthat a Service Bus és az összekötő gyorsítótára között. Ha a munkamenetek száma meghaladja ezt a korlátot, a rendszer eltávolítja a régi munkameneteket a gyorsítótárból. További információ: [üzenetküldési munkamenetek](../service-bus-messaging/message-sessions.md).

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos egyéb technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/servicebus/). További információ a Azure Service Bus üzenetkezelésről: [Mi az Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)?

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
