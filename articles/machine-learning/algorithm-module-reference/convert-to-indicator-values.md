---
title: Váltás a kijelző értékeire
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning konvertálható értékeit tartalmazó modult olyan oszlopok konvertálásához, amelyek kategorikus értékeket tartalmaznak a bináris kijelző oszlopaiba.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: fc059eca3a01b5c6cde642af5ceb6a3822672def
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165539"
---
# <a name="convert-to-indicator-values"></a>Váltás a kijelző értékeire
Ez a cikk a Azure Machine Learning Designer modulját ismerteti.

A Azure Machine Learning Designerben az **átalakítás jelző értékekkel** moduljának használatával alakítsa át a kategorikus értékeket tartalmazó oszlopokat egy bináris kijelző oszlopaiba.  

Ez a modul a mutatók értékeire való átalakításhoz használt átalakítás definícióját is megjeleníti. Ezt az átalakítást felhasználhatja más, azonos sémával rendelkező adatkészleteken az [átalakítási modul alkalmazása](apply-transformation.md) lehetőséggel.

## <a name="how-to-configure-convert-to-indicator-values"></a>A konverzió beállítása a kijelző értékeire

1.  Keresse meg az **átalakítás jelölő értékeit** , és húzza a folyamat piszkozatára. Ez a modul az **Adatátalakítási** kategóriában található.
    > [!NOTE]
    > A [metaadatok szerkesztése](edit-metadata.md) modult a **Konvertálás Indiciator értékre** elemre kattintva megadhatja, hogy a cél oszlop (ok) kategorikusként legyen megjelölve.

1. Kapcsolja össze a **Convert to Indicator Values** modult az átalakítani kívánt oszlopokat tartalmazó adatkészlethez. 

1. Válassza az **oszlop szerkesztése** lehetőséget egy vagy több kategorikus oszlop kiválasztásához.

1. Válassza a **kategorikus oszlopok felülírása** lehetőséget, ha **csak** az új logikai oszlopokat kívánja kiállítani. Alapértelmezés szerint ez a beállítás ki van kapcsolva.
    

    > [!TIP]
    >  Ha a felülírási lehetőséget választja, a forrás oszlop nem törlődik és nem módosul. Ehelyett az új oszlopok jönnek létre és jelennek meg a kimeneti adatkészletben, és a forrás oszlop elérhető marad a munkaterületen. Ha meg kell tekintenie az eredeti adatforrásokat, az [Oszlopok hozzáadása](add-columns.md) modul segítségével bármikor hozzáadhatja a forrás oszlopot a következőhöz:.

1. A folyamat futtatása.

## <a name="results"></a>Results (Eredmények)

Tegyük fel, hogy van egy olyan pontszáma, amely azt jelzi, hogy egy kiszolgáló magas, közepes vagy alacsony meghibásodási valószínűséggel rendelkezik-e.  

| Kiszolgáló azonosítója | Hiba pontszáma |
| --------- | ------------- |
| 10301     | Alacsony           |
| 10302     | Közepes        |
| 10303     | Magas          |

Ha a **konverziót jelző értékekre**alkalmazza, a tervező átalakítja a címkék egyetlen oszlopát több, logikai értékeket tartalmazó oszlopba:  

| Kiszolgáló azonosítója | Hiba pontszám – alacsony | Hiba pontszám – közepes | Hiba pontszám – magas |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Az átalakítás működése:  

-   A kockázatokat leíró **hiba pontszám** oszlopban csak három lehetséges érték létezik (magas, közepes és alacsony), és nincs hiányzó érték. Tehát pontosan három új oszlop jön létre.  

-   Az új jelző oszlopok a forrás oszlop fejlécei és értékei alapján vannak elnevezve, a következő mintával: *\<a forrás oszlop > – \<adatérték >* .  

-   Az egyes kiszolgálók csak egyetlen kockázati minősítéssel rendelkezhetnek, és az összes többi kijelző oszlopában 0 értéknek kell lennie.  

Most már használhatja a három kijelző oszlopokat a Machine learning-modellek funkcióinak használatával.

A modul két kimenetet ad vissza:

- **Results adatkészlet**: az átalakított kijelző értékeit tartalmazó adatkészlet. A tisztításra kijelölt oszlopok is áthaladnak.
- A **kijelző értékeinek átalakítása**: a kijelzőre való átalakításra használt adatátalakítás, amely menthető a munkaterületre, és később is alkalmazható az új adatokra.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Mentett kijelző értékeit tartalmazó művelet alkalmazása új adatokra

Ha többször is meg kell ismételnie a kijelzők műveleteit, az adatmanipulációs lépéseket egy *átalakítással* mentheti, hogy ugyanazt az adatkészletet használja. Ez akkor hasznos, ha gyakran újra kell importálnia, majd törölnie kell az azonos sémával rendelkező információkat.

1. Adja hozzá az [átalakítási modul alkalmazása](apply-transformation.md) a folyamathoz lehetőséget.

1. Adja hozzá a tisztítani kívánt adatkészletet, és kapcsolja össze az adatkészletet a jobb oldali bemeneti porthoz.

1. Bontsa ki az **Adatátalakítási** csoportot a tervező bal oldali paneljén. Keresse meg a mentett átalakítást, és húzza a folyamatba.

1. Kapcsolja össze a mentett átalakítást az [alkalmazás átalakításának](apply-transformation.md)bal oldali bemeneti portjával.

   Ha mentett transzformációt alkalmaz, nem választhatja ki, hogy mely oszlopok legyenek átalakítva. Ennek az az oka, hogy a transzformáció definiálva van, és automatikusan az eredeti műveletben megadott adattípusokra vonatkozik.

1. A folyamat futtatása.
 
## <a name="technical-notes"></a>Technikai megjegyzések  

Ez a szakasz megvalósítási részleteket, tippeket és válaszokat tartalmaz a gyakori kérdésekre.

### <a name="usage-tips"></a>Használati tippek

-   Csak a kategorikusként megjelölt oszlopok alakíthatók át jelző oszlopokra. Ha a következő hibaüzenet jelenik meg, akkor valószínű, hogy az egyik kiválasztott oszlop nem kategorikus:  

     0056-es hiba: a name \<nevű oszlop nem engedélyezett kategóriába tartozik, >.  

     Alapértelmezés szerint a legtöbb karakterlánc-oszlop karakterlánc-szolgáltatásként van kezelve, ezért explicit módon meg kell jelölnie azokat kategorikusként a [metaadatok szerkesztése](edit-metadata.md)lehetőség használatával.  

-   A mutatók oszlopaiba konvertálható oszlopok száma nincs korlátozva. Mivel azonban az értékek egyes oszlopai több kijelzőt is tartalmazhatnak, érdemes lehet csak néhány oszlopot konvertálni és áttekinteni.  

-   Ha az oszlop hiányzó értékeket tartalmaz, a rendszer külön jelző oszlopot hoz létre a hiányzó kategóriához, a következő névvel: *\<forrás oszlop > – hiányzó*  

-   Ha a kijelző értékre konvertált oszlop számokat tartalmaz, akkor azokat kategorikusként kell megjelölni, mint bármely más szolgáltatás oszlopát. Miután ezt megtette, a rendszer diszkrét értékként kezeli a számokat. Ha például egy numerikus oszlop, amely 25 és 30 közötti értéket tartalmaz, egy új jelző oszlop jön létre minden egyes különálló értékhez:  

    | Győződjön meg arról       | Highway mpg-25 | Highway mpg-26 | Highway mpg-27 | Highway mpg-28 | Highway mpg-29 | Highway mpg-30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Contoso-autók | 0               | 0               | 0               | 0               | 0               | 1               |

- Ha nem szeretné, hogy túl sok dimenzió legyen hozzáadva az adatkészlethez. Javasoljuk, hogy először tekintse meg az oszlopban szereplő értékek számát, és az adatok megfelelő kiosztását vagy kvantálását.  


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
