---
title: Tároló konfigurálása az űrlap-felismerőhöz
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan konfigurálhatja az űrlap-felismerő tárolót az űrlap-és a tábla-adatelemzéshez.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 5439ec0c0aab5b8c127b651147e4b25d27c58390
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379623"
---
# <a name="configure-form-recognizer-containers"></a>Űrlap-felismerő tárolók konfigurálása

Az Azure űrlap-felismerő tárolók használatával olyan alkalmazás-architektúrát építhet ki, amely a robusztus Felhőbeli képességek és a peremhálózat környékének kihasználására van optimalizálva.

Az űrlap-felismerő tároló futásidejű környezetét az `docker run` parancs argumentumok használatával konfigurálhatja. Ehhez a tárolóhoz több szükséges beállítás és néhány választható beállítás is tartozik. Néhány példa a ["Docker Run commands"](#example-docker-run-commands) című szakaszban található. A tárolóra jellemző beállítások a számlázási beállítások.

> [!IMPORTANT]
> Az űrlap-felismerő tárolók jelenleg az űrlap-felismerő API 1,0-es verzióját használják. Ehelyett a felügyelt szolgáltatással érheti el az API legújabb verzióját.

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)és [`Eula`](#eula-setting) beállításokat együtt használja a rendszer. Mindhárom beállításhoz érvényes értékeket kell megadnia; Ellenkező esetben a tároló nem indul el. A tárolók létrehozásához szükséges konfigurációs beállításokkal kapcsolatos további információkért lásd: [számlázás](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítás

A `ApiKey` beállítás megadja a tároló számlázási adatainak nyomon követéséhez használt Azure-erőforrás kulcsát. A ApiKey értékének érvényes kulcsnak kell lennie a "számlázási konfigurációs beállítás" szakaszban `Billing` számára megadott _űrlap-felismerő_ erőforráshoz.

Ez a beállítás a Azure Portalban, a **kulcsok**területen, az **űrlap-felismerő erőforrás-kezelés**szakaszban található.

## <a name="applicationinsights-setting"></a>ApplicationInsights-beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg az Azure-beli _űrlap-felismerő_ erőforrás végpont-URI-ját, amely a tároló számlázási adatainak mérésére szolgál. A konfigurációs beállítás értékének érvényes végponti URI-nak kell lennie az Azure-beli _űrlap-felismerő_ erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a Azure Portalban, az űrlap- **felismerő áttekintés**területén, a **végpont**területen található.

|Szükséges| Név | Data type | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Sztring | Számlázási végpont URI-ja. A számlázási URI beszerzésével kapcsolatos további információkért lásd: a [szükséges paraméterek összegyűjtése](form-recognizer-container-howto.md#gathering-required-parameters). További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Végfelhasználói licencszerződés beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Csatlakoztatási beállítások

A tárolóban lévő és onnan érkező adatok olvasására és írására a kötési csatlakoztatások használhatók. Megadhat egy bemeneti csatlakoztatást vagy egy kimeneti csatlakoztatást a [`docker run` parancs](https://docs.docker.com/engine/reference/commandline/run/)`--mount` lehetőségének megadásával.

Az űrlap-felismerő tárolóhoz bemeneti csatlakoztatás és kimeneti csatlakoztatás szükséges. A bemeneti csatlakoztatás csak olvasható lehet, és szükséges a betanításhoz és a pontozáshoz használt adatokhoz való hozzáféréshez. A kimeneti csatlakoztatásnak írhatónak kell lennie, és a modelleket és az ideiglenes adatokat a használatával tárolja.

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazda operációs rendszertől függően változhat. Emellett előfordulhat, hogy a [gazdaszámítógép](form-recognizer-container-howto.md#the-host-computer) csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók engedélyei és a gazdagép csatlakoztatási helyének engedélyei ütköznek.

|Választható| Név | Data type | Leírás |
|-------|------|-----------|-------------|
|Szükséges| `Input` | Sztring | A bemeneti csatlakoztatás célja. Az alapértelmezett érték 0.`/input`    <br><br>Példa:<br>`--mount type=bind,src=c:\input,target=/input`|
|Szükséges| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték 0.`/output`  <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa Docker-futtatási parancsokra

A következő példák a konfigurációs beállításokat használják a `docker run` parancsok írására és használatára. Ha fut, a tároló továbbra is futni fog, amíg [le nem állítja](form-recognizer-container-howto.md#stop-the-container).

* **Vonal-folytatási karakter**: a következő részben lévő Docker-parancsok egy hátsó perjelet (\\) használnak sor folytatási karakterként. Cserélje ki vagy távolítsa el ezt a karaktert a gazdagép operációs rendszerének követelményeitől függően.
* **Argumentumok sorrendje**: ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le a (z) {_argument_name_} értéket a következő táblázatba a saját értékeivel:

| Helyőrző | Value (Díj) |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | A tároló elindításához használt kulcs. Ez a Azure Portal űrlap-felismerési kulcsok lapon érhető el. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | A számlázási végpont URI-értéke a Azure Portal űrlap-felismerő eszköz áttekintés lapján érhető el.|
| **{COMPUTER_VISION_API_KEY}** | A kulcs a Azure Portal Computer Vision API kulcsok lapon érhető el.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | A számlázási végpont. Ha felhőalapú Computer Vision-erőforrást használ, az URI-érték a Azure Portal Computer Vision API – áttekintés oldalon érhető el. Ha *kognitív-szolgáltatások-felismerő-Text* tárolót használ, használja a `docker run` parancsban található tárolónak átadott számlázási végpont URL-címét. |

Az értékek beszerzésével kapcsolatos részletekért lásd a [szükséges paraméterek összegyűjtése](form-recognizer-container-howto.md#gathering-required-parameters) című témakört.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A tároló futtatásához adja meg a `Eula`, `Billing`és `ApiKey` beállításokat; Ellenkező esetben a tároló nem indul el. További információ: [számlázás](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Űrlap-felismerő tárolók Docker-példák

A következő Docker-példák az űrlap-felismerő tárolóra vonatkoznak.

### <a name="basic-example-for-form-recognizer"></a>Az űrlap-felismerő alapszintű példája

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Naplózási példa az űrlap-felismerőhöz

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Következő lépések

* Tekintse át [a tárolók telepítése és futtatása](form-recognizer-container-howto.md)című ismertetőt.
