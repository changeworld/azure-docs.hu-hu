---
title: Videofájlok továbbítása Azure Media Services-Node. js-vel | Microsoft Docs
description: Az oktatóanyag lépéseit követve hozzon létre egy új Azure Media Services fiókot, kódoljon egy fájlt, és továbbítsa a Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: fa9fbf3bac55ca0b26c3644b7f6818fa96088612
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639393"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Oktatóanyag: Távoli fájl kódolása URL-cím alapján és stream a video-Node. js

Ebből az oktatóanyagból megtudhatja, hogy milyen egyszerűen kódolhatja és indíthatja el a streaming-videókat számos különböző böngészőn és eszközön a Azure Media Services használatával. A bemenő tartalmak HTTPS- URL- és SAS URL-címekkel vagy az Azure Blob Storage-ban található fájlok elérési útjával határozhatók meg.

A cikkben szereplő minta egy HTTPS URL-címen keresztül elérhetővé teszi a tartalmat. Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.

Az oktatóanyag végére egy videót is továbbíthat.  

![Videó lejátszása](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Node.js](https://nodejs.org/en/download/) telepítése
- [A Media Services-fiók létrehozása](create-account-cli-how-to.md).<br/>Ügyeljen arra, hogy az erőforráscsoport neveként használt értékeket jegyezze fel, és Media Services a fiók nevét.
- Kövesse a [Azure Media Services API-nak az Azure CLI-vel való elérésének](access-api-cli-how-to.md) lépéseit, és mentse a hitelesítő adatokat. Ezeket az API-k eléréséhez kell használnia.

## <a name="download-and-configure-the-sample"></a>A minta letöltése és konfigurálása

Egy GitHub-tárház klónozása, amely tartalmazza a streaming Node. js mintát a gépre a következő parancs használatával:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

A minta a [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) mappában található.

Nyissa meg az [index. js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) fájlt a letöltött projektben. Cserélje le `endpoint config` az értékeket az [API](access-api-cli-how-to.md)-k eléréséhez kapott hitelesítő adatokkal.

A minta a következő műveleteket hajtja végre:

1. Létrehoz egy **átalakítót** (először ellenőrzi, hogy létezik-e a megadott átalakító). 
2. Létrehozza a kódolási **feladatok**kimenetéhez használt kimeneti objektumot.
3. Egy HTTPSURL-cím alapján hozza létre a feladathoz tartozó bemenetet.
4. Elküldi a kódolási **feladatot** a korábban létrehozott bemenet és kimenet használatával.
5. Ellenőrzi a feladat állapotát.
6. Adatfolyam- **keresőt**hoz létre.
7. Streamelési URL-címeket épít fel.

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

1. Az alkalmazás letölti a kódolt fájlokat. Hozzon létre egy mappát, ahol a kimeneti fájlok el szeretnének menni, és frissítse a **outputFolder** változó értékét az [index. js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) fájlban.
1. Nyissonmeg egy parancssort, keresse meg a minta címtárát, és hajtsa végre a következő parancsokat.

    ```
    npm install 
    node index.js
    ```

A futásának befejezése után a következőhöz hasonló kimenetnek kell megjelennie:

![Futtassa a következőt:](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Tesztelés az Azure Media Player használatával

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez. 

> [!NOTE]
> Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

1. Nyisson meg egy webböngészőt, majd navigáljon a következő helyre: [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Az **URL** mezőbe illessze be az alkalmazás futtatásakor kapott streamelési URL-értékek egyikét. 
 
     Az URL-címet HLS, Dash vagy Smooth formátumban is beillesztheti, és a Azure Media Player a megfelelő folyamatos átviteli protokollra vált az eszközön való automatikus lejátszás érdekében.
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoport egyik erőforrására sem, beleértve az oktatóanyaghoz létrehozott Media Services-és Storage-fiókokat, törölje az erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Lásd még

[Feladattípus](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)-hibakódok.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Media Services fogalmak](concepts-overview.md)
