---
title: Az Azure IoT Hub hibáinak elhárítása 409001 DeviceAlreadyExists
description: Ismerje meg, hogyan javíthatja a 409001-es hibát a DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960788"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Ez a cikk a **409001 DeviceAlreadyExists** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Ha IoT Hub-ben próbál meg regisztrálni egy eszközt, a kérelem meghiúsul a 409001-es **DeviceAlreadyExists**.

## <a name="cause"></a>Ok

Már van egy eszköz ugyanazzal az AZONOSÍTÓval az IoT hub-ban. 

## <a name="solution"></a>Megoldás

Használjon másik eszköz-azonosítót, és próbálkozzon újra.