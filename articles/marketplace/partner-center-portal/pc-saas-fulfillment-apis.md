---
title: SaaS-teljesítési API-k | Azure piactér
description: Bevezeti a teljesítési API-k azon verzióit, amelyek lehetővé teszik a SaaS-ajánlatok integrálását az Azure Marketplace-szel.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: ebfc278d09c244970df5807df1505295fe7016c4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819127"
---
# <a name="saas-fulfillment-apis"></a>SaaS Fulfillment API-k

A SaaS-teljesítési API-k lehetővé teszik a független szoftvergyártók számára az SaaS-alkalmazások integrálását az Azure Marketplace-szel. Ezek az API-k lehetővé teszik az ISV-alkalmazások számára az összes kereskedelmi támogatással rendelkező csatorna részvételét: közvetlen, partner által vezetett (viszonteladói) és mező-vezérelt.  Az Azure Marketplace-en a transacter SaaS-ajánlatok listázásához követelmény.

> [!WARNING]
> Az API jelenlegi verziója 2. verzió, amelyet minden új SaaS-ajánlathoz használni kell.  Az API 1. verziója elavult, és a rendszer fenntartja a meglévő ajánlatok támogatását.


## <a name="business-model-support"></a>Üzleti modell támogatása

Ez az API a következő üzleti modell-képességeket támogatja: képes vagy:

* Több csomagot is megadunk egy ajánlathoz. Ezek a csomagok különböző funkciókkal rendelkeznek, és eltérő díjszabással rendelkezhetnek.
* Adjon meg egy ajánlatot webhelyenként vagy felhasználónkénti számlázási modell alapján.
* Adja meg a havi és az éves (fizetős előzetes) számlázási lehetőségeket.
* Egy egyeztetett üzleti szerződés alapján privát díjszabást biztosíthat az ügyfeleknek.


## <a name="next-steps"></a>További lépések

Ha még nem tette meg, regisztráljon az SaaS-alkalmazást a [Azure Portal](https://ms.portal.azure.com) az [Azure ad-alkalmazás regisztrálása](./pc-saas-registration.md)című részben leírtak szerint.  Ezt követően használja az interfész legújabb verzióját a fejlesztéshez: [SaaS beteljesülés API 2-es verziója](./pc-saas-fulfillment-api-v2.md).
