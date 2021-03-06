---
title: Csapat adatelemzési folyamat szerepkörök és feladatok
description: A kulcsfontosságú összetevők, a személyzet szerepkörei és az adatelemzési csoportok kapcsolódó feladatainak vázlata.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c1ed731943abf0efdd99ea54d2318fa402835e08
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720010"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Csapat adatelemzési folyamat szerepkörök és feladatok

A csoportos adatelemzési folyamat (TDSP) a Microsoft által kifejlesztett keretrendszer, amely strukturált módszertant biztosít a prediktív elemzési megoldások és intelligens alkalmazások hatékony létrehozásához. Ez a cikk ismerteti a kulcsfontosságú személyzeti szerepköröket és az adatelemzési csapathoz kapcsolódó feladatokat, amelyek a folyamaton alapulnak.

Ez a bevezető cikk a TDSP-környezet beállításával kapcsolatos oktatóanyagokra mutató hivatkozásokat tartalmaz. Az oktatóanyagok részletes útmutatást nyújtanak a Azure DevOps Projects, az Azure Repos-Tárházak és az Azure-táblák használatához.  A motivációs cél a koncepcióról a modellezésre és a telepítésre való áttérés.

Az oktatóanyagok az Azure DevOps-t használják, mivel ez az útmutató a TDSP megvalósításához a Microsoftnál. Az Azure DevOps a szerepköralapú biztonság, a munkaelemek kezelésének és nyomon követésének, valamint a kódok üzemeltetésének, megosztásának és verziókövetés integrálásával segíti az együttműködést. Az oktatóanyagok az Azure [Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) szolgáltatást is használják az analitikai asztalként, amelynek számos népszerű adatelemző eszköze van előre konfigurálva, és integrálva van a Microsoft szoftverrel és az Azure-szolgáltatásokkal. 

Az oktatóanyagok segítségével más, a TDSP, az agilis tervezéssel és a fejlesztői eszközökkel és környezetekkel valósítható meg az alkalmazások, néhány funkció azonban nem érhető el.

## <a name="structure-of-data-science-groups-and-teams"></a>Adatelemzési csoportok és csapatok szerkezete

A vállalatok adatelemzési funkcióit gyakran a következő hierarchiában rendezik:

- Adattudományi csoport
  - Adattudományi csapat/s a csoporton belül

Ilyen struktúrában a csoport-és a Team-érdeklődők találhatók. Az adatelemzési projekteket jellemzően egy adatelemző csapat végzi. Az adatelemzési csapatoknak projekt-és irányítási feladatokhoz, valamint egyéni adatszakértőkhöz és mérnökökhöz kell rendelkezniük a projekt adatelemzési és adatkezelési részeinek elvégzéséhez. A projekt kezdeti beállítását és irányítását a csoport, a csapat vagy a projekt-érdeklődők végzik.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>A négy TDSP-szerepkör definíciója és feladatai
Feltételezve, hogy az adatelemzési egység egy csoporton belül csapatokból áll, a TDSP-munkatársaknak négy különböző szerepe van:

1. A **Group Manager**: a teljes adattudományi egységet kezeli egy vállalaton belül. Előfordulhat, hogy a data science egység több csapat, amelyek mindegyike több különböző üzleti referenciaegyenesen az adatelemzési projektek dolgozik. A csoport kezelőjének előfordulhat, hogy delegálni a feladatokat a helyettes, de a szerepkörhöz tartozó feladatok ne módosítsa.
   
2. **Csapat érdeklődője**: egy vállalat adattudományi egységében kezeli a csapatot. Egy csoport több adatszakértők áll. Egy kis adatelemzési egység esetében a csoportvezető és a csapat vezetője is lehet ugyanaz a személy.
   
3. **Projekt vezetője**: az egyes adatszakértők napi tevékenységeit kezeli egy adott adatelemzési projektben.
   
4. **Projekt – egyéni közreműködők**: adatszakértők, üzleti elemzők, adatmérnökök, építészek és az adatelemzési projektet végrehajtó mások.

> [!NOTE]
> A vállalatok struktúrájától és méretétől függően egyetlen személy több szerepkört is játszhat, vagy több személy is kitöltheti a szerepkört.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>A négy szerepkör által elvégzendő feladatok

Az alábbi ábrán az egyes csoportos adatelemzési folyamatokhoz tartozó legfelső szintű feladatok láthatók. Ez a séma és az alábbi, az egyes TDSP-szerepkörökkel kapcsolatos feladatok részletesebb áttekintése segíthet kiválasztani a szükséges oktatóanyagot a feladatai alapján.

![Szerepkörök és feladatok áttekintése](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Csoportkezelő feladatok

A Group Manager vagy a kijelölt TDSP-rendszergazda a következő feladatokat hajtja végre a TDSP elfogadásához:

- Létrehoz egy Azure DevOps- **szervezetet** és egy csoportos projektet a szervezeten belül. 
- Létrehoz egy **Project template-tárházat** az Azure DevOps Group projektben, és a Microsoft TDSP csapata által fejlesztett Project template adattárból magot. A Microsoft TDSP-sablonok tárháza a következőket biztosítja:
  - Egy **szabványosított címtár-struktúra**, beleértve az adat-, kód-és dokumentum-címtárakat.
  - **Szabványos dokumentum-sablonok** készlete, amely egy hatékony adatelemzési folyamatot irányít.
- Létrehoz egy **segédprogram-tárházat**, és magot a Microsoft TDSP csapata által fejlesztett segédprogram-tárházból. A Microsoft TDSP segédprogram-tárháza számos hasznos segédprogramot biztosít, amelyekkel hatékonyabbá teheti az adattudós munkáját. A Microsoft segédprogram-tárház az interaktív adatelemzési,-elemzési, jelentéskészítési és alapkonfiguráció-modellezési és jelentéskészítési segédeszközöket tartalmaz.
- Beállítja a szervezeti fiók **biztonsági vezérlési szabályzatát** .

Részletes útmutatásért lásd: [a Group Manager feladatai egy adatelemzési csapat számára](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Csapat vezető feladatok

A csapat érdeklődője vagy egy kijelölt projekt rendszergazdája a következő feladatokat hajtja végre a TDSP elfogadásához:

- Létrehoz egy Team- **projektet** a csoport Azure DevOps-szervezetében.
- Létrehozza a Project **template-tárházat** a projektben, és a Group Manager vagy a delegált által beállított Group Project template adattárból magot.
- Létrehozza a **csapat segédprogram-tárházat**, magot a csoport segédprogram-tárházból, és hozzáadja a csoportra jellemző segédprogramokat a tárházhoz.
- Opcionálisan létrehozza az [Azure file Storage](https://azure.microsoft.com/services/storage/files/) -t a hasznos adategységek tárolásához a csapat számára. Más csapattagokat is csatlakoztatása a megosztott felhőalapú fájltároló analytics gépeiken.
- Opcionálisan csatlakoztatja az Azure file Storage-t a csapat **DSVM** , és hozzáadja a csapat adategységeit.
- Beállítja a **biztonsági vezérlést** a csapattagok hozzáadásával és az engedélyeik konfigurálásával.

Részletes útmutatásért lásd: az [adatelemzési csapat csapatának vezető feladatai](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Érdeklődő tevékenységeket

A projekt vezetője a következő feladatokat hajtja végre a TDSP elfogadásához:

- Létrehoz egy **Project-tárházat** a csapat projektben, és a Project template-tárházból magot.
- Opcionálisan létrehozza az **Azure file Storage** -t a projekt adateszközeinek tárolásához.
- Opcionálisan csatlakoztatja az Azure file Storage-t a **DSVM** , és hozzáadja a projekthez tartozó adategységeket.
- Beállítja a **biztonsági vezérlést** a projekt tagjainak hozzáadásával és az engedélyeik konfigurálásával.

Részletes útmutatást az [adatelemzési csapat projekt-érdeklődői feladatai](project-lead-tasks.md)című témakörben talál.

## <a name="project-individual-contributor-tasks"></a>Egyéni közreműködő tevékenységeket

A projekt egyéni közreműködője – általában egy adattudós – a következő feladatokat hajtja végre a TDSP:

- Klónozott a Project-érdeklődő által beállított **Project-tárházat** .
- Opcionálisan csatlakoztatja a megosztott csapatot és a Project **Azure file Storage** -t a **Data Science Virtual Machineon** (DSVM).
- Végrehajtja a projektet.

A projektbe való bevezetéssel kapcsolatos részletes utasításokért tekintse meg az [adatelemzési csapat egyéni közreműködői feladatait](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Adatelemzési projekt végrehajtási munkafolyamata

A kapcsolódó oktatóanyagok, az adatszakértők, a projekt-érdeklődők és a csapat-érdeklődők követésével munkaelemek hozhatók létre, amelyekkel nyomon követhető az összes feladat és szakasz a projekt elejétől a végéig Az Azure Repos használata elősegíti az adatszakértők közötti együttműködést, és biztosítja, hogy a projekt végrehajtása során generált összetevők az összes projekt tagja által vezéreltek és megosztva legyenek. Az Azure DevOps lehetővé teszi az Azure-táblák munkaelemeinek összekapcsolását az Azure Repos adattár-fiókjaival, és könnyen nyomon követheti, hogy mi történt a munkaelemek esetében.

Az alábbi ábra a projekt végrehajtásának TDSP-munkafolyamatát ismerteti:

![Általános adatelemzési projekt munkafolyamata](./media/roles-tasks/overview-project-execute.png)

A munkafolyamat lépései három tevékenységbe sorolhatók:

- A Project-érdeklődők a Sprint tervezését végzik
- Az adatszakértők a munkaelemek kezelésére `git` ágakat fejlesztenek.
- A projekt-vagy más csapattagok a kód felülvizsgálatát és a munkaágak egyesítését a Master ág számára

A projekt-végrehajtási munkafolyamattal kapcsolatos részletes utasításokért lásd: az [adatelemzési projektek agilis fejlesztése](agile-development.md).

## <a name="tdsp-project-template-repository"></a>TDSP-sablonok tárháza

A Microsoft TDSP csapata [Project template adattárával](https://github.com/Azure/Azure-TDSP-ProjectTemplate) hatékonyan támogathatja a projektek végrehajtását és együttműködését. Az adattár a saját TDSP-projektjeihez használható szabványosított címtár-struktúrát és dokumentumtárakat biztosít.

## <a name="next-steps"></a>Következő lépések

Ismerje meg a szerepkörök és feladatok határozzák meg a csoportos adatelemzési folyamat részletes leírása:

- [Az adatelemzési csapat Group Manager-feladatai](group-manager-tasks.md)
- [A csapat vezető feladatai egy adattudományi csapat számára](team-lead-tasks.md)
- [Az adatelemzési csapat projekt-vezető feladatai](project-lead-tasks.md)
- [Az adatelemzési csapat egyedi közreműködő feladatainak projektje](project-ic-tasks.md)
