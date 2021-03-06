---
title: DTU erőforrás-korlátok önálló adatbázisok
description: Ez a lap a Azure SQL Database önálló adatbázisaihoz tartozó általános DTU-erőforrás-korlátokat ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: a4c435b4874301fe6fb804085c5b265954cd4f5a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256314"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>Az DTU beszerzési modellt használó önálló adatbázisok erőforrás-korlátai

Ez a cikk részletes erőforrás-korlátozásokat tartalmaz Azure SQL Database önálló adatbázisok számára a DTU beszerzési modell használatával.

A rugalmas készletek DTU megvásárlásához lásd: [DTU-erőforrások korlátai – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md). A virtuális mag erőforrás-korlátaival kapcsolatban lásd: [virtuális mag-erőforrások korlátai – önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [virtuális mag-erőforrások korlátai – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md). A különböző vásárlási modellekkel kapcsolatos további információkért lásd: [modellek és szolgáltatási szintek beszerzése](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Önálló adatbázis: tárolási méretek és számítási méretek

A következő táblázatok az egyes szolgáltatási rétegekben és számítási méretekben elérhető erőforrásokat jelenítik meg az egyes adatbázisokhoz. Az [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), a [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), a [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), az [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)vagy a [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases)használatával megadhatja a szolgáltatási szintet, a számítási méretet és a tárterületet egyetlen adatbázishoz.

> [!IMPORTANT]
> Az útmutatás és a megfontolások méretezésével kapcsolatban lásd: [önálló adatbázis skálázása](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Alapszintű szolgáltatási szint

| **Számítási méret** | **Basic** |
| :--- | --: |
| DTU-k maximális száma | 5 |
| Belefoglalt tárterület (GB) | 2 |
| Maximális tárolási lehetőségek (GB) | 2 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) |N/A |
| Egyidejű feldolgozók maximális száma (kérelem) | 30 |
| Egyidejű munkamenetek maximális száma | 300 |
|||

> [!IMPORTANT]
> Az alapszintű szolgáltatási csomag kevesebb mint egy virtuális mag (CPU) biztosít.  A CPU-igényes számítási feladatokhoz az S3 vagy nagyobb szolgáltatási réteg ajánlott. 
>
>Az adattárolásra vonatkozó alapszintű szolgáltatási szintet a standard oldal Blobokra helyezi. A standard oldal Blobok a merevlemezes (HDD-) alapú tárolóeszközöket használják, és a leghatékonyabb fejlesztéshez, teszteléshez és más, ritkán használt számítási feladatokhoz, amelyek kevésbé érzékenyek a teljesítmény változékonyságára.
>

### <a name="standard-service-tier"></a>Standard szolgáltatási szint

| **Számítási méret** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| DTU-k maximális száma | 10 | 20 | 50 | 100 |
| Belefoglalt tárterület (GB) | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségek (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | N/A | N/A | N/A | N/A |
| Egyidejű feldolgozók maximális száma (kérelem)| 60 | 90 | 120 | 200 |
| Egyidejű munkamenetek maximális száma |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> A standard S0, S1 és S2 szintjei kevesebb mint egy virtuális mag (CPU) biztosítanak.  A CPU-igényes számítási feladatokhoz az S3 vagy nagyobb szolgáltatási réteg ajánlott. 
>
>Az adattárolással kapcsolatban a standard szintű S0 és az S1 szolgáltatási szint a standard oldal Blobokra kerül. A standard oldal Blobok a merevlemezes (HDD-) alapú tárolóeszközöket használják, és a leghatékonyabb fejlesztéshez, teszteléshez és más, ritkán használt számítási feladatokhoz, amelyek kevésbé érzékenyek a teljesítmény változékonyságára.
>

### <a name="standard-service-tier-continued"></a>Standard szintű szolgáltatási szint (folytatás)

| **Számítási méret** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| DTU-k maximális száma | 200 | 400 | 800 | 1600 | 3000 |
| Belefoglalt tárterület (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségek (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | N/A | N/A | N/A | N/A |N/A |
| Egyidejű feldolgozók maximális száma (kérelem)| 400 | 800 | 1600 | 3200 |6000 |
| Egyidejű munkamenetek maximális száma |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Prémium szolgáltatási szint

| **Számítási méret** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| DTU-k maximális száma | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Belefoglalt tárterület (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Maximális tárolási lehetőségek (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Egyidejű feldolgozók maximális száma (kérelem)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Egyidejű munkamenetek maximális száma | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* 1024 GB-ig 4096 GB-onként 256 GB-os növekményekben

> [!IMPORTANT]
> A prémium szinten több mint 1 TB tárterület érhető el az összes régióban, kivéve a következőket: Kelet-Kína, Észak-Kína, Közép-Németország, Németország északkeleti régiója, az USA nyugati középső régiója, US DoD régiók és az USA kormányzati központja. Ezekben a régiókban a prémium szintű Storage Max 1 TB-ra van korlátozva.  További információ: [P11-P15 current korlátozások](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> `tempdb` korlátok esetében lásd: [tempdb korlátok](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Következő lépések

- Egyetlen adatbázis virtuális mag erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a virtuális mag beszerzési modell használatával](sql-database-vcore-resource-limits-single-databases.md)
- A rugalmas készletek virtuális mag erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a virtuális mag beszerzési modell használatával](sql-database-vcore-resource-limits-elastic-pools.md)
- A rugalmas készletek DTU erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a DTU beszerzési modell használatával](sql-database-dtu-resource-limits-elastic-pools.md)
- A felügyelt példányok erőforrás-korlátaival kapcsolatban lásd: [felügyelt példányok erőforrás-korlátai](sql-database-managed-instance-resource-limits.md).
- Az általános Azure-korlátokkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Az adatbázis-kiszolgálók erőforrás-korlátaival kapcsolatos információkért tekintse meg a kiszolgáló és az előfizetési szint korlátaival kapcsolatos információkat a [SQL Database kiszolgálók erőforrás-korlátainak áttekintése](sql-database-resource-limits-database-server.md) című témakörben.
