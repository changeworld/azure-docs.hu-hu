---
title: Azure API Management gyorsítótárazási szabályzatok | Microsoft Docs
description: Ismerje meg az Azure API Management használható gyorsítótárazási házirendeket.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06c4ede12f939e48973d3e0b502d90b848d199bb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280299"
---
# <a name="api-management-caching-policies"></a>API Management gyorsítótárazási házirendek
Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CachingPolicies"></a>Gyorsítótárazási házirendek

- Válasz gyorsítótárazási házirendjei
    - [Beolvasás gyorsítótárból](api-management-caching-policies.md#GetFromCache) – a gyorsítótár végrehajtása megkeresi és érvényes gyorsítótárazott válaszokat ad vissza, ha elérhető.
    - [Tárolás a gyorsítótárba](api-management-caching-policies.md#StoreToCache) – a gyorsítótárak a megadott gyorsítótár-vezérlési konfigurációnak megfelelően gyorsítótárazzák a válaszokat.
- Érték gyorsítótárazási házirendjei
    - [Érték lekérése a gyorsítótárból](#GetFromCacheByKey) – a gyorsítótárazott elemek kulcs szerinti beolvasása.
    - [Tárolási érték](#StoreToCacheByKey) a gyorsítótárban – a gyorsítótárban lévő elemek tárolása kulcs alapján.
    - [Érték eltávolítása a gyorsítótárból](#RemoveCacheByKey) – a kulcsban lévő elem eltávolítása a gyorsítótárból.

## <a name="GetFromCache"></a>Beolvasás a gyorsítótárból
A `cache-lookup` házirend használatával hajtsa végre a gyorsítótár megkeresését, és ha elérhető, érvényes gyorsítótárazott választ ad vissza. Ezt a szabályzatot olyan esetekben lehet alkalmazni, amikor a válasz tartalma egy adott időszakban statikus marad. A válasz gyorsítótárazása csökkenti a háttér-webkiszolgálón kiszabott sávszélesség-és feldolgozási követelményeket, és csökkenti az API-felhasználók által észlelt késéseket.

> [!NOTE]
> Ennek a szabályzatnak megfelelő tárolóval kell rendelkeznie a [gyorsítótárazási](api-management-caching-policies.md#StoreToCache) házirendhez.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Példa házirend-kifejezések használatával
Ez a példa azt mutatja be, hogyan konfigurálhatja API Management válasz gyorsítótárazási időtartamát, amely megfelel a háttérrendszer által a támogatott `Cache-Control` direktíva által megadott válasz-gyorsítótárazásnak. A szabályzat konfigurálásának és használatának bemutatását lásd: a [Cloud Cover 177-es epizódja: további API Management szolgáltatások a Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 25:25-es gyors előretekeréssel.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

További információ: [Policy Expressions](api-management-policy-expressions.md) and [Context változó](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|gyorsítótár – keresés|Gyökérelem.|Igen|
|változó – fejléc|A gyorsítótárazási válaszok megkezdése megadott fejléc alapján, például elfogadás, elfogadás – karakterkészlet, elfogadás – kódolás, elfogadás – nyelv, engedélyezés, elvárt, feladó, gazdagép, if-Match.|Nem|
|változó-by-Query-paraméter|A gyorsítótárazási válaszok indítása a megadott lekérdezési paraméterek értékével. Adjon meg egy vagy több paramétert. Pontosvesszőt használjon elválasztóként. Ha nincs megadva, a rendszer az összes lekérdezési paramétert használja.|Nem|

### <a name="attributes"></a>Attribútumok

| Name (Név)                           | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | Ha a `true`értékre van állítva, lehetővé teszi az engedélyezési fejlécet tartalmazó kérelmek gyorsítótárazását.                                                                                                                                                                                                                                                                        | Nem       | false             |
| gyorsítótárazás – típus               | Válasszon az attribútum következő értékei közül:<br />- `internal` a beépített API Management cache használatára,<br />- `external` a külső gyorsítótár használatához a külső [Azure cache használata az Azure-ban történő Redis az Azure-ban API Management](api-management-howto-cache-external.md),<br />Ha más módon konfigurált vagy belső gyorsítótárat használ, - `prefer-external` külső gyorsítótár használatára. | Nem       | `prefer-external` |
| alsóbb réteg – gyorsítótárazási típus        | Ezt az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> -nincs – az alsóbb rétegbeli gyorsítótárazás nem engedélyezett.<br />– a privát alsóbb rétegbeli magánhálózati gyorsítótárazás engedélyezett.<br />– a nyilvános és a megosztott alsóbb rétegbeli gyorsítótárazás engedélyezett.                                                                                                          | Nem       | Nincs              |
| újra kell érvényesíteni                | Ha az alsóbb rétegbeli gyorsítótárazás engedélyezve van, ez az attribútum be-és kikapcsolja az átjáróra adott válaszok `must-revalidate` Cache Control direktívát.                                                                                                                                                                                                                      | Nem       | true              |
| változó – fejlesztő              | Az [előfizetési kulcsra](https://docs.microsoft.com/azure/api-management/api-management-subscriptions)vonatkozó válaszok gyorsítótárazásához állítsa `true` értékre.                                                                                                                                                                                                                                                                                                         | Igen      |         False (Hamis)          |
| változó – fejlesztői csoportok       | Állítsa `true` értékre a válaszok gyorsítótárazása felhasználónkénti [csoportonként](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups).                                                                                                                                                                                                                                                                                                             | Igen      |       False (Hamis)            |

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirend fejezetei:** bejövő
- **Házirend-hatókörök:** az összes hatókör

## <a name="StoreToCache"></a>Tárolás a gyorsítótárba
A `cache-store` házirend a megadott gyorsítótár-beállításoknak megfelelően gyorsítótárazza a válaszokat. Ezt a szabályzatot olyan esetekben lehet alkalmazni, amikor a válasz tartalma egy adott időszakban statikus marad. A válasz gyorsítótárazása csökkenti a háttér-webkiszolgálón kiszabott sávszélesség-és feldolgozási követelményeket, és csökkenti az API-felhasználók által észlelt késéseket.

> [!NOTE]
> Ennek a szabályzatnak szerepelnie kell egy megfelelő [beolvasási gyorsítótár-](api-management-caching-policies.md#GetFromCache) házirenddel.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Példa házirend-kifejezések használatával
Ez a példa azt mutatja be, hogyan konfigurálhatja API Management válasz gyorsítótárazási időtartamát, amely megfelel a háttérrendszer által a támogatott `Cache-Control` direktíva által megadott válasz-gyorsítótárazásnak. A szabályzat konfigurálásának és használatának bemutatását lásd: a [Cloud Cover 177-es epizódja: további API Management szolgáltatások a Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 25:25-es gyors előretekeréssel.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

További információ: [Policy Expressions](api-management-policy-expressions.md) and [Context változó](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|gyorsítótár-tároló|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

| Name (Név)             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | A gyorsítótárazott bejegyzések a másodpercben megadott élettartama.                                                                                                                                                                                                                                                                                                   | Igen      | N/A               |

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirend fejezetei:** kimenő
- **Házirend-hatókörök:** az összes hatókör

## <a name="GetFromCacheByKey"></a>Érték lekérése a gyorsítótárból
A `cache-lookup-value` házirend használatával hajtsa végre a gyorsítótár-keresést kulcs alapján, és egy gyorsítótárazott értéket ad vissza. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában egy házirend-kifejezés használatával adható meg.

> [!NOTE]
> Ennek a szabályzatnak megfelelő [tárolási értékkel kell rendelkeznie a gyorsítótár-](#StoreToCacheByKey) házirendben.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Példa
A szabályzattal kapcsolatos további információkért és példákért tekintse [meg az egyéni gyorsítótárazás az Azure API Management-ban](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)című témakört.

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|cache-lookup-value|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

| Name (Név)             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| gyorsítótárazás – típus | Válasszon az attribútum következő értékei közül:<br />- `internal` a beépített API Management cache használatára,<br />- `external` a külső gyorsítótár használatához a külső [Azure cache használata az Azure-ban történő Redis az Azure-ban API Management](api-management-howto-cache-external.md),<br />Ha más módon konfigurált vagy belső gyorsítótárat használ, - `prefer-external` külső gyorsítótár használatára. | Nem       | `prefer-external` |
| alapértelmezett érték    | Egy érték, amely akkor lesz hozzárendelve a változóhoz, ha a gyorsítótár kulcsának keresése kihagyott eredményt eredményezett. Ha nincs megadva ez az attribútum, `null` lesz hozzárendelve.                                                                                                                                                                                                           | Nem       | `null`            |
| kulcs              | A kereséshez használni kívánt gyorsítótár-kulcs értéke.                                                                                                                                                                                                                                                                                                                       | Igen      | N/A               |
| változó – név    | Annak a [környezeti változónak](api-management-policy-expressions.md#ContextVariables) a neve, amelyhez a keresett érték hozzá lesz rendelve, ha a keresés sikeres. Ha a keresés kimarad, a változó a `default-value` attribútum vagy `null`értékét fogja hozzárendelni, ha a `default-value` attribútum nincs megadva.                                       | Igen      | N/A               |

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén
- **Házirend-hatókörök:** az összes hatókör

## <a name="StoreToCacheByKey"></a>Tárolási érték a gyorsítótárban
A `cache-store-value` kulcs alapján hajtja végre a gyorsítótár-tárolást. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában egy házirend-kifejezés használatával adható meg.

> [!NOTE]
> Ennek a szabályzatnak megfelelő [Get értékkel kell rendelkeznie a gyorsítótár-](#GetFromCacheByKey) házirendből.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Példa
A szabályzattal kapcsolatos további információkért és példákért tekintse [meg az egyéni gyorsítótárazás az Azure API Management-ban](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)című témakört.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|cache-Store-Value|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

| Name (Név)             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| gyorsítótárazás – típus | Válasszon az attribútum következő értékei közül:<br />- `internal` a beépített API Management cache használatára,<br />- `external` a külső gyorsítótár használatához a külső [Azure cache használata az Azure-ban történő Redis az Azure-ban API Management](api-management-howto-cache-external.md),<br />Ha más módon konfigurált vagy belső gyorsítótárat használ, - `prefer-external` külső gyorsítótár használatára. | Nem       | `prefer-external` |
| duration         | Az érték a megadott időtartamnál (másodpercben) lesz gyorsítótárazva.                                                                                                                                                                                                                                                                                 | Igen      | N/A               |
| kulcs              | Gyorsítótár-kulcs az érték a alatt lesz tárolva.                                                                                                                                                                                                                                                                                                                   | Igen      | N/A               |
| érték            | A gyorsítótárazni kívánt érték.                                                                                                                                                                                                                                                                                                                                     | Igen      | N/A               |
### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén
- **Házirend-hatókörök:** az összes hatókör

### <a name="RemoveCacheByKey"></a>Érték eltávolítása a gyorsítótárból
A `cache-remove-value` törli a kulcsával azonosított gyorsítótárazott elemeket. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában egy házirend-kifejezés használatával adható meg.

#### <a name="policy-statement"></a>Szabályzati utasítás

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Példa

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|cache-remove-value|Gyökérelem.|Igen|

#### <a name="attributes"></a>Attribútumok

| Name (Név)             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| gyorsítótárazás – típus | Válasszon az attribútum következő értékei közül:<br />- `internal` a beépített API Management cache használatára,<br />- `external` a külső gyorsítótár használatához a külső [Azure cache használata az Azure-ban történő Redis az Azure-ban API Management](api-management-howto-cache-external.md),<br />Ha más módon konfigurált vagy belső gyorsítótárat használ, - `prefer-external` külső gyorsítótár használatára. | Nem       | `prefer-external` |
| kulcs              | A korábban gyorsítótárazott, a gyorsítótárból eltávolítandó érték kulcsa.                                                                                                                                                                                                                                                                                        | Igen      | N/A               |

#### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) használható.

- **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén
- **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>Következő lépések

További információ a házirendek használatáról:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](api-management-policy-reference.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Házirend-minták](policy-samples.md)
