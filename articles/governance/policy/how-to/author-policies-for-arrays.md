---
title: Szerzői szabályzatok a tömb tulajdonságaihoz az erőforrásokon
description: Megismerheti a tömb paramétereinek és a tömb nyelvi kifejezéseknek a használatát, kiértékelheti a [*] aliast, és hozzáfűzheti az elemeket Azure Policy definíciós szabályokkal.
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280663"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Az Azure-erőforrások tömb tulajdonságainak szerzői szabályzatai

A Azure Resource Manager tulajdonságok általában karakterláncként és logikai értékként vannak definiálva. Ha egy-a-többhöz kapcsolat létezik, az összetett tulajdonságok tömbként vannak definiálva. Azure Policy a tömbök számos különböző módon használatosak:

- Egy [definíciós paraméter](../concepts/definition-structure.md#parameters)típusa több beállítás megadásához
- Egy házirend- [szabály](../concepts/definition-structure.md#policy-rule) része a vagy a **in** **notIn** feltételek használatával
- Egy olyan házirend-szabály része, amely kiértékeli a [\[\*\] aliast](../concepts/definition-structure.md#understanding-the--alias) a kiértékeléshez:
  - Olyan forgatókönyvek, mint a **none** **, sem**vagy **az összes**
  - Összetett forgatókönyvek **darabszámmal**
- Meglévő tömb lecseréléséhez vagy hozzáadásához a [hozzáfűzési effektusban](../concepts/effects.md#append)

Ez a cikk a Azure Policy egyes használatát ismerteti, és számos példát tartalmaz.

## <a name="parameter-arrays"></a>Paraméterek tömbök

### <a name="define-a-parameter-array"></a>Paraméter-tömb definiálása

A paraméter tömbként való meghatározása lehetővé teszi a szabályzat rugalmasságát, ha egynél több értékre van szükség.
Ez a szabályzat-definíció lehetővé teszi, hogy a **allowedLocations** paraméter egyetlen helye legyen, és az alapértelmezett érték a _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

A **Type** _karakterlánc_volt, csak egy érték állítható be a szabályzat kiosztásakor. Ha ez a szabályzat hozzá van rendelve, a hatókörben lévő erőforrások csak egyetlen Azure-régión belül engedélyezettek. A legtöbb szabályzat-definíciónak lehetővé kell tennie a jóváhagyott beállítások listáját, például a _eastus2_, a _eastus_és a _westus2_engedélyezését.

Ha a házirend-definíciót több beállítás engedélyezéséhez szeretné létrehozni, használja a _tömb_ **típusát**. Ugyanezt a szabályzatot a következőképpen lehet újraírni:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> A házirend-definíció mentése után a paraméter **Type** tulajdonsága nem módosítható.

Ez az új paraméter-definíció egynél több értéket vesz igénybe a szabályzat-hozzárendelés során. Ha a tömb tulajdonsága **allowedValues** van definiálva, a hozzárendelés során elérhető értékek tovább korlátozódnak az előre definiált lehetőségek listájára. A **allowedValues** használata nem kötelező.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Értékek átadása egy paraméter-tömbnek a hozzárendelés során

Ha a házirendet a Azure Portalon keresztül rendeli hozzá, a _tömb_ **típusú** paraméterek egyetlen szövegmezőként jelennek meg. A tipp a "use; az értékek elkülönítéséhez. (pl.: London; New York) ". Ha át szeretné adni a _eastus2_, a _eastus_és a _westus2_ engedélyezett tárolási értékeit a paraméternek, használja a következő karakterláncot:

`eastus2;eastus;westus2`

A paraméter értékének formátuma eltérő az Azure CLI, Azure PowerShell vagy a REST API használatakor. Az értékeket egy JSON-karakterlánc adja át, amely tartalmazza a paraméter nevét is.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Ha ezt a sztringet az egyes SDK-kal szeretné használni, használja a következő parancsokat:

- Azure CLI: parancs [az Policy hozzárendelés-létrehozás](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) paraméter **-paraméterekkel**
- Azure PowerShell: parancsmag [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) paraméterrel **PolicyParameter**
- REST API: a _put_ [create](/rest/api/resources/policyassignments/create) művelet a kérelem törzsének részeként a **Tulajdonságok. Parameters** tulajdonság értékeként

## <a name="policy-rules-and-arrays"></a>Házirend-szabályok és tömbök

### <a name="array-conditions"></a>Tömb feltételei

A házirend-szabály azon [feltételei](../concepts/definition-structure.md#conditions) , amelyekben a _tömb_
a paraméter **típusa** használható, `in` és `notIn`ra korlátozódik. A következő házirend-definíciót a feltétel `equals` példaként vegye fel:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

A házirend-definíciónak a Azure Portalon keresztüli létrehozására tett kísérlet a következő hibaüzenetet eredményezi:

- "A (z) {GUID} szabályzatot érvényesítési hibák miatt nem lehetett paraméterbe állítani. Ellenőrizze, hogy a házirend-paraméterek megfelelően vannak-e megadva. A belső kivétel "a nyelv kifejezésének" [parameters (' allowedLocations ')] típusának "Array" típusúnak kell lennie, a várt típus a "string". "

A feltétel várt **típusa** `equals` _karakterlánc_. Mivel a **allowedLocations** **típus** _tömbként_van definiálva, a házirend-végrehajtó kiértékeli a nyelvi kifejezést, és eldönti a hibát. A `in` és `notIn` feltétellel a irányelvmodul a Language kifejezésben a **típus** _tömböt_ várja. A hibaüzenet megoldásához módosítsa `equals` `in` vagy `notIn`re.

### <a name="evaluating-the--alias"></a>[*] Alias kiértékelése

Azok az aliasok, amelyek neve **\[\*\]** a nevükhöz csatolva jelzi, hogy a **típus** _tömb_. A teljes tömb értékének kiértékelése helyett a **\[\*\]** lehetővé teszi, hogy a tömb egyes elemeit egyenként, a logikai és a köztük lévő elemeket is kiértékelje. Az elemek kiértékelésének három szabványos forgatókönyve hasznos a következőben: _none_, _any_, vagy _minden_ elem egyezés. Összetett forgatókönyvek esetén használja a [darabszámot](../concepts/definition-structure.md#count).

A **házirend-végrehajtó** elindítja a **hatást** , és csak akkor, ha az **IF** -szabály igaz értéket ad vissza.
Ez a tény fontos, hogy tisztában legyen azzal, hogyan **\[\*** a tömb egyes elemeinek kiértékelése \].

Az alábbi forgatókönyv-táblázathoz tartozó példa házirend-szabály:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

A **ipRules** tömb az alábbi forgatókönyv-táblázat esetében a következő:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Az alábbi példában szereplő összes feltételnél cserélje le a `<field>`t a `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`ra.

A következő eredmények a feltétel és a példaként megadott házirend-szabály kombinációjának eredményei, valamint a fenti meglévő értékek tömbje:

|Állapot |Eredmény | Forgatókönyv |Magyarázat |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nincs |Nincs egyezés |Az egyik tömb elem hamis (127.0.0.1! = 127.0.0.1) és egy True (127.0.0.1! = 192.168.1.1) értéket ad vissza, így a **notEquals** feltétel _hamis_ , és a hatás nincs aktiválva. |
|`{<field>,"notEquals":"10.0.4.1"}` |Házirend hatása |Nincs egyezés |Mindkét tömb elem igaz értéket (10.0.4.1! = 127.0.0.1 és 10.0.4.1! = 192.168.1.1) is kiértékel, így a **notEquals** feltétel _igaz_ , és a hatás aktiválódik. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Házirend hatása |Egy vagy több egyezés |Az egyik tömb elem hamis (127.0.0.1! = 127.0.0.1) és egy True (127.0.0.1! = 192.168.1.1) értéket ad vissza, így a **notEquals** feltétel _hamis_. A logikai operátor igaz (**nem** _hamis) értéket_ad vissza, ezért a hatás aktiválódik. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nincs |Egy vagy több egyezés |Mindkét tömb elem igaz értéket (10.0.4.1! = 127.0.0.1 és 10.0.4.1! = 192.168.1.1) is kiértékel, így a **notEquals** feltétel _igaz_. A logikai operátor hamis (**nem** _igaz_) értéket ad vissza, ezért a hatás nincs aktiválva. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Házirend hatása |Nem minden egyezés |Az egyik tömb elem igaz értéket (127.0.0.1 = = 127.0.0.1) és egy hamis (127.0.0.1 = = 192.168.1.1) értéket ad vissza, így az **Equals** feltétel _hamis_. A logikai operátor igaz (**nem** _hamis) értéket_ad vissza, ezért a hatás aktiválódik. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Házirend hatása |Nem minden egyezés |A tömb elemeinek értéke false (10.0.4.1 = = 127.0.0.1 és 10.0.4.1 = = 192.168.1.1), így az **Equals** feltétel _hamis_. A logikai operátor igaz (**nem** _hamis) értéket_ad vissza, ezért a hatás aktiválódik. |
|`{<field>,"Equals":"127.0.0.1"}` |Nincs |Összes egyezés |Az egyik tömb elem igaz értéket (127.0.0.1 = = 127.0.0.1) és egy hamis (127.0.0.1 = = 192.168.1.1) értéket ad vissza, így az **egyenlő** állapot _hamis_ , és a hatás nem aktiválódik. |
|`{<field>,"Equals":"10.0.4.1"}` |Nincs |Összes egyezés |Mindkét tömb elem hamis (10.0.4.1 = = 127.0.0.1 és 10.0.4.1 = = 192.168.1.1) értéket ad eredményként, így az **egyenlő** állapot _hamis_ , és a hatás nem aktiválódik. |

## <a name="the-append-effect-and-arrays"></a>A hozzáfűzési effektus és tömbök

A [hozzáfűzési effektus](../concepts/effects.md#append) eltérő lehet attól függően, hogy a **részletek. mező** **\[\*\]** alias-e.

- Ha nem **\[\*\]** aliast, a Hozzáfűzés a teljes tömböt a **Value** tulajdonsággal helyettesíti.
- Ha egy **\[\*\]** aliast, a Hozzáfűzés hozzáadja az **Value** tulajdonságot a meglévő tömbhöz, vagy létrehoz egy új tömböt.

További információ: [hozzáfűzési példák](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
