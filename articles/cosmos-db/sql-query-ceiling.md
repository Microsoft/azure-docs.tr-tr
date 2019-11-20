---
title: Azure Cosmos DB sorgu dilinde tavan
description: Azure Cosmos DB SQL sistem işlevi tavan hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7056cfd1773e0d98fa134c9fdec3ad6a43af1bcf
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351104"
---
# <a name="ceiling-azure-cosmos-db"></a>TAVAN (Azure Cosmos DB)
 Büyüktür veya eşittir, belirtilen sayısal ifadenin en küçük tamsayı değerini döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `CEILING` işleviyle pozitif sayısal, negatif ve sıfır değerleri gösterir.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
