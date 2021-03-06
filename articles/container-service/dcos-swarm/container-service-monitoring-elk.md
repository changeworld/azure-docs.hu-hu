---
title: ELAVULT Azure DC/OS-fürt – ELK stack figyelése
description: Egy DC/OS-fürt figyelése Azure Container Service-fürtön a ELK-vel (Elasticsearch, Logstash és Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3d34ebe22344be8acc6ec3cc974071639293e2b3
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277761"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>ELAVULT Azure Container Service-fürt figyelése a ELK-vel

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben a cikkben bemutatjuk, hogyan helyezhet üzembe a ELK-t (Elasticsearch, Logstash, Kibana) a DC/OS-fürtön a Azure Container Service. 

## <a name="prerequisites"></a>Előfeltételek
Azure Container Service által konfigurált DC/OS-fürt [üzembe helyezése](container-service-deployment.md) és [összekötése](../container-service-connect.md) . [Itt](container-service-mesos-marathon-ui.md)MEGISMERHETI a DC/os irányítópultot és a Marathon-szolgáltatásokat. Telepítse a [Marathon Load Balancer](container-service-load-balancing.md)is.


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
A ELK stack a Elasticsearch, a Logstash és a Kibana kombinációja, amely egy végpontok közötti veremet biztosít, amely a fürt naplófájljainak figyelésére és elemzésére használható.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>A ELK stack konfigurálása DC/OS-fürtön
A DC/OS felhasználói felületen [http://localhost:80/](http://localhost:80/) egyszer férhet hozzá a DC/os felhasználói felületéhez, és navigáljon a **Universe**-hez. Az Elasticsearch, a Logstash és a Kibana a DC/OS-univerzumból, illetve adott sorrendben is megkeresheti és telepítheti. A konfigurálásról további információt a **Speciális telepítési** hivatkozásra kattintva kaphat.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

A ELK-tárolók és a működésük után engedélyeznie kell, hogy a Kibana a Marathon-LB használatával legyen elérhető. Navigáljon a **szolgáltatások** > **kibana**elemre, majd kattintson a **Szerkesztés** elemre az alábbi ábrán látható módon.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


**Váltson JSON módba** , és görgessen le a címkék szakaszhoz.
Itt fel kell vennie egy `"HAPROXY_GROUP": "external"` bejegyzést az alább látható módon.
Miután rákattintott a **módosítások telepítése**gombra, a tároló újraindul.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Ha szeretné ellenőrizni, hogy a Kibana regisztrálva van-e a HAPROXY-irányítópulton, akkor az ügynök-fürtön az 9090-es portot kell megnyitnia, mivel a HAPROXY a 9090-es porton fut.
Alapértelmezés szerint a DC/OS Agent-fürtön a 80, 8080 és 443 portot nyitjuk meg.
A portok megnyitására és a nyilvános értékelés megadására vonatkozó utasítások [itt](container-service-enable-public-access.md)érhetők el.

A HAPROXY-irányítópult eléréséhez nyissa meg a Marathon-LB felügyeleti felületet a következő címen: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Ha megnyitja az URL-címet, a HAPROXY-irányítópultot az alább látható módon kell látnia, és meg kell jelennie a Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Az 5601-es porton üzembe helyezett Kibana-irányítópult eléréséhez meg kell nyitnia a 5601-es portot. Kövesse az [alábbi utasításokat.](container-service-enable-public-access.md) Ezután nyissa meg a Kibana-irányítópultot a következő címen: `http://localhost:5601`.

## <a name="next-steps"></a>Következő lépések

* A rendszer-és az alkalmazás-naplók továbbítása és beállítása esetén lásd: a [naplózás a DC/os-ben és a Elk-ben](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* A naplók szűrésével kapcsolatban lásd: [a naplók szűrése a Elk](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/)használatával. 

 

