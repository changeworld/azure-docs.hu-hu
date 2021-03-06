---
title: Minta API Management-szabályzat – az IP-cím szűrése Application Gateway használatakor
titleSuffix: Azure API Management
description: Azure API Management-szabályzat – példa – bemutatja, hogyan szűrheti a kérések IP-címét a Application Gateway használatakor.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942476"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>A kérés IP-címének szűrése Application Gateway használatakor

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan történik a szűrés a kérés IP-címén, ha a API Management példány egy Application Gateway vagy más közvetítőn keresztül érhető el. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Hozzáférési korlátozási szabályzatok](../api-management-access-restriction-policies.md)
+ [Házirend-minták](../policy-samples.md)
