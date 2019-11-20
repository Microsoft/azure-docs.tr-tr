---
title: Azure Cosmos DB SQL sorgularında skaler ifadeler
description: Azure Cosmos DB için skalar ifade SQL sözdizimi hakkında bilgi edinin.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: c35ad65a584f8ee95142e9bc85a58b5b6cd99744
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003540"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Azure Cosmos DB SQL sorgularında skaler ifadeler

[Select yan tümcesi](sql-query-select.md) skaler ifadeleri destekler. Skaler bir ifade, semboller ve işleçleri tek bir değer almak için değerlendirilen bir birleşimidir. Skalar ifade örnekleri şunlardır: sabitler, özellik başvuruları, dizi öğesi başvuruları, diğer ad başvuruları veya işlev çağrıları. Skalar ifadeler, işleçler kullanılarak karmaşık ifadelerle birleştirilebilir.

## <a name="syntax"></a>Sözdizimi
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Bağımsız Değişkenler
  
- `<constant>`  
  
   Sabit bir değeri temsil eder. Bkz: [sabitleri](sql-query-constants.md) ayrıntıları bölümü.  
  
- `input_alias`  
  
   Tarafından tanımlanan bir değeri temsil `input_alias` sunulan `FROM` yan tümcesi.  
  Bu değer olmaması garanti **tanımlanmamış** –**tanımlanmamış** giriş değerleri atlanır.  
  
- `<scalar_expression>.property_name`  
  
   Bir nesnenin özellik değerini temsil eder. Özellik yok veya özelliğe bir nesne olmayan bir değer üzerinde başvuruluyorsa, ifade **tanımsız** değer olarak değerlendirilir.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Bir dizinin diziniyle `property_name` `array_index` birlikte Name veya Array öğesi olan bir özelliğin değerini temsil eder. Özelliği/dizi dizini mevcut değil veya özellik/dizi dizininden başvurulan bir değeri bir nesne/dizisi olmayan ve ardından ifadeyi tanımlanmamış değerini hesaplar.  
  
- `unary_operator <scalar_expression>`  
  
   Tek bir değer için uygulanan bir işleç temsil eder. Bkz: [işleçleri](sql-query-operators.md) ayrıntıları bölümü.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   İki değer için uygulanan bir işleç temsil eder. Bkz: [işleçleri](sql-query-operators.md) ayrıntıları bölümü.  
  
- `<scalar_function_expression>`  
  
   Bir işlev çağrısı sonucunu tarafından tanımlanan bir değeri temsil eder.  
  
- `udf_scalar_function`  
  
   Kullanıcı tanımlı skaler işlevin adı.  
  
- `builtin_scalar_function`  
  
   Yerleşik skaler işlevin adı.  
  
- `<create_object_expression>`  
  
   Belirtilen özelliklere sahip yeni bir nesne oluşturarak elde ettiği değerle ve bunların değerlerini temsil eder.  
  
- `<create_array_expression>`  
  
   Öğeleri olarak belirtilen değerlerle yeni bir dizi oluşturarak elde ettiği değerle temsil eder  
  
- `parameter_name`  
  
   Belirtilen parametre adı değerini temsil eder. Tek bir parametre adları olmalıdır \@ ilk karakteri olarak.  
  
## <a name="remarks"></a>Açıklamalar
  
  Yerleşik veya Kullanıcı tanımlı skaler bir işlev çağrılırken, tüm bağımsız değişkenlerin tanımlanması gerekir. Bağımsız değişken tanımlanmamış, işlev çağrılmaz ve sonuç tanımsız olur.  
  
  Bir nesne oluştururken, tanımlanmamış değeri atanır. herhangi bir özelliği atlandı ve oluşturulan nesneyi dahil değildir.  
  
  Zaman dizisi, herhangi bir öğe değer oluşturma atandığı **tanımlanmamış** değeri atlandı ve oluşturulan nesneyi dahil değildir. Bu şekilde oluşturulan dizi dizinleri atlandı değil, onun yerine almak sonraki tanımlanan öğe neden olur.  

## <a name="examples"></a>Örnekler

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Sonuçlar şunlardır:

```json
    [{
      "$1": 1.33333
    }]
```

Aşağıdaki sorguda, skaler ifadenin sonucu bir Boolean değer:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Sonuçlar şunlardır:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB giriş](introduction.md)
- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Alt](sql-query-subquery.md)