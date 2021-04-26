---
title: Azure Cosmos DB sorgu dilinde CONCAT
description: Azure Cosmos DB içindeki CONCAT SQL sistem işlevinin iki veya daha fazla dize değerini birleştirme sonucu olan bir dizeyi nasıl döndürdüğünü öğrenin
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f5fbbbdd9d29199f5b9bd173b6ab12d3d615943c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339209"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 İki veya daha fazla dize değerinin bitişinin sonucu olan bir dize döndürür.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*str_expr*  
   , Diğer değerleri birleştirmek için bir dize ifadesidir. `CONCAT`İşlev, en az iki *str_expr* bağımsız değişken gerektiriyor.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir dize ifadesi döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, belirtilen değerlerin birleştirilmiş dizesini döndürür.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi dizinden yararlanmayacak.

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
