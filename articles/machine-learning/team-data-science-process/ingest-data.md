---
title: Betöltés az Azure Storage-környezetbe – csoportos adatelemzési folyamat
description: Megtudhatja, hogyan lehet az adatgyűjtést különböző célpont-környezetekben tárolni és feldolgozni.
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
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720537"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Adatok betöltése a tárolási környezetekbe elemzés céljából

A csoportos adatelemzési folyamat megköveteli, hogy az egyes fázisokban az adatmennyiséget a legmegfelelőbb módon kell betölteni vagy betölteni. Az adatcélhelyek közé tartozhatnak az Azure Blob Storage, a SQL Azure SQL Server-adatbázisok, az Azure-beli virtuális gépek, a HDInsight (Hadoop), a szinapszis-elemzések és a Azure Machine Learning. 

A következő cikkek betölteni az adatokat különböző célkörnyezetekben, ahol az adatok tárolása és feldolgozása az ismertetik.

* [Azure-Blob Storage](move-azure-blob.md)
* SQL Server az Azure-beli [virtuális gépen](move-sql-server-virtual-machine.md)
* [Azure SQL Database](move-sql-azure.md)
* A [struktúra tábláiba](move-hive-tables.md)
* [SQL-particionálású táblákhoz](parallel-load-sql-partitioned-tables.md)
* Helyszíni [SQL Server](move-sql-azure-adf.md)

A technikai és üzleti igények, valamint az adatmennyiség kezdeti helye, formátuma és mérete határozza meg a legjobb adatfeldolgozási tervet. Nem ritka, hogy a legjobb terv több lépésből áll. Ezen feladatok sorozatát lehetnek például adatfeltárás, az előfeldolgozásához, a tisztítás, a le-mintavételi és a modell betanítása.  Azure Data Factory egy ajánlott Azure-erőforrás az adatáthelyezés és-átalakítás előkészítéséhez.
