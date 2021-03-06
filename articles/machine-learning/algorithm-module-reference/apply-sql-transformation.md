---
title: SQL-átalakítás alkalmazása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használható az SQL-transzformációs modul alkalmazása Azure Machine Learning egy SQLite-lekérdezés futtatásához a bemeneti adatkészleteken az adatok átalakításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 9a195497b4376633bd3c767d7d0ea029109fdf9d
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314538"
---
# <a name="apply-sql-transformation"></a>SQL-átalakítás alkalmazása

Ez a cikk a Azure Machine Learning Designer modulját ismerteti.

Az SQL-transzformációs modul alkalmazásával a következőket végezheti el:
  
-   Hozzon létre táblázatokat az eredményekhez, és mentse az adatkészleteket egy hordozható adatbázisban.  
  
-   Egyéni átalakításokat végezhet az adattípusokon, vagy létrehozhat összesítéseket.  
  
-   SQL-lekérdezési utasítások végrehajtása az adatszűréshez és az adatmódosításhoz, valamint a lekérdezés eredményének adattáblaként való visszaadása.  

> [!IMPORTANT]
> Az ebben a modulban használt SQL-motor **SQLite**. Az SQLite szintaxissal kapcsolatos további információkért lásd: az [SQL mint az SQLite által értelmezett](https://www.sqlite.org/index.html) további információ.  

## <a name="how-to-configure-apply-sql-transformation"></a>Az SQL-transzformáció alkalmazásának konfigurálása  

A modul bemenetként akár három adatkészletet is igénybe vehet. Ha az egyes bemeneti portokhoz kapcsolódó adatkészletekre hivatkozik, akkor a `t1`, a `t2`és a `t3`nevet kell használnia. A tábla száma a bemeneti port indexét jelzi.  
  
A fennmaradó paraméter egy SQL-lekérdezés, amely az SQLite szintaxist használja. Ha több sort ír be az **SQL-parancsfájl** szövegmezőbe, használjon pontosvesszőt az egyes utasítások megszakításához. Ellenkező esetben a sortörések szóközökre lesznek konvertálva.  

Ez a modul az SQLite szintaxis összes szabványos utasítását támogatja. A nem támogatott utasítások listáját a [technikai megjegyzések](#technical-notes) című szakaszban találja.

##  <a name="technical-notes"></a>Technikai megjegyzések  

Ez a szakasz megvalósítási részleteket, tippeket és válaszokat tartalmaz a gyakori kérdésekre.

-   Az 1. porton mindig szükség van egy bemenetre.  
  
-   Szóközt vagy más speciális karaktereket tartalmazó oszlopok azonosítói esetén az oszlop azonosítóját szögletes zárójelben vagy idézőjelek között kell megadni, amikor a `SELECT` vagy `WHERE` záradékban lévő oszlopra hivatkozik.  
  
### <a name="unsupported-statements"></a>Nem támogatott utasítások  

Bár az SQLite az ANSI SQL standard nagy részét is támogatja, nem tartalmazza számos, a kereskedelmi kapcsolatban álló adatbázis-rendszerek által támogatott funkciót. További információ: az [SQL mint az SQLite által értelmezett](http://www.sqlite.org/lang.html). Emellett vegye figyelembe a következő korlátozásokat az SQL-utasítások létrehozásakor:  
  
- A SQLite az értékek dinamikus beírását használja ahelyett, hogy egy típust egy oszlophoz társítson, mint a legtöbb kapcsolódó adatbázis-rendszerben. Ez gyenge módon van beírva, és lehetővé teszi az implicit típus-átalakítást.  
  
- `LEFT OUTER JOIN` implementálva van, de nem `RIGHT OUTER JOIN` vagy `FULL OUTER JOIN`.  

- A `ALTER TABLE` paranccsal `RENAME TABLE` és `ADD COLUMN` utasításokat is használhat, de más záradékok nem támogatottak, például `DROP COLUMN`, `ALTER COLUMN`és `ADD CONSTRAINT`.  
  
- Létrehozhat egy nézetet SQLite-n belül, de ezt követően a nézetek csak olvashatók. Nézeteken nem hajtható végre `DELETE`, `INSERT`vagy `UPDATE` utasítás. Létrehozhat azonban egy olyan eseményindítót, amely egy nézet `DELETE`, `INSERT`vagy `UPDATE`t próbál végrehajtani, és más műveleteket hajt végre az eseményindító törzsében.  
  

A hivatalos SQLite webhelyen elérhető nem támogatott függvények listája mellett a következő wiki a többi nem támogatott funkció listáját tartalmazza: [SQLite –](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql) nem támogatott SQL  
    
## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
