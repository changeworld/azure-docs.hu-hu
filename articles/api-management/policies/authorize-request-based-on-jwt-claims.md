---
title: Példa API Management-szabályzat – hozzáférés engedélyezése JWT jogcímek alapján
titleSuffix: Azure API Management
description: Azure API Management-szabályzat – példa – bemutatja, hogyan lehet engedélyezni az adott HTTP-metódusokhoz való hozzáférést az JWT jogcímek alapján.
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
ms.openlocfilehash: 1b84854688fbdcc017b16698dfebfe54a7210110
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422188"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Hozzáférés engedélyezése JWT jogcímek alapján

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan lehet engedélyezni az adott HTTP-metódusokhoz való hozzáférést az JWT jogcímek alapján. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

