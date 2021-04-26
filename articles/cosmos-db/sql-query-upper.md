---
title: ÜST Azure Cosmos DB sorgu dili
description: Azure Cosmos DB SQL sistem işlevi hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 06f85d97266e78b343ad8da233b77e369da5ee65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334914"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Küçük harfli karakter verilerini büyük harfe dönüştürdükten sonra bir dize ifadesi döndürür.  

ÜSTTEKI sistem işlevi dizinden kullanmaz. Büyük/küçük harfe duyarsız karşılaştırmalar yapmak istiyorsanız, ÜSTTEKI sistem işlevi önemli miktarda RU 'yı kullanabilir. Bu durumda, karşılaştırmalar için her seferinde verileri normalleştirmek üzere üstteki sistem işlevini kullanmak yerine, ekleme sırasında büyük/küçük harfleri normalleştirin. Ardından, SELECT * FROM c, UPPER (c. Name) = ' BOB ' gibi bir sorgu yalnızca c.name = ' BOB ' olduğunda SELECT * FROM c.

## <a name="syntax"></a>Söz dizimi
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*str_expr*  
   Bir dize ifadesidir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir dize ifadesi döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, bir sorguda nasıl kullanılacağını gösterir `UPPER`  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi dizinden yararlanmayacak.

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
