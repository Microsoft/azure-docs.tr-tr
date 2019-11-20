---
title: Azure Cosmos DB ORDER BY yan tümcesi
description: Azure Cosmos DB için SQL ORDER BY yan tümcesi hakkında bilgi edinin. SQL 'i Azure Cosmos DB JSON sorgu dili olarak kullanın.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 14f61d14b59dca4bcf2e0f4b93e918f101a61833
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326846"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB ORDER BY yan tümcesi

İsteğe bağlı ORDER BY yan tümcesi sorgu tarafından döndürülen sonuçların sıralama düzenini belirtir.

## <a name="syntax"></a>Sözdizimi
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Bağımsız Değişkenler
  
- `<sort_specification>`  
  
   Sorgu sonuç kümesinin sıralaması yapılacak bir özellik veya ifade belirtir. Bir sıralama sütunu, ad veya özellik diğer adı olarak belirtilebilir.  
  
   Birden çok özellik belirtilebilir. Özellik adları benzersiz olmalıdır. ORDER BY yan tümcesindeki sıralama özelliklerinin sırası, sıralanmış sonuç kümesinin organizasyonunu tanımlar. Diğer bir deyişle, sonuç kümesi ilk özelliğe göre sıralanır ve ardından sıralı liste ikinci özelliğe göre sıralanır ve bu şekilde devam eder.  
  
   ORDER BY yan tümcesinde başvurulan özellik adları, select listesindeki bir özelliğe ya da herhangi bir belirsizlikleri olmadan FROM yan tümcesinde belirtilen koleksiyonda tanımlanan bir özelliğe karşılık gelmelidir.  
  
- `<sort_expression>`  
  
   Sorgu sonuç kümesinin sıralaması yapılacak bir veya daha fazla özelliği veya ifadeyi belirtir.  
  
- `<scalar_expression>`  
  
   Ayrıntılar için [skaler ifadeler](sql-query-scalar-expressions.md) bölümüne bakın.  
  
- `ASC | DESC`  
  
   Belirtilen sütundaki değerlerin artan veya azalan sırada sıralanması gerektiğini belirtir. ASC değeri en düşük değerden en yüksek değere göre sıralanır. DESC değeri en yüksek değerden en düşük değere göre yapılır. ASC varsayılan sıralama düzeni. Null değerler mümkün olan en düşük değer olarak değerlendirilir.  
  
## <a name="remarks"></a>Açıklamalar  
  
   ORDER BY yan tümcesi, dizin oluşturma ilkesinin sıralanmakta olan alanlar için bir dizin içermesini gerektirir. Azure Cosmos DB sorgusu çalışma zamanı, hesaplanan özelliklere karşı değil, özellik adına göre sıralamayı destekler. Azure Cosmos DB birden çok SıRALAMAYı destekler. Bir sorguyu birden çok sıra özellikleriyle çalıştırmak için, sıralanan alanlarda bir [bileşik dizin](index-policy.md#composite-indexes) tanımlamanız gerekir.

## <a name="examples"></a>Örnekler

Örneğin, aileleri, yerleşik şehir adının artan sırada alan bir sorgu aşağıda verilmiştir:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Sonuçlar şunlardır:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Aşağıdaki sorgu, öğe oluşturma tarihleri sırasıyla aile `id`s alır. Öğe `creationDate`, *Dönem süresini*temsil eden bir sayı veya 1 Ocak 1970 ' den beri saniye cinsinden geçen süreyi temsil eder.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Sonuçlar şunlardır:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Ayrıca, birden çok özelliğe göre sıralama yapabilirsiniz. Birden çok özelliğe göre sipariş eden bir sorgu [bileşik dizin](index-policy.md#composite-indexes)gerektirir. Aşağıdaki sorguyu göz önünde bulundurun:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Bu sorgu, şehir adının artan sırada aile `id` ' i alır. Birden çok öğe aynı şehir adına sahip ise, sorgu `creationDate` azalan sırada sıraya alınır.

## <a name="next-steps"></a>Sonraki adımlar

- [Başlarken](sql-query-getting-started.md)
- [SELECT yan tümcesi](sql-query-select.md)
- [Konum SıNıRı yan tümcesi](sql-query-offset-limit.md)
