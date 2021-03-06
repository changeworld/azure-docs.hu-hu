---
title: 'Minta: egy házirend-definícióban szereplő Count operátor'
description: Ez a Azure Policy minta azt szemlélteti, hogyan használható a Count operátor egy házirend-definícióban.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 88c2d1083a92732ac56ca4d6da7087cc4220d9a5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172946"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure Policy minta: a Count operátor

A [Count](../concepts/definition-structure.md#count) operátor a \[\*\] alias tagjait értékeli ki.

## <a name="sample-policy-definition"></a>Példa a házirend-definícióra

Ez a házirend-definíció [naplózza](../concepts/effects.md#audit) a bejövő RDP protokoll (RDP) forgalom engedélyezésére konfigurált hálózati biztonsági csoportokat.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Magyarázat

A **Count** operátor alapvető összetevői a _mezők_, _ahol_és a feltételt. Mindegyik az alábbi kódrészletben van kiemelve.

- a _mező_ a tagok kiértékeléséhez szükséges [aliasok](../concepts/definition-structure.md#aliases) számát mutatja. Itt tekintjük meg a hálózati biztonsági csoport **securityRules\[\*\]** alias _tömbjét_ .
- _ahol_ a a szabályzat nyelvét használja annak meghatározásához, hogy mely _tömb_ tagjai felelnek meg a feltételeknek. Ebben a példában egy **allOf** logikai operátor az alias _tömb_ tulajdonságainak három különböző feltétel-értékelését csoportosítja: _irány_, _hozzáférés_és _destinationPortRange_.
- Ebben a példában a Count feltétel **nagyobb**. A Count értéke TRUE (igaz), ha az alias _tömb_ egy vagy több tagja megegyezik a _Where_ záradékkal.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Következő lépések

- Tekintse át [a többi mintázatot és a beépített definíciókat](./index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).