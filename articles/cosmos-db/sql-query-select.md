---
title: Azure Cosmos DB yan tümce SEÇIN
description: Azure Cosmos DB için SQL SELECT yan tümcesi hakkında bilgi edinin. SQL 'i Azure Cosmos DB JSON sorgu dili olarak kullanın.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: b90fc6f1f50ec2ea75619188cca36f78061f28df
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326799"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Azure Cosmos DB yan tümce SEÇIN

Her sorgu, ANSI SQL standartlarına göre, SELECT yan tümcesi ve from ve [WHERE](sql-query-where.md) yan [tümcelerinden](sql-query-from.md) oluşur. Genellikle, FROM yan tümcesindeki kaynak numaralandırılır ve WHERE yan tümcesi JSON öğelerinin bir alt kümesini almak için kaynak üzerinde bir filtre uygular. SELECT yan tümcesi daha sonra seçim listesinde istenen JSON değerlerini projeler.

## <a name="syntax"></a>Sözdizimi

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
- `<select_specification>`  

  Sonuç kümesi için seçilecek özellikler veya değer.  
  
- `'*'`  

  Değerin herhangi bir değişiklik yapılmadan alınması gerektiğini belirtir. Özellikle işlenen değer bir nesnese, tüm özellikler alınır.  
  
- `<object_property_list>`  
  
  Alınacak özelliklerin listesini belirtir. Döndürülen her değer, belirtilen özelliklerle bir nesne olacaktır.  
  
- `VALUE`  

  Tüm JSON nesnesi yerine JSON değerinin alınması gerektiğini belirtir. Bu, `<property_list>` ' dan farklı olarak, bir nesnedeki öngörülen değeri sarmaz.  
 
- `DISTINCT`
  
  Yansıtılan özelliklerin tekrarların kaldırılması gerektiğini belirtir.  

- `<scalar_expression>`  

  Hesaplanacağı değeri temsil eden ifade. Ayrıntılar için bkz. [skaler ifadeler](sql-query-scalar-expressions.md) bölümü.  

## <a name="remarks"></a>Açıklamalar

@No__t-0 sözdizimi yalnızca FROM yan tümcesi tam olarak bir diğer ad bildirmişse geçerlidir. `SELECT *`, projeksiyon gerekmiyorsa yararlı olabilecek bir kimlik projeksiyonu sağlar. SELECT * yalnızca FROM yan tümcesi belirtilmişse ve yalnızca tek bir giriş kaynağı tanıdığında geçerlidir.  
  
Hem `SELECT <select_list>` hem de `SELECT *` "sözdizimsel cukr" olur ve alternatif olarak aşağıda gösterildiği gibi basit SELECT deyimleri kullanılarak ifade edilebilir.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   eşittir:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   eşittir:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Örnekler

Aşağıdaki SELECT sorgusu örneği, `id` `AndersenFamily` ile eşleşen `Families` ' den `address` döndürür:

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

### <a name="quoted-property-accessor"></a>Alıntılanmış özellik erişimcisi
[] Alıntı özelliği işlecini kullanarak özelliklere erişebilirsiniz. Örneğin, `SELECT c.grade` ve `SELECT c["grade"]` eşdeğerdir. Bu sözdizimi, boşluk, özel karakter veya bir SQL anahtar sözcüğüyle veya ayrılmış sözcükle aynı ada sahip bir özelliğin kaçış için yararlıdır.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>İç içe Özellikler

Aşağıdaki örnek, `f.address.state` ve `f.address.city` iç içe iki özelliği projeler.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Sonuçlar şunlardır:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>JSON ifadeleri

Projeksiyon, aşağıdaki örnekte gösterildiği gibi JSON ifadelerini de destekler:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Sonuçlar şunlardır:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

Yukarıdaki örnekte, SELECT yan tümcesinin bir JSON nesnesi oluşturması gerekir ve örnek hiçbir anahtar sağladığından, yan tümce örtük bağımsız değişken adını `$1` ' ı kullanır. Aşağıdaki sorgu iki örtük bağımsız değişken değişkeni döndürüyor: `$1` ve `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Sonuçlar şunlardır:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Başlarken](sql-query-getting-started.md)
- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE yan tümcesi](sql-query-where.md)