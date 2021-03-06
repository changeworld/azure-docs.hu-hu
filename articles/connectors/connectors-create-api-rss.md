---
title: Kapcsolódás RSS-hírcsatornához Azure Logic Apps
description: Automatizálja az RSS-hírcsatornákat Azure Logic Apps használatával figyelő és kezelő feladatokat és munkafolyamatokat
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 3f1e092c2ff325cdcbc32c617af316d6fbe6dd74
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789341"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>RSS-hírcsatornák kezelése Azure Logic Apps használatával

A Azure Logic Apps és az RSS-összekötővel automatizált feladatokat és munkafolyamatokat hozhat létre az RSS-hírcsatornák számára, például:

* Figyelje az RSS-hírcsatornák közzétételekor.
* Az RSS-hírcsatornák összes elemének listázása.

Az RSS (Rich site Summary), más néven a Really Simple Syndication, népszerű formátum a webes hírszolgáltatáshoz, és gyakran frissített tartalmak, például blogbejegyzések és hírek közzétételére szolgál. Számos tartalomszolgáltató biztosít RSS-hírcsatornát, így a felhasználók előfizethetnek erre a tartalomra. 

Olyan RSS-triggert használhat, amely az RSS-hírcsatornából választ kap, és a kimenetet más műveletek számára is elérhetővé teszi. A logikai alkalmazásokban RSS-művelettel feladatokat végezhet el az RSS-hírcsatornával. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Az RSS-hírcsatorna URL-címe

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni az RSS-hírcsatornát. Ha RSS-triggert szeretne kezdeni, [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). RSS-művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="connect-to-an-rss-feed"></a>Kapcsolódás RSS-hírcsatornához

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Válasszon egy elérési utat: 

   * Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként az "RSS" kifejezést. Válassza ki a kívánt eseményindítót az eseményindítók listából. 

     – vagy –

   * A meglévő Logic apps esetében azon a lépésben, amelyben hozzá szeretne adni egy műveletet, válassza az **új lépés**lehetőséget. A keresőmezőbe írja be szűrőként az „rss” kifejezést. A műveletek listában válassza ki a kívánt műveletet.

1. Adja meg a kiválasztott trigger vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/rss/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése