---
title: Virtuális mag erőforrás-korlátok – rugalmas készletek
description: Ez az oldal néhány gyakori virtuális mag-erőforrás-korlátot ismertet a rugalmas készletek Azure SQL Databaseban.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 03/03/2020
ms.openlocfilehash: a6186753c845070ff2a5b3a3f8c6ff0de51e52f0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255703"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Rugalmas készletek erőforrás-korlátai a virtuális mag beszerzési modell használatával

Ez a cikk részletes erőforrás-korlátokat biztosít a rugalmas készletek és a készletezett adatbázisok Azure SQL Database a virtuális mag beszerzési modell használatával.

A DTU megvásárlására vonatkozó korlátokat lásd: [SQL Database DTU erőforrás-korlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információ: [a tárterület kezelése Azure SQL Databaseban](sql-database-file-space-management.md).

A szolgáltatási szintet, a számítási méretet és a tárterületet a [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), a [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), az [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)vagy a [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases)használatával állíthatja be.

> [!IMPORTANT]
> A méretezéssel kapcsolatos útmutatást és szempontokat lásd: [rugalmas készlet](sql-database-elastic-pool-scale.md) skálázása

## <a name="general-purpose---provisioned-compute---gen4"></a>Általános célú kiépített számítás – Gen4

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Általános célú szolgáltatási szintek: 4. generációs számítási platform (1. rész)

|Számítási méret|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Adatbázisok maximális száma készletenként|100|200|500|500|500|500|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|512|756|1536|1536|1536|2048|
|Napló maximális mérete|154|227|461|461|461|614|
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|Tárolási típus|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|IOPS adatkészletek maximális száma *|400|800|1200|1600|2000|2400|
|Maximális naplózási arány (MB/s)|4,7|9,4|14,1|18,8|23,4|28,1|
|Egyidejű feldolgozók maximális száma (kérelmek) * * |210|420|630|840|1050|1260|
|Egyidejű bejelentkezések maximális száma a készletben * * |210|420|630|840|1050|1260|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Replikák száma|1|1|1|1|1|1|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

\*\* az egyidejű feldolgozók maximális száma (kérése) számára az egyes adatbázisokhoz: [önálló adatbázis-erőforrások korlátai](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Általános célú szolgáltatási szintek: 4. generációs számítási platform (2. rész)

|Számítási méret|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Adatbázisok maximális száma készletenként|500|500|500|500|500|500|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|2048|2048|2048|2048|3584|4096|
|Napló maximális mérete (GB)|614|614|614|614|1075|1229|
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|Tárolási típus|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|IOPS adatkészletek maximális száma *|2800|3200|3600|4000|6400|9600|
|Maximális naplózási arány (MB/s)|32,8|37,5|37,5|37,5|37,5|37,5|
|Egyidejű feldolgozók maximális száma (kérelmek) *|1470|1680|1890|2100|3360|5040|
|Egyidejű bejelentkezések maximális száma (kérelmek) *|1470|1680|1890|2100|3360|5040|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Replikák száma|1|1|1|1|1|1|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [önálló adatbázis-erőforrás korlátai](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

## <a name="general-purpose---provisioned-compute---gen5"></a>Általános célú kiépített számítás – Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Általános célú szolgáltatási szintek: 5. generációs számítási platform (1. rész)

|Számítási méret|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Adatbázisok maximális száma készletenként|100|200|500|500|500|500|500|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|512|756|1536|1536|1536|2048|2048|
|Napló maximális mérete (GB)|154|227|461|461|461|614|614|
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|448|
|Tárolási típus|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|IOPS adatkészletek maximális száma *|800|1600|2400|3200|4000|4800|5600|
|Maximális naplózási arány (MB/s)|9,4|18,8|28,1|37,5|37,5|37,5|37,5|
|Egyidejű feldolgozók maximális száma (kérelmek) * *|210|420|630|840|1050|1260|1470|
|Egyidejű bejelentkezések maximális száma (kérelmek) * *|210|420|630|840|1050|1260|1470|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Replikák száma|1|1|1|1|1|1|1|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

\*\* az egyidejű feldolgozók maximális száma (kérése) számára az egyes adatbázisokhoz: [önálló adatbázis-erőforrások korlátai](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Általános célú szolgáltatási szintek: 5. generációs számítási platform (2. rész)

|Számítási méret|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Adatbázisok maximális száma készletenként|500|500|500|500|500|500|500|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|2048|3072|3072|3072|4096|4096|4096|
|Napló maximális mérete (GB)|614|922|922|922|1229|1229|1229|
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|IOPS adatkészletek maximális száma * |6,400|7 200|8,000|9 600|12,800|16,000|32,000|
|Maximális naplózási arány (MB/s)|37,5|37,5|37,5|37,5|37,5|37,5|37,5|
|Egyidejű feldolgozók maximális száma (kérelmek) * *|1680|1890|2100|2520|3360|4200|8400|
|Egyidejű bejelentkezések maximális száma (kérelmek) * *|1680|1890|2100|2520|3360|4200|8400|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 16, 24, 32, 40|0, 0,25, 0,5, 1... 16, 24, 32, 40, 80|
|Replikák száma|1|1|1|1|1|1|1|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

\*\* az egyidejű feldolgozók maximális száma (kérése) számára az egyes adatbázisokhoz: [önálló adatbázis-erőforrások korlátai](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Általános célú kiépített számítás – Fsv2 sorozat

### <a name="fsv2-series-compute-generation-preview"></a>Fsv2 sorozatú számítási generáció (előzetes verzió)

|Számítási méret|GP_Fsv2_72|
|:--- | --: |
|Számítási generáció|Fsv2 sorozat|
|Virtuális mag|72|
|Memória (GB)|136,2|
|Adatbázisok maximális száma készletenként|500|
|Oszlopcentrikus-támogatás|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|
|Maximális adatméret (GB)|4096|
|Napló maximális mérete (GB)|1024|
|TempDB maximális adatméret (GB)|333|
|Tárolási típus|Prémium (távoli) tárterület|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|IOPS adatkészletek maximális száma *|16,000|
|Maximális naplózási arány (MB/s)|37,5|
|Egyidejű feldolgozók maximális száma (kérelmek) * *|3780|
|Egyidejű bejelentkezések maximális száma (kérelmek) * *|3780|
|Egyidejű munkamenetek maximális száma|30,000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0-72|
|Replikák száma|1|
|Több – AZ|N/A|
|Olvasási felskálázás|N/A|
|Mellékelt biztonsági mentési tár|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

\*\* az egyidejű feldolgozók maximális száma (kérése) számára az egyes adatbázisokhoz: [önálló adatbázis-erőforrások korlátai](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

## <a name="business-critical---provisioned-compute---gen4"></a>Üzleti szempontból kritikus – kiépített számítás – Gen4

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Üzleti szempontból kritikus szolgáltatási szintek: 4. generációs számítási platform (1. rész)

|Számítási méret|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|2|3|4|5|6|
|Memória (GB)|14|21|28|35|42|
|Adatbázisok maximális száma készletenként|50|100|100|100|100|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|2|3|4|5|6|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|
|Napló maximális mérete (GB)|307|307|307|307|307|
|TempDB maximális adatméret (GB)|64|96|128|160|192|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|IOPS adatkészletek maximális száma *|9 000|13 500|18 000|22 500|27 000|
|Maximális naplózási arány (MB/s)|20|30|40|50|60|
|Egyidejű feldolgozók maximális száma (kérelmek) * *|420|630|840|1050|1260|
|Egyidejű bejelentkezések maximális száma (kérelmek) * *|420|630|840|1050|1260|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Replikák száma|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

\*\* az egyidejű feldolgozók maximális száma (kérése) számára az egyes adatbázisokhoz: [önálló adatbázis-erőforrások korlátai](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Üzleti szempontból kritikus szolgáltatási szintek: 4. generációs számítási platform (2. rész)

|Számítási méret|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Adatbázisok maximális száma készletenként|100|100|100|100|100|100|
|Oszlopcentrikus-támogatás|N/A|N/A|N/A|N/A|N/A|N/A|
|Memóriában tárolt OLTP-tároló (GB)|7|8|9.5|11|20|36|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|1024|
|Napló maximális mérete (GB)|307|307|307|307|307|307|
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|IOPS adatkészletek maximális száma *|31 500|36 000|40 500|45,000|72 000|96 000|
|Maximális naplózási arány (MB/s)|70|80|80|80|80|80|
|Egyidejű feldolgozók maximális száma (kérelmek) * *|1470|1680|1890|2100|3360|5040|
|Egyidejű bejelentkezések maximális száma (kérelmek) * *|1470|1680|1890|2100|3360|5040|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Replikák száma|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

\*\* az egyidejű feldolgozók maximális száma (kérése) számára az egyes adatbázisokhoz: [önálló adatbázis-erőforrások korlátai](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

## <a name="business-critical---provisioned-compute---gen5"></a>Üzleti szempontból kritikus – kiépített számítás – Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Üzleti szempontból kritikus szolgáltatási szintek: 5. generációs számítási platform (1. rész)

|Számítási méret|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|4|6|8|10|12|14|
|Memória (GB)|20,8|31,1|41,5|51,9|62,3|72,7|
|Adatbázisok maximális száma készletenként|50|100|100|100|100|100|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximális adatméret (GB)|1024|1536|1536|1536|3072|3072|
|Napló maximális mérete (GB)|307|307|461|461|922|922|
|TempDB maximális adatméret (GB)|128|192|256|320|384|448|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|IOPS adatkészletek maximális száma *|18 000|27 000|36 000|45,000|54,000|63 000|
|Maximális naplózási arány (MB/s)|60|90|120|120|120|120|
|Egyidejű feldolgozók maximális száma (kérelmek) * *|420|630|840|1050|1260|1470|
|Egyidejű bejelentkezések maximális száma (kérelmek) * *|420|630|840|1050|1260|1470|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Replikák száma|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

\*\* az egyidejű feldolgozók maximális száma (kérése) számára az egyes adatbázisokhoz: [önálló adatbázis-erőforrások korlátai](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Üzleti szempontból kritikus szolgáltatási szintek: 5. generációs számítási platform (2. rész)

|Számítási méret|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Adatbázisok maximális száma készletenként|100|100|100|100|100|100|100|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,68|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Napló maximális mérete (GB)|922|922|922|1229|1229|1229|1229|
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|IOPS adatkészletek maximális száma *|72 000|81 000|90,000|108 000|144 000|180,000|256 000|
|Maximális naplózási arány (MB/s)|120|120|120|120|120|120|120|
|Egyidejű feldolgozók maximális száma (kérelmek) * *|1680|1890|2100|2520|3360|4200|8400|
|Egyidejű bejelentkezések maximális száma (kérelmek) * *|1680|1890|2100|2520|3360|4200|8400|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 20, 24, 32, 40|0, 0,25, 0,5, 1... 20, 24, 32, 40, 80|
|Replikák száma|4|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

\*\* az egyidejű feldolgozók maximális száma (kérése) számára az egyes adatbázisokhoz: [önálló adatbázis-erőforrások korlátai](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

## <a name="business-critical---provisioned-compute---m-series"></a>Üzleti szempontból kritikus – kiépített számítás – M sorozat

### <a name="m-series-compute-generation-preview"></a>Az M-sorozat számítási generációja (előzetes verzió)

|Számítási méret|BC_M_128|
|:--- | --: |
|Számítási generáció|M sorozat|
|Virtuális mag|128|
|Memória (GB)|3767,1|
|Adatbázisok maximális száma készletenként|100|
|Oszlopcentrikus-támogatás|Igen|
|Memóriában tárolt OLTP-tároló (GB)|1768|
|Maximális adatméret (GB)|4096|
|Napló maximális mérete (GB)|2048|
|TempDB maximális adatméret (GB)|4096|
|Tárolási típus|Helyi SSD|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|IOPS adatkészletek maximális száma *|200,000|
|Maximális naplózási arány (MB/s)|333|
|Egyidejű feldolgozók maximális száma (kérelmek) *|13 440|
|Egyidejű bejelentkezések maximális száma (kérelmek) *|13 440|
|Egyidejű munkamenetek maximális száma|30,000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0-128|
|Replikák száma|4|
|Több – AZ|Igen|
|Olvasási felskálázás|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

\*\* az egyidejű feldolgozók maximális száma (kérése) számára az egyes adatbázisokhoz: [önálló adatbázis-erőforrások korlátai](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

Ha a rugalmas készlet összes virtuális mag foglalt, akkor a készletben lévő összes adatbázis egyenlő mennyiségű számítási erőforrást kap a lekérdezések feldolgozásához. Az SQL Database szolgáltatás egyenlő erőforrás-megosztást biztosít az adatbázisok között azáltal, hogy mindegyiküknek egyenlő szeleteket ad a számítási időből. A rugalmas készlet erőforrásainak megosztása a méltányosság érdekében az egyes adatbázisok számára más módon garantált erőforrásokhoz is, ha a virtuális mag min/adatbázis értéke nem nulla értékre van állítva.

## <a name="database-properties-for-pooled-databases"></a>A készletezett adatbázisok adatbázis-tulajdonságai

A következő táblázat a készletezett adatbázisok tulajdonságait ismerteti.

> [!NOTE]
> A rugalmas készletekben található különálló adatbázisok erőforrás-korlátai általában ugyanazok, mint a készleteken kívüli önálló adatbázisok esetében, amelyek ugyanazzal a számítási mérettel rendelkeznek. Például az GP_Gen4_1-adatbázisok maximális egyidejű feldolgozói 200 feldolgozók. Így a GP_Gen4_1-készletben lévő adatbázisok maximálisan egyidejű feldolgozói is 200 feldolgozók. Vegye figyelembe, hogy GP_Gen4_1 készletben lévő egyidejű feldolgozók száma összesen 210.

| Tulajdonság | Leírás |
|:--- |:--- |
| Virtuális mag maximális száma |A készletben lévő bármely adatbázis által használható virtuális mag maximális száma, ha a készletben lévő más adatbázisok kihasználtsága alapján elérhető. Az adatbázisok maximális virtuális mag nem erőforrás-garancia egy adatbázishoz. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Állítsa be az adatbázis maximális virtuális mag az adatbázis-kihasználtsági csúcsok kezelésére. A rendszer bizonyos fokú túllépést vár el, mivel a készlet általánosságban feltételezi az olyan adatbázisok gyakori és ritka használati mintáit, amelyekben az összes adatbázis nem rendelkezik egyszerre csúcstal.|
| Virtuális mag minimális száma |A készletben lévő bármelyik adatbázis virtuális mag minimális száma garantált. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az adatbázis min. virtuális mag értéke 0, a pedig az alapértelmezett érték is. Ez a tulajdonság 0 és egy adatbázis átlagos virtuális mag-kihasználtsága között van beállítva. A készletben található adatbázisok száma és az adatbázis min virtuális mag nem haladhatja meg a virtuális mag-t.|
| Tárterület maximális száma adatbázison |A felhasználó által a készletben lévő adatbázis számára beállított maximális adatbázis-méret. A készletezett adatbázisok megosztják a lefoglalt készlet tárterületét, így az adatbázis mérete elérheti a fennmaradó készlet tárterületét és az adatbázis méretét. Az adatbázis maximális mérete az adatfájlok maximális méretére vonatkozik, és nem tartalmazza a naplófájlok által használt területet. |
|||

## <a name="next-steps"></a>Következő lépések

- Egyetlen adatbázis virtuális mag erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a virtuális mag beszerzési modell használatával](sql-database-vcore-resource-limits-single-databases.md)
- Egyetlen adatbázis DTU erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a DTU beszerzési modell használatával](sql-database-dtu-resource-limits-single-databases.md)
- A rugalmas készletek DTU erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a DTU beszerzési modell használatával](sql-database-dtu-resource-limits-elastic-pools.md)
- A felügyelt példányok erőforrás-korlátaival kapcsolatban lásd: [felügyelt példányok erőforrás-korlátai](sql-database-managed-instance-resource-limits.md).
- Az általános Azure-korlátokkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Az adatbázis-kiszolgálók erőforrás-korlátaival kapcsolatos információkért tekintse meg a kiszolgáló és az előfizetési szint korlátaival kapcsolatos információkat a [SQL Database kiszolgálók erőforrás-korlátainak áttekintése](sql-database-resource-limits-database-server.md) című témakörben.
