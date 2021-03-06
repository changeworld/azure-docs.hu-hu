---
title: 'Oktatóanyag: webszolgáltatások migrálása a Google Maps szolgáltatásból | Microsoft Azure térképek'
description: Webszolgáltatások migrálása a Google Maps szolgáltatásból Microsoft Azure Maps-be.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: fae9b8a2101329383cc90c8f7f0ff225e3a9059c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913818"
---
# <a name="migrate-web-service-from-google-maps"></a>Webszolgáltatás migrálása a Google Mapsből

Az Azure és a Google Maps egyaránt hozzáférést biztosít a térbeli API-khoz a REST-alapú webszolgáltatásokon keresztül. Ezeknek a platformoknak a API-felületei hasonló funkciókat hajtanak végre. Azonban mindegyik különböző elnevezési konvenciókat és válasz objektumokat használ.

A táblázat megjeleníti a Azure Maps Service API-kat, amelyek hasonló funkcióval rendelkeznek a felsorolt Google Maps Service API-khoz.

| Google Maps szolgáltatás API | Azure Maps Service API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Irányban              | [Útvonal](https://docs.microsoft.com/rest/api/maps/route)                                     |
| Távolsági mátrix         | [Útvonal-mátrix](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| Geokódolás               | [Search](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Helyek keresése           | [Search](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Automatikus kiegészítés      | [Search](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Elérési út            | Lásd: [útvonalak és utasítások kiszámítása](#calculate-routes-and-directions) szakasz.            |
| Sebességkorlátozások            | Lásd: [koordináta szakasz fordított geocode](#reverse-geocode-a-coordinate) .                  |
| Statikus Térkép              | [Renderelési](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| Időzóna               | [Időzóna](https://docs.microsoft.com/rest/api/maps/timezone)                              |

A következő szolgáltatási API-k jelenleg nem érhetők el Azure Mapsban:

- Jogosultságszint
- Térinformatikai
- Helyek részletei és fényképek – a telefonszámok és a webhely URL-címe a Azure Maps Search API-ban érhető el.
- Térkép URL-címei
- Legközelebbi utak – ez elérhető a web SDK-val, ahogy az [itt](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
)látható, de jelenleg nem érhető el szolgáltatásként.
- Statikus utcai nézet

Azure Maps számos további REST-webszolgáltatással rendelkezik, amelyek érdekesek lehetnek:

- [Térbeli műveletek](https://docs.microsoft.com/rest/api/maps/spatial): összetett térbeli számítások és műveletek (például geokerítések) kiszervezése szolgáltatáshoz.
- [Forgalom](https://docs.microsoft.com/rest/api/maps/traffic): a valós idejű forgalom és az incidensek adatforgalmának elérése.

## <a name="geocoding-addresses"></a>Helymeghatározáshoz-címek

A helymeghatározáshoz a címek koordinátabe alakításának folyamata. Az "1 Microsoft Way, Redmond, WA" érték például a következőre konvertálja a hosszúságot:-122,1298, szélesség: 47,64005. Ezt követően a koordináták különböző típusú célokra használhatók, például a Térkép középpontba helyezi a jelölőt.

A Azure Maps számos módszert biztosít a helymeghatározáshoz-címekhez:

- [**Szabad formátumú helymeghatározáshoz**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): egyetlen címnek megfelelő karakterláncot kell megadni, és azonnal fel kell dolgozni a kérést. az "1 Microsoft Way, Redmond, WA" egy példa egyetlen címtartomány-karakterláncra. Ez az API akkor javasolt, ha gyorsan kell geocode az egyes címeket.
- [**Strukturált helymeghatározáshoz**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): egyetlen cím részeit adja meg, például az utca nevét, a várost, az országot és az irányítószámot, és azonnal feldolgozza a kérést. Ez az API akkor javasolt, ha gyorsan kell geocode az egyes címeket, és az adatai már az egyes címekre is bekerülnek.
- [**Batch-helymeghatározáshoz**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): hozzon létre egy legfeljebb 10 000 címet tartalmazó kérelmet, és egy adott időszakban dolgozza fel azokat. A rendszer az összes címet párhuzamosan geokódolva a kiszolgálón, és amikor elkészült, a teljes eredményhalmazt letöltheti. Ez a nagyméretű adatkészletek helymeghatározáshoz ajánlott.
- [**Fuzzy keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ez az API ötvözi a helymeghatározáshoz a keresett ponttal. Ez az API egy szabad formátumú karakterláncot vesz igénybe. Ez a karakterlánc lehet a címe, a hely, a mérföldkő, a hasznos hely vagy az érdeklődési pont kategóriája. Ez az API közel valós időben dolgozza fel a kérelmet. Ez az API olyan alkalmazások esetében ajánlott, amelyekben a felhasználók egy adott szövegmezőben keresik meg a címeket vagy érdekes pontokat.
- [**Fuzzy batch-keresés**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): hozzon létre egy olyan kérést, amely legfeljebb 10 000 címet, helyet, tereptárgyat vagy érdeklődési pontot tartalmaz, és egy adott időszakban feldolgozható. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` és `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` – város/város<br/>`municipalitySubdivision` – szomszédság, Sub/Super City<br/>`countrySubdivision` – állam vagy megye<br/>`countrySecondarySubdivision` – megye<br/>`countryTertiarySubdivision` – kerület<br/>`countryCode` – kétbetűs országkód |
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `region`                    | `countrySet`                       |

A Search szolgáltatás használatának példáját [itt](how-to-search-for-address.md)találja. Mindenképpen tekintse át [az ajánlott eljárásokat a kereséshez](how-to-use-best-practices-for-search.md).

> [!TIP]
> A szabad formátumú helymeghatározáshoz és a fuzzy keresési API-k automatikus kiegészítési módban is használhatók `&amp;typeahead=true` a kérelem URL-címéhez való hozzáadásával. Ez azt közli a kiszolgálóval, hogy a bemeneti szöveg valószínűleg részleges, és a keresés a prediktív módba lép.

## <a name="reverse-geocode-a-coordinate"></a>Koordináta fordított geocode

A fordított helymeghatározáshoz az a folyamat, amellyel a földrajzi koordinátákat egy hozzávetőleges címmé alakíthatja. Koordináták a "hosszúság:-122,1298, Latitude: 47,64005" értékre váltás "1 Microsoft Way, Redmond, WA" értékűre.

Azure Maps több fordított helymeghatározáshoz módszert biztosít:

- [**Fordított geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): egyetlen földrajzi koordináta megadásával lekérheti a koordinátanak megfelelő hozzávetőleges címeket. Feldolgozza a kérést közel valós időben.
- [**Cross Street fordított geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): egyetlen földrajzi koordinátat határozhat meg a közeli Cross Street-információk eléréséhez és a kérés azonnali feldolgozásához. Előfordulhat például, hogy a következő Cross Streets, az első Ave és a Main St.
- [**Fordított geocoder**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): hozzon létre egy legfeljebb 10 000 koordinátákat tartalmazó kérelmet, amely egy adott időszakban feldolgozható. A rendszer minden adatfeldolgozást párhuzamosan dolgoz fel a kiszolgálón. A kérelem befejezésekor letöltheti a teljes eredményt.

Ez a táblázat a Google Maps API paramétereit a Azure Maps hasonló API-paramétereivel hivatkozik.

| Google Maps API-paraméter   | Hasonló Azure Maps API-paraméter   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `latlng`                    | `query`  |
| `location_type`             | *N/A*     |
| `result_type`               | `entityType`    |

Tekintse át [az ajánlott eljárásokat a kereséshez](how-to-use-best-practices-for-search.md).

A Azure Maps fordított helymeghatározáshoz API további funkciókkal rendelkezik, amelyek nem érhetők el a Google Maps szolgáltatásban. Ezek a funkciók hasznosak lehetnek az alkalmazással való integrációhoz, amikor az alkalmazást áttelepíti:

- Sebességkorlátozó-adatlekérdezés
- Közúti használati információk beolvasása: helyi út, artériás, korlátozott hozzáférés, rámpa stb.
- Az utca azon oldalának lekérése, amelyen a koordináta található

## <a name="search-for-points-of-interest"></a>Hasznos helyek keresése

A helyek keresési API-ját a Google Maps szolgáltatásban keresheti meg. Ez az API három különböző módszert biztosít a fontos helyek kereséséhez:

- **Hely keresése a következő szövegből:** Megkeresi az érdeklődési pontot a neve, címe vagy telefonszáma alapján.
- **Közeli keresés**: megkeresi azokat az érdeklődési pontokat, amelyek egy adott távolságon belül vannak.
- **Szöveges keresés:** Megkeresi a helyeket egy szabad formátumú szöveggel, amely az érdeklődési pont és a hely adatait is tartalmazza. Például: "pizza a New York-ban" vagy "éttermek a Main St közelében".

Azure Maps számos keresési API-t biztosít a hasznos helyek számára:

- [**POI-keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): érdeklődési pontok keresése név alapján. Például: "Starbucks".
- [**POI-kategória keresése**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): az érdeklődési pontok kategória szerint kereshetők. Például: "étterem".
- [**Közeli keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): megkeresi azokat az érdeklődési pontokat, amelyek egy adott távolságon belül vannak.
- [**Fuzzy keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ez az API ötvözi a helymeghatározáshoz a keresett ponttal. Ez az API egy olyan szabad formátumú karakterláncot vesz igénybe, amely lehet egy lakcím, egy hely, egy mérföldkő, egy érdekes hely vagy egy érdekes pont. Közel valós időben dolgozza fel a kérelmet. Ez az API olyan alkalmazások esetében ajánlott, amelyekben a felhasználók egy adott szövegmezőben keresik meg a címeket vagy érdekes pontokat.
- [**Keresés a geometrián belül**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): a megadott geometrián belüli érdeklődési pontok keresése. Keressen például egy érdekes pontot egy sokszögen belül.
- [**Keresés az útvonal mentén**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): keressen olyan pontokat, amelyek egy adott útvonal útvonalán találhatók.
- [**Fuzzy batch-keresés**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): hozzon létre legfeljebb 10 000 címet, helyet, tereptárgyat vagy érdeklődési pontot tartalmazó kérelmet. A kérelem feldolgozása egy adott időszakon belül megtörtént. A rendszer minden adatfeldolgozást párhuzamosan dolgoz fel a kiszolgálón. Ha a kérés befejezi a feldolgozást, letöltheti az eredmény teljes készletét.

Jelenleg Azure Maps nem rendelkezik hasonló API-val a Google Maps-beli szöveges keresési API-hoz.

> [!TIP]
> A POI Search, a POI kategória keresése és a fuzzy Search API-k automatikus kiegészítési módban is használhatók, ha `&amp;typeahead=true` a kérelem URL-címéhez adja hozzá. Ez azt közli a kiszolgálóval, hogy a bemeneti szöveg valószínűleg részleges. Az API a keresést prediktív módban hajtja végre.

Tekintse át a keresési dokumentációval [kapcsolatos ajánlott eljárásokat](how-to-use-best-practices-for-search.md) .

### <a name="find-place-from-text"></a>Hely keresése a szövegből

Az érdeklődési pontok kereséséhez használja az Azure Maps [POI Search](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) és a [fuzzy Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) kifejezést.

A táblázat a Google Maps API paramétereit a hasonló Azure Maps API-paraméterekkel hivatkozik.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter |
|---------------------------|-------------------------------------|
| `fields`                  | *N/A*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/A*                               |
| `key`                     | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `locationbias`            | `lat`, `lon` és `radius`<br/>`topLeft` és `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Közeli keresés

A [közeli keresési](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) API-val lekérheti a közeli érdeklődési pontokat a Azure maps.

A táblázat a Google Maps API paramétereit jeleníti meg az összehasonlítható Azure Maps API-paraméterekkel.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `keyword`                   | `categorySet` és `brandSet`        |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `location`                  | `lat` és `lon`                     |
| `maxprice`                  | *N/A*                               |
| `minprice`                  | *N/A*                               |
| `name`                      | `categorySet` és `brandSet`        |
| `opennow`                   | *N/A*                               |
| `pagetoken`                 | `ofs` és `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/A*                               |
| `type`                      | `categorySet –` tekintse meg a [támogatott keresési kategóriák](supported-search-categories.md) dokumentációját.   |

## <a name="calculate-routes-and-directions"></a>Útvonalak és irányok kiszámítása

Útvonalak és irányok kiszámítása Azure Maps használatával. Azure Maps a Google Maps útválasztási szolgáltatása számos különböző funkciót tartalmaz, például:

- Érkezési és távozási idő.
- Valós idejű és prediktív forgalmi útvonalak.
- Különböző szállítási módok. Például:, vezetés, gyaloglás, kerékpározás.

> [!NOTE]
> Azure Maps az összes Útpontot koordinálni kell. Először a címeket kell geokódolva.

A Azure Maps útválasztási szolgáltatás a következő API-kat biztosítja az útvonalak kiszámításához:

- [**Útvonal kiszámítása**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): kiszámítja az útvonalat, és azonnal feldolgozza a kérést. Ez az API a GET és a POST kérelmeket is támogatja. A POST kérések használata nagy számú útpont megadása esetén ajánlott, vagy ha sok útvonal-beállítást használ, hogy az URL-cím kérése ne legyen túl hosszú, és problémákat okozzon. A Azure Maps utáni útvonal iránya olyan lehetőséggel is rendelkezhet, amely több ezer [támogató pontot](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) is igénybe vehet, és ezek használatával újra létrehozhatja a logikai útvonal elérési útját (a központhoz illesztés). 
- [**Batch Route**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): hozzon létre egy legfeljebb 1 000 útválasztási kérelmet tartalmazó kérelmet, és egy adott időszakban dolgozza fel azokat. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.
- [**Mobilitási szolgáltatások**](https://docs.microsoft.com/rest/api/maps/mobility): útvonalak és irányok kiszámítása a nyilvános átvitel használatával.

A táblázat a Google Maps API paramétereit a Azure Maps hasonló API-paramétereivel hivatkozik.

| Google Maps API-paraméter    | Hasonló Azure Maps API-paraméter  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – koordináták formátuma `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                     | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* – ez a funkció helymeghatározáshoz kapcsolatos. Használja a *countrySet* paramétert a Azure Maps helymeghatározáshoz API használatakor.  |
| `traffic_model`               | *N/A* – csak azt adhatja meg, hogy a forgalmi adatokat a *Traffic* paraméterrel kell-e használni. |
| `transit_mode`                | Lásd: [mobilitási szolgáltatások dokumentációja](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Lásd: [mobilitási szolgáltatások dokumentációja](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* – Azure Maps csak a metrikus rendszer használatát használja.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Alapértelmezés szerint a Azure Maps Route API csak összegzést ad vissza. A függvény a távolságot és az időpontokat, valamint az útvonal útvonalának koordinátáit adja vissza. Használja a `instructionsType` paramétert a lépésenkénti utasítások lekéréséhez. A `routeRepresentation` paraméterrel pedig kiszűrheti az összefoglalás és az útvonal elérési útját.

Azure Maps Routing API további funkciókkal rendelkezik, amelyek nem érhetők el a Google Maps szolgáltatásban. Az alkalmazás áttelepítésekor érdemes lehet ezeket a funkciókat használni, ha hasznosnak találja őket.

- Az útvonal típusának támogatása: legrövidebb, leggyorsabb, trilling és a legtöbb üzemanyag-takarékos.
- További utazási módok támogatása: busz, motorkerékpár, taxi, tehergépkocsi és Furgon.
- 150-es útpontok támogatása.
- Több utazási idő kiszámítása egyetlen kérelemben; korábbi forgalom, élő forgalom, forgalom nélkül.
- Kerülje a további utak típusát: telekocsi utak, nem burkolt utak, már használatban lévő utak.
- A kerülni kívánt egyéni területek meghatározása.
- Korlátozza a jogosultságszint-emelést, amelyet az útvonal felemel.
- Útvonal a motor előírásai alapján. A motor specifikációi alapján kiszámítja az égési vagy az elektromos járművek útvonalát, valamint a fennmaradó üzemanyagot vagy díjat.
- A kereskedelmi jármű útvonalának paramétereinek támogatása. Ilyenek például a járművek méretei, súlyozása, Axel-száma és a rakomány típusa.
- A jármű maximális sebességének meghatározása.

Ezen kívül a Azure Maps útvonal-szolgáltatása támogatja az [útválasztásos tartományok kiszámítását](https://docs.microsoft.com/rest/api/maps/route/getrouterange). Az átirányítható tartományok kiszámítása izokrón néven is ismert. Magában foglalja egy olyan sokszög előállítását, amely egy olyan területre mutat, amely egy adott kezdőponttól bármilyen irányban átutazható. Az összes megadott időtartam vagy üzemanyag vagy díj alapján.

## <a name="retrieve-a-map-image"></a>Térkép rendszerképének beolvasása

A Azure Maps egy API-t biztosít a statikus Térkép képének megjelenítéséhez. A [Térkép képmegjelenítési](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API-ját Azure Maps a Google Maps statikus Térkép API-ját hasonlítja össze.

> [!NOTE]
> Azure Maps a középpontot, az összes jelölőt és az elérési út helyét a "hosszúság, szélesség" formátumban kell koordinálni. Míg a Google Maps a "szélesség, hosszúság" formátumot használja. Először a címeket kell geokódolva.

A táblázat a Google Maps API paramétereit a Azure Maps hasonló API-paramétereivel hivatkozik.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – az URL elérési útjának részeként van megadva. Jelenleg csak a PNG támogatott. |
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `maptype`                   | `layer` és `style` – tekintse meg a [támogatott térképi stílusok](supported-map-styles.md) dokumentációját. |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* – ez egy helymeghatározáshoz-hez kapcsolódó szolgáltatás. Használja a `countrySet` paramétert a Azure Maps helymeghatározáshoz API használatakor.  |
| `scale`                     | *N/A*                              |
| `size`                      | `width` és `height` – mérete akár 8192x8192 is lehet. |
| `style`                     | *N/A*                              |
| `visible`                   | *N/A*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> A Azure Maps csempe rendszerében a csempék a Google Maps-ben használt Térkép csempék méretének kétszerese. Mivel a nagyítási szint értéke Azure Maps egy nagyítási szint jelenik meg Azure Maps a Google Maps-hez képest. Ahhoz, hogy kompenzálja ezt a különbséget, az áttelepíteni kívánt kérések nagyítási szintjének csökkentése.

További információ: [útmutató a Térkép rendszerkép-renderelési API-hoz](how-to-render-custom-data.md).

Egy statikus térképi rendszerkép létrehozásán kívül a Azure Maps Render szolgáltatás lehetővé teszi a térképes csempék közvetlen elérését raszteres (PNG) és vektoros formátumban:

- [**Térkép csempe**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): a RASZTER (PNG) és a vektor csempék lekérése az alaptérképekhez (utak, határok, háttér).
- [**Térkép képének csempe**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): antenna-és műholdas képcsempék lekérése.

> [!TIP]
> Számos Google Maps-alkalmazás, amely néhány éve az interaktív térképi élményről a statikus térképi képekre váltott át. Ez költségmegtakarítási módszerként történt. A Azure Maps általában költséghatékonyabb, hogy az interaktív térkép vezérlőelemet használja a web SDK-ban. Az interaktív térkép vezérlési díjai a csempék betöltésének száma alapján jelennek meg. Azure Maps a csempék nagy méretűek. Gyakran csak néhány csempét kell létrehoznia, hogy újra létrehozza ugyanazt a leképezési nézetet statikus térképként. A Térkép csempéit a böngésző automatikusan gyorsítótárazza. Így az interaktív térkép vezérlőelem gyakran generál egy tranzakció egy részét, amikor egy statikus Térkép nézetet készít elő. A pásztázás és a nagyítás több csempét is betölt; a Térkép vezérlőelemben azonban lehetőség van a viselkedés letiltására. Az interaktív térkép vezérlőelem sokkal több vizualizációs lehetőséget is biztosít, mint a statikus Térkép-szolgáltatások.

### <a name="marker-url-parameter-format-comparison"></a>Jelölő URL-paraméter formátumának összehasonlítása

**Előtte: Google Maps**

Adja hozzá a jelölőket a `markers` paraméterrel az URL-címben. A `markers` paraméter egy stílust és a térképen megjeleníteni kívánt helyszínek listáját veszi figyelembe az alábbi ábrán látható módon:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

További stílusok hozzáadásához használja a `markers` paramétereket az URL-címhez egy másik stílussal és hellyel.

A "szélesség, hosszúság" formátumban válassza a jelölők helyét.

Adja hozzá a jelölő stílusait a `optionName:value` formátumhoz, több stílussal elválasztva a cső (\|) karakterekkel, például a következőhöz: "optionName1: érték1\|optionName2: Érték2". Megjegyzés: a beállítások nevei és értékei kettősponttal vannak elválasztva (:). A Google Maps-ben a következő stílusú stílusok használhatók a Style jelölők számára:

- `color` – az alapértelmezett jelölő ikonjának színe. Lehet egy 24 bites hexadecimális szín (`0xrrggbb`) vagy a következő értékek egyike: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` – egy nagybetűs alfanumerikus karakter, amely az ikon tetején jelenik meg.
- `size` – a jelölő mérete. `tiny`, `mid`vagy `small`lehet.

A Google Maps egyéni ikonjaihoz a következő stílusú beállításokat használhatja:

- `anchor` – megadja, hogy az ikon képének hogyan legyen igazítva a koordinátahöz. Lehet egy képpont (x, y) érték vagy a következő értékek egyike: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`vagy `bottomright`.
- `icon` – az ikon képére mutató URL-cím.

Tegyük fel például, hogy egy piros, közepes méretű jelölőt adunk a térképhez a földrajzi hosszúság:-110, szélesség: 45:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps-jelölő](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Utána: Azure Maps**

Adja hozzá a jelölőket egy statikus térképi képhez az URL-cím `pins` paraméterének megadásával. A Google Mapshöz hasonlóan a paraméterben is megadunk egy stílust és a helyszínek listáját. A `pins` paraméter többször is megadható a különböző stílusú jelölők támogatásához.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Ha további stílusokat szeretne használni, adjon hozzá további `pins` paramétereket az URL-címhez egy másik stílussal és hellyel.

Azure Maps a PIN-kód helyét "hosszúsági szélesség" formátumban kell megadni. A Google Maps a "szélesség, hosszúság" formátumot használja. Egy szóköz, nem pedig vessző, elkülöníti a hosszúságot és a szélességet Azure Maps formátumban.

A `iconType` a létrehozandó PIN-kód típusát adja meg. A következő értékeket veheti fel:

- `default` – az alapértelmezett PIN-kód ikon.
- `none` – egyetlen ikon sem jelenik meg, csak a feliratok lesznek megjelenítve.
- `custom` – egyéni ikont kell használni. Az ikon képére mutató URL-címet a PIN-kód helye információ után a `pins` paraméter végéhez lehet adni.
- `{udid}` – egyedi Adatazonosító (UDID) a Azure Maps adattárolási platformon tárolt ikonhoz.

Adja hozzá a PIN-stílusokat a `optionNameValue` formátumhoz. A pipe (\|) karakterekkel több stílust is elkülönítheti. Például: `iconType|optionName1Value1|optionName2Value2`. A beállítások nevei és értékei nincsenek elválasztva. A következő stílus-beállítási neveket használja a Style jelölőknek:

- `al` – a jelölő opacitását (alfa) adja meg. 0 és 1 közötti számot adjon meg.
- `an` – a PIN-kód horgonyát adja meg. X és y képpont értékeket az "x y" formátumban kell megadni.
- `co` – a PIN-kód színe. Válasszon egy 24 bites hexadecimális színt: `000000` `FFFFFF`.
- `la` – a címke horgonyát adja meg. X és y képpont értékeket az "x y" formátumban kell megadni.
- `lc` – a címke színe. Válasszon egy 24 bites hexadecimális színt: `000000` `FFFFFF`.
- `ls` – a felirat mérete képpontban megadva. Nullánál nagyobb számot adjon meg.
- `ro` – az ikon elforgatásához szükséges fokban megadott érték. Válasszon egy-360 és 360 közötti számot.
- `sc` – a rögzítési ikon méretezési értéke. Nullánál nagyobb számot adjon meg.

Az egyes PIN-kódokhoz tartozó feliratok értékének megadása. Ez a megközelítés hatékonyabb, mint egyetlen címke értékének alkalmazása a helyszínek listáján szereplő összes jelölőre. A címke értéke több karakterből álló karakterlánc lehet. A sztringet egyetlen idézőjelek közé csomagolva biztosíthatja, hogy ne legyen helytelen a stílus vagy a hely értéke.

Vegyünk fel egy piros (`FF0000`) alapértelmezett ikont a "Space tű" címkével, amely az alábbi helyzetben van (15 50). Az ikon a következő hosszúságú:-122,349300, szélesség: 47,620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure Maps jelölő](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Vegyen fel három PIN-értéket az "1", "2" és "3" címke értékkel:

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps több jelölő](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Elérési út URL-paraméterének formátumának összehasonlítása

**Előtte: Google Maps**

Adja hozzá a vonalakat és a sokszögeket egy statikus térképi képhez az URL-cím `path` paraméterének használatával. A `path` paraméter egy stílust és a térképen megjeleníteni kívánt helyszínek listáját veszi figyelembe, az alábbi ábrán látható módon:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Ha további `path` paramétereket ad hozzá az URL-címhez, akkor további stílusokat és különböző helyszíneket adhat meg.

Az elérési utak helye a `latitude1,longitude1|latitude2,longitude2|…` formátummal van megadva. Az elérési utak kódolása vagy a pontok címeinek tárolására használható.

Adja hozzá az elérésiút-stílusokat a `optionName:value` formátumhoz, és válassza a cső (\|) karaktereinek több stílusát. És a választható nevek és értékek kettősponttal (:). Ehhez hasonlóan: `optionName1:value1|optionName2:value2`. A következő stílus-beállítási nevek használhatók a Google Maps-útvonalak stílusához:

- `color` – az elérési út vagy a sokszög körvonalának színe Lehet egy 24 bites hexadecimális szín (`0xrrggbb`), egy 32 bites hexadecimális szín (`0xrrggbbbaa`) vagy a következő értékek egyike: fekete, barna, zöld, lila, sárga, kék, szürke, narancssárga, piros, fehér.
- `fillColor` – az elérési út (sokszög) kitöltésére szolgáló szín. Lehet egy 24 bites hexadecimális szín (`0xrrggbb`), egy 32 bites hexadecimális szín (`0xrrggbbbaa`) vagy a következő értékek egyike: fekete, barna, zöld, lila, sárga, kék, szürke, narancssárga, piros, fehér.
- `geodesic` – azt jelzi, hogy az elérési útnak olyan vonalnak kell-e lennie, amely követi a föld görbületét.
- `weight` – az elérési út vonalvastagsága képpontban megadva

Adja hozzá a piros vonal opacitását és a képpont vastagságát a koordináták közötti térképhez az URL paraméterben. Az alábbi példában a vonal 50%-os opacitással és négy képpont vastagsággal rendelkezik. A koordináták a következők:-110, szélesség: 45 és hosszúság:-100, szélesség: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps-vonallánc](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Utána: Azure Maps**

Sorok és sokszögek hozzáadása statikus térképi képhez az URL-cím `path` paraméterének megadásával. A Google Mapshöz hasonlóan az ebben a paraméterben szereplő helyszínek is megtalálhatók. A `path` paramétert többször is meghatározhatja több kör, vonal és sokszög megjelenítéséhez különböző stílusokkal.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Az elérési utak helyeinél Azure Maps megköveteli, hogy a koordináták "hosszúsági szélesség" formátumúak legyenek. A Google Maps a "szélesség, hosszúság" formátumot használja. Egy szóköz, nem pedig vessző, elkülöníti a hosszúságot és a szélességet Azure Maps formátumban. A Azure Maps nem támogatja a pontok kódolására szolgáló elérési utakat vagy címeket. Az [itt](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)leírtak szerint töltsön fel nagyobb adatkészleteket GeoJSON-fájlként a Azure Maps ADATtároló API-ba.

Adja hozzá az elérésiút-stílusokat a `optionNameValue` formátumhoz. Több stílust is elválaszthat a pipe (\|) karakterekkel, például a `optionName1Value1|optionName2Value2`. A beállítások nevei és értékei nincsenek elválasztva. A következő stílus-beállítási neveket használja a Azure Maps stílusának elérési útjaihoz:

- `fa` – a sokszögek rendereléséhez használt kitöltési szín opacitása (alpha). 0 és 1 közötti számot adjon meg.
- `fc` – a sokszög területének megjelenítéséhez használt kitöltési szín.
- `la` – a vonalak és a sokszögek körvonalának megjelenítésekor használt vonal színopacitása (alpha). 0 és 1 közötti számot adjon meg.
- `lc` – a sorok megjelenítéséhez és a sokszögek körvonalához használt vonal színe.
- `lw` – a vonal szélessége képpontban megadva.
- `ra` – a körök sugarát adja meg méterben.

Adja hozzá a piros vonal fedettségét és a képpont vastagságát a koordináták között az URL paraméterben. Az alábbi példában a vonal 50%-os opacitással és négy képpont vastagsággal rendelkezik. A koordináták értékei a következők: hosszúság:-110, szélesség: 45 és hosszúság:-100, szélesség: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps vonallánc](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Távolsági mátrix kiszámítása

Azure Maps a távolsági mátrix API-t biztosítja. Ezzel az API-val kiszámíthatja az utazási időt és a távolságokat a különböző tárolóhelyek között, a távolsági mátrixtal együtt. A Google Maps szolgáltatásban a távolsági mátrix API-hoz hasonló.

- [**Útvonal-mátrix**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): aszinkron módon számítja ki az utazási időpontokat és a távolságokat az egyes eredetek és célhelyek esetében. A legfeljebb 700 cellát támogat. Az eredetek száma megszorozva a célhelyek számával. Ennek a korlátozásnak a szem előtt tartva a lehetséges mátrix-dimenziók például a következők: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> A távolsági mátrix API-ra irányuló kérés csak POST-kéréssel hozható létre a kérelem törzsében, a forrás-és a célként megadott információval. Emellett Azure Maps az összes eredetet és célhelyet koordinálni kell. Először a címeket kell geokódolva.

Ez a táblázat az összehasonlítható Azure Maps API-paraméterekkel hivatkozik a Google Maps API-paraméterekre.

| Google Maps API-paraméter      | Hasonló Azure Maps API-paraméter  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – a POST kérelem törzsében válassza a GeoJSON. |
| `key`                          | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                     | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – a POST kérelem törzsében válassza a GeoJSON.  |
| `region`                       | *N/A* – ez a funkció helymeghatározáshoz kapcsolatos. Használja a `countrySet` paramétert a Azure Maps helymeghatározáshoz API használatakor. |
| `traffic_model`                | *N/A* – csak azt adhatja meg, hogy a forgalmi adatokat a `traffic` paraméterrel kell-e használni. |
| `transit_mode`                 | *N/A – az* átvitelen alapuló távolsági mátrixok jelenleg nem támogatottak.  |
| `transit_routing_preference`   | *N/A – az* átvitelen alapuló távolsági mátrixok jelenleg nem támogatottak.  |
| `units`                        | *N/A* – Azure Maps csak a metrikus rendszer használatát használja. |

> [!TIP]
> A Azure Maps útválasztási API-ban elérhető összes speciális útválasztási lehetőség támogatott a Azure Maps távolsági mátrix API-ban. A speciális útválasztási lehetőségek a következők: Tehergépkocsi-útválasztás, motor-specifikációk stb.

## <a name="get-a-time-zone"></a>Időzóna beolvasása

A Azure Maps egy API-t biztosít a koordináta időzónájának lekéréséhez. A Azure Maps időzóna API hasonló az időzóna API-hoz a Google Maps szolgáltatásban:

- [**Időzóna koordináta szerint**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): megadási koordináta és a koordináta időzóna-adatainak fogadása.

Ez a táblázat a Google Maps API paramétereit a Azure Maps hasonló API-paramétereivel hivatkozik.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is.       |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Ezen az API-on kívül a Azure Maps számos időzóna API-t biztosít. Ezek az API-k az időzóna neve vagy azonosítói alapján alakítják át az időt:

- [**Időzóna azonosító szerint**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): a megadott IANA Időzóna-azonosító aktuális, korábbi és jövőbeli időzóna-információit adja vissza.
- [**Időzóna enumerálása IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): az IANA időzóna-azonosítók teljes listáját adja vissza. Az IANA szolgáltatás frissítései egy napon belül megjelennek a rendszeren.
- [**Időzóna enumerálása Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): a Windows időzóna-azonosítók teljes listáját adja vissza.
- [**Időzóna – IANA-verzió**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): az Azure Maps által használt aktuális IANA-verziószámot adja vissza.
- [**Időzóna-ablakok az IANA számára**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): egy megfelelő IANA-azonosítót ad vissza, amely érvényes Windows időzóna-azonosítót adott meg. Egy Windows-AZONOSÍTÓhoz több IANA-azonosító is visszaküldhető.

## <a name="client-libraries"></a>Ügyfélkódtárak

A Azure Maps a következő programozási nyelvekhez biztosít ügyféloldali kódtárakat:

- JavaScript, írógéppel, Node. js – [dokumentáció](how-to-use-services-module.md) \| [NPM csomag](https://www.npmjs.com/package/azure-maps-rest)

Ezek a nyílt forráskódú ügyféloldali kódtárak más programozási nyelveken is elérhetők:

- .NET Standard 2,0 – [GitHub-projekt](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet csomag](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>További források

Az alábbiakban a Azure Maps REST-szolgáltatásokkal kapcsolatos további dokumentációt és erőforrásokat talál.

- [Ajánlott eljárások a kereséshez](how-to-use-best-practices-for-search.md)
- [Címek keresése](how-to-search-for-address.md)
- [Azure Maps REST Service API-referenciák dokumentációja](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Következő lépések

További információ a Azure Maps REST Services szolgáltatásról.

> [!div class="nextstepaction"]
> [Ajánlott eljárások a keresési szolgáltatás használatához](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [A Services modul (web SDK) használata](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)
