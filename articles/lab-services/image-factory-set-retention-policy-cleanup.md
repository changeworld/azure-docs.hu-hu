---
title: Adatmegőrzési szabályzat beállítása a Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy adatmegőrzési szabályzatot, törölheti a gyárat, és kivonja a régi rendszerképeket a DevTest Labs szolgáltatásból
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759414"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Adatmegőrzési szabályzat beállítása Azure DevTest Labs
Ez a cikk az adatmegőrzési házirend beállítását, a gyár tisztítását és a régi rendszerképek kivonását ismerteti a szervezet többi DevTest-laborjában. 

## <a name="prerequisites"></a>Előfeltételek
A folytatás előtt győződjön meg arról, hogy követte ezeket a cikkeket:

- [Rendszerkép-előállító létrehozása](image-factory-create.md)
- [Rendszerkép-előállító futtatása az Azure DevOps](image-factory-set-up-devops-lab.md)
- [Egyéni lemezképek mentése és elosztása több laborba](image-factory-save-distribute-custom-images.md)

A következő elemeket már meg kell adni:

- A Azure DevTest Labsban található rendszerkép-előállító laborja
- Egy vagy több cél Azure DevTest Labs, ahol a gyári lemezképek terjesztése történik
- Egy Azure DevOps-projekt, amely a rendszerkép-előállító automatizálására szolgál.
- A szkripteket és a konfigurációt tartalmazó forráskód helye (példánkban a fent használt DevOps-projektben)
- Az Azure PowerShell-feladatok összeállításához szükséges Build-definíció
 
## <a name="setting-the-retention-policy"></a>Az adatmegőrzési szabály beállítása
A tisztítási lépések konfigurálása előtt határozza meg, hogy hány korábbi képet szeretne megőrizni a DevTest Labs szolgáltatásban. Ha követte a [rendszerkép futtatása az Azure DevOps-ről](image-factory-set-up-devops-lab.md) című cikket, különböző Build-változókat konfigurált. Egyikük **ImageRetention**volt. Ezt a változót `1`ra állíthatja, ami azt jelenti, hogy a DevTest Labs nem őrzi meg az egyéni lemezképek előzményeit. Csak a legújabb elosztott lemezképek lesznek elérhetők. Ha ezt a változót `2`re módosítja, a rendszer karbantartja a legújabb elosztott lemezképet és az előzőeket is. Ez az érték beállítható úgy, hogy meghatározza a DevTest Labs-ben karbantartani kívánt korábbi rendszerképek számát.

## <a name="cleaning-up-the-factory"></a>A gyár tisztítása
A gyár tisztításának első lépéseként el kell távolítania az arany Rendszerképbeli virtuális gépeket a rendszerkép-gyárból. Ehhez a feladathoz ugyanúgy kell parancsfájlt végezni, mint az előző szkriptekhez. Első lépésként vegyen fel egy másik **Azure PowerShell** -feladatot a Build-definícióba az alábbi ábrán látható módon:

![PowerShell-lépés](./media/set-retention-policy-cleanup/powershell-step.png)

Ha az új feladat szerepel a listában, válassza ki az elemet, és adja meg az összes adatot az alábbi képen látható módon:

![A régi lemezképek PowerShell-feladat tisztítása](./media/set-retention-policy-cleanup/configure-powershell-task.png)

A parancsfájl paraméterei a következők: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Régi rendszerképek kivonása 
Ez a feladat eltávolítja a régi rendszerképeket, és csak a **ImageRetention** -Build változóval egyező előzményeket tart. Adjon hozzá egy további **Azure PowerShell** Build-feladatot a Build-definícióhoz. A Hozzáadás után válassza ki a feladatot, majd adja meg a részleteket az alábbi képen látható módon: 

![Régi rendszerképek kivonása PowerShell-feladat](./media/set-retention-policy-cleanup/retire-old-image-task.png)

A parancsfájl paraméterei a következők: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>A Build várólistája
Most, hogy elvégezte a Build-definíciót, egy új buildet várólistára helyez, hogy minden működik-e. Miután a Build sikeresen befejeződik, az új egyéni lemezképek megjelennek a cél laborban, és ha bejelöli a lemezkép-előállító labort, nem látja a kiépített virtuális gépeket. Továbbá ha további buildeket is várólistára helyez, a karbantartási feladatok a régi egyéni rendszerképeket a DevTest Labs szolgáltatásból kivonásával, a létrehozási változókban beállított megőrzési értékkel összhangban láthatják.

> [!NOTE]
> Ha a sorozat utolsó cikkének végén végrehajtotta a Build folyamatot, manuálisan törölje a rendszerkép-előállító laborban létrehozott virtuális gépeket, mielőtt új buildet hozna létre.  A manuális karbantartási lépésre csak akkor van szükség, ha mindent beállít, és ellenőrzi, hogy működik-e.



## <a name="summary"></a>Összefoglalás
Most már rendelkezik egy futó rendszerkép-előállítóval, amely egyéni rendszerképeket tud létrehozni és terjeszteni igény szerint a laborokban. Ezen a ponton csak a lemezképek megfelelő beállítására és a cél Labs azonosítására van szó. Ahogy azt az előző cikkben is említettük, a **konfigurációs** mappában található **Labs. JSON** fájl határozza meg, hogy mely képeket kell elérhetővé tenni a cél Labs-ben. Ha más DevTest Labs-t ad hozzá a szervezetéhez, egyszerűen hozzá kell adnia egy bejegyzést a Labs. JSON fájlhoz az új laborhoz.

Az új rendszerkép hozzáadása a gyárhoz is egyszerű. Ha egy új rendszerképet szeretne felvenni a gyárba, nyissa meg a [Azure Portal](https://portal.azure.com), navigáljon a Factory DevTest Labs szolgáltatáshoz, válassza a gombot egy virtuális gép hozzáadásához, majd válassza ki a kívánt Piactéri képet és összetevőket. Ahelyett, hogy kijelöli a **Létrehozás** gombot az új virtuális gép kiválasztásához, válassza a **Azure Resource Manager sablon megtekintése**lehetőséget, majd mentse a sablont egy. JSON-fájlként a tárház **GoldenImages** mappájába. A rendszerkép-előállító következő futtatásakor a rendszer létrehozza az egyéni rendszerképet.


## <a name="next-steps"></a>Következő lépések
1. [Ütemezze a Build/kiadást](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) a rendszerkép-előállító rendszeres futtatásához. Rendszeresen frissíti a gyár által generált képeket.
2. További arany-lemezképek készítése a gyár számára. Azt is megteheti, hogy összetevőket hoz létre a virtuálisgép-beállítási feladatok további részeinek parancsfájlokban való [létrehozásához](devtest-lab-artifact-author.md) , és tartalmazza a gyári lemezképekben található összetevőket.
4. Hozzon létre [külön Build/kiadást](/azure/devops/pipelines/overview?view=azure-devops-2019) a **DistributeImages** -szkript külön való futtatásához. Ezt a parancsfájlt akkor futtathatja, ha módosítja a Labs. JSON fájlt, és beolvassa a cél Labs-be másolt képeket anélkül, hogy újra létre kellene hoznia az összes lemezképet.

