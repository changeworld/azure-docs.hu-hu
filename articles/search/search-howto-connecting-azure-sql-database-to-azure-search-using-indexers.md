---
title: Keresés az Azure SQL-on keresztül
titleSuffix: Azure Cognitive Search
description: Adatok importálása Azure SQL Database az indexelő használatával az Azure Cognitive Search teljes szöveges kereséséhez. Ez a cikk a kapcsolatokat, az indexelő konfigurációját és az adatfeldolgozást ismerteti.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c09727e8d92a449b41124eae6ad8381d66cb2619
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113310"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-an-azure-cognitive-search-indexer"></a>Azure SQL Database tartalomhoz való kapcsolódás és indexelés Azure Cognitive Search indexelő használatával

Az [Azure Cognitive Search indexek](search-what-is-an-index.md)lekérdezéséhez fel kell töltenie azt az adataival. Ha az adatmennyiség egy Azure SQL Database-adatbázisban található, akkor az **azure Cognitive Search indexelő Azure SQL Database** (vagy az **Azure SQL indexelő** esetében) automatizálhatja az indexelési folyamatot, ami azt jelenti, hogy kevesebb kód írható és kevésbé fontos az infrastruktúra.

Ez a cikk az [Indexelő](search-indexer-overview.md)használatának mechanikája, de a csak az Azure SQL Database-adatbázisokkal (például integrált változások követése) elérhető funkciókat ismerteti. 

Az Azure SQL Database-adatbázisok mellett az Azure Cognitive Search indexelő lehetőségeket biztosít a [Azure Cosmos db](search-howto-index-cosmosdb.md), az [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)és az [Azure Table Storage](search-howto-indexing-azure-tables.md)szolgáltatáshoz. Más adatforrások támogatásának kéréséhez adja meg az [Azure Cognitive Search visszajelzési fórumának](https://feedback.azure.com/forums/263029-azure-search/)visszajelzéseit.

## <a name="indexers-and-data-sources"></a>Indexelő és adatforrások

Az **adatforrás meghatározza,** hogy mely adatokat kell indexelni, az adathozzáférés hitelesítő adatait, valamint az adatok változásainak hatékony azonosítására szolgáló szabályzatokat (új, módosított vagy törölt sorok). Független erőforrásként van definiálva, így több indexelő is használható.

Az **Indexelő** olyan erőforrás, amely egy adott adatforrást egy célként megadott keresési indexszel csatlakoztat. Az indexelő a következő módokon használható:

* Az adatok egy egyszeri másolatának elvégzésével feltöltheti az indexeket.
* Egy index frissítése az adatforrásban lévő változásokkal egy ütemezett időpontban.
* Igény szerint futtasson igény szerinti frissítést az indexek frissítéséhez.

Egyetlen indexelő csak egyetlen táblát vagy nézetet használhat, de több indexelő is létrehozható, ha több keresési indexet szeretne feltölteni. A fogalmakkal kapcsolatos további információkért lásd [: indexelő műveletek: tipikus munkafolyamat](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Beállíthatja és konfigurálhatja az Azure SQL indexelő a használatával:

* Adatimportálás varázsló a [Azure Portal](https://portal.azure.com)
* Azure Cognitive Search [.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Azure Cognitive Search [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

Ebben a cikkben a REST API az **Indexelő** és az **adatforrások**létrehozásához használjuk.

## <a name="when-to-use-azure-sql-indexer"></a>Mikor kell használni az Azure SQL indexelő
Az adatokhoz kapcsolódó számos tényezőtől függően előfordulhat, hogy az Azure SQL indexelő használata nem megfelelő. Ha az adatai megfelelnek az alábbi követelményeknek, használhatja az Azure SQL indexelő.

| Feltételek | Részletek |
|----------|---------|
| Az adatok egyetlen táblából vagy nézetből származnak. | Ha az adatmennyiség több táblázat között van szétszórva, létrehozhat egyetlen nézetet az adatnézetből. Ha azonban nézetet használ, nem használhatja SQL Server integrált változások észlelését, hogy az indexet a növekményes módosításokkal frissítse. További információ: a [módosított és törölt sorok rögzítése](#CaptureChangedRows) . |
| Az adattípusok kompatibilisek | A legtöbb SQL-típus nem támogatott egy Azure Cognitive Search indexben. A listában tekintse meg az [adattípusok leképezése](#TypeMapping)című témakört. |
| A valós idejű adatszinkronizálás nem szükséges | Az indexelő legfeljebb öt percenként tudja újraindexelni a táblát. Ha az adatai gyakran változnak, és a módosításokat másodperceken vagy egy percen belül meg kell jelenniük az indexben, javasoljuk, hogy a [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) vagy a [.net SDK](search-import-data-dotnet.md) használatával közvetlenül leküldse a frissített sorokat. |
| Növekményes indexelés lehetséges | Ha nagy adatkészlettel rendelkezik, és az indexelő ütemezett futtatását tervezi, az Azure Cognitive Search képesnek kell lennie az új, módosított vagy törölt sorok hatékony azonosítására. A nem növekményes indexelés csak akkor engedélyezett, ha igény szerinti indexelést végez (nem ütemezés szerint), vagy kevesebb mint 100 000 sort indexel. További információ: a [módosított és törölt sorok rögzítése](#CaptureChangedRows) . |

> [!NOTE] 
> Az Azure Cognitive Search csak a SQL Server hitelesítést támogatja. Ha Azure Active Directory jelszó-hitelesítés támogatását igényli, szavazzon erre a [UserVoice javaslatra](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Azure SQL-indexelő létrehozása

1. Adatforrás létrehozása:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Lekérheti a [Azure Portal](https://portal.azure.com)a kapcsolatok karakterláncát; használja a `ADO.NET connection string` lehetőséget.

2. Ha még nem rendelkezik ilyennel, hozza létre a cél Azure Cognitive Search indexét. Létrehozhat egy indexet a [portál](https://portal.azure.com) vagy a [create index API](https://docs.microsoft.com/rest/api/searchservice/Create-Index)használatával. Győződjön meg arról, hogy a célként megadott index sémája kompatibilis a forrástábla sémájával – lásd: [leképezés az SQL és az Azure kognitív keresési adattípusok között](#TypeMapping).

3. Hozza létre az indexet úgy, hogy megadja a nevét, és hivatkozik az adatforrásra és a célként megadott indexre:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Az ily módon létrehozott indexelő nem rendelkezik ütemtervtel. A létrehozáskor automatikusan lefut. A **Run indexelő** kérelem használatával bármikor futtathatja azt:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2019-05-06
    api-key: admin-key

Testreszabhatja az indexelő viselkedésének számos aspektusát, például a köteg méretét, valamint azt, hogy hány dokumentumot lehet kihagyni az indexelő végrehajtásának sikertelensége előtt. További információ: [create indexelő API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Előfordulhat, hogy engedélyezni kell az Azure-szolgáltatások számára az adatbázishoz való kapcsolódást. Az ehhez szükséges útmutatásért lásd: [Csatlakozás az Azure-ból](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) .

Az indexelő állapot és a végrehajtási előzmények (az indexelt elemek, a hibák stb. száma) figyeléséhez használjon **Indexelő állapotra** vonatkozó kérelmet:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2019-05-06
    api-key: admin-key

A válasznak a következőhöz hasonlóan kell kinéznie:

    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

A végrehajtási előzmények akár 50 a legutóbb befejezett végrehajtásokat, amelyek fordított időrendi sorrendben vannak rendezve (így a legutolsó végrehajtás a válaszban).
A válaszról további információt talál az [Indexelő állapotának lekérése](https://go.microsoft.com/fwlink/p/?LinkId=528198) című témakörben.

## <a name="run-indexers-on-a-schedule"></a>Indexelő futtatása ütemterv szerint
Az indexelő úgy is rendezhető, hogy rendszeres időközönként fusson. Ehhez adja hozzá a **Schedule** tulajdonságot az indexelő létrehozásakor vagy frissítésekor. Az alábbi példa egy PUT-kérelmet mutat be az indexelő frissítéséhez:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Az **intervallum** paraméter megadása kötelező. Az intervallum a két egymást követő indexelő végrehajtásának kezdete közötti időpontra utal. A legkisebb megengedett intervallum 5 perc; a leghosszabb egy nap. A fájlnak XSD "dayTimeDuration" értéknek kell lennie (az [ISO 8601 időtartam](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) értékének korlátozott részhalmaza). A minta ehhez a következő: `P(nD)(T(nH)(nM))`. Példák: `PT15M` minden 15 percenként, `PT2H` 2 óránként.

Az indexelő-ütemtervek definiálásával kapcsolatos további információkért lásd: [Az Azure Cognitive Search indexelő szolgáltatásának beosztása](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Új, módosított és törölt sorok rögzítése

Az Azure Cognitive Search **növekményes indexeléssel** kerülhető el, hogy ne kelljen újraindexelni a teljes táblázatot vagy nézetet minden alkalommal, amikor egy indexelő fut. Az Azure Cognitive Search kétféle változás-észlelési szabályzatot biztosít a növekményes indexelés támogatásához. 

### <a name="sql-integrated-change-tracking-policy"></a>Integrált SQL Change Tracking házirend
Ha az SQL-adatbázis támogatja a [változások követését](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), javasoljuk, hogy az **SQL integrált Change Tracking házirendjét**használja. Ez a leghatékonyabb szabályzat. Emellett lehetővé teszi az Azure Cognitive Search számára a Törölt sorok azonosítását anélkül, hogy explicit "Soft Delete" oszlopot kellene hozzáadnia a táblához.

#### <a name="requirements"></a>Követelmények 

+ Az adatbázis verziószámára vonatkozó követelmények:
  * SQL Server 2012 SP3 és újabb verziók, ha SQL Server Azure-beli virtuális gépeken használ.
  * Azure SQL Database V12-es verziót, ha Azure SQL Databaset használ.
+ Csak táblák (nincsenek nézetek). 
+ Az adatbázison engedélyezze a táblázat [módosítás-követését](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) . 
+ Nincs összetett elsődleges kulcs (egy elsődleges kulcs, amely egynél több oszlopot tartalmaz) a táblán.  

#### <a name="usage"></a>Használat

A szabályzat használatához a következőhöz hasonló adatforrást kell létrehoznia vagy frissítenie:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Az SQL integrált módosítás-követési szabályzatának használatakor ne határozzon meg külön adattörlési észlelési házirendet – ez a házirend beépített támogatást biztosít a Törölt sorok azonosításához. Ahhoz azonban, hogy a rendszer automatikusan észlelje a törléseket, a keresési indexben szereplő dokumentum kulcsának meg kell egyeznie az SQL-tábla elsődleges kulcsával. 

> [!NOTE]  
> Ha a [truncate Table](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) használatával nagy mennyiségű sort távolít el egy SQL-táblából, az indexelő [alaphelyzetbe kell állítania](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) a sorok törlésének megváltoztatásához.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Magas vízjelek változásának észlelési szabályzata

Ez a változás-észlelési szabályzat egy "magas vízjelek" oszlopra támaszkodik, amely rögzíti a sor utolsó frissítésekor a verziót vagy az időt. Ha nézetet használ, magas vízjelzési házirendet kell használnia. A magas vízjelek oszlopnak meg kell felelnie az alábbi követelményeknek.

#### <a name="requirements"></a>Követelmények 

* Az összes Beszúrás megadja az oszlop értékét.
* Az elemek összes frissítése is megváltoztatja az oszlop értékét.
* Az oszlop értéke minden beszúrási vagy frissítési művelettel nő.
* A következő WHERE és ORDER BY záradékokkal rendelkező lekérdezések hatékonyan hajthatók végre: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Erősen ajánlott a [ROWVERSION](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) adattípust használni a magas vízjelek oszlophoz. Ha bármilyen más adattípus van használatban, a változások követése nem garantált, hogy rögzítse az összes változást az indexelő lekérdezéssel párhuzamosan végrehajtó tranzakciók jelenlétében. Ha a **ROWVERSION** -t csak olvasható replikákkal rendelkező konfigurációban használja, az indexelő az elsődleges replikán kell átirányítani. Adatszinkronizálási forgatókönyvekhez csak elsődleges replikát lehet használni.

#### <a name="usage"></a>Használat

Ha magas vízjelekre vonatkozó szabályzatot szeretne használni, hozza létre vagy frissítse az adatforrást, például:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Ha a forrástábla nem rendelkezik indextel a magas vízjel oszlopban, az SQL indexelő által használt lekérdezések időtúllépést okozhatnak. A `ORDER BY [High Water Mark Column]` záradéknak különösen egy indexre van szüksége ahhoz, hogy hatékonyan fusson, ha a tábla sok sort tartalmaz.
>
>

Ha időtúllépési hibák merülnek fel, a `queryTimeout` indexelő konfigurációs beállításával állíthatja be a lekérdezés időkorlátját az alapértelmezett 5 perces időkorlátnál nagyobb értékre. Ha például 10 percre szeretné beállítani az időkorlátot, akkor a következő konfigurációval hozza létre vagy frissítse az indexelő:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Letilthatja a `ORDER BY [High Water Mark Column]` záradékot is. Ez azonban nem ajánlott, mert ha az indexelő végrehajtása egy hiba miatt megszakad, az indexelő újra kell feldolgoznia az összes sort, ha később fut, akkor is, ha az indexelő már majdnem az összes sort feldolgozta a megszakított időpontig. A `ORDER BY` záradék letiltásához használja a `disableOrderByHighWaterMarkColumn` beállítást az indexelő definíciójában:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Törlési törlési házirend az oszlop törléséhez
Ha a sorok törlődnek a forrástábla közül, valószínűleg törölni kívánja ezeket a sorokat a keresési indexből is. Ha az SQL integrált módosítás-követési szabályzatát használja, az Ön számára is gondot kell fordítania. A magas vízjelek változás-követési szabályzata azonban nem segít a törölt sorokban. Mi a teendő ilyenkor?

Ha a sorok fizikailag el lettek távolítva a táblából, az Azure Cognitive Search nem lehet kikövetkeztetni a már nem létező rekordok jelenlétét.  Azonban a "Soft-Delete" technikával logikusan törölheti a sorokat anélkül, hogy azokat a táblából kellene eltávolítania. Adjon hozzá egy oszlopot a táblához, vagy tekintse meg a sorokat, és törölje azokat az oszlop használatával.

A Soft-delete eljárás használatakor az adatforrás létrehozásakor vagy frissítésekor a következőképpen adhatja meg a helyreállítható törlési szabályzatot:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

A **softDeleteMarkerValue** karakterláncnak kell lennie – a tényleges érték karakterlánc-ábrázolását kell használnia. Ha például van egy egész oszlop, ahol a Törölt sorok az 1 értékkel vannak megjelölve, használja a `"1"`. Ha van egy olyan bites oszlopa, ahol a Törölt sorok a true értékkel vannak megjelölve, használja a literál `True` vagy `true`karakterláncot, az eset nem számít.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Az SQL és az Azure Cognitive Search adattípusok közötti leképezés
| SQL-adattípus | Engedélyezett cél index mezők típusai | Megjegyzések |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| valós, lebegőpontos |Edm.Double, Edm.String | |
| túlcsordulási, pénzes decimális szám |Edm.String |Az Azure Cognitive Search nem támogatja a decimális típusok konvertálását a EDM. Double formátumba, mivel ez a pontosságot elveszíti |
| char, nchar, varchar, nvarchar |Edm.String<br/>Gyűjtemény (Edm.String) |Egy SQL-karakterlánc használatával feltölthető egy gyűjtemény (EDM. String) mező, ha a karakterlánc a karakterláncok JSON-tömbjét jelöli: `["red", "white", "blue"]` |
| idő adattípusúra, datetime, datetime2, Date, DateTimeOffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| földrajz |Edm.GeographyPoint |Csak a SRID 4326 (amely az alapértelmezett) típusú földrajzi példányok támogatottak |
| rowversion |N/A |A sorcsoport oszlopai nem tárolhatók a keresési indexben, de használhatók a változások követéséhez |
| idő, TimeSpan, bináris, varbinary, rendszerkép, XML, geometria, CLR-beli típusok |N/A |Nem támogatott |

## <a name="configuration-settings"></a>Konfigurációs beállítások
Az SQL indexelő számos konfigurációs beállítást tesz elérhetővé:

| Beállítás | Data type | Cél | Alapértelmezett érték |
| --- | --- | --- | --- |
| queryTimeout |sztring |Az SQL-lekérdezés végrehajtásának időtúllépését állítja be |5 perc ("00:05:00") |
| disableOrderByHighWaterMarkColumn |logikai |Azt eredményezi, hogy a magas vízjelzési házirend által használt SQL-lekérdezés kihagyja a ORDER BY záradékot. Lásd: [magas vízjelek szabályzata](#HighWaterMarkPolicy) |false |

Ezek a beállítások az indexelő definíciójában lévő `parameters.configuration` objektumban használatosak. Ha például a lekérdezés időtúllépését 10 percre szeretné beállítani, akkor a következő konfigurációval hozza létre vagy frissítse az indexelő:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>GYIK

**K: használhatom az Azure SQL indexelő szolgáltatást IaaS virtuális gépeken futó SQL-adatbázisokkal az Azure-ban?**

Igen. Azonban engedélyeznie kell a keresési szolgáltatásnak az adatbázishoz való kapcsolódást. További információkért lásd: [Kapcsolatok konfigurálása azure Cognitive Search indexelő használatával egy Azure-beli virtuális gépen való SQL Serverhoz](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**K: használhatom az Azure SQL indexelő a helyszínen futó SQL-adatbázisokkal?**

Közvetlenül nem. Nem ajánlunk és nem támogatunk közvetlen kapcsolatot, mert ehhez az szükséges, hogy az adatbázisokat az internetes forgalomhoz nyissa meg. Ennek a forgatókönyvnek a használata sikeres volt az ügyfelek számára, például Azure Data Factory. További információ: [adatok leküldése Azure Cognitive Search indexbe Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**K: használhatom az Azure SQL Indexer-t az Azure-ban futó IaaS-től eltérő adatbázisokon SQL Server?**

Nem. Ez a forgatókönyv nem támogatott, mert az indexelő nem a SQL Serveron kívüli adatbázisokkal lett tesztelve.  

**K: Létrehozhatok több indexelő szolgáltatást egy adott időpontban?**

Igen. Egyszerre azonban csak egy indexelő futhat egyszerre egy csomóponton. Ha egyszerre több indexelő rendszerre van szüksége, érdemes lehet több keresési egységre felskálázást végezni a keresési szolgáltatásban.

**K: az indexelő futtatása befolyásolja a lekérdezési munkaterhelés?**

Igen. Az indexelő a keresési szolgáltatás egyik csomópontján fut, és a csomópont erőforrásai megoszlik az indexelés és a lekérdezési forgalom és más API-kérések között. Ha intenzív indexelési és lekérdezési számítási feladatokat futtat, és magas a 503-es hiba, illetve a válaszadási idő növekszik, érdemes lehet [a keresési szolgáltatás méretezését](search-capacity-planning.md).

**K: használhatok másodlagos replikát egy [feladatátvevő fürtben](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) adatforrásként?**

Ez a konkrét licenctől függ. Egy tábla vagy nézet teljes indexeléséhez használhat másodlagos replikát. 

A növekményes indexeléshez az Azure Cognitive Search két változás-észlelési házirendet támogat: az SQL integrált változások nyomon követését és a magas vízjeleket.

Írásvédett replikák esetén az SQL Database nem támogatja az integrált változások követését. Ezért magas vízjelekre vonatkozó házirendet kell használnia. 

Standard Javaslatunk a ROWVERSION adattípusának használata a magas vízjelek oszlophoz. A ROWVERSION használata azonban a SQL Database `MIN_ACTIVE_ROWVERSION` függvényre támaszkodik, amely csak olvasható replikák esetén nem támogatott. Ezért az indexelő egy elsődleges replikára kell irányítani, ha a ROWVERSION-t használja.

Ha a ROWVERSION csak olvasható replikán kísérli meg használni, a következő hibaüzenet jelenik meg: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**K: használhatok egy másik, nem ROWVERSION oszlopot a magas vízjelek változásának nyomon követéséhez?**

Nem ajánlott. Csak a **ROWVERSION** engedélyezi a megbízható adatszinkronizálást. Az alkalmazási logikától függően azonban a következő lehet:

+ Győződjön meg arról, hogy az indexelő futtatásakor nincs függőben lévő tranzakció az indexelt táblában (például az összes tábla frissítése egy ütemezett kötegként történik, és az Azure Cognitive Search indexelő ütemterve úgy van beállítva, hogy ne legyen átfedésben a táblázattal frissítési ütemterv).  

+ A kihagyott sorok kiválasztásához rendszeresen végezzen teljes újraindexelést. 