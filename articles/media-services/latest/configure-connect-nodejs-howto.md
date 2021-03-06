---
title: Kapcsolódás a Azure Media Services V3 API-Node. js-hez
description: Ez a cikk bemutatja, hogyan csatlakozhat a Media Services V3 API-hoz a Node. js használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896104"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Kapcsolódás a Media Services V3 API-Node. js-hez

Ez a cikk bemutatja, hogyan csatlakozhat a Azure Media Services v3 Node. js SDK-hoz az egyszerű szolgáltatás bejelentkezési metódusának használatával.

## <a name="prerequisites"></a>Előfeltételek

- A [Node.js](https://nodejs.org/en/download/) telepítése.
- [Hozzon létre egy Media Services fiókot](create-account-cli-how-to.md). Ügyeljen arra, hogy jegyezze fel az erőforráscsoport nevét és a Media Services fiók nevét.

> [!IMPORTANT]
> Tekintse át az [elnevezési konvenciókat](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Package. JSON létrehozása

1. Hozzon létre egy Package. JSON fájlt a kedvenc szerkesztő használatával.
1. Nyissa meg a fájlt, és illessze be a következő kódot:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

A következő csomagokat kell megadni:

|Csomag|Leírás|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Győződjön meg arról, hogy a legújabb Azure Media Services csomagot használja, és [telepítse az Azure-ARM-Mediaservices NPM](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Storage SDK. Fájlok eszközökre való feltöltésekor használatos.|
|`ms-rest-azure`| A bejelentkezéshez használatos.|

A következő parancs futtatásával gondoskodhat arról, hogy a legújabb csomagot használja:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Kapcsolódás Node. js-ügyfélhez

1. Hozzon létre egy. js-fájlt a kedvenc szerkesztője segítségével.
1. Nyissa meg a fájlt, és illessze be a következő kódot.
1. Állítsa be az "Endpoint config" (végpont konfigurációja) szakaszban található értékeket az [Access API](access-api-cli-how-to.md)-k által kapott értékekre.

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Az alkalmazás futtatása

Nyisson meg egy parancssort. Keresse meg a minta címtárát, és hajtsa végre a következő parancsokat:

```
npm install 
node index.js
```

## <a name="see-also"></a>Lásd még:

- [Media Services fogalmak](concepts-overview.md)
- [NPM-telepítés, azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a Media Services [Node. js ref](/javascript/api/overview/azure/mediaservices/management) -dokumentációval, és tekintse meg a Media Services API Node. js-sel való használatát bemutató [példákat](https://github.com/Azure-Samples/media-services-v3-node-tutorials) .

