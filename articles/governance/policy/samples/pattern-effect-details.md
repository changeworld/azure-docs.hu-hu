---
title: 'Minta: egy szabályzat definíciójának hatásai'
description: Ez a Azure Policy minta példa arra, hogyan használható a házirend-definíció különböző hatásai.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: b86a24bc0af6c9bdd7b29bb0a931d6c78865218b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77173002"
---
# <a name="azure-policy-pattern-effects"></a>Azure Policy minta: hatások

Azure Policy számos [effektussal](../concepts/effects.md) rendelkezik, amelyek meghatározzák, hogy a szolgáltatás hogyan működik a nem megfelelő erőforrásokra. Bizonyos effektusok egyszerűek, és nem igényelnek további tulajdonságokat a házirend-definícióban, míg másoknak több tulajdonságra van szükségük.

## <a name="sample-1-simple-effect"></a>1\. minta: egyszerű hatás

Ez a szabályzat-definíció ellenőrzi, hogy a **TagName** paraméterben meghatározott címke létezik-e a kiértékelt erőforráson. Ha a címke még nem létezik, a [módosítás](../concepts/effects.md#modify) hatása aktiválódik, hogy hozzáadja a címkét a **tagValue**paraméterben szereplő értékkel.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>1\. példa: magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="30-34":::

A **módosítási** effektushoz a **' policyrule osztály. then. details** blokk szükséges, amely meghatározza a **roleDefinitionIds** és a **műveleteket**. Ezek a paraméterek tájékoztatják Azure Policy milyen szerepkörökre van szükség a címke hozzáadásához és az erőforrás szervizeléséhez, valamint a művelet végrehajtásának **módosításához** . Ebben a példában a **művelet** _hozzáadva_ , a paraméterek pedig a címke és az érték beállítására szolgálnak.

## <a name="sample-2-complex-effect"></a>2\. minta: összetett hatás

Ez a házirend-definíció az egyes virtuális gépeket naplózza, ha a paraméterek **közzétevője** és **típusa mezőben**definiált bővítmény nem létezik. A [auditIfNotExists](../concepts/effects.md#auditifnotexists) használatával vizsgálja meg a virtuális géphez kapcsolódó erőforrásokat, és ellenőrizze, hogy létezik-e olyan példány, amely megfelel a megadott paramétereknek. Ez a példa a **bővítmények** típusát ellenőrzi.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>2\. minta: magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

A **auditIfNotExists** hatásához a **' policyrule osztály. then. details** blokkot kell megadnia, és meg kell adnia a keresett **típust** és a **existenceCondition** is. A **existenceCondition** a házirend nyelvi elemeit, például a [logikai operátorokat](../concepts/definition-structure.md#logical-operators)használja annak megállapítására, hogy létezik-e egyező kapcsolódó erőforrás. Ebben a példában az egyes [aliasok](../concepts/definition-structure.md#aliases) által ellenőrzött értékek a paraméterekben vannak meghatározva.

## <a name="next-steps"></a>Következő lépések

- Tekintse át [a többi mintázatot és a beépített definíciókat](./index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).