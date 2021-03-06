---
title: Azure Integration Runtime létrehozása a Azure Data Factory-ben
description: Megtudhatja, hogyan hozhat létre Azure Integration Runtime-t a Azure Data Factoryban, amely az adatmásolási és-átalakítási tevékenységek másolására szolgál.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 87633abaaae1f6034709c6e552be6647533115ec
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260760"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Azure Integration Runtime létrehozása és konfigurálása
A Integration Runtime (IR) a Azure Data Factory által használt számítási infrastruktúra, amely különböző hálózati környezetekben biztosít adatintegrációs képességeket. Az IR-vel kapcsolatos további információkért lásd: [Integration Runtime](concepts-integration-runtime.md).

A Azure IR teljes körűen felügyelt számítást biztosít az adatáthelyezés natív módon történő elvégzéséhez és az Adatátalakítási tevékenységek elküldéséhez a számítási szolgáltatások, például a HDInsight számára. Az Azure-környezetben üzemel, és támogatja a nyilvános hálózati környezetben lévő erőforrásokhoz való csatlakozást nyilvános elérhetőségű végpontokkal.

Ez a dokumentum bemutatja, hogyan hozhat létre és konfigurálhat Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Alapértelmezett Azure IR
Alapértelmezés szerint minden egyes adatfeldolgozó Azure IR rendelkezik a háttérben, amely támogatja a Felhőbeli adattárakon és a nyilvános hálózatban lévő számítási szolgáltatásokban végzett műveleteket. A Azure IR helye automatikusan feloldásra kerül. Ha a **connectvia tulajdonsággal** tulajdonság nincs megadva a társított szolgáltatás definíciójában, a rendszer az alapértelmezett Azure IR használja. Csak explicit módon kell létrehoznia egy Azure IR, ha explicit módon meg szeretné határozni az IR helyét, vagy ha azt szeretné, hogy gyakorlatilag a tevékenység-végrehajtásokat a különböző IRs felügyeleti célokra lehessen csoportosítani. 

## <a name="create-azure-ir"></a>Azure IR létrehozása
Integration Runtime a **set-AzDataFactoryV2IntegrationRuntime PowerShell-** parancsmag használatával hozható létre. Azure IR létrehozásához adja meg a nevet, a helyet és a típust a parancshoz. Az alábbi példa egy olyan Azure IR létrehozására szolgál, amelynek a helye "Nyugat-Európa":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Azure IR esetén a típust **felügyelt**értékre kell beállítani. Nem kell megadnia a számítási adatokat, mert teljes mértékben felügyelt a felhőben. Azure-SSIS IR létrehozásához a számítási adatokat, például a csomópontok méretét és a csomópontok darabszámát kell megadni. További információ: [Azure-SSIS IR létrehozása és konfigurálása](create-azure-ssis-integration-runtime.md).

A set-AzDataFactoryV2IntegrationRuntime PowerShell-parancsmag használatával meglévő Azure IR is konfigurálhat a hely módosításához. Az Azure IR helyével kapcsolatos további információkért lásd: az [Integration Runtime bemutatása](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Azure IR használata

Azure IR létrehozása után hivatkozhat rá a társított szolgáltatás definíciójában. Alább látható egy példa arra, hogyan hivatkozhat a fent létrehozott Azure Integration Runtime egy Azure Storage-beli társított szolgáltatásból:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>További lépések
Az integrációs modulok egyéb típusainak létrehozásáról a következő cikkekben talál további információt:

- [Saját üzemeltetésű integrációs modul létrehozása](create-self-hosted-integration-runtime.md)
- [Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md)
 
