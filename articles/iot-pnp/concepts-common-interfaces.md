---
title: Általános felületek – IoT Plug and Play előzetes verzió | Microsoft Docs
description: A IoT Plug and Play-fejlesztőknek készült általános interfészek leírása
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f697a0d6aba4f137b75faa2a200424c72aa78c3b
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531411"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT Plug and Play előzetes verzió általános felületek

Minden IoT-Plug and Play eszköznek várhatóan végre kell hajtania néhány gyakori felületet. A közös interfészek előnyben IoT a megoldásokat, mivel konzisztens funkciókat biztosítanak. A [minősítés](tutorial-build-device-certification.md) megköveteli, hogy az eszköz több közös felületet implementáljon. A Common Interface-definíciókat a nyilvános modell tárházból kérheti le.

## <a name="summary-of-common-interfaces"></a>Általános felületek összefoglalása

| Név | ID (Azonosító) | Leírás | Az Azure IoT SDK implementálja | Be kell jelenteni a képesség modellben |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Modell adatai | urn: azureiot: ModelDiscovery: ModelInformation: 1 | Az eszközök számára, hogy deklarálják a képesség modell AZONOSÍTÓját és felületeit. Minden IoT Plug and Play eszközhöz szükséges. | Igen | Nem |
| Digitális Twin Client SDK-információk | urn: azureiot: ügyfél: SDKInformation: 1 | Az eszközt az Azure-hoz csatlakoztató ügyfél-SDK. A [minősítéshez](tutorial-build-device-certification.md) szükséges | Igen | Nem |
| Eszköz adatai | urn: azureiot: DeviceManagement: DeviceInformation: 1 | Hardver-és operációsrendszer-információk az eszközről. A [minősítéshez](tutorial-build-device-certification.md) szükséges | Nem | Igen |
| Modell definíciója | urn: azureiot: ModelDiscovery: ModelDefinition: 1 | Az eszközök számára, hogy deklarálják a képességeinek modelljét és felületét teljes definícióját. Akkor kell megvalósítani, ha a modell-definíciók nem találhatók meg a modell-tárházban. | Nem | Igen |
| Digitális Twin | urn: azureiot: ModelDiscovery: DigitalTwin: 1 | A megoldás fejlesztői számára a Digital Twin modell AZONOSÍTÓjának és illesztőfelület-azonosítóinak beolvasása. Ez az illesztőfelület nincs deklarálva vagy implementálva egy IoT Plug and Play eszközön. | Nem | Nem |

- Az Azure IoT SDK által implementálva – azt határozza meg, hogy az Azure IoT SDK implementálja-e az illesztőfelületekben deklarált képességeket. Az Azure IoT SDK-t használó eszközök Plug and Play IoT nem kell megvalósítani ezt a felületet.
- Szerepelnie kell a (z) képesség-modellben – ha az igen, ezt a felületet be kell jelenteni a IoT Plug and Play eszköz eszköz-képesség modellének `"implements":` szakaszán belül.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Illesztőfelület-definíciók beolvasása a nyilvános tárházból

### <a name="cli"></a>CLI

Az Azure IoT bővítményét használhatja az Azure CLI-hez az általános felületek a nyilvános modell tárházból való lekéréséhez.

```cmd/sh
az iot pnp interface show --interface {InterfaceID}
```

```cmd/sh
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS Code

1. A Command paletta megnyitásához használja a **CTRL + SHIFT + P** billentyűkombinációt.

1. Adja meg **Plug and Play** , majd válassza ki a **IoT Plug and Play: Nyissa meg a Model repository** parancsot. Válassza a **nyilvános tárház**lehetőséget. A nyilvános modell tárháza a VS Code-ban nyílik meg.

1. A nyilvános modell adattárában adja meg a felület nevét a keresőmezőbe.

1. Az interfész helyi másolatának létrehozásához válassza ki azt a keresési eredmények között, majd válassza a **Letöltés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az általános interfészeket, néhány további erőforrást is talál:

- [Digital Twin Definition Language (DTDL)](https://aka.ms/DTDL)
- [C eszköz-SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
