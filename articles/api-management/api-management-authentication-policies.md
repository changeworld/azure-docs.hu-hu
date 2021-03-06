---
title: Azure API Management hitelesítési házirendek | Microsoft Docs
description: Ismerje meg az Azure API Management használható hitelesítési házirendeket.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 5ca153f0d52b65aa1ee56d5757381f1f31c7eeb5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280325"
---
# <a name="api-management-authentication-policies"></a>API Management hitelesítési házirendek
Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AuthenticationPolicies"></a>Hitelesítési házirendek

-   Egyszerű hitelesítéssel [hitelesítheti alapszintű](api-management-authentication-policies.md#Basic) hitelesítést a háttér-szolgáltatással.

-   [Hitelesítés az ügyféltanúsítvány](api-management-authentication-policies.md#ClientCertificate) használatával – az Ügyféltanúsítványok segítségével végezzen hitelesítést egy háttér-szolgáltatással.

-   [Hitelesítés felügyelt identitással](api-management-authentication-policies.md#ManagedIdentity) – hitelesítés a API Management szolgáltatás [felügyelt identitásával](../active-directory/managed-identities-azure-resources/overview.md) .

##  <a name="Basic"></a>Hitelesítés alapszintű
 Az alapszintű hitelesítést használó háttér-szolgáltatással történő hitelesítéshez használja a `authentication-basic` szabályzatot. Ez a házirend hatékonyan állítja be a HTTP-engedélyezési fejlécet a szabályzatban megadott hitelesítő adatoknak megfelelő értékre.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Példa

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|hitelesítés – alapszintű|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|felhasználónév|Megadja az alapszintű hitelesítő adat felhasználónevét.|Igen|N.A.|
|jelszó|Megadja az alapszintű hitelesítő adat jelszavát.|Igen|N.A.|

### <a name="usage"></a>Használat
 Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="ClientCertificate"></a>Hitelesítés ügyféltanúsítvány használatával
 A `authentication-certificate` házirenddel hitelesítheti a háttér-szolgáltatást az ügyféltanúsítvány használatával. Először az [API Management kell telepíteni](https://go.microsoft.com/fwlink/?LinkID=511599) a tanúsítványt, és annak ujjlenyomata azonosítja.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Példák

Ebben a példában az ügyféltanúsítvány azonosítja az ujjlenyomatát.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
Ebben a példában az ügyféltanúsítvány az erőforrás neve alapján azonosítható.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elemek  
  
|Név|Leírás|Kötelező|  
|----------|-----------------|--------------|  
|hitelesítés – tanúsítvány|Gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Kötelező|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Az ügyféltanúsítvány ujjlenyomata.|`thumbprint` vagy `certificate-id`nek kell lennie.|N.A.|  
|tanúsítvány-azonosító|A tanúsítvány erőforrásának neve.|`thumbprint` vagy `certificate-id`nek kell lennie.|N.A.|  
  
### <a name="usage"></a>Használat  
 Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.  
  
-   **Házirend fejezetei:** bejövő  
  
-   **Házirend-hatókörök:** az összes hatókör  

##  <a name="ManagedIdentity"></a>Hitelesítés felügyelt identitással  
 A `authentication-managed-identity` szabályzat használatával hitelesítheti a háttér-szolgáltatást a API Management szolgáltatás felügyelt identitásával. Ez a szabályzat lényegében a felügyelt identitás használatával szerez hozzáférési jogkivonatot Azure Active Directoryről a megadott erőforrás eléréséhez. A jogkivonat sikeres beszerzését követően a házirend a `Bearer` séma használatával állítja be a token értékét a `Authorization` fejlécben.
  
### <a name="policy-statement"></a>Szabályzati utasítás  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Példa  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Felügyelt identitás használata a háttér-szolgáltatással történő hitelesítéshez
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Felügyelt identitás használata a küldési kérelmek házirendjében
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elemek  
  
|Név|Leírás|Kötelező|  
|----------|-----------------|--------------|  
|authentication-managed-identity |Gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Kötelező|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|resource|sztring elemet. A célként megadott webes API (biztonságos erőforrás) alkalmazás-azonosítója Azure Active Directoryban.|Igen|N.A.|  
|output-token-változó-neve|sztring elemet. Annak a környezeti változónak a neve, amely a jogkivonat értékét a következő objektumtípusként fogja fogadni: `string`. |Nem|N.A.|  
|Mellőzés – hiba|Logikai. Ha `true`értékre van állítva, akkor a házirend-folyamat akkor is végre fog hajtani, ha nem kapott hozzáférési jogkivonatot.|Nem|false|  
  
### <a name="usage"></a>Használat  
 Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.  
  
-   **Házirend fejezetei:** bejövő  
  
-   **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>További lépések
További információ a házirendek használatáról:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](api-management-policy-reference.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Házirend-minták](policy-samples.md)
