---
title: A bejelentési tábla tárolási stratégia használata az Azure Database for PostgreSQL - kiszolgáló egyetlen segítségével optimalizálhatja a lekérdezés idejét
description: Ez a cikk ismerteti, hogyan optimalizálható a lekérdezés időtartama a bejelentési table storage stratégiát az Azure-adatbázis PostgreSQL - hez-kiszolgáló egyetlen.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65066975"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>A bejelentési table storage stratégiát lekérdezési idő optimalizálásához 
Ez a cikk ismerteti, hogyan optimalizálható a nagyméretű attribútum tárolási módszer (bejelentési) tábla tárolási stratégia a gyorsaság.

## <a name="toast-table-storage-strategies"></a>BEJELENTÉSI table storage stratégiák
Négy különböző stratégiákat, amellyel bejelentési lemezen oszlopok tárolására szolgálnak. A tömörítés és a sor végét ki storage között a különböző kombinációkkal jelölnek. A stratégia megadható adattípusú szinten és az oszlopok szintjén.
- **Egyszerű** megakadályozza, hogy a tömörítés vagy a sor végét ki tárolási. Letiltja a egybájtos fejlécek varlena típusok használatát. Egyszerű az egyetlen lehetséges stratégia, amelyek nem használhatják a bejelentési adattípusú oszlopokhoz.
- **Kiterjesztett** lehetővé teszi, hogy a tömörítés és a sor végét ki tárolási. Kiterjesztett az alapértelmezett érték a legtöbb adattípusokat is használja a bejelentési. A tömörítés első kísérlet történik. Beágyazott-out tárolási kísérlet történik, ha a sor továbbra is túl nagy.
- **Külső** lehetővé teszi, hogy a sor végét ki tárolási, de nem tömörítést. Külső gyorsabb széles szöveg és bytea oszlopok karakterláncrészletet műveleteket teszi. A gyorsaság a büntetés nagyobb tárterületet tartalmaz. Ezek a műveletek beolvasni a sor végét ki érték csak a szükséges részeit, amikor a rendszer nem tömöríti vannak optimalizálva.
- **Fő** lehetővé teszi, hogy a tömörítés, de nem out sor tárolására. Beágyazott-out storage továbbra is történik ilyen oszlopokhoz, de csak végső megoldásként. Ha nincs más módon nem lehet, hogy a sor elég kicsi a lapon elfér következik be.

## <a name="use-toast-table-storage-strategies"></a>BEJELENTÉSI table storage stratégiák használata
Ha a lekérdezések bejelentési használható adattípusokat, fontolja meg a fő stratégia helyett az alapértelmezett beállítás a bővített lekérdezési idő csökkentése érdekében. Fő nem zárja ki a sor végét ki tárolási. Ha a lekérdezések nem éri a bejelentési használható adattípusokat, akkor előnyös lehet, hogy a kiterjesztett beállítás. A fő tábla sorait egy nagyobb része a megosztott pufferből gyorsítótárát, amely segít a teljesítmény elfér.

Ha egy munkaterhelés, a széles táblák és a magas karakterszám sémát használó, fontolja meg PostgreSQL bejelentési táblákat. Egy példa ügyfél tábla kellett nagyobb, mint 350 oszlopait és számos, amely felölelt 255 karakter lehet. A bejelentési-tábla fő stratégia lett konvertálva, miután a teljesítményteszt lekérdezéskor 4203 másodpercekből 467 másodperc csökken. Ez egy 89 százalékos teljesítményjavulást.

## <a name="next-steps"></a>További lépések
Tekintse át a számítási feladatok esetében az előző jellemzők. 

Tekintse át a következő PostgreSQL dokumentációja ismerteti: 
- [Fejezet 68-as, fizikai adatbázistár](https://www.postgresql.org/docs/current/storage-toast.html) 