---
title: Azure Cosmos DB sorgu dilinde CONCAT
description: Azure Cosmos DB 'de SQL sistem işlevi CONCAT hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e61d61a3d64ca7d7808619159e4dfc8e8b33d68
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351269"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 İki veya daha fazla dize değerlerini birleştirirken sonucu olan bir dize döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*str_expr*  
   , Diğer değerleri birleştirmek için bir dize ifadesidir. @No__t-0 işlevi en az iki *str_expr* bağımsız değişkeni gerektirir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir dize ifadesi döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, belirtilen değerler birleştirilmiş dizeyi döndürür.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"concat": "abcdef"}]  
```  
  

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
