---
title: A Knowledge Store bemutatása (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Gazdagított dokumentumokat küldhet az Azure Storage-ba, ahol megtekintheti, átalakíthatja és felhasználhatja a dúsított dokumentumokat az Azure Cognitive Search és más alkalmazásokban. Ez a funkció nyilvános előzetes verzióban érhető el.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942995"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Az Azure Cognitive Search tudásbázisának bemutatása

> [!IMPORTANT] 
> A Knowledge Store jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) előzetes funkciókat biztosít. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

A Knowledge Store az Azure Cognitive Search egyik funkciója, amely egy mesterséges elemzésre vagy az alsóbb rétegbeli feldolgozásra szolgáló AI-bővítési [folyamat](cognitive-search-concept-intro.md) kimenetét őrzi meg. A *dúsított dokumentum* egy folyamat kimenete, amely az AI-folyamatokkal kinyert, strukturált és elemzett tartalomból készült. A standard AI-folyamatokban a dúsított dokumentumok átmenetiek, csak indexelés során használhatók, majd elvetették őket. A Knowledge Store-ban a dúsított dokumentumok megmaradnak. 

Ha korábban már használta a kognitív képességeket, már tudja, hogy a *szakértelmével* a bővítési folyamaton keresztül helyezi át a dokumentumot. Az eredmény lehet keresési index, vagy (ebben az előzetes verzióban új) vetítések egy Tudásbázisban. A két kimenet, a keresési index és a Knowledge Store azonos folyamat termékei. azonos bemenetből származtatva, de az olyan kimenetet eredményez, amely strukturált, tárolt és nagyon különböző módokon használható.

Fizikailag a Knowledge Store az [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), az Azure Table Storage, az Azure Blob Storage vagy mindkettő. Minden olyan eszköz vagy folyamat, amely képes az Azure Storage-hoz kapcsolódni, felhasználhatja a Tudásbázis tartalmát.

![Knowledge Store a folyamat ábráján](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Knowledge Store a folyamat ábráján")

## <a name="benefits-of-knowledge-store"></a>A Knowledge Store előnyei

A Knowledge Store strukturálatlan és félig strukturált adatfájlok, például a Blobok, a képfájlok, illetve az elemzésen átesett, vagy akár strukturált adatok összegyűjtését is lehetővé teszi új űrlapokra. Egy [lépésenkénti útmutatóban](knowledge-store-create-rest.md)megtekintheti, hogy egy sűrű JSON-dokumentum hogyan osztható ki alstruktúrákba, új struktúrákba felépítve, és egyéb módon elérhetővé válik az alárendelt folyamatokhoz, például a gépi tanuláshoz és az adatelemzési feladatokhoz.

Bár hasznos lehet megtekinteni, hogy mit hozhat létre a mesterséges intelligencia-bővítési folyamat, a Knowledge Store valódi potenciálja az Adatátalakítási képesség. Érdemes lehet egy alapszintű készségkészlet, majd megismételni a szerkezet növelését, amelyet később új struktúrákba, más alkalmazásokban pedig az Azure Cognitive Search is használhat.

Enumerált, a Knowledge Store előnyei a következők:

+ A kereséstől eltérő [elemzési és jelentéskészítő eszközökön](#tools-and-apps) a dúsított dokumentumokat használhatja. A Power BI a Power Query vonzó választás, de az Azure Storage-hoz csatlakoztatható bármely eszköz vagy alkalmazás lehívható a létrehozott Tudásbázisból.

+ Egy AI-indexelési folyamat pontosítása a lépések és a készségkészlet-definíciók hibakeresése közben. A Tudásbázis egy AI-indexelési folyamat készségkészlet-definíciójának termékét jeleníti meg. Ezeket az eredményeket a jobb készségkészlet kialakításához használhatja, mert láthatja, hogy pontosan mit néz ki a gazdagítás. Az Azure Storage [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) használatával megtekintheti a Tudásbázis tartalmát.

+ Az új űrlapokra alakítja az adathalmazt. Az átformálás a szakértelmével-ben kodifikált, de a lényeg az, hogy egy készségkészlet most már megadhatja ezt a képességet. A feladathoz való igazodás érdekében a [shapeer-képesség](cognitive-search-skill-shaper.md) az Azure Cognitive Search kiterjeszthető. Az átformálás lehetővé teszi, hogy olyan leképezést határozzon meg, amely igazodik az adatfelhasználáshoz a kapcsolatok megőrzése mellett.

> [!Note]
> Újdonságok a mesterséges intelligencia és a kognitív képességek terén? Az Azure Cognitive Search Cognitive Services látási és nyelvi funkciókkal integrálható és gazdagíthatja a forrásadatokat az optikai karakterfelismerés (OCR) révén a képfájlok, az entitások felismerése és a szöveges fájlokból kinyert kulcsfontosságú kifejezések használatával. További információ: [AI-bővítés az Azure Cognitive Searchban](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Fizikai tárterület

A Tudásbázis fizikai kifejezése egy Készségkészlet egy `knowledgeStore` definíciójának `projections` elemével van elfoglalva. A leképezés a kimenet szerkezetét határozza meg, hogy az megfeleljen a kívánt felhasználásnak.

A vetítések táblázatként, objektumként vagy fájlként is megadhatók.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Az ebben a struktúrában megadott leképezés típusa határozza meg, hogy milyen típusú tárterületet használ a rendszer a Knowledge Store-ban.

+ A Table Storage `tables`megadásakor használatos. Definiáljon egy táblázatos leképezést, ha táblázatos jelentéskészítési struktúrákra van szüksége az analitikai eszközökbe való bemenetekhez vagy az adatkeretek más adattárakba való exportálásához. Több `tables` is megadhat a dúsított dokumentumok részhalmazának vagy keresztmetszetének lekéréséhez. Ugyanabban a kivetítési csoportban a tábla kapcsolatai megmaradnak, így dolgozhat együtt.

+ A blob Storage `objects` vagy `files`megadásakor használatos. Egy `object` fizikai ábrázolása egy olyan hierarchikus JSON-struktúra, amely egy dúsított dokumentumot jelöl. A `file` egy dokumentumból kinyert, érintetlenül továbbított képet a blob Storage-ba.

Egyetlen kivetítési objektum tartalmaz egy `tables`, `objects`, `files`, és számos forgatókönyv esetén elegendő lehet egy leképezés létrehozása. 

Lehetséges azonban, hogy több készletet is létrehozhat `table`-`object`-`file`-kivetítéseket, és ha eltérő adatkapcsolatot szeretne használni. Egy készleten belül az adat kapcsolódik, feltéve, hogy ezek a kapcsolatok léteznek és észlelhetők. Ha további készleteket hoz létre, az egyes csoportokban lévő dokumentumok soha nem kapcsolódnak egymáshoz. Több kivetítési csoport használatára például akkor lehet szükség, ha ugyanazt az adatmennyiséget szeretné használni az online rendszerhez, és egy adott módszert kell megjelenítenie, és ugyanazokat az adatelemzési folyamatokat kívánja használni, amelyeket egy adott adattudományi folyamatban is használhat másképp.

## <a name="requirements"></a>Követelmények 

Az [Azure Storage](https://docs.microsoft.com/azure/storage/) -t kötelező megadni. Fizikai tárterületet biztosít. A blob Storage-t, a Table Storage-t vagy mindkettőt használhatja. A blob Storage a nem ép, dúsított dokumentumokhoz használatos, általában akkor, ha a kimenet az alsóbb rétegbeli folyamatokra fog irányulni. A Table Storage az elemzéshez és jelentéskészítéshez gyakran használt, dúsított dokumentumokból álló szeletekhez használható.

A [készségkészlet](cognitive-search-working-with-skillsets.md) megadása kötelező. Tartalmazza a `knowledgeStore` definícióját, és meghatározza a dúsított dokumentumok szerkezetét és összeállítását. Nem hozhat létre a Knowledge Store-t üres készségkészlet használatával. A készségkészlet legalább egy képességgel rendelkeznie kell.

Az [Indexelő](search-indexer-overview.md) megadása kötelező. A készségkészlet egy indexelő hívja meg, amely a végrehajtást hajtja végre. Az indexelő a követelmények és attribútumok saját készletével rendelkeznek. Ezen attribútumok közül több közvetlen hatással van a Knowledge Store-ra:

+ Az indexelő egy [támogatott Azure-adatforrást](search-indexer-overview.md#supported-data-sources) igényelnek (ez a folyamat, amely végső soron létrehozza a Knowledge Store-t, az Azure-ban támogatott forrásból származó adatok kihúzásával kezdődik). 

+ Az indexelő keresési indexre van szükségük. Az indexelő megköveteli, hogy adjon meg egy index sémát, még akkor is, ha nem tervezi használni. A minimális indexnek egy sztring mezője van, amely kulcsként van kijelölve.

+ Az indexelő nem kötelező mező-hozzárendeléseket biztosít, amelyek a forrás mezőnek a cél mezőhöz való aliasához használatosak. Ha az alapértelmezett mező-hozzárendelést módosítani kell (más nevet vagy típust kell használnia), létrehozhat egy [mező-hozzárendelést](search-indexer-field-mappings.md) az indexelő alkalmazásban. A Knowledge Store kimenete esetében a cél lehet egy blob-objektum vagy-tábla mezője.

+ Az indexelő olyan ütemtervekkel és egyéb tulajdonságokkal rendelkezik, mint például a különböző adatforrások által biztosított változások észlelési mechanizmusai, szintén alkalmazhatók egy Tudásbázisban. A tartalom frissítését például rendszeres időközönként is [beütemezhetjük](search-howto-schedule-indexers.md) . 

## <a name="how-to-create-a-knowledge-store"></a>Tudásbázis létrehozása

A Knowledge Store létrehozásához használja a portált vagy az előzetes verziójú REST API (`api-version=2019-05-06-Preview`).

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Az **adat importálása** varázsló a Tudásbázis létrehozási lehetőségeit tartalmazza. A kezdeti feltáráshoz [négy lépésben hozza létre első tudásbázisát](knowledge-store-connect-power-bi.md).

1. Válasszon egy támogatott adatforrást.

1. Adja meg a dúsítást: csatoljon egy erőforrást, válassza a képességek lehetőséget, és adjon meg egy tudásbázist. 

1. Hozzon létre egy index sémát. A varázslónak szüksége van rá, és következtetni tud az Ön számára.

1. Futtassa a varázslót. A kinyerési, bővítési és tárolási művelet az utolsó lépésben történik.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Használja a Create Készségkészlet és az előnézet REST API

Egy `knowledgeStore` van definiálva egy [készségkészlet](cognitive-search-working-with-skillsets.md)belül, amelyet egy [Indexelő](search-indexer-overview.md)hív meg. A dúsítás során az Azure Cognitive Search létrehoz egy helyet az Azure Storage-fiókban, és a dúsított dokumentumokat a konfigurációtól függően blobként vagy táblázatként adja meg.

Jelenleg az előzetes verzió REST API az egyetlen olyan mechanizmus, amellyel programozott módon hozhat létre egy Knowledge Store-t. A könnyebben felderíthető megoldás az [első Knowledge áruház létrehozása a Poster és a REST API használatával](knowledge-store-create-rest.md).

Az előzetes verziójú szolgáltatásra vonatkozó hivatkozási tartalom a jelen cikk [API-hivatkozás](#kstore-rest-api) szakaszában található. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Kapcsolódás eszközökhöz és alkalmazásokhoz

Ha a dúsítások már léteznek a tárolóban, az Azure Blobhoz vagy a Table Storage-hoz kapcsolódó bármilyen eszköz vagy technológia használható a tartalmak feltárására, elemzésére és felhasználására. A következő lista a kezdete:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) a dúsított dokumentum-struktúra és-tartalom megtekintéséhez. Tekintse meg ezt az alapkonfigurációként szolgáló eszközként a Knowledge Store-tartalmak megtekintéséhez.

+ Jelentéskészítés és elemzés [Power bi](knowledge-store-connect-power-bi.md) . 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) a további manipulációhoz.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API-referencia

A REST API verzió `2019-05-06-Preview` a szakértelmével további definíciói segítségével biztosít a Tudásbázisban. A hivatkozáson kívül tekintse meg a [Knowledge Store létrehozása a Poster használatával](knowledge-store-create-rest.md) című részt az API-k meghívásával kapcsolatos részletekért.

+ [Készségkészlet létrehozása (API-Version = 2019-05 -06-előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Készségkészlet frissítése (API-Version = 2019-05 -06-előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>További lépések

A Knowledge Store az Azure Storage-fiókokhoz való hozzáférésre alkalmas bármely ügyfélalkalmazás által a készségkészlet tervezésekor hasznosnak tartja a dúsított dokumentumok megőrzését, illetve az új struktúrák és tartalmak felhasználását.

A bővített dokumentumok létrehozásának legegyszerűbb megközelítése [a portálon keresztül](knowledge-store-create-portal.md)történik, de a Poster és a REST API is használható, ami akkor hasznos, ha az objektumok létrehozásának és hivatkozásának módját szeretné megtekinteni.

> [!div class="nextstepaction"]
> [Knowledge Store létrehozása a Poster és a REST használatával](knowledge-store-create-rest.md)

További információ a kivetítésekről, a képességekről és a [készségkészlet való definiálásáról](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Kivetítések a Knowledge Store-ban](knowledge-store-projection-overview.md)

A speciális kivetítésekkel kapcsolatos fogalmakkal, például a szeleteléssel, a beágyazott formázással és a kapcsolatokkal, a [kivetítések meghatározása a Tudásbázisban](knowledge-store-projections-examples.md) című oktatóanyagban

> [!div class="nextstepaction"]
> [Kivetítések meghatározása a Knowledge Store-ban](knowledge-store-projections-examples.md)