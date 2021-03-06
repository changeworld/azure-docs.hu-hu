---
title: A Microsoft Azure Data Box Blob storage-követelményei |} A Microsoft Docs
description: További információk a támogatott verziók az API-k, SDK-k és az Azure Data Box Blob storage ügyfélkódtárai
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61436494"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Az Azure Data Box Blob tárolási követelményei

Ez a cikk az Azure API-k, Azure-ügyfélkönyvtárak és eszközök támogatott a Data Box Blob storage-verziók listája. Data Box Blob storage blob felügyeleti funkciók az Azure-konzisztens szemantikáját biztosít. Ez a cikk is az Azure Storage szolgáltatás az ismert Azure Data Box Blob storage különbségeket foglalja össze.

Azt javasoljuk, hogy tekintse át az információkat gondosan előtt a Data Box blobtárolóba való csatlakozáshoz, majd lépjen vissza, szükség szerint.


## <a name="storage-differences"></a>Tárolási különbségek

|     Funkció                                             |     Azure Storage                                     |     Data Box-Blob storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Az Azure File storage                                   |    Felhőalapú SMB-fájlmegosztások támogatott              |    Nem támogatott      |
|    Inaktív adatok titkosítását                  |    256 bites AES-titkosítás                             |    256 bites AES-titkosítás |
|    Tárfiók típusa                                 |    Általános célú és az Azure blob storage-fiókok    |    Csak az általános célú v1|
|    A blob neve                                            |    1024 karakter hosszúságú (2048 bájt)                     |    880 karakter (1,760 bájt)|
|    Block blob maximális mérete                              |    4,75 TB (100 MB X 50 000 blokk)                   |    4,75 TB (100 MB x 50 000 blokk) az Azure Data Box v 1.8-as és újabb verziók esetében.|
|    Lapblob maximális méretét                               |    8 TB                                               |    1 TB                   |
|    Lapblob oldal méretét                                  |    512 bájt                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Támogatott API-verziók

Data Box Blob storage Azure Storage szolgáltatás API-k következő verziói támogatottak:

Az Azure Data Box 1.8-as és újabb verziók

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05) |
## <a name="supported-azure-client-libraries"></a>Támogatott Azure-ügyfélkönyvtárak

A Data Box Blob storage-ban vannak adott klienskódtárak és adott végpont utótagja követelményeinek. A Data Box Blob storage-végpontok nem rendelkezik teljes paritás az Azure Blob Storage REST API legújabb verzióját, lásd: a [az Azure Data Box 1.8-as és újabb verziók esetében támogatott verziók](#supported-api-versions). A tároló ügyfélkódtárai meg kell figyelembe venni a REST API-val kompatibilis verzióra.

### <a name="azure-data-box-18-onwards"></a>Az Azure Data Box 1.8-as és újabb verziók

| Ügyfélkódtár     |Data Box-Blob storage támogatott verziója     | Hivatkozás   |     Végpont-specifikáció      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Nuget-csomagot:   https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>GitHub-verzió:   https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config file                 |
|    Java                |    7.0.0                                           |    Maven-csomag:   https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub-verzió:   https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Kapcsolati karakterlánc beállítása         |
|    Node.js             |    2.8.3                                           |    Az NPM-hivatkozás:   https://www.npmjs.com/package/azure-storage   (Futtatás: `npm install azure-storage@2.7.0` )   <br>GitHub-verzió:   https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Szolgáltatás deklarációjában    |
|    C++                 |    5.2.0                                           |    Nuget-csomagot:   https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>GitHub-verzió:   https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Kapcsolati karakterlánc beállítása         |
|    PHP                 |    1.2.0                                           |    GitHub-verzió:<br>Közös: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Szerkesztő (a "további, az alábbi részletek megtekintéséhez.) keresztül telepítése                                                                                                             |    Kapcsolati karakterlánc beállítása         |
|    Python              |    1.1.0                                           |    GitHub-verzió:<br>Közös:   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>BLOB:   https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Szolgáltatás deklarációjában    |
|    Ruby                |    1.0.1                                           |    RubyGems csomag:<br>Közös:   https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub-verzió:<br>Közös: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Kapcsolati karakterlánc beállítása         |



### <a name="install-php-client-via-composer---current"></a>Composer - aktuális keresztül PHP-ügyfél telepítése

Composer keresztül történő: (végezze el a blob példaként).
1. Hozzon létre egy fájlt a következő kódot a projekt gyökérkönyvtárában található composer.json:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Töltse le `composer.phar` a projekt gyökérkönyvtárába.

3. Futtatás: telepítse a php composer.phar.

### <a name="endpoint-declaration"></a>Végpont deklarációját

Az Azure Data Box Blob storage-végpont tartalmaz két részből áll: egy régiót és a Data Box-tartomány nevét. A Data Box Blob Storage SDK-t, az alapértelmezett végpont az `\<serial no. of the device>.microsoftdatabox.com`.  További információk a blob service-végpont, [Data Box Blob storage csatlakozás](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Példák

### <a name="net"></a>.NET

Data Box a Blob Storage, a végpont utótagja van megadva a `app.config` fájlt:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Data Box Blob storage-ban a végpont utótagja van megadva a kapcsolati karakterlánc beállítása:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Data Box Blob storage-ban a nyilatkozat példányban van megadva a végpont utótagja:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Data Box Blob storage-ban a végpont utótagja van megadva a kapcsolati karakterlánc beállítása:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Data Box Blob storage-ban a végpont utótagja van megadva a kapcsolati karakterlánc beállítása:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Data Box Blob storage-ban a nyilatkozat példányban van megadva a végpont utótagja:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Data Box Blob storage-ban a végpont utótagja van megadva a kapcsolati karakterlánc beállítása:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>További lépések

* [Az Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)
