---
title: 'yRövid útmutató: Miniatűr létrehozása – REST, cURL'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API és cURL használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6cb9dadc107c6907f1ccb28a876270e577f10395
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977301"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Gyors útmutató: miniatűr létrehozása a Computer Vision REST API és a cURL használatával

Ebben a rövid útmutatóban létrehoz egy miniatűrt a rendszerképből a Computer Vision REST API használatával. Megadhatja a kívánt magasságot és szélességet, ami eltérő lehet a bemeneti kép oldalarányának arányában. A Computer Vision az intelligens levágás használatával intelligens módon azonosítja az érdeklődési területet, és az adott régió körüli koordinátákat állít elő.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell a [cURL-lel](https://curl.haxx.se/windows).
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. A [kipróbálási Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)ingyenes próbaverziós kulcsot is beszerezhet. Vagy kövesse a [Cognitive Services fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az Computer Visionra való előfizetéshez és a kulcs beszerzéséhez című témakör utasításait.

## <a name="get-thumbnail-request"></a>Miniatűrbeszerzési kérés

A [Get Thumbnail metódussal](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) létrehozhatja egy kép miniatűrjét.

A minta futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi kódot egy szerkesztőbe.
1. A `<Subscription Key>` helyére írja be az érvényes előfizetési kulcsot.
1. A `<File>` helyére írja be a miniatűr mentési útvonalát és fájlnevét.
1. Ha szükséges, változtassa meg a kérés URL-címét (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.1`) arra a címre, ahonnan beszerezte az előfizetési kulcsot.
1. Az elemezni kívánt képet (`{\"url\":\"...`) is módosíthatja.
1. Nyisson meg egy parancssorablakot egy számítógépen, amelyen a cURL telepítve van.
1. Másolja be a kódot, és futtassa a parancsot.

>[!NOTE]
>A REST-hívásban ugyanazt a helyet kell használnia, ahonnan az előfizetési kulcsot beszerezte. Ha például a westus régióból szerezte be az előfizetési kulcsot, cserélje le az alábbi URL-címben látható „westcentralus” elemet a „westus” elemre.

## <a name="create-and-run-the-sample-command"></a>A mintaparancs létrehozása és futtatása

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi parancsot egy szövegszerkesztőbe.
1. Hajtsa végre a következő módosításokat a parancs megfelelő területein:
    1. Cserélje le a `<subscriptionKey>` értéket az előfizetői azonosítóra.
    1. Cserélj le az `<thumbnailFile>` értékét azon fájl útvonalára és nevére, amelyben menteni szeretné a miniatűrt.
    1. Cserélje le a kérelem URL-címének (`westcentralus`) első részét a saját végpont URL-címében szereplő szövegre.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Igény szerint cserélje a kép URL-címét a kérelem törzsében (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) egy másik olyan kép URL-címére, amelyből miniatűrt szeretne létrehozni.
1. Nyisson meg egy parancssort.
1. Illessze be a szövegszerkesztőből a parancsot, majd futtassa.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>A válasz vizsgálata

A sikeres válasz a(z) `<thumbnailFile>` helyen megszabott fájlba írja a miniatűrt. Ha a kérés meghiúsul, a válasz tartalmaz egy hibakódot és egy üzenetet, amely segít meghatározni a hiba okát. Ha a kérelem sikeresnek tűnik, de a létrehozott miniatűr nem érvényes képfájl, előfordulhat, hogy az előfizetési kulcs érvénytelen.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a Computer Vision APIával, hogyan elemezheti a rendszerképeket, azonosíthatja a hírességeket és tereptárgyait, miniatűrt hozhat létre, és kinyerheti a nyomtatott és a kézzel írott A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tekintse át a Computer Vision API-t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
