---
title: Azure Cosmos DB sorgu dilinde StringToArray
description: Azure Cosmos DB 'de SQL sistem işlevi StringToArray hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2d1f90da50950ac6ff4f87ffe96ebad9f3d811cc
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349285"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Bir diziye çevrilmiş bir ifade döndürür. İfade çevrilemez, tanımsız döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*str_expr*  
   , JSON dizi ifadesi olarak ayrıştırılacak bir dize ifadesidir. 
  
## <a name="return-types"></a>Dönüş türleri
  
  Dizi ifadesi veya tanımsız döndürür. 
  
## <a name="remarks"></a>Açıklamalar
  İç içe geçmiş dize değerleri, geçerli JSON olması için çift tırnaklarla yazılmalıdır. JSON biçimi hakkında daha fazla bilgi için bkz. [JSON.org](https://json.org/)
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `StringToArray` ' ın farklı türler genelinde nasıl davranacağını gösterir. 
  
 Aşağıda, geçerli girişi olan örnekler verilmiştir.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Sonuç kümesini burada verilmiştir.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Aşağıda, geçersiz giriş örneği verilmiştir. 
   
 Dizideki tek tırnak işaretleri geçerli bir JSON değil.
Bir sorgu içinde geçerli olsalar bile, geçerli dizilere ayrıştırmazlar. Dizi dizesindeki dizeler "[\\" \\ "]" ya da çevreleyen teklifin tek bir ' [""] ' olması gerekir.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Sonuç kümesini burada verilmiştir.

```json
[{}]
```

Aşağıda, geçersiz giriş örnekleri verilmiştir.
   
 Geçirilen ifade bir JSON dizisi olarak ayrıştırılacak; aşağıdaki dizi tür dizisini değerlendirmez ve tanımsız döndürür.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Sonuç kümesini burada verilmiştir.

```json
[{}]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
