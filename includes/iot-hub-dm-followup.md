---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2514325c93f0f72aa979f871e86e465093479402
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558781"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Az eszközkezelés műveleteinek testreszabása és kiterjesztése

A IoT-megoldások kiterjeszthetik a definiált eszközkezelés-mintákat, vagy engedélyezhetik az egyéni mintákat az eszköz Twin és a felhőből az eszközre való módszerének egyszerű használatával. Az eszközkezelés további példái közé tartozik a gyári beállítások visszaállítása, a belső vezérlőprogram frissítése, a szoftverfrissítés, az energiagazdálkodás, a hálózat és a kapcsolatok kezelése, valamint az adattitkosítás.

## <a name="device-maintenance-windows"></a>Eszköz-karbantartási időszakok

Általában úgy konfigurálhatja az eszközöket, hogy a megszakításokat és az állásidőt a lehető legkisebbre csökkentse. Az eszköz-karbantartási időszakok általában azt az időpontot határozzák meg, amikor egy eszköznek frissítenie kell a konfigurációját. A háttérrendszer-megoldások az eszközök Twin kívánt tulajdonságait használva meghatározhatják és aktiválják a szabályzatokat az eszközön, amely lehetővé teszi a karbantartási időszakot. Amikor egy eszköz megkapja a karbantartási időszakra vonatkozó házirendet, az eszköz Twin-objektum jelentett tulajdonságával jelentheti be a szabályzat állapotát. A háttérbeli alkalmazás ezután az eszköz kettős lekérdezéseit használva tanúsítja az eszközök és az egyes szabályzatok megfelelőségét.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy közvetlen metódust használt egy távoli újraindítás elindításához az eszközön. A jelentett tulajdonságokat használta a legutóbbi újraindítási idő jelentésére az eszközről, és lekérdezte az eszköz ikerét, hogy felderítse az eszköz legutóbbi újraindításának időpontját a felhőből.

Ha továbbra is szeretné megkezdeni a IoT Hub és az eszközkezelés mintáit, például a távoli belső vezérlőprogram frissítését, tekintse meg a [belső vezérlőprogram frissítését](../articles/iot-hub/tutorial-firmware-update.md)ismertető témakört.

Ha szeretné megtudni, hogyan bővítheti IoT-megoldását, és hogyan ütemezhet több eszközre irányuló hívásokat, tekintse meg az [ütemezett és a szórási feladatokat](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).