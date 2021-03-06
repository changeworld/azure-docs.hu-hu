---
title: 'Többosztályos döntési erdő: modul leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning többosztályos döntési erdő modulját egy gépi tanulási modell létrehozásához a *döntési erdő* algoritmusa alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: b1f7aaeaf5d1537e0e5a427ad994f7e31e43fee3
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921368"
---
# <a name="multiclass-decision-forest-module"></a>Többosztályos döntési erdő modul

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal gépi tanulási modellt hozhat létre a *döntési erdő* algoritmusa alapján. A döntési erdő egy olyan Ensemble-modell, amely gyorsan kiépíti a döntési fák egy sorozatát, miközben a címkézett adatokból tanul.

## <a name="more-about-decision-forests"></a>További információ a döntési erdőkről

A döntési erdő algoritmusa egy együttes tanulási módszer a besoroláshoz. Az algoritmus több döntési fa létrehozásával, majd a legnépszerűbb kimeneti osztályban való *szavazással* működik. A szavazás egy aggregációs űrlap, amelyben a besorolási döntési erdőben lévő összes fa a címkék nem normalizált gyakorisági hisztogramját adja vissza. Az összesítési folyamat összegzi ezeket a hisztogramokat, és normalizálja az eredményt az egyes címkék "valószínűségek" lekéréséhez. A magas előrejelző megbízhatósággal rendelkező fák nagyobb súlyt kapnak az Ensemble végső döntésében.

Az általános döntési fák nem számszerű modellek, ami azt jelenti, hogy különböző eloszlásokkal rendelkező adattípusokat támogatnak. Minden fában egy egyszerű teszt sorozata fut minden egyes osztályhoz, így a faszerkezetek szintjei addig növekednek, amíg el nem éri a levél csomópontot (döntés).

A döntési fák számos előnnyel rendelkeznek:

+ Nem lineáris döntési határokat is jelenthetnek.
+ A képzés és az előrejelzés során a számítási és a memóriahasználat hatékonyak.
+ Integrált funkciók kiválasztását és besorolását végzik.
+ Ezek rugalmasan működnek a zajos funkciók jelenlétében.

Azure Machine Learning a döntési erdő besorolása a döntési fák együttesét tartalmazza. Az Ensemble-modellek általában jobb lefedettséget és pontosságot biztosítanak, mint az önálló döntési fák. További információ: [döntési fák](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Többosztályos döntési erdő konfigurálása

1. Adja hozzá a **többosztályos döntési erdő** modult a folyamathoz a tervezőben. Ez a modul a **Machine learning**, a **modell inicializálása**és a **besorolás**területen található.

2. A **Tulajdonságok** ablaktábla megnyitásához kattintson duplán a modulra.

3. Az **újramintavételezési módszernél**válassza ki az egyes fák létrehozásához használt módszert.  A csomagok és a replikálás közül választhat.

    + **Poggyász**: a poggyászt rendszerindítási *összesítésnek*is nevezik. Ebben a módszerben minden fát egy új mintán termesztenek, és az eredeti adatkészlet véletlenszerűen történő mintavételezésével jön létre, és csak akkor történik meg, ha nem rendelkezik az eredeti méretű adatkészlettel. A modellek kimeneteit a rendszer *szavazással*kombinálja, amely az Összesítés formáját képezi. További információ: a rendszerindítási összesítések Wikipedia-bejegyzése.

    + **Replikálás**: a replikáció során minden fát pontosan ugyanazok a bemeneti adatok képeznek. Annak meghatározása, hogy az egyes facsomópontok melyik felosztó predikátumot használják, a különböző fák létrehozásakor is véletlenszerűek maradnak.

   

4. Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.

    + **Egyetlen paraméter**: válassza ezt a lehetőséget, ha tudja, hogyan szeretné konfigurálni a modellt, és adjon meg argumentumként egy értéket.

    + **Paraméter tartománya**: akkor válassza ezt a lehetőséget, ha nem biztos benne, hogy a legjobb paramétereket szeretné használni, és szeretne futtatni egy paramétert. Válassza ki a megismételni kívánt értékek tartományát, és a [finomhangolási modell hiperparaméterek beállítása](tune-model-hyperparameters.md) az optimális eredményeket eredményező hiperparaméterek beállítása meghatározásához megadott beállítások összes lehetséges kombinációján.   

5. **Döntési fák száma**: Itt adhatja meg az Ensemble-ban létrehozható döntési fák maximális számát. További döntési fák létrehozásával lehetőség van jobb lefedettségre, de a képzés hosszabb ideig is növelheti.

    Ez az érték határozza meg az eredményekben megjelenített fák számát is a betanított modell megjelenítésekor. Egyetlen fa megjelenítéséhez vagy kinyomtatásához beállíthatja az értéket 1-re; azonban ez azt jelenti, hogy csak egy fát lehet előállítani (a kezdeti paraméterekkel rendelkező fát), és a rendszer nem végez további iterációkat.

6. **A döntési fák maximális mélysége**: írjon be egy számot a döntési fa maximális mélységének korlátozására. A fa mélységének növelésével növelheti a pontosságot, és megnövelheti a beilleszkedő és a megnövekedett betanítási időt is.

7. **Véletlenszerű felosztások száma egy csomóponton**: írja be a fa egyes csomópontjainak kiépítésekor használandó felosztások számát. A *felosztás* azt jelenti, hogy a fa (csomópont) egyes szintjeinek funkciói véletlenszerűen vannak osztva.

8. **Minták minimális száma a levél csomópontjain**: adja meg, hogy legalább hány esetben szükséges a terminál csomópont (levél) létrehozása a fában. Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét.

    Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább öt olyan esetet kellene tartalmaznia, amelyek megfelelnek ugyanazoknak a feltételeknek.



10. Egy címkézett adatkészlet és egy betanítási modul csatlakoztatása:

    + Ha az **oktatói módot** **egyetlen paraméterként**állítja be, használja a [Train Model](./train-model.md) modult.

11. A folyamat futtatása.



## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 