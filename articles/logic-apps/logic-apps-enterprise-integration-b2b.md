---
title: Exchange-üzenetek B2B vállalati integrációs forgatókönyvekhez
description: VÁLLALATKÖZI üzenetek fogadása és küldése Azure Logic Apps a kereskedelmi partnerek között a Enterprise Integration Pack használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 01b2bd464db51e255930fe83a3f4321687322275
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77151206"
---
# <a name="receive-and-send-b2b-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>B2B-üzenetek fogadása és küldése Azure Logic Apps és Enterprise Integration Pack használatával

Ha olyan integrációs fiókkal rendelkezik, amely kereskedelmi partnereket és megállapodásokat határoz meg, létrehozhat egy olyan automatizált üzleti üzleti (B2B) munkafolyamatot, amely a kereskedelmi partnerek közötti üzeneteket az [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)használatával [Azure Logic apps](../logic-apps/logic-apps-overview.md) segítségével cseréli le. A Azure Logic Apps az AS2, a X12, a EDIFACT és a RosettaNet iparági szabványnak megfelelő protokollokat támogató összekötőket is működik. Ezeket az összekötőket Logic Apps, például a Salesforce és az Office 365 Outlookhoz [elérhető más összekötők](../connectors/apis-list.md)segítségével is kombinálhatja.

Ez a cikk bemutatja, hogyan hozhat létre olyan logikai alkalmazást, amely egy kérelem-trigger használatával HTTP-kérést kap, dekódolja az üzenet tartalmát az AS2 és a X12 műveletekkel, majd a válasz használatával visszaadja a választ.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Üres logikai alkalmazás, amellyel a B2B-munkafolyamatot létrehozhatja az alábbi műveletek által követett [kérelem](../connectors/connectors-native-reqres.md) -trigger használatával:

  * [AS2-dekódolás](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Feltétel](../logic-apps/logic-apps-control-flow-conditional-statement.md), amely a válasz alapján küldi el a [választ](../connectors/connectors-native-reqres.md) , hogy az AS2-dekódolási művelet sikeres vagy sikertelen-e

  * [X12-üzenet dekódolása](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: hozza létre az első logikai alkalmazását](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-accounts.md) , amely társítva van az Azure-előfizetéséhez, és kapcsolódik a logikai alkalmazáshoz. A logikai alkalmazásnak és az integrációs fióknak ugyanazon a helyen vagy az Azure-régióban kell lennie.

* Legalább két olyan [kereskedelmi partner](../logic-apps/logic-apps-enterprise-integration-partners.md) , amelyet már definiált az integrációs fiókjában, valamint az [AS2-és X12-szerződéseket](logic-apps-enterprise-integration-agreements.md) az adott partnereknek.

## <a name="add-request-trigger"></a>Kérelem-trigger hozzáadása

Ez a példa a Logic app designert használja a Azure Portalban, de a Visual Studióban a Logic app Designer hasonló lépésein is követheti.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A keresőmezőbe írja be a `when a http request`kifejezést, majd válassza ki, hogy mikor kapja meg a rendszer a **http-kérést** triggerként való használatra.

   ![A logikai alkalmazás munkafolyamatának elindításához válassza a kérelem elindítása lehetőséget](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Hagyja üresen a **kérelem törzse JSON-séma** mezőt, mert az X12 üzenet egy sima fájl.

   ![Hagyja üresen a "kérelem törzse JSON-séma" kifejezést.](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés**lehetőséget.

   Ez a lépés a logikai alkalmazást indító kérelem küldéséhez használandó **http post URL-címet** hozza létre. Az URL-cím másolásához válassza a másolás ikont az URL mellett.

   ![A kérések triggeréhez generált URL-cím a hívások fogadásához](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>AS2-dekódolási művelet hozzáadása

Most adja hozzá a használni kívánt B2B-műveleteket. Ez a példa AS2-és X12-műveleteket használ.

1. Az trigger alatt válassza az **új lépés**lehetőséget. Az trigger részleteinek elrejtéséhez kattintson az trigger címsorára.

   ![Újabb lépés hozzáadása a logikai alkalmazás munkafolyamatához](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. A **válasszon műveletet**területen a keresőmezőbe írja be `as2 decode`, majd válassza az **AS2 Decode (v2)** elemet.

   ![Keresse meg és válassza ki az "AS2 Decode (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Ahhoz, hogy az **üzenet dekódolja** a tulajdonságot, adja meg azt a bemenetet, amelyet az AS2-művelet dekódolni kíván, ami a http-kérési trigger által fogadott `body` tartalom. Ezt a tartalmat több módon is megadhatja bemenetként a dinamikus tartalmak listájából vagy kifejezésként:

   * Ha ki szeretne választani egy olyan listából, amely megjeleníti az elérhető trigger kimeneteit, kattintson az **üzenetben a Decode (dekódolás** ) elemre. Miután megjelenik a dinamikus tartalom lista, a **http-kérelem fogadása**alatt válassza ki a **törzs** tulajdonság értékét, például:

     ![Válassza ki a "Body" értéket az triggerből](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Ha olyan kifejezést szeretne megadni, amely az trigger `body` kimenetére hivatkozik, kattintson az **üzenetben a Decode (dekódolás** ) mezőre. A dinamikus tartalom lista megjelenése után válassza a **kifejezés**lehetőséget. Adja meg a kifejezést a kifejezés-szerkesztőben, majd kattintson **az OK gombra**:

     `triggerOutputs()['body']`

     Vagy a **dekódolni kívánt üzenet** mezőbe írja be a következő kifejezést:

     `@triggerBody()`

     A kifejezés feloldja a **törzs** tokenjét.

     ![Megoldott szövegtörzs kimenete triggerből](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. A **Message headers** tulajdonságnál adja meg az AS2 művelethez szükséges fejléceket, amelyeket a http-kérési trigger által fogadott `headers` tartalom ismertet.

   Ha olyan kifejezést szeretne megadni, amely az trigger `headers` kimenetére hivatkozik, kattintson az **üzenetek fejléce** mezőre. A dinamikus tartalom lista megjelenése után válassza a **kifejezés**lehetőséget. Adja meg a kifejezést a kifejezés-szerkesztőben, majd kattintson **az OK gombra**:

   `triggerOutputs()['Headers']`

   Ha ezt a kifejezést szeretné lekérni a jogkivonat feloldásához, váltson át a tervező és a kód nézet között, például:

   ![Megoldott fejlécek kimenete triggerből](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Üzenet-visszaigazolási értesítésre vonatkozó válasz hozzáadása

Ha értesíteni szeretné a kereskedelmi partnert arról, hogy az üzenet érkezett, visszaállíthat egy, az AS2-üzenetek törlésére vonatkozó értesítést (MDN) tartalmazó választ a **Válasz** művelet használatával. Ha ezt a műveletet azonnal hozzáadja az **AS2 dekódolása** művelet után, akkor a művelet meghiúsul, a logikai alkalmazás nem folytatja a feldolgozást.

1. Az **AS2-dekódolási** művelet alatt válassza az **új lépés**lehetőséget.

1. A **válasszon műveletet**területen a keresőmező alatt válassza a **beépített**lehetőséget. A keresőmezőbe írja be a `condition` kifejezést. A **műveletek** listából válassza a **feltétel**elemet.

   ![A "feltétel" művelet hozzáadása](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Ekkor megjelenik a feltétel alakzat, beleértve az elérési utakat, hogy a feltétel teljesül-e.

   ![Feltétel alakzat döntési elérési utakkal](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Most határozza meg az értékelendő feltételt. A **Válasszon értéket** mezőben adja meg a következő kifejezést:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   A középső mezőben ellenőrizze, hogy az összehasonlítási művelet `is equal to`értékre van-e állítva. A jobb oldali mezőbe írja be a `Expected`értéket. Ha meg szeretné szerezni a jogkivonatként feloldható kifejezést, váltson át a tervező és a kód nézet között.

   ![Feltétel alakzat döntési elérési utakkal](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Mostantól megadhatja, hogy az **AS2-dekódolási** művelet sikeres volt-e, vagy sem.

   1. Abban az esetben, ha az **AS2-dekódolási** művelet sikeres, a **Ha igaz** alakzatnál válassza a **művelet hozzáadása**lehetőséget. A **válasszon műveletet**területen a keresőmezőbe írja be a `response`kifejezést, majd válassza a **Válasz**lehetőséget.

      ![A "válasz" művelet megkeresése és kiválasztása](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. A következő kifejezések megadásával érheti el az AS2-MDN az **AS2 Decode** művelet kimenetében:

      * A **Response** művelet **fejlécek** tulajdonságában adja meg a következő kifejezést:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * A **Response** művelet **törzs** tulajdonságában adja meg a következő kifejezést:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. A jogkivonatként feloldható kifejezések lekéréséhez váltson a tervező és a kód nézet között:

      ![Megoldott kifejezés az AS2 MDN eléréséhez](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. Abban az esetben, ha az **AS2-dekódolási** művelet meghiúsul, a **Ha hamis** alakzatnál válassza a **művelet hozzáadása**lehetőséget. A **válasszon műveletet**területen a keresőmezőbe írja be a `response`kifejezést, majd válassza a **Válasz**lehetőséget. Állítsa be a **Válasz** műveletét az állapot és a kívánt hiba visszaadásához.

1. Mentse a logikai alkalmazást.

## <a name="add-decode-x12-message-action"></a>X12-üzenet dekódolása művelet hozzáadása

1. Most adja hozzá a **dekódolási X12-üzenet** műveletet. A **Válasz** művelet alatt válassza a **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet**területen a keresőmezőbe írja be a `x12 decode`kifejezést, majd válassza a **dekódolás X12 üzenetet**.

   ![A "X12-üzenet dekódolása" művelet megkeresése és kiválasztása](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Ha a X12 művelet a kapcsolati adatok megadását kéri, adja meg a kapcsolat nevét, válassza ki a használni kívánt integrációs fiókot, majd válassza a **Létrehozás**lehetőséget.

   ![X12-kapcsolatok létrehozása az integrációs fiókhoz](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Most adja meg a X12 művelet bemenetét. Ez a példa az AS2 művelet kimenetét használja, amely az üzenet tartalma, de vegye figyelembe, hogy ez a tartalom JSON-objektum formátumú, és Base64 kódolású. Ezért át kell alakítania ezt a tartalmat egy karakterlánccá.

   Az **X12 lapos fájljának dekódolása** mezőbe írja be ezt a kifejezést az AS2-kimenet átalakításához:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Ha meg szeretné szerezni a jogkivonatként feloldható kifejezést, váltson át a tervező és a kód nézet között.

    ![Base64 kódolású tartalom konvertálása karakterlánccá](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Mentse a logikai alkalmazást.

   Ha további lépések szükségesek ehhez a logikai alkalmazáshoz, például az üzenet tartalmának dekódolásához és a tartalom JSON-objektum formátumú kimenetének megjelenítéséhez, folytassa a logikai alkalmazás összeállításával.

Ezzel befejezte a B2B logikai alkalmazás beállítását. Egy valós alkalmazásban érdemes lehet egy üzletági (LOB) alkalmazásban vagy adattárban tárolni a dekódolású X12-adatmennyiséget. Például tekintse meg a következő cikkeket:

* [Csatlakozás SAP-rendszerekhez az Azure Logic Appsből](../logic-apps/logic-apps-using-sap-connector.md)
* [SFTP-fájlok figyelése, létrehozása és kezelése SSH és Azure Logic Apps használatával](../connectors/connectors-sftp-ssh.md)

Saját LOB-alkalmazások összekapcsolásához és az API-k a logikai alkalmazásban való használatához további műveleteket adhat hozzá, vagy [Egyéni API-kat írhat](../logic-apps/logic-apps-create-api-app.md).

## <a name="next-steps"></a>Következő lépések

* [Bejövő HTTPS-hívások fogadása és megválaszolása](../connectors/connectors-native-reqres.md)
* [Exchange AS2-üzenetek a B2B vállalati integrációhoz](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Exchange X12-üzenetek a B2B vállalati integrációhoz](../logic-apps/logic-apps-enterprise-integration-x12.md)
* További információ a [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)