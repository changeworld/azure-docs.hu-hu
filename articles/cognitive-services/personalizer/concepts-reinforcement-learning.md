---
title: Megerősítő tanulás – személyre szabás
titleSuffix: Azure Cognitive Services
description: A személyre szabással kapcsolatos javaslatok megtételéhez a megszemélyesítő a műveletekkel és az aktuális környezettel kapcsolatos információkat használ. Ezekkel a műveletekkel és környezettel kapcsolatos információk a szolgáltatásoknak nevezett attribútumok vagy tulajdonságok.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 36071cdee25cfa99fc54b0e5c0c0aa822cb5fe2f
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662838"
---
# <a name="what-is-reinforcement-learning"></a>Mi a megerősítő tanulás?

A megerősítő tanulás a gépi tanulás olyan megközelítése, amely a használattól kapott visszajelzések alapján tanulja meg a viselkedést.
 
A megerősítő tanulás az alábbiak szerint működik:

* Lehetőség vagy szabadság megteremtése a viselkedés elvégzéséhez – például döntések vagy döntések meghozatala.
* Környezetfüggő információk biztosítása a környezetről és a választási lehetőségekről.
* Visszajelzés küldése arról, hogy a viselkedés milyen jól érhető el egy bizonyos célra.

Habár a megerősítő tanulás számos altípussal és stílussal rendelkezik, így a fogalom a személyre szabott módon működik:

* Az alkalmazás lehetőséget nyújt arra, hogy egy adott tartalmat jelenítsen meg az alternatívák listájáról.
* Az alkalmazás az egyes alternatívákról és a felhasználó környezetéről nyújt információt.
* Az alkalmazás kiszámítja a _jutalom pontszámát_.

A megerősítő tanulás bizonyos módszereitől eltérően a személyre szabott eszközök nem igénylik a szimuláció használatát a alkalmazásban. A tanulási algoritmusok úgy lettek kialakítva, hogy a külvilág felé (a szabályozással szemben) és az egyes adatpontokból is tanuljanak, és ez egy olyan egyedi lehetőség, amely a költségek idejét és pénzét jelenti, és hogy nem nulla sajnálattal (a lehetséges jutalom elvesztése), ha az optimálisnál rosszabb teljesítmény történik.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Milyen típusú megerősítő tanulási algoritmusokat használ a személyre szabás?

A személyre szabott felhasználó jelenlegi verziója **környezetfüggő banditákat**használ, így a megerősítő tanulás olyan megközelítést tesz elérhetővé, amely a döntések meghozatala és a különálló műveletek között egy adott kontextusban való választás.

A _döntési memória_, a modell, amely a lehető legjobb döntés rögzítésére lett kiképezve, a kontextusban a lineáris modellek készletét használja. Ezek az üzleti eredmények többször is megjelennek, és a bevált megközelítések, részben azért, mert nagyon gyorsan tanulhatnak a valós világból anélkül, hogy a többlépéses képzést kellene elvégeznie, részben pedig a felügyelt tanulási modellek és a mély neurális funkciók kiegészítéseként is. hálózati modellek.

A felderítési és kiaknázási forgalom kiosztása véletlenszerűen történik a feltárási százalékos érték követése után, és a feltárás alapértelmezett algoritmusa epszilon-kapzsi.

### <a name="history-of-contextual-bandits"></a>Környezetfüggő banditák előzményei

John Langford megalkotta a kontextusban lévő banditák nevét (Langford és Zhang [2007]), amely leírja a megerősítő tanulás egy megvonható részhalmazát, és egy fél tucat tanulmányban dolgozott, amely az alábbi paradigma megismerését mutatja be:

* Beygelzimer és szerzőtársai [2011]
* Dudík et al. [2011a, b]
* Agár és szerzőtársai [2014, 2012]
* Beygelzimer és Langford [2009]
* Li és al. [2010]

John emellett számos olyan oktatóanyagot is kapott, mint például a közös előrejelzés (ICML 2015), a környezetfüggő bandita-elmélet (NIP 2013), az aktív tanulás (ICML 2009) és a minta bonyolultsági korlátok (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Milyen gépi tanulási keretrendszerek használják a személyre szabást?

A személyre szabás jelenleg a [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) használja a gépi tanulás alapjaként. Ez a keretrendszer lehetővé teszi a maximális átviteli sebességet és a legalacsonyabb késést a személyre szabási rangsorolása és a modell tanítása az összes eseménnyel.

## <a name="references"></a>Referencia

* [A kontextusos döntések alacsony technikai adóssággal való kihasználása](https://arxiv.org/abs/1606.03966)
* [A méltányos besoroláshoz való csökkentési megközelítés](https://arxiv.org/abs/1803.02453)
* [Hatékony Kontextusbeli banditák a nem álló világokban](https://arxiv.org/abs/1708.01799)
* [Fennmaradó veszteségek előrejelzése: Megerősítése: a növekményes visszajelzés nélküli tanulás](https://openreview.net/pdf?id=HJNMYceCW)
* [Útmutatás és vizualizációs megfigyelések a megerősítő képzéssel végzett műveletekhez](https://arxiv.org/abs/1704.08795)
* [Tanuljon jobb keresésre a tanárnál](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>További lépések

[Offline Értékelés](concepts-offline-evaluation.md) 