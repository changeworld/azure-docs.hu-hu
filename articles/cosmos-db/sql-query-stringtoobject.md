---
title: StringToObject Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function StringToObject Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296381"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Egy objektumra fordított kifejezést ad vissza. Ha a kifejezés nem fordítható le, a nem definiált értéket adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés, amelyet JSON-objektum kifejezésként kell elemezni. Vegye figyelembe, hogy a beágyazott karakterlánc-értékeket idézőjelek közé kell írni, hogy érvényesek legyenek. A JSON formátumával kapcsolatos részletekért lásd: [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy objektum kifejezését adja vissza, vagy nincs meghatározva.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan viselkedik a `StringToObject` különböző típusokban. 
  
 Az alábbi példák érvényes bemenettel rendelkeznek.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Íme az eredményhalmaz.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 A következő példák érvénytelen bemenettel rendelkeznek.
Annak ellenére, hogy egy lekérdezésen belül érvényesek, nem lesznek értelmezve érvényes objektumokra. Az objektumon belüli karakterláncokat "{\\" a\\":\\" Str\\"}" vagy a környező idézőjelnek egyetlen "{" a ":" Str "}" karakterrel kell rendelkeznie.

A környező tulajdonságok neveinek egyetlen idézőjele nem érvényes JSON.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Íme az eredményhalmaz.

```json
[{}]
```  

A környező idézőjelek nélküli tulajdonságnév nem érvényes JSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Íme az eredményhalmaz.

```json
[{}]
``` 

A következő példák érvénytelen bemenettel rendelkeznek.

 Az átadott kifejezés JSON-objektumként lesz értelmezve; Ezek a bemenetek nem értékelik ki az objektum típusát, így a nem definiált értéket adja vissza.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Íme az eredményhalmaz.

```json
[{}]
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
