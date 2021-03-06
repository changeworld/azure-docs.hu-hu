---
title: Adatok másolása OData-forrásokból Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat OData-forrásokból származó adatokból támogatott fogadó adattárakat egy Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 17b78e03e330e342e9d558dd3ca5d9071bcd3c2f
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163930"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Adatok másolása OData-forrásból Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-odata-connector.md)
> * [Aktuális verzió](connector-odata.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factory egy OData-forrásból származó adatok másolásához. A cikk a [másolási tevékenységre épül Azure Data Factoryban](copy-activity-overview.md), amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a OData-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A OData-forrásból származó adatok bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként és a fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Ez a OData-összekötő a következőket támogatja:

- A OData 3,0-es és 4,0-es verziója.
- Adatok másolása a következő hitelesítések egyikével: **Névtelen**, **alapszintű**, **Windows**és **HRE egyszerű szolgáltatásnév**.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyekkel meghatározhatja Data Factory OData-összekötőhöz tartozó entitásokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Egy OData társított szolgáltatás esetében a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **OData**értékre kell beállítani. |Igen |
| url | A OData szolgáltatás gyökerének URL-címe. |Igen |
| authenticationType | A OData-forráshoz való kapcsolódáshoz használt hitelesítés típusa. Az engedélyezett értékek: **Névtelen**, **alapszintű**, **Windows**és **AadServicePrincipal**. A felhasználó-alapú OAuth nem támogatott. | Igen |
| userName | Ha alapszintű vagy Windows-hitelesítést használ, a **felhasználónevet** kell megadnia. | Nem |
| jelszó | Adja meg a **felhasználónévhez**megadott felhasználói fiókhoz tartozó **jelszót** . A mező megjelölése **SecureString** -típusként, hogy biztonságosan tárolja azt Data Factoryban. [Hivatkozhat a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md)is. | Nem |
| servicePrincipalId | Azure Active Directory alkalmazás ügyfél-AZONOSÍTÓjának megadásához. | Nem |
| aadServicePrincipalCredentialType | Adja meg az egyszerű szolgáltatás hitelesítéséhez használandó hitelesítő adatokat. Az engedélyezett értékek a következők: `ServicePrincipalKey` vagy `ServicePrincipalCert`. | Nem |
| servicePrincipalKey | A Azure Active Directory alkalmazás kulcsának megadásához. Megjelöli ezt a mezőt **SecureString** , hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Nem |
| servicePrincipalEmbeddedCert | Adja meg az alkalmazás Azure Active Directoryban regisztrált Base64-kódolású tanúsítványát. Megjelöli ezt a mezőt **SecureString** , hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Nem |
| servicePrincipalEmbeddedCertPassword | Adja meg a tanúsítvány jelszavát, ha a tanúsítvány jelszavas védelemmel van ellátva. Megjelöli ezt a mezőt **SecureString** , hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md).  | Nem|
| tenant | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Nem |
| aadResourceId | Határozza meg az HRE-erőforrást, amelyhez engedélyt kér.| Nem |
| connectVia | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**1. példa: Névtelen hitelesítés használata**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: egyszerű hitelesítés használata**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**3. példa: Windows-hitelesítés használata**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**4. példa: egyszerű szolgáltatásnév hitelesítésének használata**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**5. példa: a szolgáltatás egyszerű tanúsítvány-hitelesítésének használata**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Ez a szakasz a OData-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md). 

Az adatok OData való másolásához állítsa az adatkészlet **Type (típus** ) tulajdonságát **ODataResource**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **Type** tulajdonságát **ODataResource**értékre kell állítani. | Igen |
| elérési út | A OData-erőforrás elérési útja. | Igen |

**Példa**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz a OData forrás által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData forrásként

Az adatok OData történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **ODataSource**értékre kell állítani. | Igen |
| lekérdezés | OData-lekérdezési beállítások az adatszűréshez. Példa: `"$select=Name,Description&$top=5"`.<br/><br/>**Megjegyzés**: az OData-összekötő a következő összevont URL-címről másolja az adatait: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`. További információ: [OData URL-összetevők](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ha `RelationalSource` gépelt forrást használ, a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="data-type-mapping-for-odata"></a>Adattípusok leképezése OData

Amikor OData másol az adatokból, a következő leképezések használatosak a OData adattípusok és a Azure Data Factory köztes adattípusok között. Ha meg szeretné tudni, hogyan képezi le a másolási tevékenység a forrás sémáját és az adattípust a fogadóra, tekintse meg a [séma és adattípus-leképezések](copy-activity-schema-and-type-mapping.md)

| OData adattípusa | Data Factory közbenső adattípus |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | tizedes tört |
| Edm.Double | Dupla |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Sztring |
| Edm.Time | Időtartam |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Az összetett adattípusok (például az **objektum**) nem támogatottak a OData.


## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések

A másolási tevékenység által támogatott adattárak listáját a Azure Data Factoryban található forrásként és nyelőként tekintse meg a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben.