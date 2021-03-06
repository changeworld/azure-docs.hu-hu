---
title: Webes API-k & REST API-k üzembe helyezése és hívása Azure Logic Apps
description: Webes API-k üzembe helyezése és hívása & REST API-k számára a rendszerintegrációs munkafolyamatokhoz Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: d1305be54a22b1460000a357074cbb1f67123bd6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790751"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Egyéni API-k üzembe helyezése és hívása munkafolyamatokból Azure Logic Apps

Miután [létrehozta az egyéni API-kat](./logic-apps-create-api-app.md) a Logic app-munkafolyamatokban való használatra, az API-k meghívása előtt telepítenie kell az API-kat. Az API-kat [webalkalmazásként](../app-service/overview.md)is üzembe helyezheti, de érdemes lehet [API-alkalmazásokként](../app-service/app-service-web-tutorial-rest-api.md)üzembe helyeznie az API-kat, amelyek megkönnyítik a feladatok elvégzését a felhőben és a helyszínen lévő API-k létrehozásakor, üzemeltetése és felhasználása során. Nem kell módosítania az API-kat, csak telepítse a kódot egy API-alkalmazásba. Az API-kat üzemeltetheti [Azure app Serviceon](../app-service/overview.md), egy szolgáltatásként nyújtott platformon (Pásti), amely kiválóan méretezhető, egyszerű API-üzemeltetést biztosít.

Habár bármely API-t meghívhat egy logikai alkalmazásból, a legjobb megoldás érdekében vegyen fel [OpenAPI (korábban hencegő) metaadatokat](https://swagger.io/specification/) , amelyek az API műveleteit és paramétereit írják le. Ez a OpenAPI-fájl megkönnyíti az API-k integrálását, és jobban együttműködik a Logic apps szolgáltatással.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Az API üzembe helyezése webalkalmazásként vagy API-alkalmazásként

Ahhoz, hogy az egyéni API-t egy logikai alkalmazásból lehessen hívni, az API-t webalkalmazásként vagy API-alkalmazásként üzembe helyezheti Azure App Service. Azt is megteheti, hogy a OpenAPI-fájlt a Logic Apps Designer is felhasználja, beállíthatja az API-definíció tulajdonságait, és bekapcsolhatja az [ágazatközi erőforrás-megosztást (CORS)](../app-service/overview.md) a webalkalmazás vagy az API-alkalmazás számára.

1. A [Azure Portal](https://portal.azure.com)válassza ki a webalkalmazást vagy az API-alkalmazást.

2. A megnyíló alkalmazás menüben az **API**alatt válassza az **API-definíció**elemet. Állítsa be az **API-definíció helyét** a OpenAPI henceg. JSON fájljának URL-címére.

   Az URL-cím általában a következő formátumban jelenik meg: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Az egyéni API-hoz tartozó OpenAPI-fájlra mutató hivatkozás](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Az **API**alatt válassza a **CORS**lehetőséget. Állítsa be a CORS házirendet az **engedélyezett eredetek** számára a **"*"** értékre (az összes engedélyezése).

   Ez a beállítás engedélyezi a Logic app designertől érkező kéréseket.

   ![A Logic app designertől érkező kérések engedélyezése az egyéni API-nak](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

További információkért lásd: [REST API üzemeltetése a CORS-ben Azure app Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Egyéni API meghívása a Logic app-munkafolyamatokból

Miután beállította az API-definíció tulajdonságait és a CORS, az egyéni API-eseményindítók és műveletek elérhetők lesznek a logikai alkalmazás munkafolyamatában való felvételhez. 

*  Ha a OpenAPI URL-címmel rendelkező webhelyeket szeretné megtekinteni, böngészhet az előfizetési webhelyeken a Logic Apps Designerben.

*  Az elérhető műveletek és bemenetek megtekintéséhez egy OpenAPI-dokumentumra mutatva használja a [http + hencegés műveletet](../connectors/connectors-native-http-swagger.md).

*  Bármely API meghívásához, beleértve az olyan API-kat, amelyek nem rendelkeznek vagy OpenAPI-dokumentumot tesznek elérhetővé, bármikor létrehozhat egy [http-művelettel](../connectors/connectors-native-http.md)rendelkező kérelmet.

## <a name="next-steps"></a>Következő lépések

* [Egyéni összekötők áttekintése](../logic-apps/custom-connector-overview.md)