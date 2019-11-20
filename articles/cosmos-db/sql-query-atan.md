---
title: Azure Cosmos DB sorgu dilinde ATAN
description: Azure Cosmos DB 'de SQL sistem işlevi ATAN hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4ee3867f3a4938358e8d61aa8a98f747756ee3e8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348571"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Tanjantı belirtilen sayısal ifadesidir radyan cinsinden açı döndürür. Bu arktanjantını olarak da adlandırılır.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, belirtilen değerin `ATAN` değerini döndürür.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
