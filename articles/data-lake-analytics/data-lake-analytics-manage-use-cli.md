---
title: Azure Data Lake Analytics kezelése az Azure CLI-vel
description: Ez a cikk azt ismerteti, hogyan használható az Azure CLI Data Lake Analytics feladatok, adatforrások & felhasználók kezelésére.
services: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: d66926d8ba87096537800d22a9c116b7b10d23cf
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309737"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Azure Data Lake Analytics kezelése az Azure parancssori felületével (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ismerje meg, hogyan kezelheti Azure Data Lake Analytics fiókokat, adatforrásokat, felhasználókat és feladatokat az Azure CLI használatával. Ha más eszközökkel szeretné megtekinteni a felügyeleti témákat, kattintson a fenti fülre.


**Előfeltételek**

Az oktatóanyag megkezdése előtt a következő erőforrásokkal kell rendelkeznie:

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* Azure CLI. Lásd: [Install and configure Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (Az Azure parancssori felület telepítése és konfigurálása).

   * Töltse le és telepítse az **előzetes kiadású** [Azure parancssori felületi eszközöket](https://github.com/MicrosoftBigData/AzureDataLake/releases) a bemutató elvégzéséhez.

* A `az login` parancsot használva végezze el a hitelesítést, és válassza ki a használni kívánt előfizetést. További információ a munkahelyi vagy iskolai fiók segítségével történő hitelesítésről: [Connect to an Azure subscription from the Azure CLI](/cli/azure/authenticate-azure-cli) (Csatlakozás Azure-előfizetéshez az Azure parancssori felületről).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Most már elérheti a Data Lake Analytics és Data Lake Store parancsokat. Futtassa a következő parancsot a Data Lake Store és Data Lake Analytics parancsok listázásához:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Fiókok kezelése

Data Lake Analytics feladatok futtatása előtt rendelkeznie kell egy Data Lake Analytics-fiókkal. Az Azure HDInsight eltérően nem kell fizetnie egy Analytics-fiókot, ha nem fut a feladatokban. Csak akkor kell fizetnie, amikor egy feladatot futtat.  További információ: [Azure Data Lake Analytics Overview (áttekintés](data-lake-analytics-overview.md)).  

### <a name="create-accounts"></a>Fiókok létrehozása

A következő parancs futtatásával hozzon létre egy Data Lake fiókot, 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Fiókok frissítése

A következő parancs frissíti egy meglévő Data Lake Analytics-fiók tulajdonságait

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Fiókok listázása

Adott erőforráscsoporthoz tartozó Data Lake Analytics fiókok listázása

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Fiók részleteinek beolvasása

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Fiók törlése

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Adatforrások kezelése

Data Lake Analytics jelenleg a következő két adatforrást támogatja:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Analytics-fiók létrehozásakor ki kell jelölnie egy Azure Data Lake Storage fiókot az alapértelmezett Storage-fiókhoz. Az alapértelmezett Data Lake Storage-fiók a feladatok metaadatainak és a feladatok naplóinak tárolására szolgál. Az Analytics-fiók létrehozása után további Data Lake Storage fiókokat és/vagy Azure Storage-fiókokat adhat hozzá. 

### <a name="find-the-default-data-lake-store-account"></a>Az alapértelmezett Data Lake Store fiók megkeresése

Megtekintheti a `az dla account show` parancs futtatásával használt alapértelmezett Data Lake Store fiókot. Az alapértelmezett fióknév a defaultDataLakeStoreAccount tulajdonság alatt jelenik meg.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>További blob Storage-fiókok hozzáadása

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Csak a blob Storage-beli rövid nevek támogatottak. Ne használja a teljes tartománynevet (például "myblob.blob.core.windows.net").
> 

### <a name="add-additional-data-lake-store-accounts"></a>További Data Lake Store fiókok hozzáadása

A következő parancs egy további Data Lake Store fiókkal frissíti a megadott Data Lake Analytics fiókot:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Meglévő adatforrás frissítése

Meglévő blob Storage-fiók kulcsának frissítése:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Adatforrások listázása:

A Data Lake Store fiókok listázása:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

A blob Storage-fiók listázása:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Data Lake Analytics adatforrások listázása](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Adatforrások törlése:
Data Lake Store fiók törlése:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

BLOB Storage-fiók törlése:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Feladatok kezelése
Ahhoz, hogy feladatot lehessen létrehozni, Data Lake Analytics fiókkal kell rendelkeznie.  További információ: Data Lake Analytics- [fiókok kezelése](#manage-accounts).

### <a name="list-jobs"></a>Feladatok listázása

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Data Lake Analytics adatforrások listázása](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Feladatok részleteinek beolvasása

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Feladatok elküldése

> [!NOTE]
> A feladat alapértelmezett prioritása 1000, a feladathoz tartozó alapértelmezett párhuzamossági fok pedig 1.
> 
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Feladatok megszakítása
A LIST parancs használatával keresse meg a feladat azonosítóját, majd a Mégse gombra kattintva szakítsa meg a feladatot.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Folyamatok és ismétlődések

**Folyamatok és ismétlődések adatainak lekérése**

A korábban elküldött feladatok folyamatadatait az `az dla job pipeline` paranccsal tekintheti meg.

```
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

A korábban elküldött feladatok ismétlődési adatait az `az dla job recurrence` paranccsal tekintheti meg.

```
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="see-also"></a>Lásd még
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [A Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md)
* [Az Azure Data Lake Analytics kezelése az Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

