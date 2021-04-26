---
title: Azure Cosmos DB yan tümce SEÇIN
description: Azure Cosmos DB için SQL SELECT yan tümcesi hakkında bilgi edinin. SQL 'i Azure Cosmos DB JSON sorgu dili olarak kullanın.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 072e17b1c0ea312b4adfa1687e447fd2cadde233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335458"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Azure Cosmos DB yan tümce SEÇIN
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Her sorgu `SELECT` , ANSI SQL standartlarına göre her bir yan tümce ve from ve [WHERE](sql-query-where.md) yan [tümcelerinden](sql-query-from.md) oluşur. Genellikle, `FROM` yan tümcesindeki kaynak numaralandırılır ve `WHERE` yan TÜMCE, JSON öğelerinin bir alt kümesini almak için kaynak üzerinde bir filtre uygular. `SELECT`Yan tümcesi daha sonra seçim listesinde Istenen JSON değerlerini projeler.

## <a name="syntax"></a>Söz dizimi

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
- `<select_specification>`  

  Sonuç kümesi için seçilecek özellikler veya değer.  
  
- `'*'`  

  Değerin herhangi bir değişiklik yapılmadan alınması gerektiğini belirtir. Özellikle işlenen değer bir nesnese, tüm özellikler alınır.  
  
- `<object_property_list>`  
  
  Alınacak özelliklerin listesini belirtir. Döndürülen her değer, belirtilen özelliklerle bir nesne olacaktır.  
  
- `VALUE`  

  Tüm JSON nesnesi yerine JSON değerinin alınması gerektiğini belirtir. Bu, aksine, `<property_list>` bir nesnedeki öngörülen değeri sarmaz.  

- `DISTINCT`
  
  Yansıtılan özelliklerin tekrarların kaldırılması gerektiğini belirtir.  

- `<scalar_expression>`  

  Hesaplanacağı değeri temsil eden ifade. Ayrıntılar için bkz. [skaler ifadeler](sql-query-scalar-expressions.md) bölümü.  

## <a name="remarks"></a>Açıklamalar

`SELECT *`Sözdizimi yalnızca from yan tümcesi tam olarak bir diğer ad bildirmişse geçerlidir. `SELECT *` yansıtma gerekmiyorsa yararlı olabilecek bir kimlik projeksiyonu sağlar. SELECT * yalnızca FROM yan tümcesi belirtilmişse ve yalnızca tek bir giriş kaynağı tanıdığında geçerlidir.  
  
Hem hem de `SELECT <select_list>` `SELECT *` "sözdizimsel cukr" ve aşağıda gösterildiği gıbı basit select deyimleri kullanılarak ifade edilebilir.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   eşittir:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   eşittir:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Örnekler

Aşağıdaki SELECT sorgusu örneği, `address` eşleşmelerin döndürdüğü `Families` örnekleri `id` `AndersenFamily` :

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Sonuçlar:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Başlarken](sql-query-getting-started.md)
- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE yan tümcesi](sql-query-where.md)
