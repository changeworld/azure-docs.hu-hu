---
title: Azure Monitor riasztásokra vonatkozó műveleti szabályok
description: Megtudhatja, hogyan konfigurálhatja és kezelheti a Azure Monitorban szereplő műveleti szabályokat.
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 42f8d9cd30caa48376cda049f6404aa897a6866c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668468"
---
# <a name="action-rules-preview"></a>Műveleti szabályok (előzetes verzió)

A műveleti szabályok segítségével meghatározhatja vagy letilthatja a műveleteket bármely Azure Resource Manager hatókörön (Azure-előfizetés, erőforráscsoport vagy cél-erőforrás). Számos szűrővel rendelkeznek, amelyek segítségével leszűkítheti az eljárni kívánt riasztási példányok meghatározott részhalmazát.

## <a name="why-and-when-should-you-use-action-rules"></a>Miért és mikor érdemes a műveleti szabályokat használni?

### <a name="suppression-of-alerts"></a>Riasztások letiltása

Számos esetben hasznos a riasztások által generált értesítések letiltása. Ezek a forgatókönyvek a tervezett karbantartási időszakokban a munkaidőn kívüli időpontokban való letiltásig terjedhetnek. A **ContosoVM** felelős csapat például a közelgő hétvégére vonatkozó riasztási értesítéseket szeretné letiltani, mivel a **ContosoVM** tervezett karbantartás alatt áll. 

Bár a csapat letilthatja az **ContosoVM** -on manuálisan konfigurált összes riasztási szabályt (és azt a karbantartás után is engedélyezheti), nem egyszerű folyamat. A műveleti szabályok segítenek a riasztások letiltásának megadásában a letiltási idő rugalmas konfigurálásával. Az előző példában a csapat meghatározhat egy műveleti szabályt a **ContosoVM** , amely letiltja a hétvégére vonatkozó összes riasztási értesítést.


### <a name="actions-at-scale"></a>Léptékű műveletek

Bár a riasztási szabályok segítenek meghatározni a riasztás előállításakor kiváltó műveleti csoportot, az ügyfeleknek gyakran közös műveleti csoportjuk van a műveleteik hatókörén belül. Például az erőforráscsoport **ContosoRG** felelős csapat valószínűleg ugyanazt a műveleti csoportot fogja meghatározni a **ContosoRG**-ben definiált összes riasztási szabályhoz. 

A műveleti szabályok segítenek leegyszerűsíteni ezt a folyamatot. A skálázási műveletek megadásával a konfigurált hatókörön létrehozott összes riasztás esetében aktiválható egy műveleti csoport. Az előző példában a csapat egyetlen műveleti szabályt is meghatározhat a **ContosoRG** , amely ugyanazt a műveleti csoportot fogja kiváltani a benne létrehozott összes riasztáshoz.

> [!NOTE]
> A műveleti szabályok jelenleg nem vonatkoznak Azure Service Health riasztásokra.

## <a name="configuring-an-action-rule"></a>Műveleti szabály konfigurálása

A szolgáltatáshoz a Azure Monitor **riasztások** kezdőlapján a **műveletek kezelése** lehetőségre kattintva férhet hozzá. Ezután válassza a **műveleti szabályok (előzetes verzió)** lehetőséget. A szabályokat a riasztások kezdőlapján a **műveleti szabályok (előzetes verzió)** lehetőség kiválasztásával érheti el.

![A Azure Monitor kezdőlapjának műveleti szabályai](media/alerts-action-rules/action-rules-landing-page.png)

Válassza az **+ új műveleti szabály**lehetőséget. 

![Új műveleti szabály hozzáadása](media/alerts-action-rules/action-rules-new-rule.png)

Azt is megteheti, hogy a riasztási szabály konfigurálása közben létrehoz egy műveleti szabályt.

![Új műveleti szabály hozzáadása](media/alerts-action-rules/action-rules-alert-rule.png)

Ekkor megjelenik a folyamat lap a műveleti szabályok létrehozásához. Konfigurálja a következő elemeket: 

![Új műveleti szabály létrehozási folyamata](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Hatókör

Először válassza ki a hatókört (Azure-előfizetés, erőforráscsoport vagy cél-erőforrás). Több hatókört is kijelölhet egy adott előfizetésen belül.

![Műveleti szabály hatóköre](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Szűrési feltételek

Emellett a riasztások adott részhalmazára szűkítheti a szűrőket is. 

A rendelkezésre álló szűrők a következők: 

* **Súlyosság**: az a lehetőség, hogy egy vagy több riasztási megszakítást válasszon ki. **Súlyosság = a Sev1** azt jelenti, hogy a műveleti szabály a Sev1 értékre beállított összes riasztásra érvényes.
* **Figyelő szolgáltatás**: egy szűrő a kezdeményező figyelő szolgáltatás alapján. Ez a szűrő is többször is kiválasztható. A (z) **"Application Insights" figyelési szolgáltatás** például azt jelenti, hogy a műveleti szabály minden Application Insights-alapú riasztás esetében alkalmazható.
* **Erőforrástípus**: egy adott erőforrástípus alapján megadott szűrő. Ez a szűrő is többször is kiválasztható. A (z) **"Virtual Machines" erőforrástípus** például azt jelenti, hogy a műveleti szabály minden virtuális gép esetében alkalmazható.
* **Riasztási szabály azonosítója**: adott riasztási szabályok szűrésének lehetősége a riasztási szabály Resource Manager-azonosítójának használatával.
* **Figyelési feltétel**: a riasztási példányok szűrője vagy **kilőtt** vagy **feloldva** , figyelő feltételként.
* **Leírás**: egy olyan regex (reguláris kifejezés) egyezés, amely a riasztási szabály részeként definiált karakterlánc-egyezést definiál a leíráshoz. Például a **Leírás tartalmazza** a "Prod" kifejezést, amely minden olyan riasztást tartalmaz, amely tartalmazza a "Prod" karakterláncot a leírásokban.
* **Riasztási környezet (hasznos adat)** : egy olyan regex-egyezés, amely meghatározza a riasztás hasznos adatainak a riasztás környezeti mezőiben szereplő karakterlánc-egyezést. Például a **riasztási környezet (hasznos adatok) a "Computer-01"** karakterláncot tartalmazza minden olyan riasztásnak, amelynek hasznos adatai tartalmazzák a "Computer-01" karakterláncot.

Ezeket a szűrőket egy másikkal együtt alkalmazza a rendszer. Ha például a **"= Virtual Machines** és **Súlyosság" = Sev0**értéket állítja be, akkor az összes **Sev0** -riasztás csak a virtuális gépeken van szűrve. 

![Műveleti szabály szűrői](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Mellőzés vagy műveleti csoport konfigurációja

Ezután konfigurálja a műveleti szabályt a riasztások letiltására vagy a műveleti csoport támogatására. Mindkettőt nem választhatja ki. A konfiguráció minden olyan riasztási példányon működik, amely megfelel a korábban definiált hatókörnek és szűrőknek.

#### <a name="suppression"></a>Elnyomása

Ha a **Mellőzés**lehetőséget választja, a műveletek és értesítések letiltásának időtartamát adja meg. Válasszon egyet az alábbi lehetőségek közül:
* **Mostantól (mindig)** : az összes értesítést határozatlan ideig letiltja.
* **Ütemezett időpontban**: az értesítések letiltását egy kötött időtartamon belül.
* **Ismétlődés**esetén: az értesítések napi, heti vagy havi rendszerességű ütemezéseit letiltja.

![Műveleti szabály letiltása](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Műveleti csoport

Ha a váltásban a **műveleti csoport** lehetőséget választja, akkor adjon hozzá egy meglévő műveleti csoportot, vagy hozzon létre újat. 

> [!NOTE]
> Egy műveleti szabállyal csak egy műveleti csoportot lehet hozzárendelni.

![Új műveleti szabály hozzáadása vagy létrehozása a műveleti csoport kiválasztásával](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Műveleti szabály részletei

Utolsó lépésként adja meg a következő adatokat a műveleti szabályhoz:
* Name (Név)
* Az erőforráscsoport, amelyben mentve van
* Leírás 

## <a name="example-scenarios"></a>Példaforgatókönyvek

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>1\. forgatókönyv: riasztások letiltása súlyosság alapján

A contoso minden hétvégén szeretné letiltani az összes Sev4-riasztás értesítéseit az előfizetési **ContosoSub** lévő összes virtuális gépen.

**Megoldás:** Műveleti szabály létrehozása a alábbiakkal:
* Hatókör = **ContosoSub**
* Szűrők
    * Súlyosság = **Sev4**
    * Erőforrás típusa = **Virtual Machines**
* Törlés az ismétlődési beállítással hetente, a **szombat** és a **vasárnap** bejelölve

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>2\. forgatókönyv: riasztások letiltása a riasztási környezet (hasznos adat) alapján

A contoso szeretné letiltani az értesítéseket minden, a **ContosoSub** -hez a **Computer-01** -hez létrehozott összes naplózási riasztáshoz, mert a karbantartási folyamatban van.

**Megoldás:** Műveleti szabály létrehozása a alábbiakkal:
* Hatókör = **ContosoSub**
* Szűrők
    * Szolgáltatás figyelése = **log Analytics**
    * A riasztási környezet (hasznos adat) tartalmazza a **számítógépet – 01**
* Letiltási beállítás **mostantól (mindig)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>3\. forgatókönyv: erőforráscsoport által definiált műveleti csoport

A contoso [metrikus riasztást adott meg egy előfizetési szinten](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). De meg szeretné határozni azokat a műveleteket, amelyek kifejezetten az erőforráscsoport **ContosoRG**létrehozott riasztásokhoz kapcsolódnak.

**Megoldás:** Műveleti szabály létrehozása a alábbiakkal:
* Hatókör = **ContosoRG**
* Nincsenek szűrők
* **ContosoActionGroup** beállított műveleti csoport

> [!NOTE]
> *A műveleti szabályokban és a riasztási szabályokban definiált műveleti csoportok egymástól függetlenül működnek, és nincs ismétlődés.* Ha a fentebb ismertetett forgatókönyvben egy műveleti csoport van megadva a riasztási szabályhoz, a művelet a műveleti szabályban definiált műveleti csoporttal együtt aktiválódik. 

## <a name="managing-your-action-rules"></a>A műveleti szabályok kezelése

A következő lista nézetből megtekintheti és kezelheti a műveleti szabályokat:

![Műveleti szabályok listája nézet](media/alerts-action-rules/action-rules-list-view.png)

Itt engedélyezheti, letilthatja vagy törölheti az egyes műveletek szabályait a mellette található jelölőnégyzet bejelölésével. A műveleti szabály kiválasztásakor megnyílik a konfigurációs lapja. Az oldalon a műveleti szabály definíciójának frissítése és engedélyezése vagy letiltása segít.

## <a name="best-practices"></a>Ajánlott eljárások

Az [eredmények száma](alerts-unified-log.md) lehetőséggel létrehozott riasztások a teljes keresési eredmény használatával hoznak létre egyetlen riasztási példányt (amely több számítógép között is terjedhet). Ebben a forgatókönyvben, ha egy műveleti szabály a **riasztási környezet (hasznos adattartalom)** szűrőt használja, akkor az a riasztási példányon működik, amíg van egyezés. A korábban ismertetett 2. forgatókönyvben, ha a generált naplózási riasztás keresési eredményei mind a **Computer-01** , mind a **Computer-02**karakterláncot tartalmazzák, a teljes értesítés le lesz tiltva. Nincs értesítés létrehozva a **Computer-02-** hez.

![Műveleti szabályok és naplók riasztásai (eredmények száma)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Ha a riasztásokat a műveleti szabályokkal szeretné legjobban használni, hozzon létre naplózási riasztásokat a [metrika mértékének](alerts-unified-log.md) beállításával. Ezzel a beállítással külön riasztási példányok jönnek létre a definiált csoport mező alapján. Ezt követően a 2. forgatókönyvben különálló riasztási példányok jönnek létre a **Computer-01** és a **Computer-02**esetében. A forgatókönyvben ismertetett műveleti szabály miatt a rendszer csak a **számítógép-01** értesítését letiltja. A **számítógép-02-** es értesítés a szokásos módon folytatódik.

![Műveleti szabályok és naplók riasztásai (eredmények száma)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>GYIK

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>A műveleti szabály konfigurálása közben szeretném megtekinteni az összes lehetséges átfedésben lévő művelet szabályát, így elkerülhetők az ismétlődő értesítések. Ez lehetséges?

Miután meghatározta a hatókört a műveleti szabály konfigurálása során, megtekintheti az ugyanazon hatókörön (ha van) átfedésben lévő műveleti szabályok listáját. Az átfedés az alábbi lehetőségek egyike lehet:

* Pontos egyezés: például a definiált műveleti szabály és az átfedésben lévő műveleti szabály ugyanahhoz az előfizetéshez van kapcsolva.
* Egy részhalmaz: például a definiált műveleti szabály egy előfizetésen belül van, és az átfedésben lévő műveleti szabály az előfizetésben található erőforráscsoporthoz van.
* Felülbíráló: például a definiált műveleti szabály egy erőforráscsoport, és az átfedésben lévő műveleti szabály az erőforráscsoportot tartalmazó előfizetésen található.
* Metszet: például a definiált műveleti szabály a **VM1** és a **VM2**, és az átfedésben lévő műveleti szabály a **VM2** és a **VM3**.

![Átfedésben lévő műveleti szabályok](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>A riasztási szabályok konfigurálása során tudnia kell, hogy vannak-e már definiált műveleti szabályok, amelyek az általam definiált riasztási szabály esetében működhetnek?

Miután meghatározta a riasztási szabály céljának erőforrását, megtekintheti az ugyanazon hatókörön (ha van) lévő műveleti szabályok listáját, ha a **műveletek** szakaszban a **konfigurált műveletek megtekintése** lehetőségre kattint. A lista a hatókörre vonatkozó következő forgatókönyvek alapján töltődik fel:

* Pontos egyezés: például a definiált riasztási szabály, és a műveleti szabály ugyanahhoz az előfizetéshez tartoznak.
* Egy részhalmaz: például a definiált riasztási szabály egy előfizetésen belül van, és a műveleti szabály az előfizetésben található erőforráscsoporthoz van.
* Felülbíráló: például a definiált riasztási szabály egy erőforráscsoport, és a műveleti szabály az erőforráscsoportot tartalmazó előfizetésen található.
* Metszet: például a definiált riasztási szabály a **VM1** és a **VM2**, a műveleti szabály pedig a **VM2** és a **VM3**.
    
![Átfedésben lévő műveleti szabályok](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Megtekinthetem a műveleti szabály által letiltott riasztásokat?

A [riasztások listája oldalon](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)kiválaszthatja az **elnyomási állapot**nevű további oszlopot is. Ha a riasztási példány értesítését a rendszer letiltotta, akkor az az állapot jelenik meg a listában.

![Letiltott riasztási példányok](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Ha van műveleti szabály egy műveleti csoporttal, és egy másik a letiltással aktív ugyanazon a hatókörön, mi történik?

A Mellőzés mindig elsőbbséget élvez ugyanazon a hatókörön.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Mi történik, ha két külön műveleti szabályban megfigyelt erőforrással rendelkezem? Kapok egy vagy két értesítést? A **VM2** például a következő esetben:

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

A VM1 és a VM3 összes riasztása esetén a Action Group AG1 egyszer aktiválódik. A **VM2**összes riasztása esetében a műveleti csoport AG1 kétszer aktiválódik, mert a műveleti szabályok nem ismétlik le a műveleteket. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Mi történik, ha egy erőforrást két külön műveleti szabályban figyeltem meg, és egy másikat a művelet közben letiltanak? A **VM2** például a következő esetben:

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

A VM1 összes riasztása esetén a Action Group AG1 egyszer aktiválódik. A VM2 és a VM3 összes riasztására vonatkozó műveleteket és értesítéseket a rendszer letiltja. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Mi történik, ha a riasztási szabály és az ugyanahhoz az erőforráshoz definiált műveleti szabály különböző műveleti csoportokat hív meg? A **VM1** például a következő esetben:

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
A VM1 összes riasztása esetén a Action Group AG1 egyszer aktiválódik. Ha a "rule1" riasztási szabályt aktiválták, akkor a AG2 is aktiválódik. A műveleti szabályokban és a riasztási szabályokban definiált műveleti csoportok egymástól függetlenül működnek, és nincs ismétlődés. 

## <a name="next-steps"></a>Következő lépések

- [További információ az Azure-beli riasztásokról](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
