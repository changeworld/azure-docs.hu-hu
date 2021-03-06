---
title: Előre elkészített modellek – LUIS
titleSuffix: Azure Cognitive Services
description: Előre összeállított modellek adja meg a tartományok, szándék fog vonatkozni, utterances és entitásokat. Indítsa el az alkalmazást egy előre elkészített tartomány vagy a megfelelő tartomány hozzáadása az alkalmazáshoz később.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: e0b5b95a7524b60a7c3367035a15a7158fa7908a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280835"
---
# <a name="prebuilt-models"></a>Előre összeállított modellek

Előre összeállított modellek adja meg a tartományok, szándék fog vonatkozni, utterances és entitásokat. Elindíthatja az alkalmazást egy előre elkészített modellel, vagy hozzáadhat egy megfelelő modellt az alkalmazáshoz később. 

## <a name="types-of-prebuilt-models"></a>Előre összeállított modellek típusa

A LUIS három típusú előre elkészített modellt biztosít. Minden modell az alkalmazás bármikor lehet hozzáadni. 

|Modell típusa|Tartalmazza|
|--|--|
|[Tartományi](luis-reference-prebuilt-domains.md)|Leképezések, utterances, entitások|
|Leképezések|Leképezések, kimondott szöveg|
|[Szervezetek](luis-reference-prebuilt-entities.md)|Entitások csak| 

## <a name="prebuilt-domains"></a>Előre összeállított tartományok

Language Understanding (LUIS) előre *elkészített tartományokat*biztosít, amelyek előre betanított [minták és](luis-how-to-add-intents.md) [entitások](luis-concept-entity-types.md) , amelyek együttesen működnek a tartományokban vagy az ügyfélalkalmazások általános csoportjaiban. 

Az előre összeállított tartományok a következők: betanított és készen áll a LUIS-alkalmazás hozzáadása. Az előre elkészített tartományhoz tartozó szándékok és entitások teljes mértékben testreszabhatók az alkalmazáshoz való hozzáadásuk után. 

> [!TIP]
> A leképezések és a egy előre elkészített tartományban entitások működnek együtt a leghatékonyabban. Érdemes úgy, hogy szándékokat és entitásokat, amikor csak lehetséges ugyanahhoz a tartományhoz.
> A segédprogramok előre összeállított tartományban van, amely testre szabható használható minden olyan tartományban szándékok. Hozzáadhat például `Utilities.Repeat`t az alkalmazáshoz, és betaníthatja, hogy a felhasználó milyen műveleteket lehet megismételni az alkalmazásban. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Egy előre elkészített tartomány szándékot viselkedésének módosítása

Előfordulhat, hogy egy előre elkészített tartományt tartalmaz megjelölésű megjelölésű szeretné engedélyezni a LUIS-alkalmazás a hasonló, de azt szeretné, hogy eltérően viselkednek. Például a **helyek** előre összeépített tartománya `MakeReservation` szándékot biztosít egy éttermi foglaláshoz, de azt szeretné, hogy az alkalmazás ezt a szándékot használja a szállodai foglalások készítéséhez. Ebben az esetben módosíthatja a szándék viselkedését úgy, hogy hozzáad egy példát a hosszúságú kimondott szöveg a Szállásfoglaláshoz, majd újratanítja az alkalmazást. 

Az előre elkészített tartományokról az [előre elkészített tartományok című témakörben](./luis-reference-prebuilt-domains.md)talál teljes listát.

## <a name="prebuilt-intents"></a>Előre összeállított leképezések

A LUIS előre összeépített leképezéseket és azok hosszúságú kimondott szöveg biztosítja az előre elkészített tartományokhoz. A teljes tartomány hozzáadása nélkül leképezéseket adhat hozzá. A szándék hozzáadásával egy szándékot és annak hosszúságú kimondott szöveg adhat hozzá az alkalmazáshoz. A leképezés neve és az utterance (kifejezés) listát is módosíthatók.  

## <a name="prebuilt-entities"></a>Előre összeállított entitások

LUIS az általános típusú adatok, például a dátumok, időpontok, számok, mértékek és pénznem FELISMERVE előre összeállított entitások egy készletét tartalmazza. Előre összeállított entitások támogatása a LUIS-alkalmazás kulturális környezete eltérő. A LUIS által támogatott előre elkészített entitások teljes listáját, beleértve a kulturális környezet támogatását, az [előre elkészített entitások referenciája](./luis-reference-prebuilt-entities.md)című témakörben talál.

Ha az alkalmazás tartalmaz egy előre összeállított entitások, az előrejelzéseket a közzétett alkalmazás szerepelnek. Az előre elkészített entitások viselkedése előre betanítva, és **nem** módosítható. 

> [!NOTE]
> a **beépített. datetime** elavult. A rendszer helyébe a [**beépített. datetimeV2**](luis-reference-prebuilt-datetimev2.md), amely a dátum-és időtartományok felismerését, valamint a kétértelmű dátumok és időpontok jobb felismerését is lehetővé teszi.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [adhat hozzá előre elkészített entitásokat](luis-prebuilt-entities.md) az alkalmazáshoz.
