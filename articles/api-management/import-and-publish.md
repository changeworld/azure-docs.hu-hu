---
title: Az első API importálása és közzététele az Azure-ban API Management
description: Megtudhatja, hogyan importálhat egy OpenAPI-specifikációs API-t az Azure API Managementba, és hogyan tesztelheti az API-t a Azure Portal
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: apimpm
ms.openlocfilehash: 886063dcf886d79ac960814f20b3789e8e3b6839
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163492"
---
# <a name="import-and-publish-your-first-api"></a>Az első API importálása és közzététele 

Ez az oktatóanyag bemutatja, hogyan importálhat egy OpenAPI-specifikációs háttérbeli API-t JSON formátumban az Azure API Managementba. A Microsoft biztosítja a háttér-API-t, és az Azure-ban üzemelteti az [https://conferenceapi.azurewebsites.net?format=jsonon ](https://conferenceapi.azurewebsites.net?format=json).

Miután a háttér-API-t API Managementba importálta, a API Management API a háttérrendszer-API homlokzata lesz. API Management igényeinek megfelelően testre szabhatja a homlokzatot a háttér-API érintése nélkül. További információ: [Az API átalakítása és védelme](transform-api.md). 

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * API importálása API Managementba
> * Az API tesztelése az Azure Portalon

![Új API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure API Management terminológiájának](api-management-terminology.md)ismertetése.
- [Hozzon létre egy Azure API Management-példányt](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Háttérbeli API importálása és közzététele

Ez a szakasz bemutatja, hogyan importálhat és tehet közzé egy OpenAPI-specifikációs háttér-API-t.
 
1. A API Management példány bal oldali navigációs sávján válassza az **API** -k lehetőséget a **API Management** szakaszban.
1. Válassza a **OpenAPI** csempét, majd válassza az előugró képernyő **teljes** elemét.
1. Az API létrehozásához a **Létrehozás a OpenAPI-specifikáció** alapján képernyőn használja az alábbi táblázat értékeit.
   
   Az űrlap mezője melletti piros csillag azt jelzi, hogy a mező megadása kötelező. Az API-értékeket a létrehozás során vagy később is beállíthatja a **Beállítások** lapon. 
   
   ![API létrehozása](./media/api-management-import-and-publish/create-api.png)
   
   |Beállítás|Érték|Leírás|
   |-------|-----|-----------|
   |**OpenAPI-specifikáció**|*https:\//conferenceapi.azurewebsites.net? Format = JSON*|Az API-t végrehajtó szolgáltatás. Az API Management erre a címre továbbítja a kérelmeket.|
   |**Megjelenített név**|Az előző szolgáltatás URL-címének megadása után API Management kitölti ezt a mezőt a JSON alapján.|A fejlesztői portálon megjelenő név.|
   |**Name (Név)**|Az előző szolgáltatás URL-címének megadása után API Management kitölti ezt a mezőt a JSON alapján.|Az API egyedi neve.|
   |**Leírás**|Az előző szolgáltatás URL-címének megadása után API Management kitölti ezt a mezőt a JSON alapján.|Az API opcionális leírása.|
   |**URL-séma**|**HTTPS**|Mely protokollok használhatók az API eléréséhez.|
   |**API URL-címének utótagja**|*conference*|Az API Management szolgáltatás alapszintű URL-címéhez hozzáfűzött utótag. API Management megkülönbözteti az API-kat az utótag alapján, így az utótagnak egyedinek kell lennie az adott közzétevő minden API-jának.|
   |**Termékek**|**Korlátlan**|Egy vagy több API társítása. Minden API Management példány két minta termékkel rendelkezik: **kezdő** és **korlátlan**. Egy API-t úgy tehet közzé, hogy társítja az API-t egy termékkel, amely ebben a példában **korlátlan** .<br/>Több API-t is felvehet egy termékbe, és a fejlesztői portálon keresztül biztosíthatja őket a fejlesztőknek. Ha ezt az API-t egy másik termékhez szeretné adni, írja be vagy válassza ki a termék nevét. Ismételje meg ezt a lépést az API több termékhez való hozzáadásához. A **Beállítások** lapon később is hozzáadhat API-kat a termékekhez.<br/>Ahhoz, hogy a fejlesztők hozzáférhessenek az API-hoz, elő kell fizetniük a termékre. Amikor előfizetnek, egy olyan előfizetési kulcsot kapnak, amely megfelelő az adott termékben található API-hoz. <br/>Ha létrehozta a API Management példányt, akkor már rendszergazda, ezért előfizetett a példány minden termékére.|
   |**Címkék**| |Címkék az API-k kereséshez, csoportosításhoz vagy szűréshez való rendszerezéséhez.|
   |**Az API verziója?**|Kijelölés vagy kijelölés kiválasztása|További információ a verziószámozásról: [az API több verziójának közzététele](api-management-get-started-publish-versions.md).|
   
   > [!NOTE]
   > Az API közzétételéhez társítania kell azt egy termékkel. Ezt a **Beállítások** lapon teheti meg.
   
1. Kattintson a **Létrehozás** gombra.

Ha problémája van az API-definíciók importálásával, tekintse meg az [ismert problémák és korlátozások listáját](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Az új API tesztelése a Azure Portal

Az API-műveleteket közvetlenül a Azure Portal hívhatja, amely kényelmes módot biztosít a műveletek megtekintésére és tesztelésére.

1. A API Management példány bal oldali navigációs sávján válassza az **API** -k lehetőséget a **API Management** szakaszban, majd válassza a **bemutató konferencia API**elemet.
1. Válassza a **teszt** fület, majd válassza a **GetSpeakers**lehetőséget. A lapon láthatók a **lekérdezési paraméterek** és a **fejlécek**, ha vannak ilyenek. Az **OCP-APIM-Subscription-Key** automatikusan ki van töltve az ehhez az API-hoz társított előfizetési kulcshoz.
1. Kattintson a **Küldés** gombra.
   
   ![API-leképezés tesztelése](./media/api-management-import-and-publish/01-import-first-api-01.png)
   
   A háttérrendszer a **200 OK** üzenetet és néhány adatot küld válaszként.

## <a name="next-steps"> </a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az első API importálása
> * Az API tesztelése az Azure Portalon

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre és tehet közzé egy terméket:

> [!div class="nextstepaction"]
> [Termékek létrehozása és közzététele](api-management-howto-add-products.md)
