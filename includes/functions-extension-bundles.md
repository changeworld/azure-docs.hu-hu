---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639135"
---
A kötési bővítmények telepítésének legegyszerűbb módja a [bővítmény-csomagok](../articles/azure-functions/functions-bindings-register.md#extension-bundles)engedélyezése. Ha engedélyezi a csomagokat, a rendszer automatikusan telepíti a bővítmények előre meghatározott készletét.

A bővítmények engedélyezéséhez nyissa meg a Host. JSON fájlt, és frissítse annak tartalmát, hogy az megfeleljen a következő kódnak:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```