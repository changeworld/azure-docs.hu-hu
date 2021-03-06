---
title: Az Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások | Microsoft Docs
description: Ismerje meg, hogy mely Azure-szolgáltatások integrálva vannak Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: cb68f1bc851a8573ddec01d1eee803135a11b067
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195358"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások

Az Azure-szolgáltatások az adatok betöltésére, az elemzések elvégzésére és a vizuális ábrázolások létrehozására használhatók. Ez a cikk felsorolja a támogatott Azure-szolgáltatásokat, felfedi a támogatási szintet, és olyan cikkekre mutató hivatkozásokat tartalmaz, amelyek segítenek ezeknek a szolgáltatásoknak a Azure Data Lake Storage Gen2 való használatához.

## <a name="supported-azure-services"></a>Támogatott Azure-szolgáltatások

Ez a táblázat felsorolja a Azure Data Lake Storage Gen2 használható Azure-szolgáltatásokat. A táblázatokban megjelenő elemek idővel változnak, ahogy a támogatás továbbra is bővül.

> [!NOTE]
> A támogatási szint csak arra vonatkozik, hogy a szolgáltatás milyen módon támogatott a Data Lake Storage Gen 2 használatával.

|Azure-szolgáltatás |Támogatási szint |Kapcsolódó cikkek |
|---------------|-------------------|---|
|Azure Data Factory|Általánosan elérhető|[Betöltés az Azure Data Lake Storage Gen2ba Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Általánosan elérhető|[Használat Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Gyors útmutató: Azure Data Lake Storage Gen2ban lévő adatelemzés Azure Databricks használatával](data-lake-storage-quickstart-create-databricks-account.md) <br>[Oktatóanyag: adatok kinyerése, átalakítása és betöltése a Azure Databricks használatával](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Oktatóanyag: Data Lake Storage Gen2-adatelérés Azure Databricks a Spark használatával](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hubs rögzítés| Általánosan elérhető|[Események rögzítése Azure-Event Hubs az Azure-ban Blob Storage vagy Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps|Általánosan elérhető|[Áttekintés – mi az Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Általánosan elérhető|[Az Azure Storage-szolgáltatásokban tárolt adathozzáférés](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Általánosan elérhető|[Gyors útmutató: Stream Analytics-feladatok létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Kimenő Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box| Általánosan elérhető|[A helyszíni HDFS-tárolóból az Azure Storage-ba történő Migrálás Azure Data Box használata](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | Általánosan elérhető|[Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[A HDFS CLI használata Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Oktatóanyag: adatok kinyerése, átalakítása és betöltése az Azure HDInsight Apache Hive használatával](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub | Általánosan elérhető|[Eszközről a felhőbe irányuló üzenetek küldése különböző végpontokra IoT Hub üzenet-útválasztás használatával](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI| Általánosan elérhető|[Data Lake Storage Gen2 adatai elemzése a Power BI használatával](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Általánosan elérhető|[Használat Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Általánosan elérhető|[Azure Storage-kapcsolatkezelő](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search| Előzetes verzió|[Azure Data Lake Storage Gen2 dokumentumok indexelése és keresése (előzetes verzió)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Content Delivery Network|Még nem támogatott|[Azure Data Lake Storage Gen2 dokumentumok indexelése és keresése (előzetes verzió)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Lásd még

- [Ismert problémák a Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [A blob Storage funkciói a Azure Data Lake Storage Gen2ban érhetők el](data-lake-storage-supported-blob-storage-features.md)
- [A Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md)
- [Több protokollos hozzáférés Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)