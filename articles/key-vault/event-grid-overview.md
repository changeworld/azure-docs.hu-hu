---
title: Key Vault figyelése Azure Event Grid
description: Azure Event Grid használata Key Vault eseményekre való előfizetéshez
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 039b7e57c1f98368ab33f9f17e19a741dc9eb5f4
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184910"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Key Vault figyelése Azure Event Grid (előzetes verzió)

Key Vault a Event Grid-integráció jelenleg előzetes verzióban érhető el. Lehetővé teszi a felhasználók számára, hogy értesítést kapjanak, ha a Key vaultban tárolt titkos kód állapota megváltozott. Az állapotváltozás olyan titokként van definiálva, amely hamarosan lejár (a lejárat napjától számított 30 napon belül), egy lejárt titkos kulcsot vagy egy új verziót tartalmazó titkos kulcsot. Az értesítések mind a három titkos típus (kulcs, tanúsítvány és titkos) esetében támogatottak.

Az alkalmazások a modern kiszolgáló nélküli architektúrák segítségével reagálnak ezekre az eseményekre, anélkül, hogy bonyolult programkódot vagy költséges és nem hatékony lekérdezési szolgáltatásokat kellene igénybe venniük. Az eseményeket [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) az eseménykezelők, például a [Azure Functions](https://azure.microsoft.com/services/functions/), a [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját webhook számára is leküldik, és csak azért kell fizetnie, amit ténylegesen használ. További információ a díjszabásról: [Event Grid díjszabása](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Események és sémák Key Vault

Az Event Grid [esemény-előfizetések](../event-grid/concepts.md#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Key Vault események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Azonosíthatja Key Vault eseményt, mert a eventType tulajdonság a "Microsoft. kulcstartó" karakterrel kezdődik.

További információ: [Key Vault esemény sémája](../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Az értesítési események csak a titkok, kulcsok és tanúsítványok új verzióin aktiválódnak, és az értesítések fogadásához először elő kell fizetnie az eseményre a kulcstartóban.
> 
> A tanúsítványokra vonatkozó értesítési eseményeket csak akkor kapja meg, ha a tanúsítvány automatikusan megújul a tanúsítványhoz megadott szabályzatnak megfelelően.

## <a name="practices-for-consuming-events"></a>Az események felhasználásának eljárásai

Az Key Vault eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot követniük kell:

* Több előfizetés is konfigurálható az események ugyanahhoz az eseménykezelőhöz való továbbítására. Fontos, hogy ne tegyük fel, hogy az események egy adott forrásból származnak, de az üzenet témaköreinek ellenőrzésével biztosíthatja, hogy a várt kulcstartóból származik.
* Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
* Figyelmen kívül hagyhatja a nem értelmezhető mezőket.  Ez a gyakorlat segít megőrizni a jövőben esetlegesen hozzáadott új funkciókkal való ellenálló képességet.
* Az események adott eseményre való korlátozásához használja a "tárgy" előtagot és utótagot.

## <a name="next-steps"></a>További lépések

- [Azure Key Vault áttekintése](key-vault-overview.md)
- [Azure Event Grid áttekintése](../event-grid/overview.md)
- Útmutató: [Key Vault-események átirányítása az Automation Runbook (előzetes verzió)](event-grid-tutorial.md).
- Útmutató: [e-mailek fogadása a Key Vault titkos változásairól](event-grid-logicapps.md)
- [Azure Key Vault Azure Event Gridi esemény sémája (előzetes verzió)](../event-grid/event-schema-key-vault.md)
- [Az Azure Automation áttekintése](../automation/index.yml)
