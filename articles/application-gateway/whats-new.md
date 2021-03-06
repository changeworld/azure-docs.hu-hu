---
title: Az Azure Application Gateway újdonságai
description: Ismerje meg az Azure Application Gateway újdonságait, például a legújabb kibocsátási megjegyzéseket, ismert problémákat, hibajavításokat, elavult funkciókat és a közelgő változásokat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: c6d4d290493bbd234ab048e613b88f8857513cc8
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299555"
---
# <a name="whats-new-in-azure-application-gateway"></a>Az Azure Application Gateway újdonságai

Az Azure Application Gateway frissítése folyamatosan történik. A legfrissebb fejlesztésekkel kapcsolatos információkért a cikk a következő információkat tartalmazza:

- A legújabb kiadásaihoz.
- Ismert problémák
- Hibajavítások
- Elavult funkciók

## <a name="new-features"></a>Új funkciók

|Funkció  |Leírás  |Hozzáadás dátuma  |
|---------|---------|---------|
|Affinitási cookie-változások |Ha engedélyezve van a cookie-alapú affinitás, Application Gateway befecskendez egy *ApplicationGatewayAffinityCORS* nevű másik azonos cookie-t a meglévő ApplicationGatewayAffinity-cookie mellett. A *ApplicationGatewayAffinityCORS* két további attribútummal bővült (*SameSite = none; Biztonságos*), hogy a Sticky-munkamenetek még a származási kérelmek esetében is fennmaradnak. További információért tekintse meg [Application Gateway cookie-alapú affinitást](configuration-overview.md#cookie-based-affinity) . |2020. február |
|Mintavételi fejlesztések |A Application Gateway v2 SKU-ban az egyéni mintavételi fejlesztések segítségével egyszerűsítettük a mintavételi [konfigurációt](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku), az [igény szerinti háttér-tesztelést](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) és a háttér-állapottal kapcsolatos problémák elhárításához szükséges [további diagnosztikai információkat](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) .  |2019. október |
|További mérőszámok |A következő új mérőszámokat adtuk hozzá az Application Gateway v2 SKU figyeléséhez: [időzítéssel kapcsolatos metrikák](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), háttérbeli válasz állapota, fogadott bájtok, bájtok, ügyfél TLS protokoll és aktuális számítási egységek. Lásd: [Application Gateway v2 SKU által támogatott metrikák](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |2019. augusztus |
|WAF egyéni szabályok |A Application Gateway WAF_v2 mostantól támogatja az egyéni szabályok létrehozását. Lásd: [Application Gateway egyéni szabályok](custom-waf-rules-overview.md). |2019. június |
|Automatikus skálázás, zóna-redundancia, statikus VIP-támogatás GA |Általános rendelkezésre állás a v2 SKU-hoz, amely támogatja az automatikus skálázást, a zóna redundanciát, a teljesítmény növelését, a statikus VIP-címek, a Key Vault, a fejléc újraírását. Lásd: [Application Gateway automatikus skálázás dokumentációja](application-gateway-autoscaling-zone-redundant.md). |2019. április |
|Key Vault integráció |A Application Gateway mostantól támogatja a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványok Key Vault (nyilvános előzetes verzióban) való integrálását. Lásd: [SSL-lezárás Key Vault tanúsítványokkal](key-vault-certs.md). |2019. április |
|A fejlécben szereplő szifilisz/újraírások     |Mostantól újraírhatók a HTTP-fejlécek. További információt az [oktatóanyag: Application Gateway létrehozása és a HTTP-fejlécek újraírása](tutorial-http-header-rewrite-powershell.md) című témakörben talál.|2018. december|
|WAF-konfiguráció és kizárási lista     |További lehetőségeket adtunk hozzá a WAF konfigurálásához és a téves pozitív riasztások csökkentéséhez. További információ: [webalkalmazási tűzfal kérelmének mérete és kizárási listája](application-gateway-waf-configuration.md).|2018. december|
|Automatikus skálázás, zóna-redundancia, statikus VIP-támogatás      |A v2 SKU számos fejlesztést tartalmaz, többek között az automatikus skálázást, a jobb teljesítményt és egyebeket. További információért lásd: [Mi az az Azure Application Gateway?](overview.md)|2018. szeptember|
|Kapcsolatkiürítés     |A kapcsolatok kiürítése lehetővé teszi, hogy szabályosan eltávolítsa a háttér-készletek tagjait. További információ: a [kapcsolatok kiürítése](features.md#connection-draining).|2018. szeptember|
|Egyéni hibalapok     |Az egyéni hibaüzenetek oldalain a webhely többi részének formátumán belüli hibaüzenetet hozhat létre. Ennek engedélyezéséhez tekintse meg az [egyéni hibaüzenetek Application Gateway létrehozása](custom-error.md)című témakört.|2018. szeptember|
|Mérőszámok továbbfejlesztései     |Jobb áttekintést kaphat a Application Gateway állapotáról a továbbfejlesztett metrikákkal. A Application Gateway metrikáinak engedélyezéséhez tekintse meg a [háttér állapota, a diagnosztikai naplók és a metrikák Application Gatewayhoz](application-gateway-diagnostics.md)című témakört.|2018. június|

## <a name="next-steps"></a>Következő lépések

További információ az Azure Application Gatewayról: [Mi az az azure Application Gateway?](overview.md)
