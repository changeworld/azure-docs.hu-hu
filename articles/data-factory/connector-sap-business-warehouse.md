---
title: Adatok másolása SAP BWról
description: Megtudhatja, hogyan másolhat adatok az SAP Business Warehouse-ból egy Azure Data Factory adatcsatorna másolási tevékenységének használatával támogatott fogadó adattárakba.
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
ms.date: 09/04/2019
ms.openlocfilehash: 0c37d77ca73ddbe8b79351f90275a1d639757633
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923741"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Adatok másolása az SAP Business Warehouse-ból Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktuális verzió](connector-sap-business-warehouse.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok egy SAP Business Warehouse-ból (BW) történő másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

>[!TIP]
>Az ADF SAP-adatintegrációs forgatókönyvre vonatkozó általános támogatásának megismeréséhez tekintse meg az [SAP-Adatintegráció Azure Data Factory tanulmány használatával](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) részletes bevezetést, comparsion és útmutatást.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SAP Business Warehouse-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az SAP Business Warehouse-ból származó adatok bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez az SAP Business Warehouse-összekötő a következőket támogatja:

- SAP Business Warehouse **7-es verzió. x**.
- Az adatok másolása az **InfoCubes és a QueryCubes** (beleértve a többek között a többek között a következőt: MDX-lekérdezések
- Adatok másolása egyszerű hitelesítés használatával.

## <a name="prerequisites"></a>Előfeltételek

Az SAP Business Warehouse-összekötő használatához a következőket kell tennie:

- Saját üzemeltetésű Integration Runtime beállítása. További részletekért tekintse meg a saját üzemeltetésű [Integration Runtime](create-self-hosted-integration-runtime.md) szóló cikket.
- Telepítse az **SAP NetWeaver Library** -t a Integration Runtime gépre. Az SAP NetWeaver kódtárat az SAP-rendszergazdától, vagy közvetlenül az [SAP Software Download Center](https://support.sap.com/swdc)webhelyről szerezheti be. Keresse meg az **SAP-megjegyzés #1025361** a legfrissebb verzió letöltési helyének lekéréséhez. Ügyeljen arra, hogy az Integration Runtime-telepítésnek megfelelő **64 bites** SAP NetWeaver könyvtárat válassza. Ezután telepítse az SAP NetWeaver RFC SDK-ban található összes fájlt az SAP-Megjegyzés alapján. Az SAP NetWeaver könyvtárat az SAP-ügyféleszközök telepítése is tartalmazza.

>[!TIP]
>A SAP BW kapcsolódási problémáinak megoldásához ügyeljen a következőre:
>- A NetWeaver RFC SDK-ból kinyert összes függőségi könyvtár a%windir%\System32 mappában található. Általában a icudt34. dll, a icuin34. dll, a icuuc34. dll, a libicudecnumber. dll, a librfc32. dll, a libsapucum. dll, a sapcrypto. dll, a sapcryto_old. dll, a sapnwrfc. dll.
>- Az SAP-kiszolgálóhoz való csatlakozáshoz használt szükséges portok engedélyezve vannak a saját üzemeltetésű IR-gépen, amely általában a 3300-es és a 3201-es port.

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az SAP Business Warehouse-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SAP Business Warehouse (BW) társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **SapBw** | Igen |
| kiszolgáló | Annak a kiszolgálónak a neve, amelyen az SAP BW-példány található. | Igen |
| systemNumber | A SAP BWrendszer rendszerszáma.<br/>Engedélyezett érték: két számjegyből álló decimális szám karakterláncként megadva. | Igen |
| ügyfél-azonosító | A-ügyfél ügyfél-azonosítója az SAP W rendszeren.<br/>Engedélyezett érték: a háromjegyű decimális szám karakterláncként van megadva. | Igen |
| userName (Felhasználónév) | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve. | Igen |
| jelszó | A felhasználó jelszava. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . A saját üzemeltetésű Integration Runtime az [Előfeltételek](#prerequisites)szakaszban említettek szerint kell megadni. |Igen |

**Példa**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a SAP BW adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok SAP BWból való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **SapBwCube**értékre. A RelationalTable típusú SAP BW adatkészlet esetében nem támogatottak a típus-specifikus tulajdonságok.

**Példa**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Ha `RelationalTable` gépelt adatkészletet használ, a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a SAP BW forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-bw-as-source"></a>SAP BW forrásként

Az adatok SAP BWból történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **SapBwSource** | Igen |
| lekérdezés | Megadja az MDX-lekérdezést, amely beolvassa az SAP BW-példány adatait. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ha `RelationalSource` gépelt forrást használ, a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="data-type-mapping-for-sap-bw"></a>SAP BW adattípusának leképezése

Az adatok SAP BWból való másolása során a rendszer a következő leképezéseket használja SAP BW adattípusokból Azure Data Factory köztes adattípusokra. A másolási tevékenység a forrás sémájának és adattípusának a fogadóba való leképezésével kapcsolatos tudnivalókat lásd: [séma-és adattípus-leképezések](copy-activity-schema-and-type-mapping.md) .

| SAP BW adattípus | Az adatgyár átmeneti adattípusa |
|:--- |:--- |
| ACCP | Int |
| CHAR | Sztring |
| Ügyf | Sztring |
| CURR | Decimális |
| CUKY | Sztring |
| DECEMBER | Decimális |
| FLTP | Double |
| INT1 | Bájt |
| INT2 | Int16 |
| INT4 | Int |
| LANG | Sztring |
| LCHR | Sztring |
| LRAW | Bájt [] |
| PREC | Int16 |
| QUAN | Decimális |
| NYERS | Bájt [] |
| RAWSTRING | Bájt [] |
| KARAKTERLÁNC | Sztring |
| EGYSÉG | Sztring |
| DATS | Sztring |
| NUMC | Sztring |
| TIMS | Sztring |


## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
