---
title: Mi az az Azure Databricks?
description: Ismerkedjen meg a Azure Databricks és a Spark on Databricks az Azure-ba való beszerzésével. Az Azure Databricks a Microsoft Azure Cloud Services platformra optimalizált Apache Spark-alapú elemzési platform.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: overview
ms.date: 05/08/2019
ms.author: mamccrea
ms.custom: mvc
ms.openlocfilehash: ed93f332c6361d2f7cd5189ee5fedf3d9f5cf82d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75410448"
---
# <a name="what-is-azure-databricks"></a>Mi az az Azure Databricks?

Az Azure Databricks a Microsoft Azure Cloud Services platformra optimalizált Apache Spark-alapú elemzési platform. Az Apache Spark alapítóival közösen tervezett Databricks integrálva van az Azure-ral, így egyetlen kattintással beállítható, zökkenőmentes munkafolyamatokat tesz lehetővé, valamint olyan interaktív munkaterületet biztosít, amellyel lehetővé válik az adatszakértők, az adatmérnökök és az üzleti elemzők közötti együttműködés.

![Mi az Azure Databricks?](./media/what-is-azure-databricks/azure-databricks-overview.png "Mi az az Azure Databricks?")

Azure Databricks egy gyors, könnyű és együttműködő Apache Spark elemzési szolgáltatás. Egy big data folyamat esetében az adatmennyiség (nyers vagy strukturált) az Azure-ba kerül betöltésre a kötegekben Azure Data Factory, vagy közel valós időben áramlik a Kafka, az Event hub vagy a IoT Hub használatával. Ezek az adattárak a hosszú távú tároláshoz, az Azure Blob Storage vagy a Azure Data Lake Storage. Az elemzési munkafolyamat részeként Azure Databricks több adatforrásból (például [Azure Blob Storageból](../storage/blobs/storage-blobs-introduction.md), [Azure Data Lake Storageból](../data-lake-store/index.md), [Azure Cosmos DBból](../cosmos-db/index.yml)vagy [Azure SQL Data Warehouseból](../sql-data-warehouse/index.yml) ) származó adatok beolvasására, és a Spark használatával áttekinthető információkra vált.

![Databricks folyamat](./media/what-is-azure-databricks/databricks-pipeline.png)

## <a name="apache-spark-based-analytics-platform"></a>Apache Spark-alapú elemzési platform

Az Azure Databricks a teljesen nyílt forráskódú Apache Spark fürtszolgáltatás technológiáiból és képességeiből áll. Az Apache Spark az Azure Databricksben a következő összetevőkből áll:

![Apache Spark a Azure Databricks](./media/what-is-azure-databricks/apache-spark-ecosystem-databricks.png "Apache Spark az Azure Databricksben")

* **Spark SQL és DataFrames**: A Spark SQL a strukturált adatokkal végzett munka során használható Spark-modul. A DataFrame egy elosztott adatgyűjtemény megnevezett oszlopokba rendezve. Elméleti szinten azonos a relációs adatbázisokban található táblákkal vagy R/Python adatkeretekkel.

* **Streamelés**: Valós idejű adatfeldolgozás és -elemzés analitikai és interaktív alkalmazásokhoz. Integrálható az HDFS, Flume és Kafka szolgáltatásokkal.

* **MLlib**: Machine learning könyvtár, amely közös tanulási algoritmusokból és segédprogramokból áll, beleértve a besorolást, a regressziót, a fürtözést, az együttműködési szűrést, a dimenzióját-csökkentést, valamint a mögöttes optimalizálási primitíveket.

* **GraphX**: Grafikonok és grafikonszámítások a használati esetek széles köréhez, a kognitív analitikától egészen az adatfeltárásig.

* **Spark Core API**: Támogatást biztosít az R, SQL, Python, Scala és Java szolgáltatásokhoz.

## <a name="apache-spark-in-azure-databricks"></a>Apache Spark az Azure Databricksben

Az Azure Databricks a Spark képességeire építve egy olyan felügyeletet nem igénylő felhőalapú platformot biztosít, amely a következőket tartalmazza:

- Teljes körűen felügyelt Spark-fürtök
- Interaktív munkaterület a felfedezéshez és vizualizációhoz
- Platform a kedvenc Spark-alapú alkalmazásai futtatásához

### <a name="fully-managed-apache-spark-clusters-in-the-cloud"></a>Teljes körűen felügyelt Apache Spark-fürtök a felhőben

Az Azure Databricks egy olyan biztonságos és megbízható éles környezetet biztosít a felhőben, amelyet Spark-szakértők felügyelnek és támogatnak. Előnyök:

* Másodpercek alatt létrehozhat fürtöket.
* Dinamikusan és automatikusan fel- vagy leskálázhatja a fürtöket, beleértve a kiszolgáló nélküli fürtöket, és megoszthatja ezeket csapatok között. 
* Programozott módon használhat fürtöket REST API-k segítségével. 
* A Sparkra épülő biztonságos adatintegrációs képességek használatával központosítás nélkül egyesítheti az adatait. 
* Azonnali hozzáférést nyerhet a legújabb Apache Spark szolgáltatásokhoz minden kiadással.

### <a name="databricks-runtime"></a>A Databricks futtatókörnyezete
A Databricks futtatókörnyezete az Apache Sparkra épül, és natív módon az Azure-felhőhöz készült. 

A **Kiszolgáló nélküli** beállítással az Azure Databricks teljesen megszünteti az infrastruktúra összetettségét és a specializált szakértelem szükségességét az adatinfrastruktúra beállításához és konfigurálásához. A Kiszolgáló nélküli beállítás segítségével az adatszakértők gyors iterációkat végezhetnek csapatként.

Az éles feladatok teljesítményével foglalkozó adatmérnökök számára az Azure Databricks egy olyan Spark-motort biztosít, amely az I/O-réteg és a feldolgozási réteg (Databricks I/O) különböző optimalizálásainak köszönhetően még gyorsabb és nagyobb teljesítményű.

### <a name="workspace-for-collaboration"></a>Munkaterület az együttműködéshez

Az együttműködési és integrált környezet révén az Azure Databricks leegyszerűsíti az adatfeltárás, a prototípus-készítés, illetve az adatvezérelt alkalmazások futtatásának folyamatát a Sparkban.

* Meghatározhatja az adatok felhasználási módját az egyszerű adatfeltárás segítségével.
* Az előrehaladást jegyzetfüzetekben dokumentálhatja az R, Python, Scala vagy SQL alkalmazásokban.
* Néhány kattintással megjelenítheti adatait, valamint olyan jól ismert eszközöket használhat, mint a Matplotlib, a ggplot vagy a d3.
* Interaktív irányítópultok használatával hozhat létre dinamikus jelentéseket.
* A Spark használatával egyidejűleg interakcióba léphet az adataival.

## <a name="enterprise-security"></a>Vállalati biztonság

Az Azure Databricks vállalati szintű Azure-biztonságot kínál, beleértve az adatait és vállalkozását védő Azure Active Directory integrációt, a szerepköralapú vezérlőket és az SLA-kat.

* Az integráció az Azure Active Directoryval lehetővé teszi, hogy teljes Azure-alapú megoldásokat futtasson az Azure Databricks használatával.
* Az Azure Databricks szerepköralapú hozzáférésével részletes felhasználói engedélyeket állíthat be jegyzetfüzetekhez, fürtökhöz, feladatokhoz és adatokhoz.
* Vállalati szintű SLA-k. 

## <a name="integration-with-azure-services"></a>Együttműködés az Azure-szolgáltatásokkal

Az Azure Databricks mélyen együttműködik az Azure-adatbázisokkal és -tárolókkal: az SQL Data Warehouse, a Cosmos DB, a Data Lake Store és a Blob Storage szolgáltatásokkal. 

## <a name="integration-with-power-bi"></a>Integráció a Power BI-jal
A Power BI és az Azure Databricks gazdag integrációjának köszönhetően gyorsan és könnyedén felfedezheti és megoszthatja hatékony betekintéseit. Egyéb BI-eszközöket is használhat, például a Tableau Software-t JDBC/ODBC fürtvégpontokon keresztül.

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: Spark-feladatok futtatása az Azure Databricksben](quickstart-create-databricks-workspace-portal.md)
* [Spark-fürtök használata](/azure/databricks/clusters/index)
* [Jegyzetfüzetek használata](/azure/databricks/notebooks/index)
* [Spark-feladatok létrehozása](/azure/databricks/jobs)

 









