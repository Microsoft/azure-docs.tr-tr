---
title: Azure Cosmos DB sorgu dilinde StringToObject
description: Azure Cosmos DB SQL sistem işlevi StringToObject hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 369c86c8fc2ef4d125c3f13db4eb3af88b7e726e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337855"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Nesneye çevrilmiş bir ifade döndürür. İfade çevrilemez, tanımsız döndürür.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*str_expr*  
   , JSON nesnesi ifadesi olarak ayrıştırılacak bir dize ifadesidir. İç içe geçmiş dize değerlerinin geçerli olması için çift tırnaklarla yazılması gerektiğini unutmayın. JSON biçimi hakkında daha fazla bilgi için bkz. [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir nesne ifadesi veya tanımsız döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `StringToObject` farklı türlerde nasıl davrandığını gösterir. 
  
 Aşağıda, geçerli girişi olan örnekler verilmiştir.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Sonuç kümesini burada bulabilirsiniz.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Aşağıda, geçersiz girişe sahip örnekler verilmiştir.
Bir sorgu içinde geçerli olsalar bile, geçerli nesnelere ayrıştırılamaz. Nesne dizesindeki dizeler "{ \\ " a \\ ": \\ " Str \\ "}" ya da çevreleyen teklifin tek bir ' {"a": "Str"} ' olması gerekir.

Özellik adları çevreleyen tek tırnak geçerli bir JSON değil.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Sonuç kümesini burada bulabilirsiniz.

```json
[{}]
```  

Çevreleyen tırnak işaretleri olmadan Özellik adları geçerli bir JSON değil.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Sonuç kümesini burada bulabilirsiniz.

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
 
 Sonuç kümesini burada bulabilirsiniz.

```json
[{}]
```

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi dizinden yararlanmayacak.

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
