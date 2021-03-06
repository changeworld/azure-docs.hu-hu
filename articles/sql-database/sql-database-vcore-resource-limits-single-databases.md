---
title: Virtuális mag erőforrás-korlátai – önálló adatbázis
description: Ez az oldal a Azure SQL Database egyetlen adatbázisának néhány gyakori virtuális mag-erőforrás-korlátját ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/11/2020
ms.openlocfilehash: a8f62a24ff2c6571b5267fdbf4f23bd9e05ee499
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255755"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Az virtuális mag beszerzési modellt használó önálló adatbázisok erőforrás-korlátai

Ez a cikk részletes erőforrás-korlátozásokat tartalmaz Azure SQL Database önálló adatbázisok számára a virtuális mag beszerzési modell használatával.

A SQL Database-kiszolgálókon található önálló adatbázisok DTU megvásárlásához tekintse [meg a SQL Database-kiszolgálók erőforrás-korlátainak áttekintése](sql-database-resource-limits-database-server.md)című témakört.

Az [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), a [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), a [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), az [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)vagy a [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases)használatával megadhatja a szolgáltatási szintet, a számítási méretet és a tárterületet egyetlen adatbázishoz.

> [!IMPORTANT]
> Az útmutatás és a megfontolások méretezésével kapcsolatban lásd: [önálló adatbázis](sql-database-single-database-scale.md)skálázása.

## <a name="general-purpose---serverless-compute---gen5"></a>Általános célú kiszolgáló nélküli számítás – Gen5

A [kiszolgáló nélküli számítási rétegek](sql-database-serverless.md) jelenleg csak Gen5 hardveren érhetők el.

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generációja (1. rész)

|Számítási méret|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|
|Minimális – maximális virtuális mag|0,5 – 1|0,5 – 2|0,5 – 4|0,75 – 6|1.0 – 8|
|Minimális memória maximális mérete (GB)|2.02 – 3|2.05 – 6|2.10-12|2,25 – 18|3,00 – 24|
|Automatikus szüneteltetés minimális késleltetése (perc)|60|60|60|60|60|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|512|1024|1024|1024|1536|
|Napló maximális mérete (GB)|154|307|307|307|461|
|TempDB maximális adatméret (GB)|32|64|128|192|256|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|320|640|1280|1920|2560|
|Maximális naplózási arány (MBps)|3.8|7.5|15|22.5|30|
|Egyidejű feldolgozók maximális száma (kérelem)|75|150|300|450|600|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|1|1|1|1|1|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generációja (2. rész)

|Számítási méret|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|
|Minimális – maximális virtuális mag|1,25 – 10|1,50 – 12|1,75 – 14|2,00 – 16|
|Minimális memória maximális mérete (GB)|3,75 – 30|4.50 – 36|5.25 – 42|6,00 – 48|
|Automatikus szüneteltetés minimális késleltetése (perc)|60|60|60|60|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|1536|3072|3072|3072|
|Napló maximális mérete (GB)|461|461|461|922|
|TempDB maximális adatméret (GB)|320|384|448|512|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|3200|3840|4480|5120|
|Maximális naplózási arány (MBps)|30|30|30|30|
|Egyidejű feldolgozók maximális száma (kérelem)|750|900|1050|1200|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|
|Replikák száma|1|1|1|1|
|Több – AZ|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen4"></a>Nagy kapacitású – kiépített számítás – Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 számítási generációja (1. rész)

|Teljesítményszint|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100|
|Napló maximális mérete (TB)|1 |1 |1 |1 |1 |1 |
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|Tárolási típus| [1. Megjegyzés](#notes) |[1. Megjegyzés](#notes)|[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |
|Maximális adatmennyiség IOPS *|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|
|Maximális naplózási arány (MBps)|105 |105 |105 |105 |105 |105 |
|IO-késés (becsült)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tár megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

### <a name="gen4-compute-generation-part-2"></a>Gen4 számítási generációja (2. rész)

|Teljesítményszint|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100 |
|Napló maximális mérete (TB)|1 |1 |1 |1 |1 |1 |
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|Tárolási típus| [1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |
|Maximális adatmennyiség IOPS *|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|
|Maximális naplózási arány (MBps)|105 |105 |105 |105 |105 |105 |
|IO-késés (becsült)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|
|Egyidejű feldolgozók maximális száma (kérelem)|1400|1600|1800|2000|3200|4800|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tár megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen5"></a>Nagy kapacitású – kiépített számítás – Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generációja (1. rész)

|Teljesítményszint|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100 |100|
|Napló maximális mérete (TB)|1 |1 |1 |1 |1 |1 |1 |
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|448|
|Tárolási típus| [1. Megjegyzés](#notes) |[1. Megjegyzés](#notes)|[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |
|Maximális adatmennyiség IOPS *|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|
|Maximális naplózási arány (MBps)|105 |105 |105 |105 |105 |105 |105 |
|IO-késés (becsült)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|1400|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tár megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generációja (2. rész)

|Teljesítményszint|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100 |100 |
|Napló maximális mérete (TB)|1 |1 |1 |1 |1 |1 |1 |
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus| [1. Megjegyzés](#notes) |[1. Megjegyzés](#notes)|[1. Megjegyzés](#notes)|[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |
|Maximális adatmennyiség IOPS *|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|
|Maximális naplózási arány (MBps)|105 |105 |105 |105 |105 |105 |105 |
|IO-késés (becsült)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|
|Egyidejű feldolgozók maximális száma (kérelem)|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tár megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

#### <a name="notes"></a>Megjegyzések

**1. Megjegyzés**: a nagy kapacitású egy többrétegű architektúra, külön számítási és tárolási összetevőkkel: a [nagy kapacitású szolgáltatási réteg architektúrája](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**2. Megjegyzés**: a többrétegű architektúra nagy kapacitású több szinten van gyorsítótárazás. A hatékony IOPS a munkaterheléstől függ.

**3. Megjegyzés**: a késés a számítási REPLIKÁK RBPEX SSD-alapú gyorsítótárában lévő adatok esetében 1-2 MS, amely a leggyakrabban használt adatlapokat gyorsítótárazza. A lapozófájlokból beolvasott adatok nagyobb késése.

## <a name="general-purpose---provisioned-compute---gen4"></a>Általános célú kiépített számítás – Gen4

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában.

### <a name="gen4-compute-generation-part-1"></a>Gen4 számítási generációja (1. rész)

|Számítási méret|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|
|Napló maximális mérete (GB)|307|307|461|461|461|922|
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|320|640|960|1280|1600|1920|
|Maximális naplózási arány (MBps)|3.75|7.5|11.25|15|18,75|22.5|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|1|1|1|1|1|1|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Gen4 számítási generációja (2. rész)

|Számítási méret|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|3072|3072|3072|3072|4096|4096|
|Napló maximális mérete (GB)|922|922|922|922|1229|1229|
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)
|Maximális adatmennyiség IOPS *|2240|2560|2880|3200|5120|7680|
|Maximális naplózási arány (MBps)|26,3|30|30|30|30|30|
|Egyidejű feldolgozók maximális száma (kérelem)|1400|1600|1800|2000|3200|4800|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|1|1|1|1|1|1|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Általános célú kiépített számítás – Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generációja (1. rész)

|Számítási méret|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|3072|
|Napló maximális mérete (GB)|307|307|461|461|461|922|922|
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|384|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|640|1280|1920|2560|3200|3840|4480|
|Maximális naplózási arány (MBps)|7.5|15|22.5|30|30|30|30|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|1400|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|1|1|1|1|1|1|1|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generációja (2. rész)

|Számítási méret|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Napló maximális mérete (GB)|922|922|922|1229|1229|1229|1229|
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|5120|5760|6400|7680|10240|12800|25600|
|Maximális naplózási arány (MBps)|30|30|30|30|30|30|30|
|Egyidejű feldolgozók maximális száma (kérelem)|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|1|1|1|1|1|1|1|
|Több – AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Általános célú kiépített számítás – Fsv2 sorozat

### <a name="fsv2-series-compute-generation-preview"></a>Fsv2 sorozatú számítási generáció (előzetes verzió)

|Számítási méret|GP_Fsv2_72|
|:--- | --: |
|Számítási generáció|Fsv2 sorozat|
|Virtuális mag|72|
|Memória (GB)|136,2|
|Oszlopcentrikus-támogatás|Igen|
|Memóriában tárolt OLTP-tároló (GB)|N/A|
|Maximális adatméret (GB)|4096|
|Napló maximális mérete (GB)|1024|
|TempDB maximális adatméret (GB)|333|
|Tárolási típus|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|12,800|
|Maximális naplózási arány (MBps)|30|
|Egyidejű feldolgozók maximális száma (kérelem)|3600|
|Egyidejű bejelentkezések maximális száma|3600|
|Egyidejű munkamenetek maximális száma|30,000|
|Replikák száma|1|
|Több – AZ|N/A|
|Olvasási felskálázás|N/A|
|Mellékelt biztonsági mentési tár|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Üzleti szempontból kritikus – kiépített számítás – Gen4

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában.

### <a name="gen4-compute-generation-part-1"></a>Gen4 számítási generációja (1. rész)

|Számítási méret|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|1|2|3|4|5|6|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|1024|
|Napló maximális mérete (GB)|307|307|307|307|307|307|
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS *|4,000|8,000|12 000|16,000|20,000|24,000|
|Maximális naplózási arány (MBps)|8|16|24|32|40|48|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|
|Egyidejű bejelentkezések maximális száma|200|400|600|800|1000|1200|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Gen4 számítási generációja (2. rész)

|Számítási méret|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|7|8|9.5|11|20|36|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|1024|
|Napló maximális mérete (GB)|307|307|307|307|307|307|
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS |28 000|32,000|36 000|40,000|64,000|76 800|
|Maximális naplózási arány (MBps)|56|64|64|64|64|64|
|Egyidejű feldolgozók maximális száma (kérelem)|1400|1600|1800|2000|3200|4800|
|Egyidejű bejelentkezések maximális száma (kérelmek)|1400|1600|1800|2000|3200|4800|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Üzleti szempontból kritikus – kiépített számítás – Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generációja (1. rész)

|Számítási méret|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|1,57|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|3072|
|Napló maximális mérete (GB)|307|307|461|461|461|922|922|
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|448|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS *|8000|16,000|24,000|32,000|40,000|48 000|56 000|
|Maximális naplózási arány (MBps)|24|48|72|96|96|96|96|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|1400|
|Egyidejű bejelentkezések maximális száma|200|400|600|800|1000|1200|1400|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|4|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generációja (2. rész)

|Számítási méret|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,64|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Napló maximális mérete (GB)|922|922|922|1229|1229|1229|1229|
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS *|64,000|72 000|80,000|96 000|128 000|160 000|204 800|
|Maximális naplózási arány (MBps)|96|96|96|96|96|96|96|
|Egyidejű feldolgozók maximális száma (kérelem)|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű bejelentkezések maximális száma|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|4|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Üzleti szempontból kritikus – kiépített számítás – M sorozat

### <a name="m-series-compute-generation-preview"></a>Az M-sorozat számítási generációja (előzetes verzió)

|Számítási méret|BC_M_128|
|:--- | --: |
|Számítási generáció|M sorozat|
|Virtuális mag|128|
|Memória (GB)|3767,1|
|Oszlopcentrikus-támogatás|Igen|
|Memóriában tárolt OLTP-tároló (GB)|1768|
|Maximális adatméret (GB)|4096|
|Napló maximális mérete (GB)|2048|
|TempDB maximális adatméret (GB)|4096|
|Tárolási típus|Helyi SSD|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS *|160 000|
|Maximális naplózási arány (MBps)|264|
|Egyidejű feldolgozók maximális száma (kérelem)|12,800|
|Egyidejű bejelentkezések maximális száma|12,800|
|Egyidejű munkamenetek maximális száma|30000|
|Replikák száma|4|
|Több – AZ|Igen|
|Olvasási felskálázás|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|

Az i/o-méretek maximális értékének \* 8 KB és 64 KB között kell lennie. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](sql-database-resource-limits-database-server.md#resource-governance).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információ: [a tárterület kezelése Azure SQL Databaseban](sql-database-file-space-management.md).

## <a name="next-steps"></a>Következő lépések

- Egyetlen adatbázis DTU erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a DTU beszerzési modell használatával](sql-database-dtu-resource-limits-single-databases.md)
- A rugalmas készletek virtuális mag erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a virtuális mag beszerzési modell használatával](sql-database-vcore-resource-limits-elastic-pools.md)
- A rugalmas készletek DTU erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a DTU beszerzési modell használatával](sql-database-dtu-resource-limits-elastic-pools.md)
- A felügyelt példányok erőforrás-korlátaival kapcsolatban lásd: [felügyelt példányok erőforrás-korlátai](sql-database-managed-instance-resource-limits.md).
- Az általános Azure-korlátokkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Az adatbázis-kiszolgálók erőforrás-korlátaival kapcsolatos információkért tekintse meg a kiszolgáló és az előfizetési szint korlátaival kapcsolatos információkat a [SQL Database kiszolgálók erőforrás-korlátainak áttekintése](sql-database-resource-limits-database-server.md) című témakörben.
