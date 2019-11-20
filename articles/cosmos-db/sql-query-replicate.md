---
title: Azure Cosmos DB sorgu dilinde Çoğalt
description: Azure Cosmos DB 'de SQL sistem işlevi çoğaltma hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5e0b7f29c503daa8a95dcc46238e60728c0cec50
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349561"
---
# <a name="replicate-azure-cosmos-db"></a>Çoğalt (Azure Cosmos DB)
 Bir dize değeri, belirtilen sayıda yineler.
  
## <a name="syntax"></a>Sözdizimi
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*str_expr*  
   Bir dize ifadesidir.
  
*num_expr*  
   Sayısal bir ifadedir. *Num_expr* negatifse veya sonlu değilse, sonuç tanımsızdır.
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir dize ifadesi döndürür.
  
## <a name="remarks"></a>Açıklamalar
  Sonucun uzunluk üst sınırı 10.000 karakterdir (length (*str_expr*) * *num_expr*) < = 10.000.

## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, bir sorguda `REPLICATE` ' ın nasıl kullanılacağını gösterir.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Sonuç kümesini burada verilmiştir.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
