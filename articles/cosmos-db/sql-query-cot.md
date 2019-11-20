---
title: Azure Cosmos DB sorgu dilinde COT
description: Azure Cosmos DB 'de SQL sistem işlevi COT hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d980319730e69fdcb529272ba1b8fb48d2b5b230
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351237"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Trigonometrik belirtilen bir açının kotanjantını radyan cinsinden belirtilen bir sayısal ifade döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, belirtilen açının `COT` ' i hesaplar.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
