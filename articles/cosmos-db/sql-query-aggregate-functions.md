---
title: Azure Cosmos DB içindeki toplama işlevleri
description: SQL toplama işlevi sözdizimi, Azure Cosmos DB tarafından desteklenen toplama işlevlerinin türleri hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555565"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB içindeki toplama işlevleri
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Toplama işlevleri, yan tümcesindeki bir değer kümesi üzerinde bir hesaplama gerçekleştirir `SELECT` ve tek bir değer döndürür. Örneğin, aşağıdaki sorgu bir kapsayıcı içindeki öğelerin sayısını döndürür:

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>Toplama işlevlerinin türleri

SQL API aşağıdaki toplama işlevlerini destekler. `SUM` ve sayı `AVG` `COUNT` `MIN` `MAX` , dize, Boole değerleri ve null değerler üzerinde çalışır.

| İşlev | Açıklama |
|-------|-------------|
| [CIN](sql-query-aggregate-avg.md) | İfadedeki değerlerin ortalamasını döndürür. |
| [COUNT](sql-query-aggregate-count.md) | İfadedeki öğelerin sayısını döndürür. |
| [MAX](sql-query-aggregate-max.md) | İfadedeki en büyük değeri döndürür. |
| [MIN](sql-query-aggregate-min.md) | İfadedeki en küçük değeri döndürür. |
| [SUM](sql-query-aggregate-sum.md) | İfadedeki tüm değerlerin toplamını döndürür. |


Ayrıca, VALUE anahtar sözcüğünü kullanarak toplamanın yalnızca skaler değerini de döndürebilirsiniz. Örneğin, aşağıdaki sorgu değer sayısını tek bir sayı olarak döndürür:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Sonuçlar:

```json
    [ 2 ]
```

Toplamaları filtreler ile de birleştirebilirsiniz. Örneğin, aşağıdaki sorgu, adres durumu olan öğe sayısını döndürür `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Sonuçlar:

```json
    [ 1 ]
```

## <a name="remarks"></a>Açıklamalar

Bu toplama sistemi işlevleri, bir [Aralık dizininden](index-policy.md#includeexclude-strategy)faydalanır. Bir özelliğinde,,, veya bir özelliği yapmak istiyorsanız `AVG` `COUNT` `MAX` `MIN` `SUM` , [ilgili yolu dizin oluşturma ilkesine dahil](index-policy.md#includeexclude-strategy)etmelisiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB'ye giriş](introduction.md)
- [Sistem işlevleri](sql-query-system-functions.md)
- [Kullanıcı tanımlı işlevler](sql-query-udfs.md)