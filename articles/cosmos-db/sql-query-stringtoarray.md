---
title: Azure Cosmos DB sorgu dilinde StringToArray
description: Azure Cosmos DB 'de SQL sistem işlevi StringToArray hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7ae1f69e92e890daae528eb1f4dfb95f76560043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337991"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Bir diziye çevrilmiş bir ifade döndürür. İfade çevrilemez, tanımsız döndürür.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*str_expr*  
   , JSON dizi ifadesi olarak ayrıştırılacak bir dize ifadesidir. 
  
## <a name="return-types"></a>Dönüş türleri
  
  Dizi ifadesi veya tanımsız döndürür. 
  
## <a name="remarks"></a>Açıklamalar
  İç içe geçmiş dize değerleri, geçerli JSON olması için çift tırnaklarla yazılmalıdır. JSON biçimi hakkında daha fazla bilgi için bkz. [JSON.org](https://json.org/). Bu sistem işlevi dizinden yararlanmayacak.
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `StringToArray` farklı türlerde nasıl davrandığını gösterir. 
  
 Aşağıda, geçerli girişi olan örnekler verilmiştir.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Sonuç kümesini burada bulabilirsiniz.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Aşağıda, geçersiz giriş örneği verilmiştir. 
   
 Dizideki tek tırnak işaretleri geçerli bir JSON değil.
Bir sorgu içinde geçerli olsalar bile, geçerli dizilere ayrıştırmazlar. Dizi dizesi içindeki dizeler "[ \\ " \\ "]" ya da çevreleyen teklifin tek bir ' [""] ' olması gerekir.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Sonuç kümesini burada bulabilirsiniz.

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

Sonuç kümesini burada bulabilirsiniz.

```json
[{}]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
