---
title: Azure Cosmos DB sorgu dilinde ARRAY_CONCAT
description: Azure Cosmos DB içindeki array Concat SQL sistem işlevinin, iki veya daha fazla dizi değerini birleştirme sonucu olan bir dizi döndürdüğünü öğrenin
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b6424a161b5fc4d0c075ade6f852b33dff20a09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93332772"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 İki veya daha fazla dizi değerini bitiştirme sonucu olan bir dizi döndürür.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*arr_expr*  
   , Diğer değerleri birleştirmek için bir dizi ifadesidir. `ARRAY_CONCAT`İşlev, en az iki *arr_expr* bağımsız değişken gerektiriyor.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir dizi ifadesi döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek iki diziyi birleştirme.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi dizinden yararlanmayacak.

## <a name="next-steps"></a>Sonraki adımlar

- [Dizi işlevleri Azure Cosmos DB](sql-query-array-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
