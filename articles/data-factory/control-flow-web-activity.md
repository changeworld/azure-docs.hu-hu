---
title: Webes tevékenység Azure Data Factory
description: Megtudhatja, hogyan használhatja a webes tevékenységeket, a Data Factory által támogatott vezérlési folyamatok egyikét a REST-végpontok egy folyamatból való meghívásához.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: c700c9786f3bec4c79cae904a95deb5fd1c670b4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260864"
---
# <a name="web-activity-in-azure-data-factory"></a>Webes tevékenység Azure Data Factory
A webes tevékenység segítségével meghívható egy egyéni REST-végpont egy Data Factory-folyamatból. Az adatkészleteket és a társított szolgáltatásokat továbbíthatja a tevékenység számára felhasználásra vagy elérés céljára.

> [!NOTE]
> A webes tevékenység csak nyilvánosan elérhető URL-címeket hívhat fel. A magánhálózati virtuális hálózatokban tárolt URL-címek esetében nem támogatott.

## <a name="syntax"></a>Szintaxis

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
név | A webes tevékenység neve | Sztring | Igen
type | **Webtevékenységre**kell beállítani. | Sztring | Igen
method | A célként megadott végpont REST API-metódusa. | sztring elemet. <br/><br/>Támogatott típusok: "GET", "POST", "PUT" | Igen
url | Cél végpontja és elérési útja | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés). A tevékenység 1 percenként időtúllépést jelez, ha a végponttól nem érkezik válasz. | Igen
fejlécek | A kérelembe küldendő fejlécek. Például egy kérelem nyelvének és típusának megadásához: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Igen, a Content-Type fejléc megadása kötelező. `"headers":{ "Content-Type":"application/json"}`
törzse | A végpontnak elküldhető adattartalmat jelöli.  | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés). <br/><br/>Tekintse meg a kérelem hasznos adatainak sémáját a [kérelmek hasznos adatait tartalmazó sémában](#request-payload-schema) . | A POST/PUT metódusokhoz szükséges.
hitelesítés | A végpont meghívásához használt hitelesítési módszer. A támogatott típusok az "alapszintű vagy ClientCertificate". További információ: [hitelesítés](#authentication) szakasz. Ha nincs szükség hitelesítésre, zárja be ezt a tulajdonságot. | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Nem
datasets | A végpontnak átadott adatkészletek listája. | Adatkészlet-hivatkozások tömbje. Üres tömb lehet. | Igen
linkedServices | A végpontnak átadott társított szolgáltatások listája. | Társított szolgáltatási referenciák tömbje. Üres tömb lehet. | Igen

> [!NOTE]
> A webes tevékenység által meghívott REST-végpontoknak JSON típusú választ kell visszaadniuk. A tevékenység 1 percenként időtúllépést jelez, ha a végponttól nem érkezik válasz.

A következő táblázat a JSON-tartalomra vonatkozó követelményeket mutatja be:

| Érték típusa | A kérés törzse | Válasz törzse |
|---|---|---|
|JSON-objektum | Támogatott | Támogatott |
|JSON-tömb | Támogatott <br/>(Jelenleg a JSON-tömbök nem működnek hiba miatt. A javítás folyamatban van.) | Nem támogatott |
| JSON-érték | Támogatott | Nem támogatott |
| Nem JSON-típus | Nem támogatott | Nem támogatott |
||||

## <a name="authentication"></a>Authentication

Az alábbiakban láthatók a webes tevékenységben támogatott hitelesítési típusok.

### <a name="none"></a>Nincs

Ha nincs szükség hitelesítésre, ne adja meg a "hitelesítés" tulajdonságot.

### <a name="basic"></a>Alapszintű

Adja meg az alapszintű hitelesítéshez használni kívánt felhasználónevet és jelszót.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Ügyféltanúsítvány

A PFX-fájl és a jelszó Base64 kódolású tartalmának megadása.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Felügyelt identitás

Itt adhatja meg azt az erőforrás-URI-t, amelynek a hozzáférési jogkivonatát a rendszer az adatok előállítójának felügyelt identitása alapján kéri le. Az Azure Resource Management API meghívásához használja a `https://management.azure.com/`. További információ a felügyelt identitások működéséről: [felügyelt identitások az Azure-erőforrások áttekintéséhez](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Ha az adat-előállító git-tárházral van konfigurálva, az alapszintű vagy az ügyféltanúsítvány-alapú hitelesítés használatához a hitelesítő adatait Azure Key Vault kell tárolnia. A Azure Data Factory nem tárol jelszavakat a git-ben.

## <a name="request-payload-schema"></a>Adattartalom-séma kérése
Ha a POST/PUT metódust használja, a Body (törzs) tulajdonság a végpontnak elküldhető adattartalmat jelöli. A hasznos adatok részeként a társított szolgáltatásokat és adatkészleteket is átadhatja. Itt látható a hasznos adatok sémája:

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>Példa
Ebben a példában a folyamat webes tevékenysége REST-végpontot hív meg. Egy Azure SQL társított szolgáltatást és egy Azure SQL-adatkészletet továbbít a végpontnak. A REST-végpont az Azure SQL-kapcsolati karakterlánc használatával csatlakozik az Azure SQL Serverhez, és az SQL Server-példány nevét adja vissza.

### <a name="pipeline-definition"></a>Folyamat definíciója

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>A folyamat paramétereinek értékei

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Webszolgáltatás végpontjának kódja

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Következő lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat:

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
