---
title: Adatok áthelyezése az Azure SQL Database - csoportos adatelemzési folyamat
description: Az adatok áthelyezhetők az egyszerű fájlokból (CSV vagy TSV formátumokból) vagy egy helyszíni SQL Server tárolt adatokból egy Azure SQL Databaseba.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722458"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Adatok áthelyezése egy Azure SQL-adatbázisba az Azure Machine Learning számára

Ez a cikk az adatok sima fájlokból (CSV vagy TSV formátumokból) vagy egy helyszíni SQL Server tárolt adatokból egy Azure SQL Database való áthelyezésének lehetőségeit ismerteti. Adatok áthelyezése a felhőbe ezeket a feladatokat a csoportos adatelemzési folyamat részét képezik.

Egy olyan témakörben, amely az adatáthelyezési lehetőségeket ismerteti egy helyszíni SQL Server Machine Learning számára, tekintse meg az [Azure-beli virtuális gépeken az adatáthelyezés SQL Serverre](move-sql-server-virtual-machine.md)című témakört.

A következő táblázat összefoglalja az adatok áthelyezése az Azure SQL Database, a beállításokat.

| <b>FORRÁS</b> | <b>CÉL: Azure SQL Database</b> |
| --- | --- |
| <b>Sima fájl (CSV vagy TSV formázva)</b> |[SQL-lekérdezés tömeges beszúrása](#bulk-insert-sql-query) |
| <b>Helyszíni SQL Server</b> |1.[Exportálás a lapos fájlba](#export-flat-file)<br> 2. [SQL Database áttelepítési varázsló](#insert-tables-bcp)<br> 3. [az adatbázis biztonsági mentése és visszaállítása](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Előfeltételek
Az itt ismertetett eljárások szükség van:

* Egy **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure Storage-fiók**. Ez az oktatóanyag az adatok tárolása Azure storage-fiók használhat. Ha nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című cikket. Miután létrehozta a tárfiókot, szerezze be a tárterület elérésére használt fiók kulcsot kell. Lásd: a [Storage-fiók elérési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md).
* Hozzáférés egy **Azure SQL Databasehoz**. Ha be kell állítania egy Azure SQL Databaset, [első lépések a Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) a Azure SQL Database új példányának kiépítésével kapcsolatos információkat tartalmaz.
* **Azure PowerShell** helyileg telepítve és konfigurálva. Útmutatásért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

**Adat**: az áttelepítési folyamatokat a [New York-i taxi adatkészlet](https://chriswhong.com/open-data/foil_nyc_taxi/)mutatja be. A New York-i taxi adatkészlet az utazási adatokról és a vásárokról tartalmaz információkat, és az Azure Blob Storage-ban érhető el: a [NYC-taxi adatai](https://www.andresmh.com/nyctaxitrips/). Ezen fájlok mintáját és leírását a [New York-i taxis adatkészletének leírásában](sql-walkthrough.md#dataset)ismertetjük.

Mindig a saját adatok egy készletét az itt leírt eljárásokat, vagy kövesse a lépéseket, a NYC Taxi adatkészlet használatával leírtak szerint. A New York-i taxi-adatkészlet helyszíni SQL Server adatbázisba való feltöltéséhez kövesse az [adatok tömeges importálása SQL Server-adatbázisba](sql-walkthrough.md#dbload)című szakaszban leírt eljárást. Ezek az utasítások a egy SQL Server Azure virtuális gépen, de tölt fel a helyszíni SQL Server eljárás megegyezik.

## <a name="file-to-azure-sql-database"></a>Adatok áthelyezése egy egyszerű fájlból a Azure SQL Databaseba
Az egyszerű fájlokban (CSV vagy TSV formátumú) tárolt adat áthelyezhető egy Azure SQL Databaseba egy tömeges INSERT SQL-lekérdezés használatával.

### <a name="bulk-insert-sql-query"></a>SQL-lekérdezés tömeges beszúrása
A tömeges beszúrási SQL-lekérdezést használó eljárás lépései hasonlóak az Azure-beli virtuális gépeken SQL Server az adatok egy sima fájlból való áthelyezéséhez. További részletek: [SQL-lekérdezés tömeges beszúrása](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a>Adatok áthelyezése a helyszíni SQL Serverból egy Azure SQL Databaseba
Ha a forrásadatok tárolása helyszíni SQL Server történik, számos lehetőség áll rendelkezésre az adatAzure SQL Databaseba való áthelyezésre:

1. [Exportálás az egyszerű fájlba](#export-flat-file)
2. [SQL Database áttelepítési varázsló](#insert-tables-bcp)
3. [Adatbázis biztonsági mentése és visszaállítása](#db-migration)
4. [Azure Data Factory](#adf)

Az első három lépés hasonló ahhoz, hogy az [adatáthelyezés SQL Server egy Azure-beli virtuális gépen](move-sql-server-virtual-machine.md) , amely ezekre az eljárásokra vonatkozik. A témakör megfelelő részeiben mutató hivatkozások szerepelnek az alábbi utasításokat.

### <a name="export-flat-file"></a>Exportálás az egyszerű fájlba
A lapos fájlba való exportálás lépései hasonlóak az [Exportálás az egyszerű fájlba](move-sql-server-virtual-machine.md#export-flat-file)művelethez tartozó utasításokhoz.

### <a name="insert-tables-bcp"></a>SQL Database áttelepítési varázsló
Az SQL Database áttelepítési varázsló használatának lépései hasonlóak a [SQL Database áttelepítési varázsló](move-sql-server-virtual-machine.md#sql-migration)által jelzett utasításokhoz.

### <a name="db-migration"></a>Adatbázis biztonsági mentése és visszaállítása
Az adatbázis biztonsági mentésének és visszaállításának használatának lépései hasonlóak az [adatbázis biztonsági mentése és visszaállítása](move-sql-server-virtual-machine.md#sql-backup)során felsorolt utasításokhoz.

### <a name="adf"></a>Azure Data Factory
Megtudhatja, hogyan helyezhet át adatáthelyezést egy Azure Data Factory (ADF) Azure SQL Databaseba a témakörben, hogyan [helyezhet át egy helyszíni SQL Server-kiszolgálóról a Azure Data Factory-be SQL Azure](move-sql-azure-adf.md). Ez a témakör azt mutatja be, hogyan lehet az ADF használatával áthelyezni az adatok egy helyszíni SQL Server-adatbázisból egy Azure SQL Databaseba az Azure Blob Storage használatával.

Akkor érdemes az ADF használatát használni, ha a helyszíni és a Felhőbeli forrásokból folyamatosan át kell telepíteni az adatátvitelt.  Az ADF abban az esetben is segít az adatátalakításban, vagy új üzleti logikát igényel az áttelepítés során. Az ADF lehetővé teszi, hogy az ütemezés és a figyelési feladatok használata kezelheti az adatmozgás rendszeres időközönként egyszerű JSON-szkript. Az ADF is tartalmaz egyéb szolgáltatásokat, például az összetett műveletek támogatása.
