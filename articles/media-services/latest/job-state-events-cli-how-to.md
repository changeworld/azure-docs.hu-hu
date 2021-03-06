---
title: Azure Media Services események figyelése a Event Grid parancssori felület használatával | Microsoft Docs
description: Ez a cikk bemutatja, hogyan fizethet elő Event Gridre az Azure Media Services események figyelése érdekében.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 619d40ab56715b4444d8e5649c7fb3401b3f57ff
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937286"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Media Services események létrehozása és figyelése Event Grid az Azure CLI használatával

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ez a szolgáltatás [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Media Services események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Azonosíthatja Media Services eseményt, mert a eventType tulajdonság a "Microsoft. Media" karakterlánccal kezdődik. További információ: [Media Services esemény sémái](media-services-event-schemas.md).

Ebben a cikkben az Azure CLI használatával fizethet elő Azure Media Services-fiókjához tartozó eseményekre. Ezután aktiválhatja az eseményeket az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. Ebben a cikkben az eseményeket egy webalkalmazásba küldi, amely összegyűjti és megjeleníti az üzeneteket.

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- Telepítse és a parancssori Felületet helyileg használja, ez a cikk az Azure CLI 2.0-s vagy újabb verziójára van szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

    Jelenleg nem minden [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) parancsok működnek az Azure Cloud shellben. Javasoljuk, hogy a parancssori Felületet helyileg használja.

- [A Media Services-fiók létrehozása](create-account-cli-how-to.md).

    Ügyeljen arra, hogy az erőforráscsoport neveként használt értékeket jegyezze fel, és Media Services a fiók nevét.

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

Az Media Services-fiók eseményeire való feliratkozás előtt hozzon létre egy végpontot az esemény üzenethez. A végpont általában az eseményadatok alapján hajt végre műveleteket. Ebben a cikkben egy [előre elkészített webes alkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyez üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

Ha átvált a "Azure Event Grid Viewer" webhelyre, akkor azt láthatja, hogy még nincsenek események.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Az Azure-előfizetés beállítása

Az alábbi parancsban adja meg a Media Services-fiókhoz használni kívánt Azure-előfizetés azonosítóját. Az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lapot megnyitva tekintheti meg az Ön által elérhető előfizetések listáját.

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Előfizetés Media Services eseményekre

A cikkre való előfizetéssel megtudhatja, hogy Event Grid mely eseményeket kívánja nyomon követni. A következő példa előfizet a létrehozott Media Services fiókra, és átadja az URL-címet az esemény-értesítés végpontja létrehozott webhelyről. 

Cserélje le a `<event_subscription_name>` értéket az esemény-előfizetés egyedi nevére. @No__t – 0 és `<ams_account_name>` esetében használja az Media Services-fiók létrehozásakor használt értékeket. A `<endpoint_URL>` esetében adja meg a webalkalmazás URL-címét, és adja hozzá a `api/updates` értéket a Kezdőlap URL-címéhez. Ha a végpontot a feliratkozáskor megadta, Event Grid kezeli az események útválasztását az adott végpontra. 

1. Erőforrás-azonosító lekérése

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Példa:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Előfizetés az eseményekre

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Példa:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Előfordulhat, hogy az érvényesítési kézfogás figyelmeztetést kap. Adjon meg néhány percet, és a kézfogásnak érvényesíteni kell.

Most aktiváljuk az eseményeket, hogy meglássuk, hogyan osztja el a Event Grid az üzenetet a végpontnak.

## <a name="send-an-event-to-your-endpoint"></a>Esemény elküldése a végpontra

A Media Services-fiókhoz tartozó eseményeket a kódolási feladatok futtatásával aktiválhatja. [Ezt](stream-files-dotnet-quickstart.md) a rövid útmutatót követve kódolhat egy fájlt, és megkezdheti az események küldését. 

Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A végpontnak a `validationResponse` értéket kell beállítania a `validationCode` értékhez. További információkért lásd: [Event Grid biztonsági és hitelesítési](../../event-grid/security-authentication.md). Megtekintheti a webalkalmazás kódját, hogy megtudja, hogyan érvényesíti az előfizetést.

> [!TIP]
> Az eseményadatok kibontásához kattintson a szem ikonra. Ne frissítse az oldalt, ha meg szeretné tekinteni az összes eseményt.

![Előfizetési esemény megtekintése](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>További lépések

[Feltöltés, kódolás és stream](stream-files-tutorial-with-api.md)

