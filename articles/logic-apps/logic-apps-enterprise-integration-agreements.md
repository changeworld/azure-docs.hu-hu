---
title: Kereskedelmi partneri szerződések
description: Szerződések létrehozása és kezelése a kereskedelmi partnerek között Azure Logic Apps és Enterprise Integration Pack használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790737"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Kereskedelmi partneri szerződések létrehozása és kezelése Azure Logic Apps

A [kereskedelmi partnerek](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*szerződése* révén a szervezetek és a vállalatok zökkenőmentesen kommunikálhatnak egymással azáltal, hogy meghatározzák a vállalat és a vállalat közötti (B2B) üzenetek cseréjekor használandó egyedi, iparági szabványnak megfelelő protokollt. A szerződések közös előnyöket biztosítanak, például:

* Lehetővé teheti a szervezetek számára, hogy a jól ismert formátum használatával kicseréljék az adatokat.
* Növelje a hatékonyságot a B2B-tranzakciók végrehajtásakor.
* Egyszerűen létrehozhatók, kezelhetők és használhatók vállalati integrációs megoldások létrehozásához.

Ez a cikk bemutatja, hogyan hozhat létre olyan AS2-, EDIFACT-vagy X12-szerződést, amelyet a [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) és [Azure Logic apps](../logic-apps/logic-apps-overview.md)használatával vállalati integrációs megoldások LÉTREHOZÁSához használhat a B2B-forgatókönyvekhez. A szerződések létrehozása után az AS2, a EDIFACT vagy a X12 összekötőt használhatja a B2B-üzenetek cseréjéhez.

A RosettaNet-üzenetek cseréjére vonatkozó szerződések létrehozásával kapcsolatban lásd: [Exchange RosettaNet-üzenetek](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [Integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a szerződés és más B2B-összetevők tárolásához. Ezt az integrációs fiókot hozzá kell rendelni az Azure-előfizetéséhez.

* Legalább két, az integrációs fiókban már létrehozott [kereskedelmi partner](../logic-apps/logic-apps-enterprise-integration-partners.md) . A szerződésekhez egy fogadó partner és egy vendég partner is szükséges. Mindkét partnernek ugyanazt az "üzleti identitás" minősítést kell használnia, mint a létrehozni kívánt szerződés, például AS2, X12 vagy EDIFACT.

* Nem kötelező: az a logikai alkalmazás, amelyben a szerződést és a logikai alkalmazás munkafolyamatát indító triggert szeretné használni. Az integrációs fiók és a B2B-összetevők létrehozásához nincs szükség logikai alkalmazásra. Azonban ahhoz, hogy a logikai alkalmazás használhassa a B2B-összetevőket az integrációs fiókjában, csatolnia kell az integrációs fiókot a logikai alkalmazáshoz. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)lehetőséget.

## <a name="create-agreements"></a>Szerződések létrehozása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
Az Azure fő menüjében válassza a **minden szolgáltatás**lehetőséget. A keresőmezőbe írja be szűrőként az "integráció" kifejezést. Az eredmények közül válassza ki ezt az erőforrást: **integrációs fiókok**

   ![Integrációs fiók megkeresése](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Az **integrációs fiókok**területen válassza ki azt az integrációs fiókot, amelyben létre szeretné hozni a szerződést.

   ![Válassza ki azt az integrációs fiókot, ahol létre szeretné hozni a szerződést](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. A jobb oldali ablaktábla **összetevők**területén válassza a **szerződések** csempét.

   ![Válassza a "szerződések" lehetőséget](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. A **szerződések**területen válassza a **Hozzáadás**lehetőséget. A **Hozzáadás** ablaktáblán adja meg a szerződésre vonatkozó információkat, például:

   ![Válassza a "Hozzáadás" lehetőséget.](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Tulajdonság | Szükséges | Value (Díj) | Leírás |
   |----------|----------|-------|-------------|
   | **Name (Név)** | Igen | <*Szerződés – név*> | A szerződés neve |
   | **Szerződés típusa** | Igen | **AS2**, **X12**vagy **EDIFACT** | A szerződéshez tartozó protokoll típusa. A szerződési fájl létrehozásakor a fájl tartalmának meg kell egyeznie a szerződés típusával. | |  
   | **Gazda partner** | Igen | <*gazdagép-partner-név*> | A fogadó partner a szerződést megadó szervezetet jelöli. |
   | **Gazdagép identitása** | Igen | <*gazdagép – partner-azonosító*> | A gazda partner azonosítója |
   | **Vendég partner** | Igen | <*vendég-partner-név*> | A vendég partner a gazda partnerrel üzleti tevékenységet folytató szervezetet jelöl |
   | **Vendég identitás** | Igen | <*vendég-partner-azonosító*> | A vendég partner azonosítója |
   | **Fogadási beállítások** | Változó | Változó | Ezek a tulajdonságok határozzák meg, hogy a gazdagép partnere hogyan fogadja az összes bejövő üzenetet a szerződésben szereplő vendég partnertől. További információért lásd a vonatkozó szerződés típusát: <p>- [AS2-üzenetek beállításai](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>[EDIFACT - üzenet beállításai](logic-apps-enterprise-integration-edifact.md) <br>[X12 - üzenet beállításai](logic-apps-enterprise-integration-x12.md) |
   | **Küldési beállítások** | Változó | Változó | Ezek a tulajdonságok határozzák meg, hogy a gazdagép partnere hogyan küldi el az összes kimenő üzenetet a szerződésben szereplő vendég partnernek. További információért lásd a vonatkozó szerződés típusát: <p>- [AS2-üzenetek beállításai](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>[EDIFACT - üzenet beállításai](logic-apps-enterprise-integration-edifact.md) <br>[X12 - üzenet beállításai](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Amikor elkészült a szerződés létrehozásával, az Add ( **Hozzáadás** ) lapon kattintson az **OK gombra**, és térjen vissza az integrációs fiókjához.

   A **szerződések** listája most már az új szerződést mutatja.

## <a name="edit-agreements"></a>Szerződések szerkesztése

1. A [Azure Portal](https://portal.azure.com)az Azure fő menüjében válassza a **minden szolgáltatás**lehetőséget.

1. A keresőmezőbe írja be szűrőként az "integráció" kifejezést. Az eredmények közül válassza ki ezt az erőforrást: **integrációs fiókok**

1. Az **integrációs fiókok**területen válassza ki a szerkeszteni kívánt szerződést tartalmazó integrációs fiókot.

1. A jobb oldali ablaktábla **összetevők**területén válassza a **szerződések** csempét.

1. A **szerződések**területen válassza ki a szerződést, és válassza a **Szerkesztés**lehetőséget.

1. Végezze el, majd mentse a módosításokat.

## <a name="delete-agreements"></a>Szerződések törlése

1. A [Azure Portal](https://portal.azure.com)az Azure fő menüjében válassza a **minden szolgáltatás**lehetőséget.

1. A keresőmezőbe írja be szűrőként az "integráció" kifejezést. Az eredmények közül válassza ki ezt az erőforrást: **integrációs fiókok**

1. Az **integrációs fiókok**területen válassza ki a törölni kívánt szerződést tartalmazó integrációs fiókot.

1. A jobb oldali ablaktábla **összetevők**területén válassza a **szerződések** csempét.

1. A **szerződések**területen válassza ki a szerződést, és válassza a **Törlés**lehetőséget.

1. Erősítse meg, hogy törölni kívánja a kijelölt szerződést.

## <a name="next-steps"></a>Következő lépések

* [Exchange AS2-üzenetek](logic-apps-enterprise-integration-as2.md)
* [Exchange-EDIFACT üzenetei](logic-apps-enterprise-integration-edifact.md)
* [Exchange-X12 üzenetei](logic-apps-enterprise-integration-x12.md)
