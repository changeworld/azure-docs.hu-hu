---
title: Témakörök és esemény-előfizetések figyelése – Azure Event Grid IoT Edge | Microsoft Docs
description: Témakörök és esemény-előfizetések figyelése
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086673"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Témakörök és esemény-előfizetések figyelése

A Event Grid on Edge számos mérőszámot nyújt a témakörökhöz és az esemény-előfizetésekhez a [Prometheus kiállítási formátumában](https://prometheus.io/docs/instrumenting/exposition_formats/). Ez a cikk a rendelkezésre álló mérőszámokat és azok engedélyezésének módját ismerteti.

## <a name="enable-metrics"></a>Metrikák engedélyezése

Konfigurálja a modult a metrikák kibocsátására úgy, hogy a `metrics__reporterType` környezeti változót `prometheus`re állítja be a tároló létrehozási beállításaiban:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

A metrikák a HTTP-n és a https-ben `4438/metrics` modul `5888/metrics`én lesznek elérhetők. Például `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` a http-hez. Ezen a ponton a metrikák modulja lekérdezheti a végpontot, hogy összegyűjtse a mérőszámokat az ebben a [példában szereplő architektúrában](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Rendelkezésre álló metrikák

A témakörök és az esemény-előfizetések mérőszámokat bocsátanak ki, hogy betekintést nyújtson az események kézbesítésére és a modulok teljesítményére.

### <a name="topic-metrics"></a>Témakör metrikái

| Metrika | Leírás |
| ------ | ----------- |
| EventsReceived | A témakörben közzétett események száma
| UnmatchedEvents | A témakörben közzétett események száma, amelyek nem felelnek meg az esemény-előfizetésnek, és el lettek dobva
| SuccessRequests | A témakörben fogadott bejövő közzétételi kérelmek száma
| SystemErrorRequests | A bejövő közzétételi kérelmek száma belső rendszerhiba miatt sikertelen volt.
| UserErrorRequests | A bejövő közzétételi kérelmek száma felhasználói hiba miatt meghiúsult, például helytelenül formázott JSON
| SuccessRequestLatencyMs | Kérelemre adott válasz késésének közzététele ezredmásodpercben


### <a name="event-subscription-metrics"></a>Esemény-előfizetési metrikák

| Metrika | Leírás |
| ------ | ----------- |
| deliverySuccessCounts | A konfigurált végponthoz sikeresen küldött események száma
| deliveryFailureCounts | A konfigurált végpontnak sikertelenül küldött események száma
| deliverySuccessLatencyMs | Az események késése (ezredmásodpercben) sikeresen lefutott
| deliveryFailureLatencyMs | Az események kézbesítési hibáinak késése ezredmásodpercben
| systemDelayForFirstAttemptMs | Események rendszerkésleltetése az első kézbesítési kísérlet előtt ezredmásodpercben
| deliveryAttemptsCount | Esemény kézbesítési kísérletek száma – sikeres és sikertelen
| expiredCounts | A lejárt és a beállított végpontnak nem továbbított események száma