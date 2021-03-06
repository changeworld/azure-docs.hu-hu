---
title: ELAVULT Azure DC/OS-fürt figyelése – Dynatrace
description: Azure Container Service DC/OS-fürt figyelése a Dynatrace. Telepítse a Dynatrace OneAgent a DC/OS irányítópult használatával.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a82481c5cb3d12b11179b41999f73e67583ec43b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277743"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>ELAVULT Azure Container Service DC/OS-fürt figyelése Dynatrace SaaS/felügyelt

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben a cikkben bemutatjuk, hogyan helyezheti üzembe a [Dynatrace](https://www.dynatrace.com/) Azure Container Service-OneAgent a fürt összes ügynök-csomópontjának figyeléséhez. Ehhez a konfigurációhoz a Dynatrace SaaS/Managed fiókot kell használnia. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/felügyelt
A Dynatrace egy Felhőbeli natív figyelési megoldás a dinamikus tárolók és fürtök környezetéhez. Lehetővé teszi a tárolók üzembe helyezésének és a memória kiosztásának jobb optimalizálását valós idejű használati adatok használatával. Képes automatikusan kijelölni az alkalmazások és az infrastruktúra problémáit az automatizált viszonyítási, a probléma korrelációs és a kiváltó okok észlelésének biztosításával.

Az alábbi ábra a Dynatrace felhasználói felületet mutatja be:

![Dynatrace felhasználói felület](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Előfeltételek 
[Telepítsen](container-service-deployment.md) és [kapcsolódjon](./../container-service-connect.md) Azure Container Service által konfigurált fürthöz. Ismerkedjen meg a [Marathon felhasználói felülettel](container-service-mesos-marathon-ui.md). Dynatrace SaaS-fiók beállításához nyissa meg a [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) .  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Dynatrace-telepítés konfigurálása a Marathon segítségével
Ezek a lépések bemutatják, hogyan konfigurálhat és helyezhet üzembe Dynatrace-alkalmazásokat a fürtön a Marathon segítségével.

1. A DC/OS felhasználói felületét [http://localhost:80/](http://localhost:80/)használatával érheti el. Egyszer a DC/OS felhasználói felületén navigáljon az **univerzum** lapra, és keresse meg a **Dynatrace**.

    ![Dynatrace a DC/OS-univerzumban](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. A konfiguráció elvégzéséhez szüksége lesz egy Dynatrace SaaS-fiókra vagy egy ingyenes próbaverziós fiókra. Miután bejelentkezett a Dynatrace-irányítópultra, válassza a **Dynatrace telepítése**lehetőséget.

    ![Dynatrace beállítása a Pásti-integrációhoz](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. A lapon válassza a **Pásti integráció beállítása**lehetőséget. 

    ![Dynatrace API-Token](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Adja meg az API-tokent a DC/OS univerzumban található Dynatrace OneAgent-konfigurációban. 

    ![Dynatrace OneAgent-konfiguráció a DC/OS-univerzumban](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Állítsa a példányokat a futtatni kívánt csomópontok számára. A nagyobb szám beállítása is működik, de a DC/OS továbbra is megpróbál új példányokat keresni, amíg az adott szám ténylegesen el nem éri. Ha szeretné, beállíthatja úgy is, hogy a 1000000-as értéket adja meg. Ebben az esetben, amikor új csomópontot ad hozzá a fürthöz, a Dynatrace automatikusan üzembe helyez egy ügynököt az új csomóponton, a DC/OS díjszabásával folyamatosan próbálkozik a további példányok üzembe helyezésével.

    ![Dynatrace-konfiguráció a DC/OS-univerzumban – példányok](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Következő lépések

A csomag telepítése után váltson vissza a Dynatrace-irányítópultra. A fürtben található tárolók különböző használati metrikáit is megismerheti. 
