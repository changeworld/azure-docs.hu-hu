---
title: Tárolt eljárások és triggerek írása a JavaScript lekérdezési API használatával Azure Cosmos DB
description: Megtudhatja, hogyan írhat tárolt eljárásokat és triggereket a JavaScript lekérdezési API használatával Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 221a3118808a044ef1b1b822b9c95772bf792f34
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441713"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Tárolt eljárások és triggerek írása a Azure Cosmos DBban a JavaScript lekérdezési API használatával

A Azure Cosmos DB lehetővé teszi, hogy az optimalizált lekérdezéseket egy Fluent JavaScript-felület használatával hajtsa végre, az SQL nyelv ismerete nélkül, amely a tárolt eljárások vagy triggerek írására használható. Ha többet szeretne megtudni a Azure Cosmos DB JavaScript-lekérdezési API-támogatásáról, tekintse meg a következő témakört: [a JavaScript nyelv integrált lekérdezési API használata Azure Cosmos db](javascript-query-api.md) cikkben.

## <a id="stored-procedures"></a>Tárolt eljárás a JavaScript lekérdezési API használatával

A következő mintakód azt szemlélteti, hogyan használható a JavaScript lekérdezési API egy tárolt eljárás kontextusában. A tárolt eljárás beszúr egy bemeneti paraméter által megadott Azure Cosmos-elemeket, és frissíti a metaadat-dokumentumot a `__.filter()` metódus használatával, a minSize, a maxSize és a totalSize a bemeneti elem méret tulajdonsága alapján.

> [!NOTE]
> a `__` (dupla aláhúzás) a JavaScript lekérdezési API használatakor `getContext().getCollection()` alias.

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>Következő lépések

A következő cikkekből megtudhatja, hogyan használhatók a tárolt eljárások, eseményindítók és a felhasználó által definiált függvények a Azure Cosmos DBban:

* [Tárolt eljárások, eseményindítók, felhasználó által definiált függvények használata Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Tárolt eljárások regisztrálása és használata Azure Cosmos DBban](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Az [Eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) és az [Eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) regisztrálása és használata a Azure Cosmos DBban

* [Felhasználó által definiált függvények regisztrálása és használata Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Szintetikus partíciókulcsok az Azure Cosmos DB-ben](synthetic-partition-keys.md)
