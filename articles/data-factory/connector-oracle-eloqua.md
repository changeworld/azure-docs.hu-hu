---
title: Adatok másolása az Oracle Eloqua (előzetes verzió)
description: Megtudhatja, hogyan másolhat adatokat az Oracle Eloqua egy Azure Data Factory-folyamat másolási tevékenységének használatával a támogatott fogadó adattárakba.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: deb5c87073a8963fc052d90f0f7c494cc0644f51
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927561"
---
# <a name="copy-data-from-oracle-eloqua-using-azure-data-factory-preview"></a>Adatok másolása Oracle-Eloqua Azure Data Factory használatával (előzetes verzió)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Oracle-Eloqua való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Kipróbálhatja, és visszajelzést is küldhet. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Oracle Eloqua-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az Oracle-Eloqua származó adatok bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie az adott összekötőt használó illesztőprogramokat.

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek az Oracle Eloqua-összekötőhöz tartozó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Oracle Eloqua társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **Eloqua** | Igen |
| endpoint | A Eloqua-kiszolgáló végpontja. A Eloqua több adatközpontot is támogat, a végpont meghatározásához jelentkezzen be https://login.eloqua.com a hitelesítő adataival, majd másolja ki az **alapszintű URL** -címet az átirányított URL-ből a `xxx.xxx.eloqua.com` mintázatával. | Igen |
| felhasználónév | A Eloqua-fiókhoz tartozó hely neve és felhasználóneve a (z) `SiteName\Username` formában: például `Eloqua\Alice`.  | Igen |
| jelszó | A felhasználónévnek megfelelő jelszó. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| useEncryptedEndpoints | Meghatározza, hogy az adatforrás-végpontok HTTPS protokollal legyenek titkosítva. Az alapértelmezett érték az igaz.  | Nem |
| useHostVerification | Azt adja meg, hogy szükséges-e az állomásnév a kiszolgáló tanúsítványában, hogy egyezzen a kiszolgáló állomásneve, amikor SSL-kapcsolaton keresztül csatlakozik. Az alapértelmezett érték az igaz.  | Nem |
| usePeerVerification | Meghatározza, hogy az SSL protokollon keresztüli kapcsolódáskor ellenőrizni kell-e a kiszolgáló identitását. Az alapértelmezett érték az igaz.  | Nem |

**Példa**

```json
{
    "name": "EloquaLinkedService",
    "properties": {
        "type": "Eloqua",
        "typeProperties": {
            "endpoint" : "<base URL e.g. xxx.xxx.eloqua.com>",
            "username" : "<site name>\\<user name e.g. Eloqua\\Alice>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az Oracle Eloqua-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Oracle-Eloqua való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **EloquaObject**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **EloquaObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "EloquaDataset",
    "properties": {
        "type": "EloquaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Eloqua linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az Oracle Eloqua-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="eloqua-as-source"></a>Eloqua forrásként

Az adatok Oracle-Eloqua való másolásához állítsa a forrás típusát a másolás tevékenység **EloquaSource**értékére. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **EloquaSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM Accounts"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromEloqua",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Eloqua input dataset name>",
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
                "type": "EloquaSource",
                "query": "SELECT * FROM Accounts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések
A Azure Data Factory által tárolt támogatott adatmennyiségek listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
