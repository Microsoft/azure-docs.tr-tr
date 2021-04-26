---
title: Azure Cosmos DB sorgu dilinde LOG10
description: Belirtilen sayısal ifadenin 10 tabanında logaritmasını döndürmek için Azure Cosmos DB 'teki LOG10 SQL sistem işlevi hakkında bilgi edinin
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27f7d916c1a3f84f26674fea6b04597fd9e546bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338464"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Belirtilen sayısal ifadenin 10 tabanında logaritmasını döndürür.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*numeric_expression*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="remarks"></a>Açıklamalar
  
  LOG10 ve güç işlevleri, özellikle bir birbirleriyle ilişkilidir. Örneğin, 10 ^ LOG10 (n) = n. Bu sistem işlevi dizinden yararlanmayacak.
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek bir değişken bildirir ve belirtilen değişkenin LOG10 değerini döndürür (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
