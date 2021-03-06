---
title: Azure IoT Edge modul technikai eszközeinek létrehozása | Azure piactér
description: Hozzon létre egy IoT Edge modul technikai eszközeit.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 57bc2f789836a7d3453004cdacc59029c4b24129
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827621"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>A IoT Edge modul technikai eszközeinek előkészítése

Ez a cikk azokat a követelményeket ismerteti, amelyekkel a IoT Edge modul technikai eszközeinek meg kell felelniük az Azure Marketplace-en való közzététel előtt.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>A IoT Edge moduljainak és első lépéseinek ismertetése

Az IoT Edge modul egy IoT Edge eszközön futtatható Docker-kompatibilis tároló.

- IoT Edge modulokról további információt a [Azure IoT Edge modulok ismertetése](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)című témakörben talál.
- A IoT Edge modul fejlesztésének megkezdéséhez lásd: [követelmények és eszközök IoT Edge modulok fejlesztéséhez](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Technikai követelmények

Az alábbi technikai követelmények teljesülése szükséges ahhoz, hogy a IoT Edge modult hitelesíteni lehessen, és közzé lehessen tenni az Azure piactéren.

### <a name="platform-support"></a>Platformtámogatás

A IoT Edge modulnak támogatnia kell az alábbi platform-lehetőségek egyikét.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>A IoT Edge által támogatott 1. szintű platformok

A IoT Edge által támogatott összes 1. szintű platform támogatása ( [Azure IoT Edge-támogatásban](https://docs.microsoft.com/azure/iot-edge/support)rögzített módon). Ezt a lehetőséget javasoljuk, mert jobb felhasználói élményt nyújt. Az ehhez a feltételhez tartozó modulok bemutatásra kerülnek. A platformot használó modulnak a következőket kell tennie:

- Adjon meg egy `latest` címkét és egy verziószámot (például `1.0.1`), amely a GitHub [manifest-Tool eszközzel](https://github.com/estesp/manifest-tool)létrehozott jegyzékfájlok címkéje.
- A [piactér lapon](./cpp-marketplace-tab.md) hozzáadhat egy, a [kompatibilis IoT Edge minősített eszközökre](https://aka.ms/iot-edge-certified)mutató hivatkozást. Ez a hivatkozás feloldja a `https://aka.ms/iot-edge-certified`t, egy webhelyet, ahol az ügyfelek böngészhetik vagy kereshetik meg a hitelesített eszközöket. Ezt a webhelyet [Azure IoT Edge minősített](https://catalog.azureiotsolutions.com/) eszköz katalógusának is nevezzük.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Az 1. szintű platform egy részhalmaza, amelyet a IoT Edge támogat
  
A IoT Edge által támogatott 1. rétegbeli platformok (legalább egy) részhalmazának támogatása (Azure IoT Edge- [támogatásban](https://docs.microsoft.com/azure/iot-edge/support)rögzített módon). A platformot használó modulnak a következőket kell tennie:

- Adjon meg egy `latest` címkét és egy Version (például `1.0.1`) címkét, amely a GitHub [manifest-Tool eszközzel](https://github.com/estesp/manifest-tool) létrehozott manifest-címkék, ha több platform is támogatott. A jegyzékfájl címkéi csak akkor választhatók, ha egy platform támogatott.
- A [piactér lapon](./cpp-marketplace-tab.md) adjon meg egy hivatkozást legalább egy IoT Edge eszközre a [Azure IoT Edge minősített](https://catalog.azureiotsolutions.com/) eszköz katalógusában.

### <a name="device-dimensions"></a>Eszköz méretei

IoT Edge modul méretei (CPU/RAM/Storage/GPU/etc) a megcélozt IoT Edge eszközökön a következő követelményeknek kell megfelelniük:

- A modulnak **legalább egy IoT Edge tanúsítvánnyal rendelkező eszközt kell működnie** a [Azure IoT Edge minősített](https://catalog.azureiotsolutions.com/) eszköz katalógusában.
- A **minimális hardverkövetelmények** dokumentációját az ajánlat leírásában szereplő utolsó bekezdésnek kell megadnia (a [piactér lapon](./cpp-marketplace-tab.md)). Igény szerint a javasolt hardverkövetelmények is kiválaszthatók, ha azok jelentősen eltérnek. Adja meg például a következő szakaszt az ajánlat leírásának végén:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Konfiguráció

Emellett az alapértelmezett konfigurációs beállításokat is tartalmazza, amelyekkel a központi telepítés IoT Edge eszközön a lehető legrövidebb továbbítással végezhető el. A tároló a IoT Edge modul SDK-val is rendelkezhet, amely lehetővé teszi a kommunikációt a edgeHub és a IoT Hubokkal.

#### <a name="default-configuration"></a>Alapértelmezett konfiguráció

IoT Edge moduloknak képesnek kell lenniük a [felhőalapú partner portál SKU lapján](./cpp-skus-tab.md)megadott alapértelmezett beállításokkal való indulásra. A következő alapértelmezett beállítások érhetők el:

- Alapértelmezett **útvonalak**
- Alapértelmezett **Twin kívánt tulajdonságok**
- Alapértelmezett **környezeti változók**
- Alapértelmezett **createOptions**

Olyan esetekben, amikor egy alapértelmezett értékhez egy paraméter szükséges (például az ügyfél kiszolgálójának IP-címe), az alapértelmezett értékként adja hozzá a paramétert. Ez az érték szögletes zárójelben és nagybetűvel van bezárva. Ebben a példában a következő alapértelmezett környezeti változót kell beállítania:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Konfigurációs dokumentáció

Egy IoT Edge modul összes konfigurációs beállítását egyértelműen dokumentálni kell (hogyan használhatók az útvonalak, a dupla kívánt tulajdonságok, a környezeti változók, a createOptions stb.). Adja meg a dokumentációra mutató hivatkozást, vagy a dokumentációnak az ajánlat/SKU leírása részét kell képeznie.

### <a name="tags-and-versioning"></a>Címkék és verziószámozás

Az ügyfeleknek egyszerűen üzembe kell helyezniük egy modult, és automatikusan le kell kérniük a frissítéseket a piactérről (fejlesztői forgatókönyv esetén). Emellett képesnek kell lenniük arra, hogy a tesztelt pontos verziót is használják (éles környezetben).

Az ügyfelek elvárásainak kielégítéséhez és a piactéren való közzétételéhez IoT Edge moduloknak az alábbi követelményeknek kell megfelelniük:

- Vegyen fel egy manifest `latest` címkét, amely az összes támogatott platformon a legújabb verziót mutat.
- A verziószámnak X. Y. Z formátumúnak kell lennie, ahol az X, az Y és a Z egész számok.
- Adjon meg egy "version" címkét, például `1.0.1`, amely az összes támogatott platformon egy adott verzióra mutat.
- Ne frissítse a "version" címkéket, például a `1.0.1`t, mert nem változtathatók meg.

>[!Note]
>Igény szerint a verziószámozás tartalmazhat "Rolling version" címkéket, például `2.0` és `1.0`. Ez támogatja több főverzió párhuzamos fenntartását.

### <a name="telemetry"></a>Telemetria

A IoT modul SDK-`PublisherId.OfferId.SkuId` t használó moduloknak az egyedi modul azonosítóját kell telemetria célokra beállítani. Az egyedi azonosító lehetővé teszi, hogy az Azure Marketplace azonosítsa a futtatott modul példányainak számát.

 A IoT modul SDK-k segítségével a következő módszerekkel állíthatja be a `ProductInfo` az azonosítóra:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

A IoT modul SDK-t nem használó modulok esetében a kevésbé pontos adatellenőrzések a Cloud Partner Portalon keresztül érhetők el, például a letöltések száma.

### <a name="security"></a>Biztonság

IoT Edge moduloknak a lehető legkevesebb jogosultsággal rendelkező hozzáférést kell kérniük a gazdagéphez. A [Kiemelt modulokat](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) el kell kerülni.

### <a name="module-iot-sdk"></a>Modul IoT SDK

A IoT modul SDK-val együtt nem előfeltétel a minősítés. A IoT modul SDK-val együtt azonban jobb felhasználói élményt biztosíthat. Például az Útválasztás és az üzenetek felhőbe való küldésének támogatásához.

A IoT modul SDK-nak meg kell szereznie a (telemetria) rendszerű modul példányainak számát.


## <a name="recertification-process"></a>Újraminősítési folyamat

<!-- Add legal time windows-->
A partnerek értesítést kapnak, ha a modulokat érintő megszakítási változás történik, például:

- A IoT Edge által támogatott 1. szintű operációs rendszer/Arch támogatás mátrixa
- IoT modul SDK
- IoT Edge futtatókörnyezet
- A IoT Edge modul minősítési irányelvei

A partnereknek frissíteniük kell a moduljaikat, és újra hitelesíteniük kell őket a Cloud Partner Portal eszköz használatával.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>A IoT Edge modul üzemeltetése egy Azure Container Registry

A IoT Edge modulnak a Cloud Partner Portalba való feltöltéséhez először egy [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR)-ben kell üzemeltetni. A modulnak tartalmaznia kell az összes közzétenni kívánt címkét, beleértve a jegyzékfájl által hivatkozott képcímkét is.


## <a name="next-steps"></a>További lépések

- [A IoT Edge modul ajánlatának létrehozása](./cpp-create-offer.md)
