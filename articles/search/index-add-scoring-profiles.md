---
title: Keresési rangsor növelése pontozási profilok használatával
titleSuffix: Azure Cognitive Search
description: Pontozási profilok hozzáadásával növelheti az Azure Cognitive Search eredményeinek keresési rangsorolási pontszámait.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/28/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 516637b812afece1966006ce6d894dd1e32e6293
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245459"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Pontozási profilok hozzáadása egy Azure Cognitive Search indexhez

  A pontozás a keresési eredményekben visszaadott összes elem *keresési pontszámának* kiszámítására vonatkozik. A pontszám az elemek relevanciáját jelzi az aktuális keresési művelet kontextusában. Minél nagyobb a pontszám, annál relevánsabb az elem. A keresési eredmények között az elemek sorrendje magasról alacsonyra van rendezve, az egyes elemek kiszámított keresési pontszáma alapján.  

 Az Azure Cognitive Search az alapértelmezett pontozást használja a kezdeti pontszám kiszámításához, de a számítást *pontozási profilon*keresztül is testreszabhatja. A pontozási profilok nagyobb mértékben szabályozzák a keresési eredményekben lévő elemek rangsorolását. Előfordulhat például, hogy a bevételi potenciál alapján szeretné növelni az elemeket, előléptetheti az újabb elemeket, vagy növelheti az olyan elemeket, amelyek túl hosszúak voltak a leltárban.  

 A pontozási profil az index definíciójának részét képezi, amely a súlyozott mezőkből, függvényekből és paraméterekből áll.  

 A következő példa egy "geo" nevű egyszerű profilt mutat be, amely azt mutatja be, hogy a pontozási profil milyen módon néz ki. Ez növeli a keresési kifejezést tartalmazó elemeket a **pezsgő** mezőben. Emellett az `distance` függvényt is használja az aktuális helytől számított tíz kilométeren belül. Ha valaki megkeresi a "Csárda" kifejezést, és a "Csárda" a Hotel neve részévé válik, akkor a találatok között a "Inn" helyet tartalmazó, a jelenlegi hely 10 KM-es sugarú helyén található dokumentumok nagyobbak lesznek.  


```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  


 A pontozási profil használatához a lekérdezés úgy lett kialakítva, hogy megadja a profilt a lekérdezési karakterláncban. Az alábbi lekérdezésben figyelje meg a kérelemben `scoringProfile=geo` lekérdezési paramétert.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2019-05-06 
```  

 Ez a lekérdezés a "Csárda" kifejezésre keres, és az aktuális helyen halad át. Figyelje meg, hogy ez a lekérdezés más paramétereket is tartalmaz, például `scoringParameter`. A lekérdezési paramétereket az [Azure Cognitive Search &#40;REST API&#41;keresési dokumentumok](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)című cikkben találja.  

 Kattintson a [példa](#bkmk_ex) lehetőségre egy pontozási profil részletesebb példájának áttekintéséhez.  

## <a name="what-is-default-scoring"></a>Mi az alapértelmezett pontozás?  
 A pontozás kiszámítja a keresési pontszámot a rangsorban rendezett eredményhalmaz minden egyes eleménél. A keresési eredményhalmaz minden eleme egy keresési pontszámhoz van rendelve, majd a legmagasabb a legalacsonyabb értékre van állítva. A magasabb pontszámú elemeket a rendszer visszaadja az alkalmazásnak. Alapértelmezés szerint a rendszer a legfontosabb 50-as értéket adja vissza, de a `$top` paraméterrel kisebb vagy nagyobb számú elemet adhat vissza (legfeljebb 1000 egyetlen válaszban).  

A keresési pontszám kiszámítása az adatok és a lekérdezés statisztikai tulajdonságai alapján történik. Az Azure Cognitive Search megkeresi azokat a dokumentumokat, amelyek tartalmazzák a keresési kifejezéseket a lekérdezési karakterláncban (a `searchMode`tól függően), a keresési kifejezés számos példányát tartalmazó dokumentumokat. A keresési pontszám még magasabbra nő, ha a kifejezés az adatindexben ritkán előfordul, de a dokumentumon belül is. A számítástechnikai szempontoknak való megfelelés alapja a [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) vagy a kifejezés gyakorisága – fordított dokumentum gyakorisága.  

 Feltéve, hogy nincs egyéni rendezés, a találatok a keresési pontszám szerint vannak rangsorolva, mielőtt visszatérnek a hívó alkalmazáshoz. Ha $top nincs megadva, a rendszer 50 elemet ad vissza a legmagasabb keresési pontszámmal.  

 A keresési pontszám értékei megismételhetők egy eredményhalmaz során. Előfordulhat például, hogy 10 elemet tartalmaz egy 1,2-as pontszámmal, 20 elemet egy 1,0-es pontszámmal, 20 elemet pedig egy 0,5-as pontszámmal. Ha több találat azonos keresési pontszámmal rendelkezik, az azonos pontszámú elemek sorrendje nincs definiálva, és nem stabil. Futtassa újra a lekérdezést, és előfordulhat, hogy az elemek eltolási pozíciója látható. Az azonos pontszámú két elem esetében nincs garancia arra, hogy az egyik első megjelenjen.  

## <a name="when-to-use-custom-scoring"></a>Mikor kell egyéni pontozást használni  
 Hozzon létre egy vagy több pontozási profilt, ha az alapértelmezett rangsorolási viselkedés nem elég messze az üzleti céloknak megfelelően. Dönthet például úgy, hogy a keresés relevanciája előnyben részesített, újonnan hozzáadott elemeket is tartalmaz. Hasonlóképpen lehet olyan mező, amely haszonkulcsot vagy más, bevételi potenciált jelző mezőt tartalmaz. Az üzleti előnyökkel járó látogatottság növelése fontos tényező lehet a pontozási profilok használatának eldöntésekor.  

 A relevancia-alapú rendezés a pontozási profilok használatával is megvalósítható. A múltban használt keresési találati lapokon érdemes megfontolnia, hogy az ár, a dátum, a minősítés vagy a relevancia alapján rendezhető legyen. Az Azure Cognitive Searchban a pontozási profilok a "relevancia" lehetőséget vezetik be. A relevancia definícióját Ön vezérli, az üzleti célkitűzésekkel és a kívánt keresési élmény típusával.  

##  <a name="bkmk_ex"></a>Például  
 Ahogy azt korábban említettük, a testreszabott pontozás az index sémában definiált egy vagy több pontozási profilon keresztül valósítható meg.  

 Ez a példa egy index sémáját mutatja be két pontozási profillal (`boostGenre`, `newAndHighlyRated`). Az indextel kapcsolatos bármilyen lekérdezés, amely a profilt lekérdezési paraméterként tartalmazza, a profilt fogja használni az eredményhalmaz kiértékeléséhez.  

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>Munkafolyamat  
 Az egyéni pontozási viselkedés megvalósításához adjon hozzá egy pontozási profilt az indexet definiáló sémához. Egy indexen belül akár 100 pontozási profilt is megadhat (lásd a [szolgáltatási korlátokat](search-limits-quotas-capacity.md)), de az adott lekérdezésben egyszerre csak egy profilt lehet megadni.  

 Kezdje a témakörben megadott [sablonnal](#bkmk_template) .  

 Adjon meg egy nevet. A pontozási profilok megadása nem kötelező, de ha hozzáad egyet, a nevet kötelező megadni. Ügyeljen arra, hogy kövesse a mezők elnevezési konvencióit (betűvel kezdődik, kerülje a speciális karaktereket és a fenntartott szavakat). Lásd: az [Azure &#40;Cognitive Search&#41; elnevezési szabályai](https://docs.microsoft.com/rest/api/searchservice/naming-rules) a teljes listához.  

 A pontozási profil törzse súlyozott mezőkből és függvényekből épül fel.  

|||  
|-|-|  
|**Súlyozással**|Adjon meg olyan név-érték párokat, amelyek egy relatív súlyozást rendelnek egy mezőhöz. A [példában](#bkmk_ex)a albumTitle, a műfaj és a artistName mező a 1,5, 5 és 2 közötti növekedést eredményezi. Miért növelte a műfaj a többinél nagyobb mértékben? Ha a keresés olyan adatmennyiségen keresztül történik, amely némileg homogén (például a "műfaj" a `musicstoreindex`ban), akkor nagyobb eltérésre lehet szükség a relatív súlyok esetében. A `musicstoreindex`például a "rock" a műfajként és az azonos módon megfogalmazott műfajok leírásában jelenik meg. Ha azt szeretné, hogy a műfaj meghaladja a műfaj leírását, a műfaj mezőnek sokkal nagyobb relatív súlyra van szüksége.|  
|**Functions**|Akkor használatos, ha további számításokra van szükség az adott környezetekhez. Az érvényes értékek: `freshness`, `magnitude`, `distance`és `tag`. Minden függvényhez egyedi paraméterek tartoznak.<br /><br /> -   `freshness` akkor kell használni, ha az új vagy a régi elemek kiemelését szeretné növelni. Ez a függvény csak `datetime` mezőkkel használható (EDM. DataTimeOffset). Figyelje meg, hogy a `boostingDuration` attribútum csak a `freshness` függvénnyel használható.<br />-   `magnitude`t akkor kell használni, ha a magas vagy alacsony numerikus érték alapján szeretne fokozni. A függvényt meghívó forgatókönyvek közé tartozik a haszonkulcs, a legmagasabb ár, a legalacsonyabb ár vagy a letöltések száma. Ez a függvény csak Double és Integer mezőkkel használható.<br />     A `magnitude` függvénynél visszafordíthatja a tartományt, magas – alacsony értékre, ha azt szeretné, hogy az inverz minta (például az alacsonyabb díjszabású elemek magasabb árú elemek kiemelése). A $100 és $1 közötti árak széles választéka miatt a `boostingRangeStart` a 100-as és a `boostingRangeEnd` az alacsonyabb árú elemek növelése érdekében.<br />-   `distance` akkor kell használni, ha a közelséget vagy a földrajzi helyet szeretné növelni. Ezt a függvényt csak `Edm.GeographyPoint` mezőkkel lehet használni.<br />-   `tag`t akkor kell használni, ha a dokumentumok és a keresési lekérdezések között közös címkéket szeretne növelni. Ezt a függvényt csak `Edm.String` és `Collection(Edm.String)` mezőkkel lehet használni.<br /><br /> **A függvények használatára vonatkozó szabályok**<br /><br /> A függvény típusa (`freshness`, `magnitude`, `distance`), a `tag`nak kisbetűnek kell lennie.<br /><br /> A függvények nem tartalmazhatnak null értékű vagy üres értékeket. Pontosabban, ha a mezőnév-t is tartalmazza, be kell állítania valamire.<br /><br /> A függvények csak szűrhető mezőkre alkalmazhatók. A szűrhető mezőkkel kapcsolatos további információkért lásd: [create index &#40;Azure Cognitive Search REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) .<br /><br /> A függvények csak olyan mezőkre alkalmazhatók, amelyek az index mezőinek gyűjteményében vannak meghatározva.|  

 Az index meghatározása után hozza létre az indexet az index sémájának feltöltésével, amelyet a dokumentumok követnek. Lásd: [create &#40;index Azure Cognitive Search&#41; REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) , valamint az [Azure Cognitive Search REST API &#40;&#41; dokumentumok hozzáadása, frissítése vagy törlése](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) a műveletekre vonatkozó utasításokért. Az index létrehozása után olyan funkcionális pontozási profillal kell rendelkeznie, amely együttműködik a keresési adataival.  

##  <a name="bkmk_template"></a>Sablon  
 Ez a szakasz a pontozási profilok szintaxisát és sablonját mutatja be. Az attribútumok leírását a következő szakaszban található [index attribútumok](#bkmk_indexref) című rész tartalmazza.  

```  
. . .   
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "...",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="bkmk_indexref"></a>Index attribútumainak referenciája  

> [!NOTE]  
>  Pontozási függvény csak szűrhető mezőkre alkalmazható.  

|Attribútum|Leírás|  
|---------------|-----------------|  
|`name`|Kötelező. Ez a pontozási profil neve. Egy mező azonos elnevezési konvencióit követi. Betűvel kell kezdődnie, nem tartalmazhat pontokat, kettőspontokat vagy @ szimbólumokat, és nem kezdődhet a "azureSearch" kifejezéssel (kis-és nagybetűk megkülönböztetése).|  
|`text`|A súlyok tulajdonságot tartalmazza.|  
|`weights`|Választható. Név-érték párokat tartalmaz, amelyek mindegyike megadja a mező nevét és a relatív súlyt. A relatív súlyozásnak pozitív egész számnak vagy lebegőpontos számnak kell lennie.<br /><br /> A súlyok a kereshető mezők egy másikhoz viszonyított fontosságának jelzésére szolgálnak.|  
|`functions`|Választható. Pontozási függvény csak szűrhető mezőkre alkalmazható.|  
|`type`|Pontozási függvények esetén szükséges. Meghatározza a használni kívánt függvény típusát. Az érvényes értékek közé tartozik a magnitúdó, a frissesség, a távolság és a címke. Az egyes pontozási profilokban több függvényt is felvehet. A függvény nevének kisbetűnek kell lennie.|  
|`boost`|Pontozási függvények esetén szükséges. A nyers pontszám szorzóként használt pozitív száma. Értéke nem lehet 1.|  
|`fieldname`|Pontozási függvények esetén szükséges. Pontozási függvény csak olyan mezőkre alkalmazható, amelyek az index mező-gyűjteményének részét képezik, és amelyek szűrhetők. Emellett a függvények egyes típusai további korlátozásokat is bevezetnek (a frissesség a DateTime mezőkkel, az egész számmal vagy a dupla mezőkkel, valamint a távolság a hely mezőivel). Függvény definíciójában csak egyetlen mezőt lehet megadni. Ha például egy profilban kétszer szeretné használni a magnitúdót, két definíciót kell tartalmaznia, egyet az egyes mezőkhöz.|  
|`interpolation`|Pontozási függvények esetén szükséges. Meghatározza azt a lejtőt, amelynek a pontszámának növelése a tartomány elejétől a tartomány végéig nő. Az érvényes értékek a következők: lineáris (alapértelmezett), állandó, másodfokú és logaritmikus. További részletek: [Interpolációk beállítása](#bkmk_interpolation) .|  
|`magnitude`|A magnitúdó pontozási függvény egy numerikus mező értékeinek tartománya alapján változtatja meg a rangsort. A leggyakoribb felhasználási példák a következők:<br /><br /> -   **Star Ratings:** módosítsa a pontozást a "Star rating" mező értéke alapján. Ha két elem van jelentősége, akkor a magasabb minősítésű elem jelenik meg először.<br />-   **margin:** ha két dokumentumra van szükség, akkor a kiskereskedő a magasabb árrésű dokumentumokat szeretné növelni.<br />-   **kattintson a Counts (számlálások** ) elemre: olyan alkalmazások esetében, amelyek nyomon követik a termékek vagy lapok műveleteit<br />-   **letöltési számok:** a letöltéseket nyomon követő alkalmazások esetében a magnitúdó függvény lehetővé teszi a legtöbb letöltéssel rendelkező elemek növelését.|  
|`magnitude` &#124; `boostingRangeStart`|Megadja annak a tartománynak az indítási értékét, amelynél a skálán szerepel a magnitúdó. Az értéknek egész vagy lebegőpontos számnak kell lennie. Az 1 – 4 csillagos minősítések esetében ez 1. 50%-nál nagyobb árrések esetén ez a 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Annak a tartománynak a záró értékét állítja be, amelynek a nagysága a pontszám. Az értéknek egész vagy lebegőpontos számnak kell lennie. Az 1 – 4 csillagos minősítések esetében ez 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Az érvényes értékek: true vagy FALSE (alapértelmezett). Ha igaz értékre van állítva, a teljes növekedés továbbra is érvényes lesz azokra a dokumentumokra, amelyek értéke a cél mezőnél magasabb, mint a tartomány felső vége. Hamis érték esetén a függvény fellendítése nem lesz alkalmazva azokra a dokumentumokra, amelyek értéke a tartományon kívül eső cél mezőhöz tartozik.|  
|`freshness`|A frissességi pontozási függvény az elemek rangsorolási pontszámának módosítására szolgál `DateTimeOffset` mezőkben lévő értékek alapján. Például egy újabb dátummal rendelkező elem magasabb lehet a régebbi elemeknél.<br /><br /> Lehetőség van olyan elemek rangsorolására is, mint például a naptári események jövőbeli dátumokkal való kiosztása, hogy a jelenben lévő elemek a jövőben is magasabbak legyenek.<br /><br /> A jelenlegi kiadásban a tartomány egy végét az aktuális időpontra rögzíti a rendszer. A másik végén egy idő van a múltban a `boostingDuration`alapján. A jövőbeli időtartományok kiemeléséhez használjon negatív `boostingDuration`.<br /><br /> A maximális és a minimális tartomány változásának növelését a pontozási profilra alkalmazott interpoláció határozza meg (lásd az alábbi ábrát). Az alkalmazott növelési tényező visszafordításához válasszon egy 1-nél kisebb növelési faktort.|  
|`freshness` &#124; `boostingDuration`|A lejárati időszakot állítja be, amely után a kiemelés leáll egy adott dokumentum esetében. A szintaxist és példákat a következő szakaszban találja: [BoostingDuration beállítása](#bkmk_boostdur) .|  
|`distance`|A távolsági pontozási függvény a dokumentumok pontszámának befolyásolására szolgál, attól függően, hogy milyen közel vagy messze vannak a hivatkozási földrajzi helyhez viszonyítva. A hivatkozás helye a lekérdezés részeként szerepel egy paraméterben (a `scoringParameterquery` karakterlánc beállítás használatával), amely Lon, Lat argumentumként van megadva.|  
|`distance` &#124; `referencePointParameter`|A hivatkozási helyként használandó lekérdezésekben átadandó paraméter. `scoringParameter` egy lekérdezési paraméter. A lekérdezési paraméterek leírását lásd: [dokumentumok &#40;keresése az Azure Cognitive Search&#41; REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) .|  
|`distance` &#124; `boostingDistance`|Egy szám, amely megadja a távolságot kilométerben annak a hivatkozásnak a helyétől, ahol a kiemelési tartomány véget ér.|  
|`tag`|A címke pontozása függvény a dokumentumok és a keresési lekérdezések címkéjén alapuló dokumentumok pontszámának befolyásolására szolgál. A keresési lekérdezéssel közös címkékkel rendelkező dokumentumok is lendületet kapnak. A keresési lekérdezés címkéi pontozási paraméterként jelennek meg az egyes keresési kérelmekben (a `scoringParameterquery` karakterlánc beállítás használatával).|  
|`tag` &#124; `tagsParameter`|Egy adott kéréshez tartozó címkék megadására szolgáló lekérdezésekben átadandó paraméter. `scoringParameter` egy lekérdezési paraméter. A lekérdezési paraméterek leírását lásd: [dokumentumok &#40;keresése az Azure Cognitive Search&#41; REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) .|  
|`functionAggregation`|Választható. Csak akkor érvényes, ha a függvények meg vannak adva. Az érvényes értékek a következők: Sum (alapértelmezett), Average, minimum, maximum és firstMatching. A keresési pontszám egyetlen érték, amely több változóból lett kiszámítva, beleértve több függvényt is. Ez az attribútum azt jelzi, hogy az összes függvény fellendítése egyetlen összesített lökésbe van összevonva, amely azután az alapdokumentum pontszámára lesz alkalmazva. Az alappontszám a dokumentumból és a keresési lekérdezésből kiszámított [TF-IDF](http://www.tfidf.com/) értéken alapul.|  
|`defaultScoringProfile`|Ha keresési kérést hajt végre, ha nincs megadva pontozási profil, akkor a rendszer az alapértelmezett pontozást használja (csak[TF-IDF](http://www.tfidf.com/) esetén).<br /><br /> Itt állíthatja be az alapértelmezett pontozási profil nevét, ami azt eredményezi, hogy az Azure Cognitive Search használja ezt a profilt, ha a keresési kérelemben nincs megadva adott profil.|  

##  <a name="bkmk_interpolation"></a>Interpolációk beállítása  
 Az Interpolációk lehetővé teszik a pontozáshoz használt lejtő alakjának megadását. Mivel a pontozás magas – alacsony, a lejtő mindig csökken, de az interpoláció határozza meg a lefelé lejtő görbe görbéjét. A következő interpolációkat lehet használni:  

|||  
|-|-|  
|`linear`|A maximális és a minimális tartományon belüli elemek esetében az elemre alkalmazott kiemelés folyamatosan csökkenő mennyiségű lesz. A lineáris egy pontozási profil alapértelmezett interpolációja.|  
|`constant`|A kezdő és a záró tartományba tartozó elemek esetében állandó lökést alkalmaz a rendszer a rangsor eredményeire.|  
|`quadratic`|A folyamatosan csökkenő lendülettel rendelkező lineáris interpolációval összehasonlítva a másodfokú rendszer kezdetben kisebb ütemben csökken, majd a végpontot megközelítve sokkal nagyobb intervallumot eredményez. Ez az interpolációs beállítás nem engedélyezett a címkézési pontozási függvényeknél.|  
|`logarithmic`|A folyamatosan csökkenő lendülettel rendelkező lineáris interpolációval összehasonlítva a logaritmikus érték kezdetben nagyobb ütemben csökken, majd a végpontot megközelítő módon kevesebb időt vesz fel. Ez az interpolációs beállítás nem engedélyezett a címkézési pontozási függvényeknél.|  

 ![Állandó, lineáris, másodfokú, log10 vonalak a gráfon](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="bkmk_boostdur"></a>BoostingDuration beállítása  
 a `boostingDuration` a `freshness` függvény egyik attribútuma. Ezzel a beállítással megadhat egy lejárati időszakot, amely után az egy adott dokumentumra vonatkozó növelés leáll. Ha például egy terméket vagy márkát szeretne egy 10 napos promóciós időszakra kijavítani, akkor az adott dokumentumok esetében a 10 napos időszakot "P10D" értékre kell állítani.  

 az `boostingDuration`t XSD "dayTimeDuration" értékként kell formázni (az ISO 8601 időtartamának korlátozott részhalmaza). A minta ehhez a következő: "P [nD] [T [nH] [nM] [nS]]".  

 A következő táblázat több példát is tartalmaz.  

|Időtartam|boostingDuration|  
|--------------|----------------------|  
|1 nap|"P1D"|  
|2 nap és 12 óra|"P2DT12H"|  
|15 perc|"PT15M"|  
|30 nap, 5 óra, 10 perc és 6,334 másodperc|"P30DT5H10M 6.334 S"|  

 További példákat az [XML-séma: adattípusok (w3.org webhely)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)című témakörben talál.  

## <a name="see-also"></a>Lásd még  
 [Azure Cognitive Search REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Index &#40;létrehozása Azure Cognitive Search REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
