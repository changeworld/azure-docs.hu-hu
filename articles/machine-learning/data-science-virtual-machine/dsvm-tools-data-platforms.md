---
title: Támogatott adatplatformok
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg az Azure Data Science Virtual Machine által támogatott adatplatformokat és eszközöket.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bfae8147c348c76fa0e406fec283144ebc26e86b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270107"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>A Data Science virtuális gépen támogatott adatplatform

Data Science Virtual Machine (DSVM) használatával számos adatplatformon létrehozhatja az elemzést. A DSVM felületek távoli azokat a platformokat, gyors fejlesztési lehetőségeket és prototípus-készítés helyi példányt nyújt.

A DSVM a következő adatplatform-eszközöket támogatja.

## <a name="sql-server-developer-edition"></a>SQL Server Developer kiadás

| | |
| ------------- | ------------- |
| Mi ez?   | Egy helyi relációs adatbázis-példányt      |
| Támogatott DSVM-kiadások      | Windows: SQL Server 2017, Windows 2019 (előzetes verzió): SQL Server 2019      |
| Gyakori használati      | Gyors fejlesztés helyileg kisebb adatkészlet <br/> Futtassa az adatbázis-R   |
| A minták mutató hivatkozások      |    A New York City-adathalmazok egy kis mintája betöltődik az SQL Database-be:<br/>  `nyctaxi` <br/> A Microsoft Machine Learning Server és az adatbázison belüli elemzéseket bemutató Jupyter-minta a következő helyen található:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| A DSVM kapcsolódó eszközök       | SQL Server Management Studio <br/> ODBC/JDBC-illesztőprogramok<br/> a pyodbc RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server Developer kiadás csak fejlesztési és tesztelési célokra használható. Licenc vagy egy, az SQL Serveres virtuális gépek üzemi környezetben való futtatás szükséges.


### <a name="setup"></a>Beállítás

Az adatbázis-kiszolgáló már előre konfigurálva van, és a SQL Serverhoz (például `SQL Server (MSSQLSERVER)`) kapcsolódó Windows-szolgáltatások automatikus futtatásra vannak beállítva. Az egyetlen manuális lépés magában foglalja az adatbázis-elemzések Microsoft Machine Learning Server használatával történő engedélyezését. Az elemzés engedélyezéséhez futtassa a következő parancsot egyszeri műveletként a SQL Server Management Studio (SSMS) alkalmazásban. Futtassa ezt a parancsot a számítógép-rendszergazdaként való bejelentkezés után, nyisson meg egy új lekérdezést a SSMS, és győződjön meg arról, hogy a kiválasztott adatbázis `master`:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
SQL Server Management Studio futtatásához megkeresheti a programok listájában a "SQL Server Management Studio" kifejezést, vagy a Windows Search használatával megkeresheti és futtathatja. Amikor a rendszer kéri a hitelesítő adatokat, válassza a **Windows-hitelesítés** lehetőséget, és használja a gép nevét vagy ```localhost``` a **SQL Server név** mezőben.

### <a name="how-to-use-and-run-it"></a>Használat és Futtatás

Alapértelmezés szerint az alapértelmezett adatbázis-példánnyal rendelkező adatbázis-kiszolgáló automatikusan lefut. A virtuális gép eszközökkel, mint például az SQL Server Management Studio használatával helyben SQL Server adatbázisának elérésére. A helyi rendszergazdai fiókok rendszergazdai hozzáféréssel rendelkeznek az adatbázishoz.

Emellett a DSVM ODBC-és JDBC-illesztőprogramokkal is rendelkezik, amelyekkel a SQL Server, az Azure SQL Database és a Azure SQL Data Warehouse különböző nyelveken írt alkalmazásokból, beleértve a Pythont és a Machine Learning Servert is.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hogyan van konfigurálva és telepítve a DSVM? 

 A SQL Server a szabványos módon települ. A `C:\Program Files\Microsoft SQL Server`címen érhető el. Az adatbázison belüli Machine Learning Server példány a következő helyen található: `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. A DSVM külön önálló Machine Learning Server-példánnyal is rendelkezik, amely `C:\Program Files\Microsoft\R Server\R_SERVER`re van telepítve. Ez a két Machine Learning Server példány nem osztja meg a kódtárakat.


## <a name="apache-spark-2x-standalone"></a>Az Apache Spark-2.x (önálló)

| | |
| ------------- | ------------- |
| Mi ez?   | A népszerű Apache Spark platform önálló (egyetlen csomóponton belüli) példánya; gyors, nagy léptékű adatfeldolgozási és gépi tanulási rendszer     |
| Támogatott DSVM-kiadások      | Linux     |
| Gyakori használati      | * A Spark/PySpark alkalmazások gyors fejlesztése egy kisebb adatkészlettel és későbbi, nagyméretű Spark-fürtökön, például az Azure HDInsight-ben való üzembe helyezéssel<br/> * Microsoft Machine Learning Server Spark-környezet tesztelése <br />* A SparkML vagy a Microsoft nyílt forráskódú [MMLSpark](https://github.com/Azure/mmlspark) -függvénytárának használata ml-alkalmazások készítéséhez |
| A minták mutató hivatkozások      |    Jupyter-minta: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (Spark Context):/dsvm/samples/MRS/MRSSparkContextSample.R |
| A DSVM kapcsolódó eszközök       | PySpark, Scala<br/>Jupyter (Spark-/ PySpark-kernelek)<br/>Microsoft Machine Learning Server, Sparker, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Használat
A parancssorban a `spark-submit` vagy `pyspark` parancs futtatásával is elküldheti a Spark-feladatokat. Jupyter notebook hozzon létre egy új jegyzetfüzetet a Spark-kernel is létrehozhat.

A Spark for R használatával olyan könyvtárakat használhat, mint a Sparker, a Sparklyr és a Microsoft Machine Learning Server, amelyek elérhetők a DSVM. Tekintse meg az előző táblázatban szereplő minták mutatókat tartalmaznak.

### <a name="setup"></a>Beállítás
Mielőtt a (z) Ubuntu Linux DSVM kiadásban Microsoft Machine Learning Server Spark-kontextusban fut, végre kell hajtania egy egyszeri telepítési lépést, amely lehetővé teszi a helyi egycsomópontos Hadoop HDFS és a fonal példányának engedélyezését. Alapértelmezés szerint Hadoop-szolgáltatásokhoz vannak telepítve, de a dsvm-hez a letiltva. Az engedélyezéshez futtassa a következő parancsokat root-ként az első alkalommal:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Ha ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```futtatásával már nincs szüksége rájuk, leállíthatja a Hadoop-hez kapcsolódó szolgáltatásokat.

Egy minta, amely bemutatja, hogyan fejlesztheti és tesztelheti a MRS-t egy távoli Spark-környezetben (amely a DSVM önálló Spark-példánya), és elérhető a `/dsvm/samples/MRS` könyvtárban.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hogyan van konfigurálva és telepítve a DSVM? 
|Platform|Telepítési hely ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Az Azure Blob Storage-ból vagy a Azure Data Lake Storageból származó adatok eléréséhez szükséges kódtárak a Microsoft MMLSpark gépi tanulási kódtárak használatával $SPARK _HOME/jars. Spark indulásakor a rendszer automatikusan betölti a JAR-fájlok kivételével. Alapértelmezés szerint a Spark a helyi lemezen lévő adatátvitelt használja. 

Ahhoz, hogy a DSVM található Spark-példány hozzáférjen a blob Storage-ban vagy a Azure Data Lake Storageban tárolt információhoz, létre kell hoznia és konfigurálnia kell a `core-site.xml` fájlt a $SPARK _HOME/conf/Core-site.xml.template. A blob Storage-hoz és a Azure Data Lake Storagehoz is hozzá kell férnie a megfelelő hitelesítő adatokkal. (Vegye figyelembe, hogy a sablonfájlok helyőrzőket használnak a blob Storage és a Azure Data Lake Storage konfigurációkhoz.)

Azure Data Lake Storage szolgáltatás hitelesítő adatainak létrehozásával kapcsolatos részletes információkért lásd: [hitelesítés a Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)használatával. A blob Storage vagy a Azure Data Lake Storage hitelesítő adatainak a Core-site. xml fájlban való megadása után az ezekben a forrásokban tárolt adatokra a wasb://vagy a adl://URI-előtagján keresztül hivatkozhat.

