---
title: Azure Service Fabric fordított proxy biztonságos kommunikációja
description: Fordított proxy konfigurálása az Azure Service Fabric-alkalmazások biztonságos, végpontok közötti kommunikációjának engedélyezéséhez.
author: kavyako
ms.topic: conceptual
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 4cfeaf34a39231ffa91ea970a61f66632bae40c7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282249"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Csatlakozás biztonságos szolgáltatáshoz fordított proxyval

Ez a cikk azt ismerteti, hogyan hozható létre biztonságos kapcsolat a fordított proxy és a szolgáltatások között, így lehetővé válik a végpontok közötti biztonságos csatorna engedélyezése. További információ a fordított proxyról: [fordított proxy az Azure-ban Service Fabric](service-fabric-reverseproxy.md)

A biztonságos szolgáltatásokhoz való csatlakozás csak akkor támogatott, ha a fordított proxy a HTTPS-figyelésre van konfigurálva. Ez a cikk azt feltételezi, hogy ez a helyzet.
A fordított proxy Service Fabric-ben való konfigurálásához tekintse meg a [fordított proxy beállítása az Azure Service Fabricban](service-fabric-reverseproxy-setup.md) című témakört.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Biztonságos kapcsolat létesítése a fordított proxy és a szolgáltatások között 

### <a name="reverse-proxy-authenticating-to-services"></a>Fordított proxy hitelesítése a szolgáltatásokban:
A fordított proxy a tanúsítványa alapján azonosítja magát a szolgáltatásokhoz. Azure-fürtök esetén a tanúsítvány a Resource Manager [ **-sablon Microsoft. ServiceFabric/Clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [erőforrástípus szakaszában](../azure-resource-manager/templates/template-syntax.md) , a ***reverseProxyCertificate*** tulajdonsággal van megadva. Önálló fürtök esetén a tanúsítvány a ClusterConfig. JSON **biztonsági** szakaszában, a ***ReverseProxyCertificate*** vagy a ***ReverseProxyCertificateCommonNames*** tulajdonsággal van megadva. További információ: [fordított proxy engedélyezése önálló fürtökön](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

A szolgáltatások a fordított proxy által megjelenített tanúsítvány ellenőrzéséhez implementálják a logikát. A szolgáltatások megadhatják az elfogadott ügyféltanúsítvány részleteit konfigurációs beállításokként a konfigurációs csomagban. Ez futásidőben olvasható, és a fordított proxy által bemutatott tanúsítvány ellenőrzéséhez használható. A konfigurációs beállítások hozzáadásához tekintse meg az [alkalmazás paramétereinek kezelése](service-fabric-manage-multiple-environment-app-configuration.md) című témakört. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Fordított proxy a szolgáltatás identitásának ellenőrzéséhez a szolgáltatás által bemutatott tanúsítványon keresztül:
A fordított proxy a következő házirendeket támogatja a szolgáltatások által bemutatott tanúsítványok tanúsítvány-ellenőrzésének végrehajtásához: none, ServiceCommonNameAndIssuer és ServiceCertificateThumbprints.
A fordított proxy használatára vonatkozó szabályzat kiválasztásához adja meg a **ApplicationCertificateValidationPolicy** a **ApplicationGateway/http** szakaszban a [fabricSettings](service-fabric-cluster-fabric-settings.md)alatt.

A következő szakasz az egyes beállítások konfigurációs részleteit mutatja be.

### <a name="service-certificate-validation-options"></a>Szolgáltatás tanúsítvány-ellenőrzési lehetőségei 

- **Nincs**: a fordított proxy kihagyja a proxyzott szolgáltatás tanúsítványának ellenőrzését, és létrehozza a biztonságos kapcsolatot. Ez az az alapértelmezett viselkedést.
A [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban válassza a **none** értéket a **ApplicationCertificateValidationPolicy** .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**: a fordított proxy ellenőrzi a szolgáltatás által bemutatott tanúsítványt a tanúsítvány köznapi neve és az azonnali kiállító ujjlenyomata alapján: a [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban adja meg a **ApplicationCertificateValidationPolicy** értéket a **ServiceCommonNameAndIssuer** értékkel.

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   A szolgáltatás köznapi nevének és kiállítójának ujjlenyomatai megfelelnek megadásához vegyen fel egy [**ApplicationGateway/http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) szakaszt a **fabricSettings**alatt az alább látható módon. A **Parameters** tömbben több tanúsítvány köznapi neve és kiállítója is felvehető. 

   Ha a végpontok fordított proxyja csatlakozik ahhoz, hogy egy olyan tanúsítványt adjon meg, amely köznapi neve és kiállítói ujjlenyomata megfelel az itt megadott értékek bármelyikének, az SSL-csatorna létrejött. 
   A tanúsítvány részleteinek egyeztetése után a fordított proxy nem teljesíti az ügyfél 502-as (hibás átjáróval rendelkező) állapotkóddal kapcsolatos kérelmét. A HTTP-állapotsorban az "érvénytelen SSL-tanúsítvány" kifejezés is szerepelni fog. 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**: a fordított proxy ellenőrzi a proxyn lévő szolgáltatás tanúsítványát az ujjlenyomata alapján. Dönthet úgy is, hogy ezt az útvonalat adja meg, ha a szolgáltatások önaláírt tanúsítványokkal vannak konfigurálva: a [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban adja meg a **ServiceCertificateThumbprints** értékkel rendelkező **ApplicationCertificateValidationPolicy** .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   A **ApplicationGateway/http** szakaszban a **ServiceCertificateThumbprints** bejegyzést is megadhatja a ujjlenyomatai megfelelnek. Több ujjlenyomatai megfelelnek is megadható vesszővel tagolt listaként az érték mezőben, az alábbi ábrán látható módon:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Ha a kiszolgálói tanúsítvány ujjlenyomata szerepel ebben a konfigurációs bejegyzésben, a fordított proxy az SSL-kapcsolat sikerességét eredményezi. Ellenkező esetben megszűnik a kapcsolat, és az ügyfél 502-as (hibás átjáróval rendelkező) kérelme sikertelen lesz. A HTTP-állapotsorban az "érvénytelen SSL-tanúsítvány" kifejezés is szerepelni fog.

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Végpont-kiválasztási logika, ha a szolgáltatások biztonságos és nem biztonságos végpontokat tesznek elérhetővé
A Service Fabric támogatja a szolgáltatások több végpontjának konfigurálását. További információ: [erőforrások megadása egy szolgáltatás jegyzékfájljában](service-fabric-service-manifest-resources.md).

A fordított proxy kijelöli az egyik végpontot, hogy továbbítsa a kérést a [szolgáltatás URI-ja](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy) **ListenerName** lekérdezési paramétere alapján. Ha a **ListenerName** paraméter nincs megadva, a fordított proxy a végpontok listából bármelyik végpontot kiválaszthatja. A szolgáltatáshoz konfigurált végpontok függvényében a kiválasztott végpont HTTP-vagy HTTPS-végpont lehet. Lehetnek olyan forgatókönyvek vagy követelmények, amelyekben a fordított proxyt csak "biztonságos módban" kell használni; vagyis nem szeretné, hogy a biztonságos fordított proxy továbbítsa a kérelmeket a nem biztonságos végpontoknak. Ha a fordított proxyt csak biztonságos módra szeretné beállítani, adja meg a **SecureOnlyMode** konfigurációs bejegyzést az **igaz** értékkel a [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban.   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> Ha a **SecureOnlyMode**-ben működik, ha az ügyfél egy http (nem védett) végpontnak megfelelő **ListenerName** adott meg, akkor a fordított proxy a 404 (nem található) http-állapotkód miatt meghiúsul a kérelemben.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Ügyféltanúsítvány-alapú hitelesítés beállítása fordított proxyn keresztül
Az SSL-lezárás a fordított proxyn történik, és az összes ügyféltanúsítvány-érték elvész. Ahhoz, hogy a szolgáltatások ügyféltanúsítvány-alapú hitelesítést végezzenek, a [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban a **ForwardClientCertificate** beállítást kell megadnia.

1. Ha a **ForwardClientCertificate** **hamis**értékre van állítva, akkor a fordított proxy nem kéri le az ügyféltanúsítványt az SSL-kézfogás során az ügyféllel.
Ez az az alapértelmezett viselkedést.

2. Ha a **ForwardClientCertificate** értéke **true (igaz**), a fordított proxy az ügyfél tanúsítványát az SSL-kézfogás során kéri le.
Ezután továbbítja az ügyféltanúsítvány-adatkészletet egy **X-Client-Certificate**nevű egyéni http-fejlécbe. A fejléc értéke az ügyfél tanúsítványának Base64 kódolású PEM formátumú karakterlánca. A szolgáltatás a tanúsítvány adatai vizsgálatát követően a megfelelő állapotkódot követően sikeres vagy sikertelen lehet.
Ha az ügyfél nem tartalmaz tanúsítványt, a fordított proxy egy üres fejlécet továbbít, és lehetővé teszi, hogy a szolgáltatás kezelje az esetet.

> [!NOTE]
> A fordított proxy egy egyszerű továbbító. Az ügyfél tanúsítványának érvényesítése nem történik meg.


## <a name="next-steps"></a>Következő lépések
* [Állítsa be és konfigurálja a fordított proxyt egy fürtön](service-fabric-reverseproxy-setup.md).
* Tekintse meg a [fordított proxy konfigurálása a biztonságos szolgáltatásokhoz való csatlakozáshoz](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) című témakört.
* Tekintse [meg a githubon](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)található, a szolgáltatások közötti http-kommunikáció példáját.
* [Távoli eljáráshívás Reliable Services táveléréssel](service-fabric-reliable-services-communication-remoting.md)
* [OWIN-t használó webes API Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Fürttanúsítványok kezelése](service-fabric-cluster-security-update-certs-azure.md)
