---
title: Példa API Management-szabályzat – válasz gyorsítótárazási időtartamának beállítása
titleSuffix: Azure API Management
description: Azure API Management-szabályzat – példa – bemutatja, hogyan állíthatja be a válasz-gyorsítótár időtartamát a háttér által küldött Cache-Control fejléc maxAge értéke használatával.
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
ms.openlocfilehash: 3101c5695272e8fa6b577ad313897cbc1fa29629
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442394"
---
# <a name="set-response-cache-duration"></a>Válasz-gyorsítótár időtartamának beállítása

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan állíthatja be a válasz-gyorsítótár időtartamát a háttér által küldött Cache-Control fejléc maxAge értéke használatával. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

