---
title: Hitelesítő adatok tárolása az Azure Key Vaultban
description: Megtudhatja, hogyan tárolhatja az Azure Key vaultban használt adattárak hitelesítő adatait, amelyek Azure Data Factory automatikusan lekérhető futásidőben.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: 1418205843fefc76db4e73832736b308d0cc79a3
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122610"
---
# <a name="store-credential-in-azure-key-vault"></a>Hitelesítő adatok tárolása Azure Key Vaultban

Az adattárakhoz és a számítási feladatokhoz tartozó hitelesítő adatokat egy [Azure Key Vault](../key-vault/key-vault-overview.md)tárolhatja. Azure Data Factory beolvassa a hitelesítő adatokat, amikor egy adattárat/számítást használó tevékenységet hajt végre.

Jelenleg az egyéni tevékenység kivételével az összes tevékenységtípus támogatja ezt a funkciót. Az összekötő konfigurálásához a részletekért olvassa el az [egyes összekötők témakör](copy-activity-overview.md#supported-data-stores-and-formats) "társított szolgáltatás tulajdonságai" szakaszát.

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció az adatok gyári felügyelt identitására támaszkodik. Ismerje meg, hogyan működik a [felügyelt identitás a refactoryban](data-factory-service-identity.md) , és győződjön meg arról, hogy az adatok előállítója társítva van.

## <a name="steps"></a>Lépések

A Azure Key Vaultban tárolt hitelesítő adatokra való hivatkozáshoz a következőket kell tennie:

1. Az **adatok gyári felügyelt identitásának beolvasása** a gyári környezettel együtt generált "felügyelt identitási objektum azonosító" értékének másolásával. Ha az ADF authoring felhasználói felületét használja, akkor a felügyelt identitás objektum azonosítója megjelenik a Azure Key Vault társított szolgáltatás létrehozása ablakban; a Azure Portalból is lekérheti, ha [beolvassa az adatok gyári felügyelt identitását](data-factory-service-identity.md#retrieve-managed-identity).
2. **Adja meg a felügyelt identitás hozzáférését a Azure Key Vaulthoz.** A Key vaultban – > hozzáférési szabályzatok – > Új-> Keresés a felügyelt identitásban a **Get** engedély megadása a titkos engedélyek legördülő menüben. Lehetővé teszi a kijelölt gyár számára a titkos kulcs elérését a Key vaultban.
3. **Hozzon létre egy társított szolgáltatást, amely a Azure Key Vaultre mutat.** Tekintse meg [Azure Key Vault társított szolgáltatást](#azure-key-vault-linked-service).
4. **Hozzon létre egy adattárhoz társított szolgáltatást, amely a Key vaultban tárolt megfelelő titkos kulcsra hivatkozik.** Tekintse meg a [Key vaultban tárolt hivatkozási titkot](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Társított szolgáltatás Azure Key Vault

Azure Key Vault társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **AzureKeyVault**. | Igen |
| BaseUrl | A Azure Key Vault URL-cím megadása. | Igen |

**Szerzői felhasználói felület használata:**

Kattintson a **kapcsolatok** -> **társított szolgáltatások** ->  **+ New** -> a "Azure Key Vault" kifejezésre:

![AKV keresése](media/store-credentials-in-key-vault/search-akv.png)

Válassza ki a kiépített Azure Key Vault, ahol a hitelesítő adatait tárolja. A **kapcsolódási teszttel** ellenőrizheti, hogy a AKV-kapcsolatok érvényesek-e. 

![AKV konfigurálása](media/store-credentials-in-key-vault/configure-akv.png)

**JSON-példa:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>A Key vaultban tárolt hivatkozási titok

A következő tulajdonságok támogatottak, ha olyan mezőt konfigurál a társított szolgáltatásban, amely a Key Vault-titokra hivatkozik:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A mező Type (típus) tulajdonságát a következőre kell beállítani: **AzureKeyVaultSecret**. | Igen |
| SecretName | A titok neve Azure Key Vaultban. | Igen |
| Titkoskulcsverziója | A titok verziója Azure Key Vaultban.<br/>Ha nincs megadva, mindig a titkos kulcs legújabb verzióját használja.<br/>Ha meg van adva, a rendszer az adott verzióra ragaszkodik.| Nem |
| store | Egy Azure Key Vault társított szolgáltatásra hivatkozik, amelyet a hitelesítő adatok tárolására használ. | Igen |

**Szerzői felhasználói felület használata:**

Válassza a titkos mezők **Azure Key Vault** lehetőséget, miközben létrehozza a kapcsolódást az adattárhoz vagy a számítási feladatokhoz. Válassza ki a kiépített Azure Key Vault társított szolgáltatást, és adja meg a **titkos nevet**. Igény szerint megadhat egy titkos verziót is. 

>[!TIP]
>A társított szolgáltatás kapcsolati karakterláncát (például SQL Server, blob Storage stb.) használó összekötők esetében választhat, hogy csak a titkos mezőt kívánja-e tárolni (például jelszó a AKV), vagy a teljes kapcsolati karakterláncot a AKV tárolja. Mindkét lehetőséget megtalálhatja a felhasználói felületen is.

![AKV titkos kulcsának konfigurálása](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON-példa: (lásd a "jelszó" szakaszt)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
