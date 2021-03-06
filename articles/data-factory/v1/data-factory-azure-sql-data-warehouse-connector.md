---
title: Adatok másolása Azure SQL Data Warehouse
description: Megtudhatja, hogyan másolhat adatok Azure SQL Data Warehouse a Azure Data Factory használatával
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 18f30af4595a7679d5c3ef56763e992d54fae536
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282002"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Adatok másolása Azure SQL Data Warehouseba és onnan a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-sql-data-warehouse-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az Azure SQL Data Warehouse Connector v2-ben](../connector-azure-sql-data-warehouse.md)című témakört.

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Azure SQL Data Warehouseba való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

> [!TIP]
> A legjobb teljesítmény érdekében, az adatok betöltése az Azure SQL Data Warehouse a polybase szolgáltatást akkor használja. Az [adatok Azure SQL Data Warehouse szakaszba való betöltéséhez használja](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) a következőt:. A használati eseteket bemutató bemutatóért lásd: [1 TB Betöltése Azure SQL Data Warehouse 15 perc alatt Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Támogatott esetek
**Azure SQL Data Warehouse** adatait a következő adattárakba másolhatja:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Az adatok a következő adattárakból másolhatók **Azure SQL Data Warehouseba**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Ha SQL Server vagy Azure SQL Databaseról másol adatokból Azure SQL Data Warehousere, ha a tábla nem létezik a célhelyen, Data Factory automatikusan létrehozhatja a táblát a SQL Data Warehouseban a forrás adattárban található tábla sémájának használatával. További részletekért lásd az [automatikus tábla létrehozása](#auto-table-creation) című témakört.

## <a name="supported-authentication-type"></a>Támogatott hitelesítési típus
Azure SQL Data Warehouse-összekötő támogatja az egyszerű hitelesítést.

## <a name="getting-started"></a>Bevezetés
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely a különböző eszközök/API-k segítségével áthelyezi az adatokra egy Azure SQL Data Warehouse.

A legkönnyebben olyan folyamat hozható létre, amely az adatok másolását Azure SQL Data Warehouse az adatok másolása varázsló használatával másolja. Tekintse meg az [oktatóanyag: az adatgyűjtés SQL Data Warehouseba való betöltését](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) ismertető témakört Data Factory, amely gyors áttekintést nyújt a folyamat létrehozásáról az Adatmásolás varázsló segítségével.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API**és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. Hozzon létre egy **adatelőállítót**. Egy adatelőállító egy vagy több folyamatot is tartalmazhat. 
2. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához. Ha például egy Azure Blob Storage-ból másol egy Azure SQL-adattárházba, két társított szolgáltatást hoz létre az Azure Storage-fiók és az Azure SQL-adattárház összekapcsolásához. A Azure SQL Data Warehousera jellemző társított szolgáltatás tulajdonságairól a [társított szolgáltatás tulajdonságai](#linked-service-properties) című részben olvashat. 
3. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. Az utolsó lépésben említett példában létrehoz egy adatkészletet, amely megadja a bemeneti adatokat tartalmazó BLOB-tárolót és mappát. Emellett létrehoz egy másik adatkészletet is, amely meghatározza az Azure SQL-adattárházban lévő táblázatot, amely a blob Storage-ból másolt adatokat tárolja. A Azure SQL Data Warehousera jellemző adatkészlet-tulajdonságokért lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
4. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. A korábban említett példában a BlobSource forrásként és SqlDWSinkként használja a másolási tevékenységhez. Hasonlóképpen, ha Azure SQL Data Warehouseról az Azure Blob Storagera másol, a másolási tevékenységben a SqlDWSource és a BlobSink is használja. A Azure SQL Data Warehousera jellemző másolási tevékenység tulajdonságairól a [másolási tevékenység tulajdonságai](#copy-activity-properties) című szakaszban olvashat. Az adattár forrásként vagy fogadóként való használatával kapcsolatos részletekért kattintson az adattár előző szakaszában található hivatkozásra.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. Az adatok egy Azure SQL Data Warehouseba való másolásához használt Data Factory JSON-definíciókkal rendelkező minták esetében tekintse meg a jelen cikk [JSON-példák](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) című szakaszát.

A következő szakaszokban részletesen ismertetjük azokat a JSON-tulajdonságokat, amelyek a Azure SQL Data Warehouse specifikus entitások definiálásához használhatók Data Factory:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a Azure SQL Data Warehouse társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A Type tulajdonságot a következőre kell beállítani: **AzureSqlDW** |Igen |
| connectionString |A connectionString tulajdonsághoz Azure SQL Data Warehouse-példányhoz való kapcsolódáshoz szükséges adatok megadása. Csak az alapszintű hitelesítés támogatott. |Igen |

> [!IMPORTANT]
> Konfigurálja [Azure SQL Database tűzfalat](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) és az adatbázis-kiszolgálót, hogy [engedélyezze az Azure-szolgáltatások számára a kiszolgáló elérését](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Emellett, ha az Azure-on kívülről Azure SQL Data Warehousera másol adatokat, beleértve a helyszíni adatforrások és a (z) adatokat a Factory Gateway használatával, konfigurálja a megfelelő IP-címtartományt azon számítógép számára, amely adatokat küld a Azure SQL Data Warehousenak.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & Tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A typeProperties szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **AzureSqlDWTable** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Azon tábla vagy nézet neve a Azure SQL Data Warehouse adatbázisban, amelyhez a társított szolgáltatás hivatkozik. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & Tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

> [!NOTE]
> A másolási tevékenység csak egy bemenetet hoz létre, és csak egy kimenetet állít elő.

Míg a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

### <a name="sqldwsource"></a>SqlDWSource
Ha a forrás **SqlDWSource**típusú, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Például: select * from MyTable. |Nem |
| sqlReaderStoredProcedureName |Azon tárolt eljárás neve, amely beolvassa az adatokat a forrás táblából. |A tárolt eljárás neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |

Ha a **sqlReaderQuery** meg van adva a SqlDWSource, a másolási tevékenység lefuttatja ezt a lekérdezést a Azure SQL Data Warehouse forráson az adatkéréshez.

Azt is megteheti, hogy megadhat egy tárolt eljárást a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paraméterekkel rendelkezik).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, a JSON-adatkészlet szerkezet szakaszában definiált oszlopok a Azure SQL Data Warehouseon futtatandó lekérdezés létrehozásához használatosak. Példa: `select column1, column2 from mytable`. Ha az adatkészlet definíciója nem rendelkezik a struktúrával, az összes oszlop ki lesz választva a táblából.

#### <a name="sqldwsource-example"></a>SqlDWSource example

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**A tárolt eljárás definíciója:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>SqlDWSink
A **SqlDWSink** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |A másolási tevékenységre vonatkozó lekérdezés megadása úgy, hogy egy adott szeletből származó adatmennyiséget takarítson meg. Részletekért lásd: [ismételhetőség szakasz](#repeatability-during-copy). |A lekérdezési utasítást. |Nem |
| allowPolyBase |Azt jelzi, hogy a BULKINSERT mechanizmus helyett a következőt kell-e használni (ha van ilyen). <br/><br/> **Az adatok a SQL Data Warehouseba való betöltésének ajánlott módja a Base használata.** A korlátozások és részletek a következő témakörben olvashatók: az adatok Azure SQL Data Warehouse szakaszba való [betöltésének használata](#use-polybase-to-load-data-into-azure-sql-data-warehouse) . |True (Igaz) <br/>False (alapértelmezett) |Nem |
| polyBaseSettings |A tulajdonságok olyan csoportja, amely akkor adható meg, ha a **allowPolybase** tulajdonság értéke **true (igaz**). |&nbsp; |Nem |
| rejectValue |Megadja a szám vagy százalékos aránya, amelyek is vissza kell utasítani, mielőtt a lekérdezés nem sikerült sorokat. <br/><br/>További információ a [create External Table (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) című témakör **argumentumok** szakaszában található alapszintű elutasítás beállításairól. |0 (alapértelmezett), 1, 2,... |Nem |
| rejectType |Megadja, hogy a rejectValue beállítás literál értékként vagy százalékként van-e megadva. |Érték (alapértelmezett), százalék |Nem |
| rejectSampleValue |Meghatározza a lekérdezni kívánt sorok számát, mielőtt a rendszer újraszámítja az elutasított sorok százalékos arányát. |1, 2, … |Igen, ha a rejectType **százaléka** |
| useTypeDefault |Itt adhatja meg, hogyan szeretné kezelni a PolyBase kér le adatokat a szövegfájl elválasztójellel tagolt szöveges fájlok a hiányzó értékeket.<br/><br/>Erről a tulajdonságról a [külső fájlformátum létrehozása (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)argumentumai című szakaszban olvashat bővebben. |Igaz, hamis (alapértelmezett) |Nem |
| writeBatchSize |Az SQL-táblába szúrja be az adatmennyiséget, ha a puffer mérete eléri a writeBatchSize |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt. |TimeSpan<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |

#### <a name="sqldwsink-example"></a>SqlDWSink példa

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse a PolyBase használatával
A **[Base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** használatával nagy mennyiségű adat a nagy adatátviteli sebességű Azure SQL Data Warehouseba való betöltésének hatékony módja. Az átviteli sebesség nagy mennyiségű nyereségét az alapértelmezett BULKINSERT mechanizmus helyett a Base használatával láthatja. Lásd: a [teljesítmény hivatkozási számának másolása](data-factory-copy-activity-performance.md#performance-reference) részletes összehasonlítással. A használati eseteket bemutató bemutatóért lásd: [1 TB Betöltése Azure SQL Data Warehouse 15 perc alatt Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Ha a forrásadatok az **Azure blobban vagy a Azure Data Lake Storeban**vannak, és a formátum kompatibilis a következővel, akkor közvetlenül is másolhatja a Azure SQL Data Warehouset a albase használatával. A részleteket lásd: a **[közvetlen másolás a Base használatával](#direct-copy-using-polybase)** .
* Ha a forrás adattárat és a formátumot eredetileg nem a Base támogatja, használhatja a **[szakaszos másolást a albase szolgáltatás használatával](#staged-copy-using-polybase)** . Emellett jobb átviteli sebességet is biztosít, ha automatikusan konvertálja az adatok alap-kompatibilis formátumba, és az adatok tárolása az Azure Blob Storage-ban történik. Ezután betölti az adatSQL Data Warehouseba.

Állítsa a `allowPolyBase` tulajdonságot **true (igaz** ) értékre, ha az alábbi példában látható, hogy a Azure Data Factory a Azure SQL Data Warehouseba másolt albase-t használja. Ha a allowPolyBase értéke TRUE (igaz), akkor a `polyBaseSettings` tulajdonsággal megadhatja a kiindulási specifikus tulajdonságokat. a polyBaseSettings használatával használható tulajdonságokkal kapcsolatos részletekért tekintse meg a [SqlDWSink](#sqldwsink) szakaszt.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>Közvetlen másolás a Base használatával
SQL Data Warehouse a közvetlenül támogatja az Azure blobot és Azure Data Lake Store (egyszerű szolgáltatásnév használatával) forrásként és adott fájlformátumokra vonatkozó követelményekkel. Ha a forrásadatok megfelelnek az ebben a szakaszban ismertetett feltételeknek, a forrás adattárból közvetlenül másolhatja a Azure SQL Data Warehouset a Base paranccsal. Ellenkező esetben [a szakaszos másolást a Base paranccsal](#staged-copy-using-polybase)végezheti el.

> [!TIP]
> Ha az adatok Data Lake Storeról SQL Data Warehouse hatékonyan szeretné átmásolni a szolgáltatást, többet tudhat meg a [Azure Data Factoryről, így még könnyebbé és kényelmesebben felhasználhatja az adatok elemzését, amikor az Data Lake Store SQL Data Warehouse használatával](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Ha a követelmények nem teljesülnek, Azure Data Factory ellenőrzi a beállításokat, és automatikusan visszakerül a BULKINSERT mechanizmusra az adatáthelyezéshez.

1. A **forráshoz társított szolgáltatás** típusa: **AzureStorage** vagy **AzureDataLakeStore az egyszerű szolgáltatás hitelesítésével**.
2. A **bemeneti adatkészlet** típusa: **AzureBlob** vagy **AzureDataLakeStore**, és a formátum típusa `type` tulajdonságok alatt a következő konfigurációknál: **OrcFormat**, **ParquetFormat**vagy **Szövegformátum** .

   1. a `rowDelimiter`nak **\n**-nek kell lennie.
   2. `nullValue` értéke **üres karakterlánc** (""), vagy a `treatEmptyAsNull` értéke **true (igaz**).
   3. `encodingName` az **UTF-8**értékre van beállítva, amely az **alapértelmezett** érték.
   4. nincs megadva `escapeChar`, `quoteChar`, `firstRowAsHeader`és `skipLineCount`.
   5. `compression` nem lehet **tömörítés**, **gzip**vagy **deflate**.

      ```JSON
      "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
      },
      ```

3. Nincs `skipHeaderLineCount` beállítás a **BlobSource** vagy a **AzureDataLakeStore** alatt a folyamat másolási tevékenységéhez.
4. Nincs `sliceIdentifierColumnName` beállítás a **SqlDWSink** alatt a folyamat másolási tevékenységéhez. (A Base garantálja, hogy minden adat frissítve lett, vagy egyetlen futtatásban sem frissül. Az **ismételhetőség**eléréséhez használhatja a `sqlWriterCleanupScript`).
5. Nincs használatban `columnMapping` a társított másolási tevékenységben.

### <a name="staged-copy-using-polybase"></a>Előkészített másolás a Base használatával
Ha a forrásadatok nem felelnek meg az előző szakaszban bemutatott feltételeknek, engedélyezheti az adatok másolását egy átmeneti Azure-Blob Storage használatával (nem lehet Premium Storage). Ebben az esetben a Azure Data Factory automatikusan elvégzi az adatok átalakítását, hogy megfeleljenek az adatformátumra vonatkozó követelményeknek, majd az adatok SQL Data Warehouseba való betöltéséhez használja a albase-et, és végül törölje a temp-adatokból a blob Storage-ból. Az adatok átmeneti Azure-blobon keresztül történő másolásával kapcsolatos részletekért tekintse meg a [szakaszos másolást](data-factory-copy-activity-performance.md#staged-copy) .

> [!NOTE]
> Ha a helyszíni adattárban lévő adatok másolása a (z) és a (z) Azure SQL Data Warehouse használatával történik, ha a adatkezelés-átjáró verziója 2,4, a JRE (Java Runtime Environment) szükséges a forrás átalakításához használt átjáró-gépen. az adatformátum megfelelő formátumú. Javasoljuk, hogy az ilyen függőség elkerülése érdekében frissítse az átjárót a legújabb verzióra.
>

A szolgáltatás használatához hozzon létre egy [Azure Storage-beli társított szolgáltatást](data-factory-azure-blob-connector.md#azure-storage-linked-service) , amely az ideiglenes blob Storage-t tartalmazó Azure Storage-fiókra hivatkozik, majd adja meg a másolási tevékenység `enableStaging` és `stagingSettings` tulajdonságait a következő kódban látható módon:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>Ajánlott eljárások a Base használatakor
A következő szakaszokban további ajánlott eljárásokat talál a [Azure SQL Data Warehouse ajánlott eljárásaiban](../../sql-data-warehouse/sql-data-warehouse-best-practices.md)említettek közül.

### <a name="required-database-permission"></a>Adatbázishoz szükséges engedélyt
A (z) rendszerhez szükséges, hogy a felhasználó az adatok betöltéséhez használt felhasználónak a ["vezérlés" engedéllyel](https://msdn.microsoft.com/library/ms191291.aspx) rendelkezzen a céladatbázis SQL Data Warehouse. Ennek egyik módja, ha az adott felhasználót a "db_owner" szerepkör tagjaként adja hozzá. [Ebből a szakaszból](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)megtudhatja, hogyan teheti meg ezt a műveletet.

### <a name="row-size-and-data-type-limitation"></a>Sor mérete és adattípus korlátozása
A kiinduló terhelések csak **1 MB** -nál kisebb sorok betöltésére korlátozódnak, és nem tölthetők be VARCHR (max), NVARCHAR (max) vagy VARBINARY (max) értékre. Tekintse [meg a](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)következőt:.

Ha 1 MB-nál nagyobb méretű adatsorokkal rendelkezik, érdemes lehet függőlegesen felosztani a forrás táblákat több kis méretre, ahol a legnagyobb sorok mérete nem haladhatja meg a korlátot. Ezután a kisebb táblák betölthetők a Base használatával, és a Azure SQL Data Warehouse együtt egyesítve lesznek.

### <a name="sql-data-warehouse-resource-class"></a>Az SQL Data Warehouse erőforrásosztály
A lehető legjobb átviteli sebesség eléréséhez vegye fontolóra, hogy nagyobb erőforrás-osztályt rendeljen hozzá a felhasználóhoz, amellyel az adatok betölthetők a SQL Data Warehouseba a Base használatával. Ebből a témakörből megtudhatja, hogyan teheti ezt meg a [felhasználói erőforrás osztályra vonatkozó példa módosítása](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md)után.

### <a name="tablename-in-azure-sql-data-warehouse"></a>Táblanév Azure SQL Data Warehouse
A következő táblázat példákat mutat be arra, hogyan határozhatja meg a **Táblanév** tulajdonságot az adatkészlet JSON-ban a séma és a tábla különböző kombinációi esetében.

| Adatbázis-séma | Tábla neve | Táblanév JSON-tulajdonság |
| --- | --- | --- |
| dbo |MyTable |MyTable vagy dbo.MyTable vagy [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable vagy [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] vagy [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

Ha a következő hiba jelenik meg, akkor a táblanév tulajdonsághoz megadott értékkel kapcsolatos probléma lehet. A táblanév JSON-tulajdonság értékeinek megadásához tekintse meg a táblázatot a megfelelő módon.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Az alapértelmezett értékekkel oszlopok
A Data Factory-alapú alapszolgáltatások jelenleg csak ugyanannyi oszlopot fogadnak el, mint a célként megadott táblában. Tegyük fel, hogy van egy négy oszlopot tartalmazó táblája, és az egyik az alapértelmezett értékkel van definiálva. A bemeneti adatoknak továbbra is négy oszlopot kell tartalmazniuk. A 3 oszlopos bemeneti adatkészlet megadása a következő üzenethez hasonló hibát eredményez:

```
All columns of the table must be specified in the INSERT BULK statement.
```
A NULL érték az alapértelmezett érték egy speciális formája. Ha az oszlop üres, akkor az oszlop bemeneti adatok (blobban) üresek lehetnek (a bemeneti adatkészletből nem lehet hiányzik). A Azure SQL Data Warehouseban NULLát szúrnak be.

## <a name="auto-table-creation"></a>Automatikus tábla létrehozása
Ha a másolás varázslót használja az adatok SQL Serverból való másolásához, vagy Azure SQL Data Warehouse Azure SQL Database a forrás táblához tartozó tábla nem létezik a célhelyen, akkor a Data Factory automatikusan létrehozhatja a táblát az adattárházban u a forrástábla sémájának eléneklése.

Data Factory létrehozza a tárolóban található táblát a forrás adattárban található azonos nevű táblával. Az oszlopok adattípusai a következő típusú leképezés alapján vannak kiválasztva. Ha szükséges, a típus konverziót hajt végre a forrás-és a célhelyek közötti inkompatibilitás javítása érdekében. Emellett ciklikus multiplexelés-táblázat eloszlást is használ.

| Forrás SQL Database oszlop típusa | Cél SQL DW-oszlop típusa (méret korlátozása) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bites | Bites |
| tizedes tört | tizedes tört |
| Numeric | tizedes tört |
| Float | Float |
| money | money |
| Valós | Valós |
| Túlcsordulási | Túlcsordulási |
| Bináris | Bináris |
| Varbinary | Varbinary (legfeljebb 8000) |
| Dátum | Dátum |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Time | Time |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Szöveg | Varchar (legfeljebb 8000) |
| NText | NVarChar (legfeljebb 4000) |
| Image (Kép) | VarBinary (legfeljebb 8000) |
| UniqueIdentifier | UniqueIdentifier |
| CHAR | CHAR |
| NChar | NChar |
| VarChar | VarChar (legfeljebb 8000) |
| NVarChar | NVarChar (legfeljebb 4000) |
| Xml | Varchar (legfeljebb 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse leképezésének típusa
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikk ismerteti, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Az adatok Azure SQL Data Warehouseból &ba való áthelyezésekor a következő leképezések használhatók az SQL típusa és a .NET típus között, és fordítva.

A leképezés megegyezik a [ADO.net Adattípusának SQL Server-leképezésével](https://msdn.microsoft.com/library/cc716729.aspx).

| SQL Server adatbázismotor típusa | .NET-keretrendszer típusa |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Logikai |
| char |String, Char[] |
| dátum |DateTime |
| Dátum és idő |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| tizedes tört |tizedes tört |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Dupla |
| image |Byte[] |
| int |Int32 |
| money |tizedes tört |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |tizedes tört |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |tizedes tört |
| sql_variant |Object * |
| szöveg |String, Char[] |
| time |Időtartam |
| időbélyeg |Byte[] |
| tinyint |Bájt |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

A másolási tevékenység definíciójában a forrás adatkészletből származó oszlopokat is leképezheti a fogadó adatkészletből származó oszlopokra. Részletekért lásd: [adatkészlet-oszlopok leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>JSON-példák az adatok SQL Data Warehouseba való másolásához
Az alábbi példák olyan JSON-definíciókat biztosítanak, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Bemutatják, hogyan másolhatók az adatok Azure SQL Data Warehouse és az Azure Blob Storageba. Az adatok azonban **közvetlenül** a forrásokból bármelyik forrásból átmásolhatók, ha a másolási tevékenység a Azure Data Factoryban [szerepel.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Példa: adatok másolása Azure SQL Data Warehouseból az Azure-Blobba
A minta a következő Data Factory entitásokat határozza meg:

1. [AzureSqlDW](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [AzureSqlDWTable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [SqlDWSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta átmásolja az idősorozatot (óránként, naponta stb.) egy Azure SQL Data Warehouse adatbázisban lévő táblából minden órában egy blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**Azure SQL Data Warehouse társított szolgáltatás:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Blob Storage társított szolgáltatás:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure SQL Data Warehouse bemeneti adatkészlet:**

A minta azt feltételezi, hogy létrehozott egy "Sajáttábla" táblát a Azure SQL Data Warehouseban, és egy "timestampcolumn" nevű oszlopot tartalmaz az idősorozat-adatsorokhoz.

A "külső": "true" beállítás azt tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure-Blob kimeneti adatkészlete:**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Másolási tevékenység SqlDWSource és BlobSink rendelkező folyamatokban:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **SqlDWSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés a másoláshoz az elmúlt órában kijelöli az adatforrást.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
> [!NOTE]
> A példában a **sqlReaderQuery** meg van adva a SqlDWSource. A másolási tevékenység ezt a lekérdezést a Azure SQL Data Warehouse forráson futtatja az adatlekérdezéshez.
>
> Azt is megteheti, hogy megadhat egy tárolt eljárást a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paraméterekkel rendelkezik).
>
> Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, a JSON-adatkészlet szerkezet szakaszában definiált oszlopok a lekérdezés létrehozásához használatosak (válassza a Column1, Oszlop2 from sajáttábla) parancsot a Azure SQL Data Warehouse. Ha az adatkészlet definíciója nem rendelkezik a struktúrával, az összes oszlop ki lesz választva a táblából.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Példa: adatok másolása az Azure Blobból a Azure SQL Data Warehouseba
A minta a következő Data Factory entitásokat határozza meg:

1. [AzureSqlDW](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureSqlDWTable](#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [SqlDWSink](#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta idősorozat-adatok (óránként, naponta stb.) másolását végzi az Azure blobból egy Azure SQL Data Warehouse adatbázisban lévő táblába óránként. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**Azure SQL Data Warehouse társított szolgáltatás:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Blob Storage társított szolgáltatás:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Blob bemeneti adatkészlet:**

Az adatok minden órában egy új blobból származnak (frekvencia: óra, intervallum: 1). A blob mappa elérési útját és fájlnevét a feldolgozás alatt álló szelet kezdési időpontja alapján dinamikusan értékeli a rendszer. A mappa elérési útja az év, hónap és nap részeként használja a kezdési időt, és a fájlnév a kezdési időpont óra részét használja. a "külső": "true" beállítás tájékoztatja a Data Factory szolgáltatást arról, hogy ez a tábla kívül esik az adatelőállítón, és nem az adatelőállító tevékenysége.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL Data Warehouse kimeneti adatkészlet:**

A minta egy "Sajáttábla" nevű táblába másol egy Azure SQL Data Warehouse. Hozza létre a táblát Azure SQL Data Warehouse azonos számú oszloppal, ahogy azt várná, hogy a blob CSV-fájlja tartalmazni fog. Minden órában új sor kerül a táblázatba.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Másolási tevékenység BlobSource és SqlDWSink rendelkező folyamatokban:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **BlobSource** értékre van állítva, a **fogadó típusa** pedig **SqlDWSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
A bemutatókat lásd: az [1 TB-os terhelés Betöltése Azure SQL Data Warehouse 15 perc alatt, Azure Data Factory](data-factory-load-sql-data-warehouse.md) és az [adat betöltése](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) a Azure SQL Data Warehouse dokumentációjában Azure Data Factory cikkel.

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.
