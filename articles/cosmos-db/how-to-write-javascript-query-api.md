---
title: Azure Cosmos DB içindeki JavaScript sorgu API 'sini kullanarak saklı yordamları ve Tetikleyicileri yazma
description: Azure Cosmos DB içinde JavaScript sorgu API 'sini kullanarak saklı yordamları ve Tetikleyicileri yazmayı öğrenin
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/07/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 449af63ef3409b8c8173edef7a5d77074cca02b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335849"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>JavaScript sorgu API 'sini kullanarak Azure Cosmos DB saklı yordamları ve Tetikleyicileri yazma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB, saklı yordamları veya Tetikleyicileri yazmak için kullanılabilecek herhangi bir SQL dili bilgisi olmadan akıcı bir JavaScript arabirimi kullanarak iyileştirilmiş sorgular gerçekleştirmenizi sağlar. Azure Cosmos DB 'de JavaScript sorgu API 'SI desteği hakkında daha fazla bilgi için, bkz. [Azure Cosmos DB makalesinde JavaScript Dil tümleşik sorgu API 'si Ile çalışma](javascript-query-api.md) .

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>JavaScript sorgu API 'SI kullanılarak saklı yordam

Aşağıdaki kod örneği, JavaScript sorgu API 'sinin saklı yordam bağlamında nasıl kullanıldığına ilişkin bir örnektir. Saklı yordam, bir giriş parametresi tarafından belirtilen bir Azure Cosmos öğesi ekler ve yöntemi kullanılarak bir meta veri belgesini `__.filter()` MinSize, MaxSize ve totalSize giriş öğesinin size özelliğine göre güncelleştirir.

> [!NOTE]
> `__` (çift alt çizgi), `getContext().getCollection()` JavaScript sorgu API 'si kullanılırken bir diğer addır.

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

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB içindeki saklı yordamlar, Tetikleyiciler ve Kullanıcı tanımlı işlevler hakkında bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure Cosmos DB içindeki saklı yordamlar, Tetikleyiciler, Kullanıcı tanımlı işlevlerle çalışma](how-to-use-stored-procedures-triggers-udfs.md)

* [Azure Cosmos DB depolanan yordamları kaydetme ve kullanma](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Azure Cosmos DB ' de [ön Tetikleyiciler](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) ve [tetiklemeleri](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) kaydetme ve kullanma

* [Kullanıcı tanımlı işlevleri Azure Cosmos DB kaydetme ve kullanma](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Azure Cosmos DB'de yapay bölümleme anahtarları](synthetic-partition-keys.md)
