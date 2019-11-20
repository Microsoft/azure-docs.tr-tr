---
title: Azure Cosmos DB sorgu dilinde S_SAYI_ÜRET
description: Azure Cosmos DB 'de SQL sistem işlevi S_SAYI_ÜRET hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4a0672000e630c9e06df84d9c2da5cb8b988c05a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349608"
---
# <a name="rand-azure-cosmos-db"></a>S_SAYI_ÜRET (Azure Cosmos DB)
 [0, 1) öğesinden rastgele oluşturulan sayısal değeri döndürür.
 
## <a name="syntax"></a>Sözdizimi
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Dönüş türleri

  Sayısal bir ifade döndürür.

## <a name="remarks"></a>Açıklamalar

  `RAND` belirleyici olmayan bir işlevdir. @No__t yinelenen çağrıları-0 aynı sonuçları döndürmez.

## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek rastgele oluşturulmuş bir sayısal değer döndürür.
  
```sql
SELECT RAND() AS rand 
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
