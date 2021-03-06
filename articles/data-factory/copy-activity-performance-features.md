---
title: Másolási tevékenység teljesítményének optimalizálási funkciói
description: Ismerkedjen meg a főbb funkciókkal, amelyek segítenek a másolási tevékenység teljesítményének optimalizálásában Azure Data Factory 。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/09/2020
ms.openlocfilehash: a31c6229220142acea9ded571128ab54c50d34b7
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79125783"
---
# <a name="copy-activity-performance-optimization-features"></a>Másolási tevékenység teljesítményének optimalizálási funkciói

Ez a cikk a másolási tevékenység teljesítmény-optimalizálási funkcióit ismerteti, amelyeket Azure Data Factory használhat.

## <a name="data-integration-units"></a>Adatintegrációs egységek

Az adatintegrációs egység olyan mérték, amely a Azure Data Factory egyetlen egységének a CPU-, memória-és hálózati erőforrás-lefoglalási kombinációját jelöli. Az adatintegrációs egység csak az [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)szolgáltatásra vonatkozik, a saját üzemeltetésű [integrációs](concepts-integration-runtime.md#self-hosted-integration-runtime)modul nem.

A másolási tevékenység futtatásának engedélyezése engedélyezett DIUs **2 és 256 között**van. Ha nincs megadva, vagy a felhasználói felületen az "automatikus" lehetőséget választja, Data Factory a forrás-fogadó pár és az adatminta alapján dinamikusan alkalmazza az optimális DIU beállítást. A következő táblázat felsorolja a támogatott DIU-tartományokat és az alapértelmezett viselkedést a különböző másolási forgatókönyvekben:

| Másolja ki a forgatókönyv | Támogatott DIU-tartomány | Szolgáltatás által meghatározott alapértelmezett DIUs |
|:--- |:--- |---- |
| A file Stores között |- **Másolás egyetlen fájlba**: 2-4 <br>- **Másolás több fájlból**: 2-256, a fájlok számától és méretétől függően <br><br>Ha például egy 4 nagyméretű fájllal rendelkező mappából másol adatokból, és úgy dönt, hogy megőrzi a hierarchiát, a maximális érvényes DIU 16; Ha a fájl egyesítését választja, a maximális érvényes DIU 4. |A fájlok számától és méretétől függően 4 és 32 között |
| A file Store-ból a nem file Store-ba |- **Másolás egyetlen fájlból**: 2-4 <br/>- **Másolás több fájlból**: 2-256 a fájlok számától és méretétől függően <br/><br/>Ha például egy 4 nagy méretű fájllal rendelkező mappából másol adatokból, a maximális érvényes DIU 16. |- **másolás Azure SQL Database vagy Azure Cosmos db**: a fogadó szintjétől (DTU/RUs) és a forrásfájl mintától függően 4 és 16 között<br>- **Másolás az Azure szinapszis analyticsbe** a Base vagy a copy utasítás használatával: 2<br>– Egyéb forgatókönyv: 4 |
| Nem a file Store-ból a file Store-ba |- **Másolás a partíció-kapcsolóval kompatibilis adattárakból** (beleértve az [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256, amikor egy mappába ír, és 2-4, ha egyetlen fájlba ír. Megjegyzés: a forrásoldali adatpartíciók akár 4 DIUs is használhatnak.<br>- **egyéb forgatókönyvek**: 2-4 |- **Másolás Rest-ből vagy http-ről**: 1<br/>- **az Amazon vöröseltolódásból való másolás a** kitöltéssel: 2<br>- **egyéb forgatókönyv**: 4 |
| Nem fájlos tárolók között |- **Másolás a partíció-kapcsolóval kompatibilis adattárakból** (beleértve az [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256, amikor egy mappába ír, és 2-4, ha egyetlen fájlba ír. Megjegyzés: a forrásoldali adatpartíciók akár 4 DIUs is használhatnak.<br/>- **egyéb forgatókönyvek**: 2-4 |- **Másolás Rest-ből vagy http-ről**: 1<br>- **egyéb forgatókönyv**: 4 |

A másolási tevékenység figyelési nézetében vagy a tevékenység kimenetében az egyes másolásokhoz használt DIUs láthatók. További információ: [másolási tevékenység figyelése](copy-activity-monitoring.md). Az alapértelmezett érték felülbírálásához a következőképpen kell megadnia a `dataIntegrationUnits` tulajdonság értékét. A másolási művelet által a futási időben használt *DIUs tényleges száma* az adatminta alapján egyenlő vagy kisebb a beállított értéknél.

**A felhasznált DIUs-\* a másolási időtartam után kell fizetnie \* egység ára/DIU óra**. Tekintse meg az [aktuális árakat.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) A helyi pénznem és a különálló kedvezmények előfizetési típusok esetén alkalmazhatók.

**Példa**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Saját üzemeltetésű Integration Runtime skálázhatósága

Ha nagyobb átviteli sebességet szeretne elérni, a saját üzemeltetésű integrációs modul vertikális felskálázásával vagy felskálázásával végezhető el:

- Ha a saját üzemeltetésű IR-csomópont CPU-és rendelkezésre álló memóriája nincs teljesen kihasználva, de az egyidejű feladatok végrehajtása eléri a korlátot, akkor a csomóponton futtatható egyidejű feladatok számának növelésével bővítse a skálázást.  Útmutatásért lásd [itt](create-self-hosted-integration-runtime.md#scale-up) .
- Ha viszont a CPU túl magas a saját üzemeltetésű IR-csomóponton, vagy kevés a rendelkezésre álló memória, akkor hozzáadhat egy új csomópontot a több csomópont terhelésének kiskálázásához.  Útmutatásért lásd [itt](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

Vegye figyelembe, hogy a következő esetekben az Egypéldányos tevékenységek végrehajtása több saját üzemeltetésű IR-csomópontot is kihasználhat:

- A fájlok számától és méretétől függően az adatok másolása a fájl alapú áruházakból.
- Az adatpartíciók számától függően másolja az adatok másolása a particionálási lehetőséggel rendelkező adattárolóból (beleértve az [Oracle](connector-oracle.md#oracle-as-source), a [Netezza](connector-netezza.md#netezza-as-source), a [Teradata](connector-teradata.md#teradata-as-source), az [SAP HANA](connector-sap-hana.md#sap-hana-as-source), az [SAP Table](connector-sap-table.md#sap-table-as-source)és az [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)szolgáltatást).

## <a name="parallel-copy"></a>Párhuzamos másolás

A másolási tevékenységnél párhuzamos másolást (`parallelCopies` tulajdonságot) is beállíthat, hogy jelezze a másolási tevékenység által használni kívánt párhuzamosságot. Ezt a tulajdonságot úgy tekintheti meg, mint a másolási tevékenységben a forrásból beolvasott szálak maximális száma, vagy párhuzamosan írható a fogadó adattárba.

A párhuzamos másolás az [adatintegrációs egységek](#data-integration-units) vagy a [saját üzemeltetésű IR-csomópontok](#self-hosted-integration-runtime-scalability)felé merőleges. A rendszer az összes DIUs vagy saját üzemeltetésű IR-csomópontra kiterjed.

Az egyes másolási tevékenységek futtatásakor a rendszer alapértelmezés szerint Azure Data Factory dinamikusan alkalmazza az optimális párhuzamos másolási beállítást a forrás-fogadó pár és az adatminta alapján. 

> [!TIP]
> A párhuzamos másolás alapértelmezett viselkedése általában megadja a legjobb átviteli sebességet, amelyet az ADF automatikusan határoz meg a forrás-fogadó pár, az adatok mintája, a DIUs vagy a saját üzemeltetésű IR CPU/memória/csomópont száma alapján. Tekintse át a [másolási tevékenység teljesítményének hibakeresését](copy-activity-performance-troubleshooting.md) a párhuzamos másolás finomhangolásához című témakörben.

A következő táblázat a párhuzamos másolási viselkedést sorolja fel:

| Másolja ki a forgatókönyv | Párhuzamos másolási viselkedés |
| --- | --- |
| A file Stores között | `parallelCopies` meghatározza a párhuzamosságot **a fájl szintjén**. Az egyes fájlokon belüli adatdarabolás automatikusan és transzparens módon történik. Úgy van kialakítva, hogy egy adott adattár-típushoz a legmegfelelőbb méretet használja az adat párhuzamos betöltéséhez. <br/><br/>A párhuzamos példányszámú másolási tevékenységek tényleges száma a futási időben nem haladja meg az Ön által használt fájlok számát. Ha a másolási viselkedés **mergeFile** , a másolási tevékenység nem tudja kihasználni a fájl szintű párhuzamosságot. |
| A file Store-ból a nem file Store-ba | -Az adatok Azure SQL Database vagy Azure Cosmos DBba másolásakor a párhuzamos másolás a fogadó rétegtől (DTU/RUs) függ.<br>-Az adatok Azure-táblázatba másolásakor az alapértelmezett párhuzamos másolás 4. |
| Nem a file Store-ból a file Store-ba | – Ha az adatok másolása a partícióról engedélyezett adattárból (például [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)és [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) történik, az alapértelmezett párhuzamos másolás 4. A párhuzamos példányszámú másolási tevékenységek tényleges száma a futási időben nem haladja meg az Ön által használt adatpartíciók számát. Ha saját üzemeltetésű Integration Runtime használ, és másolja az Azure Blob/ADLS Gen2ba, vegye figyelembe, hogy a maximális effektív párhuzamos másolás 4 vagy 5.<br>– Más esetekben a párhuzamos másolás nem lép érvénybe. Még ha a párhuzamosság is meg van adva, a rendszer nem alkalmazza. |
| Nem fájlos tárolók között | -Az adatok Azure SQL Database vagy Azure Cosmos DBba másolásakor a párhuzamos másolás a fogadó rétegtől (DTU/RUs) függ.<br/>-Az adatok Azure-táblázatba másolásakor az alapértelmezett párhuzamos másolás 4. |

Az adattárakat üzemeltető gépek terhelésének szabályozásához, vagy a másolási teljesítmény finomhangolásához felülbírálhatja az alapértelmezett értéket, és megadhatja a `parallelCopies` tulajdonság értékét. Az érték nagyobb vagy egyenlő 1 egész számnak kell lennie. Futásidőben a legjobb teljesítmény érdekében a másolási tevékenység olyan értéket használ, amely kisebb vagy egyenlő, mint a megadott érték.

Ha a `parallelCopies` tulajdonság értékét adja meg, vegye figyelembe a terhelés növekedését a forrás-és a fogadó adattárakban. Vegye figyelembe a terhelés növekedését is a saját üzemeltetésű integrációs modul esetében, ha a másolási tevékenységet a rendszer felhatalmazza. Ez a terhelés növekszik, különösen akkor, ha több tevékenység vagy ugyanazon tevékenység egyidejű futtatása történik ugyanazon az adattárban. Ha azt észleli, hogy az adattár vagy a saját üzemeltetésű integrációs modul túlterhelt a terheléssel, csökkentse a `parallelCopies` értéket a terhelés enyhítéséhez.

**Példa**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>Szakaszos másolás

Amikor adatokat másol egy forrásadattárból egy fogadó adattárba, választhatja egy átmeneti előkészítési tárolását a Blob storage használata. Átmeneti különösen hasznos a következő esetekben:

- **A különböző adattárakból származó adatok betöltését a SQL Data Warehouseon keresztül kell használni.** Az SQL Data Warehouse a polybase nagy átviteli sebességű mechanizmusként nagy mennyiségű adatok betöltése az SQL Data Warehouse-bA. A forrásadatok csak blob Storage-ban vagy Azure Data Lake Storeban szerepelhetnek, és meg kell felelniük a további feltételeknek. Ha egy adattár eltérő a Blob storage vagy az Azure Data Lake Store tölt be adatokat, az adatok másolását az átmeneti előkészítési Blob storage-n keresztül aktiválhatja. Ebben az esetben a Azure Data Factory végrehajtja a szükséges adatátalakításokat annak érdekében, hogy az megfeleljen a bázisterület követelményeinek. A PolyBase majd az adatok betöltése az SQL Data Warehouse-bA hatékonyan használja. További információkért lásd: az [adatok Azure SQL Data Warehouseba való betöltésének alapja](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)
- **Időnként igénybe veheti a hibrid adatáthelyezést (azaz egy helyszíni adattárból a Felhőbeli adattárolóba történő másolást) lassú hálózati kapcsolaton keresztül.** A teljesítmény javítása érdekében a szakaszos másolással tömörítheti a helyszíni adatok, így kevesebb időt vesz igénybe az adatok áthelyezése a Felhőbeli átmeneti adattárba. Ezután kibonthatja az átmeneti tárolóban lévő adatok kibontását a cél adattárba való betöltés előtt.
- **A vállalati informatikai házirendek miatt nem szeretné megnyitni a 80-as és a 443-es porton kívüli portokat a tűzfalon.** Például ha egy Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó adatokat másol egy helyszíni adattárolóban, kell aktiválni a kimenő TCP-kommunikációt az 1433-as porton, a Windows tűzfal és a vállalati tűzfalon. Ebben az esetben a szakaszos másolás kihasználhatja a saját üzemeltetésű integrációs modul előnyeit, hogy először másolja az adatait egy blob Storage-alapú átmeneti példányba HTTP vagy HTTPS protokollon keresztül a 443-es porton. Ezt követően az adatok betölthetők SQL Databaseba vagy SQL Data Warehouse a blob Storage átmeneti környezetből. Ebben a folyamatban nem kell 1433-as port engedélyezése.

### <a name="how-staged-copy-works"></a>Hogyan szakaszos másolás működése

Ha aktiválja az átmeneti tárolási szolgáltatás, először az adatokat másolja át a forrásadattár az átmeneti tárolási Blob storage (használata a saját). Ezután az adatok átmásolva az átmeneti adattár a fogadó adattárba. A Azure Data Factory automatikusan kezeli a kétlépcsős folyamatot. Az adatáthelyezés befejezése után a Azure Data Factory az átmeneti tárolóból is törli az ideiglenes adatok mennyiségét.

![Szakaszos másolás](media/copy-activity-performance/staged-copy.png)

Ha egy átmeneti tároló használatával aktiválja az adatáthelyezést, megadhatja, hogy szeretné-e tömöríteni az adatok tömörítését, mielőtt a forrás adattárból egy ideiglenes vagy átmeneti adattárba helyezi az adatátvitelt, majd kibontja az adatok ideiglenes vagy átmeneti dat-ból való áthelyezése előtt. egy tároló a fogadó adattárba.

Jelenleg nem másolhat Adatmásolást két olyan adattár között, amely különböző saját üzemeltetésű IRs-kapcsolaton keresztül csatlakozik, sem a, sem a szakaszos másolat nélkül. Ilyen esetben két explicit módon láncolt másolási tevékenységet konfigurálhat a forrásról az előkészítésre való másoláshoz, majd az előkészítésből a fogadóba.

### <a name="configuration"></a>Konfiguráció

Konfigurálja a **enableStaging** beállítást a másolási tevékenységben annak megadásához, hogy a blob Storage-ban kívánja-e az adatelőkészítést, mielőtt betölti azt egy célhely-adattárba. Ha a **enableStaging** `TRUE`értékre állítja, adja meg az alábbi táblázatban felsorolt további tulajdonságokat. Ha még nem rendelkezik ilyennel, létre kell hoznia egy Azure Storage vagy Storage közös hozzáférésű aláírással társított szolgáltatást az átmeneti tároláshoz.

| Tulajdonság | Leírás | Alapértelmezett érték | Kötelező |
| --- | --- | --- | --- |
| enableStaging |Adja meg, hogy szeretné-e az ideiglenes tároló átmeneti keresztül adatok másolása. |False (Hamis) |Nem |
| linkedServiceName |Adja meg egy [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) társított szolgáltatás nevét, amely az átmeneti előkészítési tárolóként használt tárolási példányra hivatkozik. <br/><br/> Nem használhat megosztott hozzáférési aláírással rendelkező tárolót az adatok SQL Data Warehouseba való betöltéséhez a Base használatával. Más esetekben használhatja azt. |N/A |Igen, ha a **enableStaging** értéke TRUE (igaz) |
| elérési út |Adja meg a Blob elérési útja, amelyet szeretne az előkészített adatokat tartalmaznak. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót az ideiglenes adattároláshoz. <br/><br/> Csak akkor, ha a Storage használata a közös hozzáférésű jogosultságkód vagy ideiglenes az adatokat egy adott helyen van szüksége, adjon meg egy elérési utat. |N/A |Nem |
| enableCompression |Megadja, hogy a rendszer a célhelyre való másolás előtt tömöríti-e az adatfájlokat. Ez a beállítás átvitt adatok mennyiségét csökkenti. |False (Hamis) |Nem |

>[!NOTE]
> Ha a szakaszos másolást engedélyezte a tömörítést, az átmeneti blobhoz társított szolgáltatás egyszerű vagy MSI-hitelesítése nem támogatott.

A másolási tevékenység mintájának definíciója az előző táblázatban ismertetett tulajdonságokkal rendelkezik:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Szakaszos másolás számlázási gyakorolt hatás

A díj két lépésből áll: a másolás időtartama és a másolás típusa alapján.

* Ha a Felhőbeli másolás során használ átmeneti tárolást, amely az adatok Felhőbeli adattárból egy másik Felhőbeli adattárba való másolását végzi el, az Azure Integration Runtime által felhatalmazott mindkét szakaszban az [1. lépés és a 2. lépés] x [Felhőbeli másolási egység ára] értékének összegét kell megfizetnie.
* Ha a hibrid másolás során átmeneti tárolást használ, amely egy helyszíni adattárból egy Felhőbeli adattárba másol egy olyan szakaszt, amely egy saját üzemeltetésű integrációs modul által felhatalmazott, akkor a [hibrid másolási időtartam] x [hibrid másolási egység ára] + [Felhőbeli másolás időtartama] x [Felhőbeli másolási egység ára].

## <a name="next-steps"></a>Következő lépések
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Másolási tevékenység teljesítményére és méretezhetőségére vonatkozó útmutató](copy-activity-performance.md)
- [A másolási tevékenység teljesítményével kapcsolatos hibák](copy-activity-performance-troubleshooting.md)
- [Az adatok áttelepíthetők a Azure Data Factory használatával az Azure-ba vagy az adattárházból](data-migration-guidance-overview.md)
- [Adatok migrálása az Amazon S3-ból az Azure Storage-ba](data-migration-guidance-s3-azure-storage.md)