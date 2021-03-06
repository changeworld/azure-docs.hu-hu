---
title: Iterációs alkalmazás tervezése – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422599"
---
# <a name="iterative-app-design-for-luis"></a>A LUIS-hoz készült iterációs alkalmazás kialakítása

A Language Understanding (LUIS) alkalmazás a leghatékonyabban tanul és hajt végre iterációval. Íme egy tipikus iterációs ciklus:

* új verzió létrehozása
* A LUIS-alkalmazás sémájának szerkesztése. Az érintett műveletek közé tartoznak az alábbiak:
    * leképezések példa hosszúságú kimondott szöveg
    * Entitások
    * Szolgáltatások
* Betanítás, tesztelés és közzététel
    * Tesztelés az aktív tanulás előrejelzési végpontján
* adatok összegyűjtése végponti lekérdezésekből

![Tartalomkészítési ciklus](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>LUIS-séma létrehozása

Az alkalmazás sémája határozza meg, hogy a felhasználó mit kér (a _szándékot_ vagy szándékot), és a szándék mely részeit adja meg ( _entitások_ _),_ amelyek segítségével meghatározhatja a választ. 

Az alkalmazás sémájának egyedinek kell lennie az alkalmazás-tartományokban, hogy meghatározza a releváns szavakat és kifejezéseket, valamint hogy meghatározza a szokásos Word-sorrendet. 

A hosszúságú kimondott szöveg olyan felhasználói bemeneteket jelentenek, mint például a felismert beszéd vagy szöveg, amelyet az alkalmazás futásidőben vár. 

A sémához leképezések szükségesek, és _rendelkeznie kell_ entitásokkal. 

### <a name="example-schema-of-intents"></a>Példa a szándékok sémájára

A leggyakoribb séma a szándékokkal rendezett leképezési séma. Ez a típusú séma a LUIS használatával határozza meg a felhasználó szándékát. 

Előfordulhat, hogy a szándék sémájának típusa entitásokkal rendelkezik, ha segítséget nyújt a felhasználóknak a felhasználó szándékának meghatározásában. Például egy szállítási entitás (a szándéknak megfelelően) segít a kiszállítási szándék meghatározásában. 

### <a name="example-schema-of-entities"></a>Példa entitások sémája

Az entitások sémái az entitásokra összpontosítanak, amelyek a felhasználói hosszúságú kimondott szöveg kinyert adatok. Ha például egy felhasználó azt mondta, hogy "Szeretnék három pizzát rendelni." Két entitást kell kinyerni: _három_ és _pizzát_. Ezek a célok teljesítéséhez szükségesek, amely a megrendelés megrendelése volt. 

Az entitások sémája esetében a Kimondás célja kevésbé fontos az ügyfélalkalmazás számára. 

Az entitások sémájának megszervezésének közös módszere az összes példa hosszúságú kimondott szöveg hozzáadása a **nincs** szándékhoz. 

### <a name="example-of-a-mixed-schema"></a>Példa vegyes sémára

A leghatékonyabb és legérettebb séma olyan leképezési séma, amely az entitások és szolgáltatások teljes skáláját tartalmazza. Ez a séma megkezdhető úgy, mint a szándék vagy az entitás sémája, és az is növekszik, hogy mindkét fogalomban szerepelnek, mivel az ügyfélalkalmazás ezekre az adatokra van szüksége. 

## <a name="add-example-utterances-to-intents"></a>Példa hosszúságú kimondott szöveg hozzáadása a leképezésekhez

A LUIS-nek néhány példát kell hosszúságú kimondott szöveg az egyes **szándékokhoz**. A példának a Word Choice és a Word hosszúságú kimondott szöveg elég variációra van szüksége ahhoz, hogy meg tudja határozni, melyik szándékot jelenti a kiírás. 

> [!CAUTION]
> Ne vegyen fel több példát a hosszúságú kimondott szöveg. Kezdje a 15 – 30 konkrét és változó példával. 

Minden esetben a kiírásnak minden szükséges adattal rendelkeznie kell az **entitásokkal**megtervezett és címkézett **adatok kinyeréséhez** . 

|Kulcs eleme|Cél|
|--|--|
|Szándék|A felhasználó hosszúságú kimondott szöveg egyetlen célra vagy műveletbe **osztályozhatja** . Ilyenek például a `BookFlight` és a `GetWeather`.|
|Entitás|Az adatok **kinyerése** a cél befejezéséhez szükséges. Ilyenek például az utazás dátuma és időpontja, valamint a hely.|

A LUIS-alkalmazás úgy van kialakítva, hogy figyelmen kívül hagyja az alkalmazás tartományához nem kapcsolódó hosszúságú kimondott szöveg úgy, hogy nem rendeli hozzá a kiírást a **nincs** szándékhoz.

## <a name="test-and-train-your-app"></a>Az alkalmazás tesztelése és betanítása

Miután 15 – 30 különböző példát hosszúságú kimondott szöveg az egyes szándékokhoz, és a szükséges entitások címkével rendelkeznek, meg kell vizsgálnia és be kell [tanítania](luis-how-to-train.md) a Luis alkalmazást. 

## <a name="publish-to-a-prediction-endpoint"></a>Közzététel előrejelzési végponton

A LUIS-alkalmazást közzé kell tenni, hogy elérhető legyen a List [előrejelzési végpontok régióiban](luis-reference-regions.md).

## <a name="test-your-published-app"></a>A közzétett alkalmazás tesztelése

A közzétett LUIS-alkalmazást a HTTPS-előrejelzési végpontról tesztelheti. Az előrejelzési végpont tesztelése lehetővé teszi, hogy a LUIS kiválassza az alacsony megbízhatóságú hosszúságú kimondott szöveg az [ellenőrzéshez](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Új verzió létrehozása minden ciklushoz

Minden verzió egy pillanatkép a LUIS-alkalmazás időpontjában. Mielőtt módosításokat hajt végre az alkalmazásban, hozzon létre egy új verziót. A régebbi verzióra való visszalépés könnyebb, mint a leképezések eltávolításának és a hosszúságú kimondott szöveg egy korábbi állapotának kipróbálása.

A verzióazonosító karakterből, számjegyből vagy "." áll, és nem lehet hosszabb 10 karakternél.

A kezdeti verzió (0,1) az alapértelmezett aktív verzió. 

### <a name="begin-by-cloning-an-existing-version"></a>Kezdés egy meglévő verzió klónozásával

Meglévő verzió klónozása az egyes új verziók kiindulási pontként való használatához. Egy verzió klónozása után az új verzió lesz az **aktív** verzió. 

### <a name="publishing-slots"></a>Közzétételi résidők

Közzéteheti a fázist és/vagy az éles tárolóhelyeket is. Az egyes tárolóhelyek eltérő verziójúak vagy azonos verziójúak lehetnek. Ez akkor lehet hasznos, ha az éles környezetbe való közzététel előtt ellenőrzi a módosításokat, ami elérhető a botok vagy más LUIS hívó alkalmazások számára. 

A betanított verziók nem érhetők el automatikusan a LUIS-alkalmazás [végpontján](luis-glossary.md#endpoint). Ahhoz, hogy a LUIS-alkalmazás végpontján elérhető legyen, [közzé](luis-how-to-publish-app.md) kell tennie vagy újra közzé kell tennie egy verziót. Közzéteheti az **előkészítést** és a **gyártást**, így az alkalmazás két verziója érhető el a végponton. Ha az alkalmazás több verzióját is elérhetőnek kell lennie egy végponton, exportálnia kell a verziót, és újra importálnia kell egy új alkalmazásba. Az új alkalmazáshoz egy másik alkalmazás-azonosító tartozik.

### <a name="import-and-export-a-version"></a>Verzió importálása és exportálása

A verziók az alkalmazás szintjén importálhatók. Ez a verzió lesz az aktív verzió, és a verziószámot használja az alkalmazás `versionId` tulajdonságában. A verzió szintjén is importálhat egy meglévő alkalmazást. Az új verzió lesz az aktív verzió. 

Egy verzió is exportálható az alkalmazás vagy a verzió szintjén is. Az egyetlen különbség, hogy az alkalmazás-szintű exportált verzió a jelenleg aktív verzió a verzió szintjén, a **[Beállítások](luis-how-to-manage-versions.md)** lapon bármilyen verziót kiválaszthat az exportáláshoz. 

Az exportált fájl **nem** tartalmazza a következőket:

* A géppel megtanult információk, mert az alkalmazás a importálása után újra be lett tanítva
* közreműködői információ

A LUIS-alkalmazás sémájának biztonsági mentéséhez exportáljon egy verziót a [Luis portálról](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Közreműködői változások kezelése verziók és közreműködők révén

A LUIS az Azure-erőforrásokra vonatkozó engedélyek biztosításával a közreműködők fogalmát használja egy alkalmazáshoz. Ezt a koncepciót a verziószámozással kombinálva megcélozható együttműködés biztosítható. 

A következő módszerekkel kezelheti az alkalmazás közreműködői módosításait.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Alkalmazáson belül több verziók kezelése

Először [klónozást](luis-how-to-manage-versions.md#clone-a-version) kell kezdenie az egyes szerzők alapverziójából. 

Minden szerző módosítja az alkalmazás saját verzióját. Ha a szerző elégedett a modellel, exportálja az új verziókat a JSON-fájlokba.  

Az exportált alkalmazások, a. JSON vagy a. lu fájlok összehasonlítható a változásokkal. Egyesítse a fájlokat úgy, hogy egyetlen fájlt hozzon létre az új verzióval. Módosítsa a `versionId` tulajdonságot úgy, hogy az az új egyesített verziót jelenti. Importálja azt a verziót az eredeti alkalmazásba. 

Ez a módszer lehetővé teszi, hogy egy aktív verzióját, egy szakasz és egy közzétett verziója. Az aktív verzió eredményeit összehasonlíthatja egy közzétett verzióval (fázis vagy éles környezet) az [interaktív tesztelési panelen](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Alkalmazások több verziók kezelése

[Exportálja](luis-how-to-manage-versions.md#export-version) az alapverziót. Mindegyik Szerző importálja a verziót. A személy, amely az alkalmazás importál a verzió tulajdonosa. Amikor végzett a verzió módosítása az alkalmazás exportálása. 

Exportált alkalmazások olyan JSON-formátumú fájlokat, amelyek a módosítások az alap exportálás összehasonlíthatók. A fájlokat, és hozzon létre egy egyetlen JSON-fájlt az új verzió össze. Módosítsa a JSON **versionId** tulajdonságát úgy, hogy az az új egyesített verziót jelenti. Importálja azt a verziót az eredeti alkalmazásba.

További információ a [közreműködők](luis-how-to-collaborate.md)hozzájárulásainak létrehozásáról.

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Az új iterációs ciklus megkezdéséhez tekintse át a végpont hosszúságú kimondott szöveg

Ha egy iterációs ciklust használ, megismételheti a folyamatot. Első lépésként [tekintse meg az előrejelzési végpont hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md) , amely alacsony megbízhatósággal van megjelölve. Ezeket a hosszúságú kimondott szöveg a helyes előre jelzett szándékot, valamint a helyes és a kinyert entitást is megvizsgálhatja. A módosítások áttekintése és elfogadása után a felülvizsgálati listának üresnek kell lennie.  

## <a name="next-steps"></a>További lépések

Ismerje meg az [együttműködéssel](luis-concept-keys.md)kapcsolatos fogalmakat.
