---
title: Azure IoT Hub kommunikációs protokollok és portok | Microsoft Docs
description: Fejlesztői útmutató – az eszközről a felhőbe és a felhőből az eszközre irányuló kommunikációhoz támogatott kommunikációs protokollok, valamint a nyitva lévő portszámok ismertetése.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 6d1ab50e471c9c603c7886130375dc74e9b2a755
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284628"
---
# <a name="reference---choose-a-communication-protocol"></a>Hivatkozás – kommunikációs protokoll kiválasztása

IoT Hub lehetővé teszi, hogy az eszközök az alábbi protokollokat használják az eszközön belüli kommunikációhoz:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT WebSocketen keresztül
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP WebSocketen keresztül
* HTTPS

További információ arról, hogy ezek a protokollok hogyan támogatják az adott IoT Hub funkciókat: az [eszközről a felhőbe irányuló kommunikációs útmutató](iot-hub-devguide-d2c-guidance.md) és [a felhőből az eszközre irányuló kommunikációs útmutató](iot-hub-devguide-c2d-guidance.md).

A következő táblázat a választott protokollhoz nyújt magas szintű javaslatokat:

| Protokoll | Ha ezt a protokollt választja |
| --- | --- |
| MQTT <br> MQTT WebSocket-en keresztül |Minden olyan eszközön használható, amely nem igényli több eszköz csatlakoztatását (mindegyiket a saját eszközönkénti hitelesítő adataival) ugyanazon a TLS-kapcsolaton keresztül. |
| AMQP <br> AMQP WebSocket-en keresztül |A helyszíni és a Felhőbeli átjárók használatával kihasználhatja a kapcsolatok többszörösét az eszközök között. |
| HTTPS |Olyan eszközökhöz használható, amelyek nem támogatják más protokollok használatát. |

A következő szempontokat kell figyelembe vennie, amikor kijelöli a protokollt az eszközök közötti kommunikációhoz:

* **Felhő – eszköz minta**. A HTTPS nem rendelkezik hatékony módszerrel a kiszolgáló leküldésének megvalósításához. Így ha a HTTPS protokollt használja, az eszközök lekérdezése IoT Hub a felhőből az eszközre irányuló üzenetekhez. Ez a megközelítés nem hatékony az eszköz és a IoT Hub esetében is. A jelenlegi HTTPS-irányelvek alatt minden eszköznek 25 percenként kell lekérdezni az üzeneteket. A MQTT és a AMQP támogatja a kiszolgáló leküldését a felhőből az eszközre irányuló üzenetek fogadásakor. Lehetővé teszik az üzenetek azonnali leküldését IoT Hubról az eszközre. Ha a kézbesítés késése aggodalomra ad okot, a MQTT vagy a AMQP a legjobb használatú protokollok. A ritkán csatlakoztatott eszközök esetében a HTTPS is működik.

* **Mező-átjárók**. A MQTT és a HTTPS használatakor nem csatlakoztathat több eszközt (mindegyiket saját eszközönkénti hitelesítő adataival) ugyanazzal a TLS-kapcsolattal. Azoknál a [mező-átjárók](iot-hub-devguide-endpoints.md#field-gateways) esetében, amelyek egy TLS-kapcsolatot igényelnek a mező átjárója és a IoT hub között minden csatlakoztatott eszközhöz, ezek a protokollok nem optimálisak.

* **Alacsony erőforrás-eszközök**. A MQTT és a HTTPS-kódtárak kisebb helyigénysel rendelkeznek, mint a AMQP-kódtárak. Ennek megfelelően, ha az eszköz korlátozott erőforrásokkal rendelkezik (például kevesebb, mint 1 MB RAM), akkor ezek a protokollok az egyetlen elérhető protokoll-implementáció.

* **Hálózati bejárás**. A standard AMQP protokoll a 5671-es portot használja, és a MQTT a 8883-es porton figyeli. Ezeknek a portoknak a használata problémákat okozhat a nem HTTPS protokollokhoz lezárt hálózatokban. A MQTT-t websocketeken keresztül, a AMQP-en keresztül, vagy a HTTPS-t használhatja ebben a forgatókönyvben.

* **Hasznos adatok mérete**. A MQTT és a AMQP bináris protokollok, ami a HTTPS-nél nagyobb méretű adattartalomot eredményez.

> [!WARNING]
> A HTTPS használatakor minden eszköznek 25 percenként legfeljebb egyszer kell lekérdezni a felhőből az eszközre irányuló üzeneteket. A fejlesztés során minden eszköz gyakrabban tud lekérdezni, ha szükséges.

## <a name="port-numbers"></a>Portszámok

Az eszközök különböző protokollok használatával kommunikálhatnak az Azure IoT Hubával. A protokoll választását jellemzően a megoldás konkrét követelményei vezérlik. A következő táblázat felsorolja azokat a kimenő portokat, amelyeknek az eszköz számára nyitva kell lennie egy adott protokoll használatához:

| Protokoll | Port |
| --- | --- |
| MQTT |8883 |
| MQTT WebSocketen keresztül |443 |
| AMQP |5671 |
| AMQP WebSocketen keresztül |443 |
| HTTPS |443 |

Miután létrehozott egy IoT hubot egy Azure-régióban, az IoT hub megtartja ugyanazt az IP-címet az IoT hub élettartama szempontjából. Ha azonban a Microsoft az IoT hub-t egy másik méretezési egységbe helyezi a szolgáltatás minőségének fenntartása érdekében, akkor az új IP-címet kap.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni arról, hogy IoT Hub hogyan valósítja meg a MQTT protokollt, tekintse meg [a kommunikáció az IoT hub használatával című témakört a MQTT protokoll segítségével](iot-hub-mqtt-support.md).
