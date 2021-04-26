---
title: Sorgu dili Azure Cosmos DB oturum açma
description: SQL sistem işlevi oturum açma Azure Cosmos DB hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02fceb8ac594a8caff8942b8a7cb0c6bcd2a47e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339623"
---
# <a name="sign-azure-cosmos-db"></a>IMZALA (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Belirtilen sayısal ifadenin pozitif (+ 1), sıfır (0) veya negatif (-1) işaretini döndürür.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
SIGN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `SIGN` -2 ' den 2 ' ye kadar olan sayıların değerlerini döndürür.  
  
```sql
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi dizinden yararlanmayacak.

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
