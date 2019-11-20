---
title: Azure Cosmos DB sorgu dilinde StringToObject
description: Azure Cosmos DB SQL sistem işlevi StringToObject hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f09c27458a630386664f3f6579cfeee0721d8be9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349205"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Nesneye çevrilmiş bir ifade döndürür. İfade çevrilemez, tanımsız döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*str_expr*  
   , JSON nesnesi ifadesi olarak ayrıştırılacak bir dize ifadesidir. İç içe geçmiş dize değerlerinin geçerli olması için çift tırnaklarla yazılması gerektiğini unutmayın. JSON biçimi hakkında daha fazla bilgi için bkz. [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir nesne ifadesi veya tanımsız döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `StringToObject` ' ın farklı türler genelinde nasıl davranacağını gösterir. 
  
 Aşağıda, geçerli girişi olan örnekler verilmiştir.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Sonuç kümesini burada verilmiştir.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Aşağıda, geçersiz girişe sahip örnekler verilmiştir.
Bir sorgu içinde geçerli olsalar bile, geçerli nesnelere ayrıştırılamaz. Nesne dizesindeki dizeler "{\\" a @ no__t-1 ": \\" Str @ no__t-3 "}" ya da çevreleyen teklifin tek bir ' {"a": "Str"} ' olması gerekir.

Özellik adları çevreleyen tek tırnak geçerli bir JSON değil.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Sonuç kümesini burada verilmiştir.

```json
[{}]
```  

Çevreleyen tırnak işaretleri olmadan Özellik adları geçerli bir JSON değil.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Sonuç kümesini burada verilmiştir.

```json
[{}]
``` 

Aşağıda, geçersiz girişe sahip örnekler verilmiştir.

 Geçirilen ifade bir JSON nesnesi olarak ayrıştırılacak; Bu girişler nesne türüne göre değerlendirilmez ve tanımsız döndürülür.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Sonuç kümesini burada verilmiştir.

```json
[{}]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
