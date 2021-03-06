---
title: Azure API Management fejlesztői portál sablonjai | Microsoft Docs
description: Megtudhatja, hogyan szabhatja testre a fejlesztői portál oldalain található tartalmakat az Azure API Management sablonjainak használatával.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 5189f3d8-2a4c-4dc8-ab19-11c7df0114d4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 9bbb7ef46fbde881c8968128870d24a03b046c23
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176789"
---
# <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai

Az Azure API Management lehetővé teszi a fejlesztői portál oldalai tartalmának testreszabását a tartalmukat konfiguráló sablonok használatával. A [DotLiquid](http://dotliquidmarkup.org/) szintaxis és az Ön által választott szerkesztő használatával, például a [tervezők DotLiquid](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), valamint a honosított [karakterlánc-erőforrások](api-management-template-resources.md#strings), a karakterjel- [erőforrások](api-management-template-resources.md#glyphs)és a [lapok vezérlőelemek](api-management-page-controls.md)széles választékával nagy rugalmasságot biztosíthat a konfiguráláshoz a lapok tartalmának megjelenítése, ahogy az a sablonok használatával illik.  
  
További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

##  <a name="DeveloperPortalTemplates"></a>Fejlesztői portál sablonjai  
  
-   [API-k](api-management-api-templates.md)  
    -   [API-lista](api-management-api-templates.md#APIList)  
    -   [Művelet](api-management-api-templates.md#Product)  
    -   [Kódminták](api-management-api-templates.md#CodeSamples)  
        -   [Curl](api-management-api-templates.md#Curl)  
        -   [C#](api-management-api-templates.md#CSharp)  
        -   [Java](api-management-api-templates.md#Stub)  
        -   [JavaScript](api-management-api-templates.md#JavaScript)  
        -   [C célkitűzés](api-management-api-templates.md#ObjectiveC)  
        -   [PHP](api-management-api-templates.md#PHP)  
        -   [Python](api-management-api-templates.md#Python)  
        -   [Ruby](api-management-api-templates.md#Ruby)  
-   [Termékek](api-management-product-templates.md)  
    -   [Terméklista](api-management-product-templates.md#ProductList)  
    -   [Termék](api-management-product-templates.md#Product)  
-   [Alkalmazások](api-management-application-templates.md)  
    -   [Alkalmazások listája](api-management-application-templates.md#ProductList)  
    -   [Alkalmazás](api-management-application-templates.md#Application)  
-   [Hibák](api-management-issue-templates.md)  
    -   [Problémák listája](api-management-issue-templates.md#IssueList)  
-   [Felhasználói profil](api-management-user-profile-templates.md)  
    -   [Profil](api-management-user-profile-templates.md#Profile)  
    -   [Előfizetések](api-management-user-profile-templates.md#Subscriptions)  
    -   [Alkalmazások](api-management-user-profile-templates.md#Applications)  
    -   [Fiókadatok frissítése](api-management-user-profile-templates.md#UpdateAccountInfo)  
-   [Oldalak](api-management-page-templates.md)  
    -   [bejelentkezés](api-management-page-templates.md#SignIn)  
    -   [Regisztráció](api-management-page-templates.md#SignUp)  
    -   [A lap nem található](api-management-page-templates.md#PageNotFound)

## <a name="next-steps"></a>Következő lépések  

+ [Sablonreferencia](api-management-developer-portal-templates-reference.md)  
+ [Adatmodell-referencia](api-management-template-data-model-reference.md)  
+ [Oldalvezérlők](api-management-page-controls.md)  
+ [Sablonerőforrások](api-management-template-resources.md)