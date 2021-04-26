---
title: Azure Cosmos DB sorgu dilinde oturum aç
description: Belirtilen sayısal ifadenin doğal logaritmasını döndürmek için Azure Cosmos DB SQL sistem işlevi hakkında bilgi edinin
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 44a9d5b273e13886b0674b3b2e9f5f7a75e72fcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338586"
---
# <a name="log-azure-cosmos-db"></a>GÜNLÜK (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Belirtilen sayısal ifadenin doğal logaritmasını döndürür.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
*base*  
   Logaritmanın temelini ayarlayan isteğe bağlı sayısal bağımsız değişken.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="remarks"></a>Açıklamalar
  
  Varsayılan olarak, LOG () doğal logaritmayı döndürür. İsteğe bağlı temel parametreyi kullanarak logaritmanın temelini başka bir değere değiştirebilirsiniz.  
  
  Doğal logaritma, **e** 'nin bir ırrational sabiti olarak 2,718281828 'e eşit olduğu, taban **e**'nin logaritmasına neden olur.  
  
  Bir sayının üstel değerinin doğal logaritması, sayının kendisidir: LOG (EXP (n)) = n. Bir sayının doğal logaritmanın üstel değeri, sayının kendisidir: EXP (günlük (n)) = n.

  Bu sistem işlevi dizinden yararlanmayacak.
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek bir değişken bildirir ve belirtilen değişkenin (10) logaritma değerini döndürür.  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 Aşağıdaki örnek, `LOG` bir sayının üs değeri için öğesini hesaplar.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
