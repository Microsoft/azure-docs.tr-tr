---
title: Azure Cosmos DB içindeki konum SıNıRı yan tümcesi
description: Azure Cosmos DB için fark SıNıRı yan tümcesi hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 7aae56783f83f13b50321c88d69f07d910e589dd
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326885"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Azure Cosmos DB içindeki konum SıNıRı yan tümcesi

Konum SıNıRı yan tümcesi, atlamak için isteğe bağlı bir yan tümce ve sonra sorgudan birkaç değer alır. Konum sayısı ve sınır sayısı, sınır SıNıRı yan tümcesinde gereklidir.

Sınır sınırı ORDER BY yan tümcesiyle birlikte kullanıldığında, sonuç kümesi, sıralı değerler üzerinde atlama ve alma işlemleri gerçekleştirerek oluşturulur. ORDER BY yan tümcesi kullanılmazsa, değerin belirleyici bir sırası oluşur.

## <a name="syntax"></a>Sözdizimi
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler

- `<offset_amount>`

   Sorgu sonuçlarının atlayacağı öğelerin tamsayı sayısını belirtir.

- `<limit_amount>`
  
   Sorgu sonuçlarının içermesi gereken öğelerin tamsayı sayısını belirtir

## <a name="remarks"></a>Açıklamalar
  
  Konum SıNıRı yan tümcesinde hem konum sayısı hem de LIMIT sayısı gereklidir. İsteğe bağlı bir `ORDER BY` yan tümcesi kullanılırsa, sonuç kümesi sıralı değerlerin atlanarak oluşturulur. Aksi halde sorgu sabit bir değerler sırası döndürür. Şu anda bu yan tümce yalnızca tek bir bölüm içindeki sorgularda desteklenir, çapraz bölümleme sorguları henüz desteklememektedir.

## <a name="examples"></a>Örnekler

Örneğin, ilk değeri atlayan ve ikinci değeri döndüren (yerleşik şehrin adının sırasıyla) bir sorgu aşağıda verilmiştir:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Sonuçlar şunlardır:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

İlk değeri atlayan ve ikinci değeri döndüren (sıralama olmadan) bir sorgu aşağıda verilmiştir:

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Sonuçlar şunlardır:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Başlarken](sql-query-getting-started.md)
- [SELECT yan tümcesi](sql-query-select.md)
- [ORDER BY yan tümcesi](sql-query-order-by.md)
