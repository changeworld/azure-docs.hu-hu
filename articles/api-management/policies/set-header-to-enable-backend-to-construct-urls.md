---
title: Azure API Management-házirend minta – továbbított fejléc hozzáadása | Microsoft Docs
description: Azure API Management-szabályzat – példa – bemutatja, hogyan adhat hozzá továbbított fejlécet a bejövő kérelemben, hogy a háttér-API megfelelő URL-címeket lehessen létrehozni.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: e525029ae8eab086d11126a4e18958423e207aa1
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067507"
---
# <a name="add-a-forwarded-header"></a>Továbbított fejléc hozzáadása

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan adhat hozzá továbbított fejlécet a bejövő kérelemben, hogy a háttérrendszer API-ját megfelelő URL-címeket lehessen létrehozni. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="code"></a>Kód

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)
