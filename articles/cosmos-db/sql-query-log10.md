---
title: Azure Cosmos DB sorgu dilinde LOG10
description: Azure Cosmos DB 'de SQL sistem işlevi LOG10 hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 56f1e96e7d4ee1b5f38ee7392665e17819ae554b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349723"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 Belirtilen sayısal ifade 10 tabanında logaritmasını döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*numeric_expression*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="remarks"></a>Açıklamalar
  
  LOG10 ve güç işlevleri inversely birbirleriyle ilişkilidir. Örneğin, 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, bir değişken bildirir ve belirtilen değişkeni (100) LOG10 değerini döndürür.  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
