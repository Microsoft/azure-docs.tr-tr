---
title: Azure Cosmos DB WHERE yan tümcesi
description: Azure Cosmos DB için SQL WHERE yan tümcesi hakkında bilgi edinin
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: cd5643d8be06afcd43c5bfe38d6f5e9caa6f906e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326633"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Azure Cosmos DB WHERE yan tümcesi

İsteğe bağlı WHERE yan tümcesi (`WHERE <filter_condition>`), kaynak JSON öğelerinin sorgunun sonuçlara dahil etmek için karşılaması gereken koşulları belirtir. Bir JSON öğesi, sonuç için değerlendirilecek `true` için belirtilen koşulları değerlendirmelidir. Dizin Katmanı WHERE yan tümcesini kullanarak sonucun parçası olabilecek en küçük kaynak öğeleri alt kümesini tespit edebilir.
  
## <a name="syntax"></a>Sözdizimi
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler

- `<filter_condition>`  
  
   Döndürülecek belgelerin karşılanabileceği koşulu belirtir.  
  
- `<scalar_expression>`  
  
   Hesaplanacağı değeri temsil eden ifade. Ayrıntılar için bkz. [skalar ifadeler](sql-query-scalar-expressions.md) .  
  

## <a name="remarks"></a>Açıklamalar
  
  Belgenin döndürülmesi için filtre koşulu true olarak değerlendirilmelidir. Yalnızca true Boole değeri koşulu karşılar, başka bir değer vardır: tanımsız, null, yanlış, sayı, dizi veya nesne koşulu karşılamıyor. 

## <a name="examples"></a>Örnekler

Aşağıdaki sorgu, değeri `AndersenFamily` olan `id` özelliği içeren öğeleri ister. @No__t-0 özelliği olmayan ya da değeri `AndersenFamily` ile eşleşmeyen herhangi bir öğeyi dışlar.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Sonuçlar şunlardır:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE yan tümcesindeki skaler ifadeler

Önceki örnekte basit bir eşitlik sorgusu gösteriliyordu. SQL API ayrıca çeşitli [Skalar ifadeleri](sql-query-scalar-expressions.md)destekler. En yaygın olarak kullanılan ikili ve birli ifadelerdir. Kaynak JSON nesnesinden Özellik başvuruları da geçerli ifadelerdir.

Aşağıdaki desteklenen ikili işleçleri kullanabilirsiniz:  

|**İşleç türü**  | **Değerler** |
|---------|---------|
|Tiğinin | +,-,*,/,% |
|Operatörün    | \|, &, ^, < <, > >, > > > (sıfır-doldur sağ SHIFT) |
|Mantıksal    | VE, VEYA DEĞIL      |
|Karşılaştırma | =,! =, &lt;, &gt;, &lt; =, &gt; =, < > |
|Dize     |  \| @ no__t-1 (Birleştir) |

Aşağıdaki sorgular ikili işleçler kullanır:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Ayrıca, aşağıdaki örneklerde gösterildiği gibi, sorgularda DEĞIL Birli İşleçler +,-, ~, kullanabilirsiniz:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Sorgularda Özellik başvurularını da kullanabilirsiniz. Örneğin `SELECT * FROM Families f WHERE f.isRegistered`, `true` değerine eşit değeri olan `isRegistered` özelliğini içeren JSON öğesini döndürür. @No__t-0, `null`, `Undefined`, `<number>`, `<string>`, `<object>` veya `<array>` gibi başka herhangi bir değer, öğeyi sonuçtan dışlar. 

## <a name="next-steps"></a>Sonraki adımlar

- [Başlarken](sql-query-getting-started.md)
- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [FROM yan tümcesi](sql-query-from.md)