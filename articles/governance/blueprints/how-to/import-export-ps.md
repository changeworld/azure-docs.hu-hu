---
title: Tervrajzok importálása és exportálása a PowerShell-lel
description: Megtudhatja, hogyan dolgozhat a terv-definíciók kóddal. Az exportálási és importálási parancsok használatával megoszthatja, vezérelheti és felügyelheti őket.
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: fc7b9818072665d79deaf8a456868943e8428730
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873199"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Terv-definíciók importálása és exportálása a PowerShell-lel

Az Azure-tervezetek teljes mértékben kezelhetők Azure Portalon keresztül. Ahogy a szervezetek előzetesen használják a tervrajzokat, a tervekben szereplő definíciókat felügyelt kódként kell elkezdeni. Ezt a koncepciót gyakran a Code (IaC) infrastruktúrának nevezzük. A tervrajz-definíciók kódjaként való kezelése további előnyökkel jár, mint a Azure Portal kínálata. Ezek az előnyök a következők:

- Tervezet-definíciók megosztása
- A terv definícióinak biztonsági mentése
- Tervrajz-definíciók újrafelhasználása különböző bérlők vagy előfizetések esetén
- A terv definícióinak elhelyezése a forrás vezérlőelemben
  - Tervrajz-definíciók automatizált tesztelése tesztkörnyezetben
  - Folyamatos integráció és folyamatos üzembe helyezés (CI/CD) folyamatok támogatása

Az Ön igényeinek megfelelően a tervrajz-definíciók kódként való kezelésének előnyei is vannak. Ez a cikk bemutatja, hogyan használható az `Import-AzBlueprintWithArtifact` és a `Export-AzBlueprintWithArtifact` parancs az az [. Blueprint](https://powershellgallery.com/packages/Az.Blueprint/) modulban.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk az Azure-tervezetek mérsékelt munkaismeretét feltételezi. Ha még nem tette meg, végezze el a következő cikkeket:

- [Terv létrehozása a portálon](../create-blueprint-portal.md)
- További információ az [üzembe helyezési szakaszokról](../concepts/deployment-stages.md) és [a terv életciklusáról](../concepts/lifecycle.md)
- Tervrajz-definíciók és-hozzárendelések [létrehozása](../create-blueprint-powershell.md) és [kezelése](./manage-assignments-ps.md) a PowerShell-lel

Ha még nincs telepítve, kövesse az [az. Blueprint modul hozzáadása](./manage-assignments-ps.md#add-the-azblueprint-module) című témakör utasításait az az **. Blueprint** modul telepítéséhez és ellenőrzéséhez a PowerShell-Galéria.

## <a name="folder-structure-of-a-blueprint-definition"></a>Tervrajz definíciójának mappastruktúrát

A tervrajzok exportálásának és importálásának megkezdése előtt nézzük meg, hogyan épülnek fel a terv definícióját alkotó fájlok. A terv definícióját a saját mappájába kell menteni.

> [!IMPORTANT]
> Ha nem ad át értéket az `Import-AzBlueprintWithArtifact`-parancsmag **Name** paraméterének, a rendszer a terv definícióját tároló mappa nevét használja.

A terv definíciója mellett, amelynek `blueprint.json`nak kell lennie, a terv definícióját alkotó összetevők. Minden összetevőnek a `artifacts`nevű almappába kell esnie.
Együttesen a terv definíciójának struktúráját a mappákban található JSON-fájloknak a következőképpen kell kinéznie:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>A terv definíciójának exportálása

A terv definíciójának exportálásának lépései egyszerűek. A terv definíciójának exportálása hasznos lehet a forrás-vezérlőelemek megosztására, biztonsági mentésére vagy behelyezésére.

- **Terv** [kötelező]
  - Meghatározza a terv definícióját
  - A Reference objektum beolvasásához használja a `Get-AzBlueprint`
- **OutputPath** [kötelező]
  - Megadja azt az elérési utat, amellyel a terv-definíció JSON-fájljait menteni kell
  - A kimeneti fájlok egy olyan almappában találhatók, amely a terv definíciójának nevét adja meg
- **Verzió** (nem kötelező)
  - Megadja a kimeneti verziót, ha a **terv** hivatkozási objektuma több verzióra mutató hivatkozásokat tartalmaz.

1. Tekintse át az előfizetésnek a `{subId}`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. A `Export-AzBlueprintWithArtifact` parancsmag használatával exportálja a megadott terv definícióját:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>A terv definíciójának importálása

Ha az [exportált terv definíciója](#export-your-blueprint-definition) vagy manuálisan létrehozott terv definíciója szerepel a [szükséges mappák struktúrájában](#folder-structure-of-a-blueprint-definition), akkor importálhatja a terv definícióját egy másik felügyeleti csoportba vagy előfizetésbe.

A beépített tervrajzok leírását a [Azure Blueprint GitHub](https://github.com/Azure/azure-blueprints/tree/master/samples/builtins)-tárházban találhatja meg.

- **Név** [kötelező]
  - Megadja az új terv definíciójának nevét
- **InputPath** [kötelező]
  - Meghatározza a terv definíciójának létrehozási útvonalát a következőből:
  - Meg kell egyeznie a [szükséges mappastruktúrát](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (nem kötelező)
  - A felügyeleti csoport azonosítója, amelybe menteni szeretné a terv definícióját, hogy ha nem az aktuális környezeti hiba
  - Meg kell adni a **ManagementGroupId** vagy a **SubscriptionId** értéket.
- **SubscriptionId** (nem kötelező)
  - Az előfizetés-azonosító a terv definíciójának mentéséhez, ha nem az aktuális környezet alapértelmezett értéke
  - Meg kell adni a **ManagementGroupId** vagy a **SubscriptionId** értéket.

1. A megadott terv definíciójának importálásához használja az `Import-AzBlueprintWithArtifact` parancsmagot:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

A terv definíciójának importálása után [rendelje hozzá a PowerShell](./manage-assignments-ps.md#create-blueprint-assignments)-lel.

A speciális tervrajz-definíciók létrehozásával kapcsolatos információkért tekintse meg a következő cikkeket:

- [Statikus és dinamikus paraméterek](../concepts/parameters.md)használata.
- Szabja testre a [terv sorrendjét](../concepts/sequencing-order.md).
- Az üzembe helyezések elleni védelem a [terv erőforrás-zárolásával](../concepts/resource-locking.md).
- [A tervrajzokat kódként kezelheti](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).