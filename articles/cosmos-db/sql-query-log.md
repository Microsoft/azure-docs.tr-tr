---
title: Azure Cosmos DB sorgu dilinde oturum aç
description: Azure Cosmos DB SQL sistem işlevi günlüğü hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 16f2321eb48afacbc9636b5c0588b1ea3a01a284
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349742"
---
# <a name="log-azure-cosmos-db"></a>GÜNLÜK (Azure Cosmos DB)
 Belirtilen sayısal ifadenin doğal logaritmasını döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
*temel*  
   Logaritmanın tabanı ayarlayan isteğe bağlı sayısal bağımsız değişken.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="remarks"></a>Açıklamalar
  
  Varsayılan olarak, LOG() doğal logaritmasını döndürür. Logaritmanın tabanı, isteğe bağlı temel parametresini kullanarak başka bir değere değiştirebilirsiniz.  
  
  Logaritmanın tabanı için doğal logaritmasını olan **e**burada **e** bir Irrational 2.718281828 için yaklaşık olarak eşit sabittir.  
  
  Bir sayının üstel değerinin doğal logaritması sayının kendisidir: GÜNLÜK (EXP (n)) = n. Ve bir sayının doğal logaritmanın üstel değerinin kendisi sayıdır: EXP (günlük (n)) = n.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, bir değişken bildirir ve belirtilen değişkeni (10) logaritmasını döndürür.  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 Aşağıdaki örnek bir sayının üs değeri için `LOG` ' yı hesaplar.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
