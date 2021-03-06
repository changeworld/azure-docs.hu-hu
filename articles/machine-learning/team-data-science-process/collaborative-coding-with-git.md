---
title: Együttműködésen alapuló kódolás a Git - csoportos adatelemzési folyamat
description: Együttműködési kód fejlesztés a Git használata az Agilis tervezéshez adatelemzési projektek módjáról.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0708e395eff90ff5b889c05f0fd5e7a98205c5bc
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721897"
---
# <a name="collaborative-coding-with-git"></a>Együttműködésen alapuló kódolás a Gittel

Ez a cikk azt ismerteti, hogyan használható a git az adatelemzési projektek együttműködési kód-fejlesztési keretrendszereként. A cikk azt ismerteti, hogyan lehet kódot csatolni az Azure-adattárakban az Azure-táblák [agilis fejlesztési](agile-development.md) munkaelemeihez, a kódok felülvizsgálatához, valamint a lekéréses kérelmek létrehozásához és egyesítéséhez.

## <a name='Linkaworkitemwithagitbranch-1'></a>Munkaelem összekapcsolása egy Azure Repos-ágra 

Az Azure DevOps kényelmes megoldást kínál az Azure-táblák felhasználói történetének vagy munkaelemének egy Azure Repos git-tárházbeli ágban való összekapcsolására. A felhasználói történetet vagy a feladatot közvetlenül a hozzá társított kódhoz társíthatja. 

Ha egy munkaelemet új ágra szeretne összekapcsolni, válassza a munkaelem melletti **három pontot** ( **..** .), majd a helyi menüben görgessen az **új ág**lehetőségre, és válassza ki a következőt:.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

A **ág létrehozása** párbeszédpanelen adja meg az új ág nevét, valamint az alap Azure Repos git-tárházat és ágat. Az alaptárháznak ugyanabban az Azure DevOps-projektben kell lennie, mint a munkaelemnek. Az alapág lehet a fő ág vagy egy másik meglévő ág. Válassza az **ág létrehozása**lehetőséget. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Létrehozhat egy új ágat is a következő git bash-parancs használatával Windows vagy Linux rendszeren:

```bash
git checkout -b <new branch name> <base branch name>

```
Ha nem ad meg \<alapág nevet >, az új ág `master`on alapul. 

A munkaágra való váltáshoz futtassa a következő parancsot: 

```bash
git checkout <working branch name>
```

A munkaágra való váltás után megkezdheti a kód vagy a dokumentációs összetevők fejlesztését a munkaelem befejezéséhez. A `git checkout master` futtatása visszavált a `master` ágra.

Célszerű létrehozni egy git ágat minden egyes felhasználói történet munkaelemhez. Ezután az egyes feladatok munkaelemekhez a felhasználói történet ág alapján hozhat létre ágat. Rendezze az ágakat egy olyan hierarchiában, amely a felhasználói történet – feladat kapcsolatnak felel meg, ha több ember dolgozik ugyanazon projekt különböző felhasználói történetén, vagy ugyanazon felhasználói történet különböző feladataiban. Az ütközések csökkentése érdekében az egyes csapattagok egy másik ág, illetve más kódok vagy egyéb összetevők esetében is dolgozhatnak egy adott ág megosztásakor. 

Az alábbi ábrán a TDSP ajánlott elágazási stratégiája látható. Előfordulhat, hogy nincs szüksége az itt látható sok ágra, különösen akkor, ha csak egy vagy két személy dolgozik egy projekten, vagy csak egy felhasználó dolgozik a felhasználói történet összes feladatán. De a fejlesztési ág a Master ág alapján való elkülönítése mindig jó gyakorlat, és segít megakadályozni, hogy a kiadási ág megszakítsa a fejlesztési tevékenységeket. A git-ág modell teljes leírását a git- [elágazások sikeres modellje](https://nvie.com/posts/a-successful-git-branching-model/)című témakörben tekintheti meg.

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Egy meglévő ágban is hozzárendelhet egy munkaelemet. A munkaelem **részletek** lapján válassza a **hivatkozás hozzáadása**elemet. Ezután válasszon ki egy meglévő ágat, hogy összekapcsolja a munkaelemet, majd kattintson **az OK gombra**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name='WorkonaBranchandCommittheChanges-2'></a>Az ág használata és a módosítások elvégzése 

Miután módosította a munkaelemét, például egy R-parancsfájl hozzáadását a helyi számítógép `script` ága számára, a következő git bash-parancsok használatával véglegesítheti a helyi ág változását a felsőbb rétegbeli munkaágra:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name='CreateapullrequestonVSTS-3'></a>Lekéréses kérelem létrehozása

Egy vagy több véglegesítés és leküldés után, amikor készen áll a jelenlegi munkaág alapágra való egyesítésére, létrehozhat és elküldhet egy *pull-kérelmet* az Azure reposban. 

Az Azure DevOps-projekt főoldaláról válassza a **Repos** > pull- **kérések** lehetőséget a bal oldali navigációs sávon. Ezután válassza ki az **új pull-kérés** gombok egyikét, vagy a **pull-kérelem létrehozása** hivatkozást.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Ha szükséges, az **új lekéréses kérelem** képernyőjén navigáljon a git-tárházhoz, és válassza ki azt az ágat, amelyben egyesíteni szeretné a módosításokat. Adja meg vagy módosítsa a kívánt adatokat. A **felülvizsgálók**területen adja hozzá a felülvizsgálók nevét, majd válassza a **Létrehozás**lehetőséget. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name='ReviewandMerge-4'></a>Áttekintés és egyesítés

A pull-kérelem létrehozása után a véleményezők e-mailben értesítik a lekéréses kérelmet. A felülvizsgálók megvizsgálják, hogy a módosítások működnek-e, és ha lehetséges, ellenőrizze a változtatásokat a kérelmezővel. A felülvizsgálók megjegyzéseket tehetnek, módosításokat igényelhetnek, és elhagyhatják a lekéréses kérelmet az értékelésük alapján. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Miután a felülvizsgálók jóváhagyják a módosításokat, Ön vagy valaki más az egyesítési engedélyekkel egyesítheti a munkaágat az alapágra. Válassza a **Befejezés**lehetőséget, majd kattintson az **Egyesítés befejezése** gombra a **teljes lekéréses kérelem** párbeszédablakban. Azt is megteheti, hogy az egyesítés után törli a munkaágat. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Győződjön meg arról, hogy a kérelem **készként**van megjelölve. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Ha a bal oldali navigációs sávon visszatér a **repóhoz** , láthatja, hogy a `script` ág törlése óta átváltott a fő ágra.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

A következő git bash-parancsokkal egyesítheti az `script` munkaágat az alapágra, és az egyesítés után törölheti a munkaágat:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Következő lépések

Az adatelemzési [feladatok végrehajtása](execute-data-science-tasks.md) azt mutatja be, hogyan használhatók a segédprogramok számos gyakori adatelemzési feladat végrehajtásához, például az interaktív adatok feltárásához, az adatelemzéshez, a jelentéskészítéshez és a modell létrehozásához

A [forgatókönyvek példái](walkthroughs.md) a hivatkozásokat és a miniatűr leírásait felsoroló forgatókönyvek. A hivatkozott forgatókönyvek bemutatják, hogyan kombinálhatja a Felhőbeli és a helyszíni eszközöket és szolgáltatásokat munkafolyamatokban vagy folyamatokban intelligens alkalmazások létrehozásához. 

