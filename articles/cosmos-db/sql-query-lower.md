---
title: Azure Cosmos DB sorgu dilinde daha düşük
description: Azure Cosmos DB daha düşük SQL sistem işlevi hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3a9c122ef65772458b832d3b1651e7e63e42985e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349713"
---
# <a name="lower-azure-cosmos-db"></a>DAHA düşük (Azure Cosmos DB)
 Büyük harf karakter verileri küçük harfe dönüştürmenin sonra bir dize ifadesi döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*str_expr*  
   Bir dize ifadesidir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir dize ifadesi döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, bir sorguda `LOWER` ' ın nasıl kullanılacağını gösterir.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
