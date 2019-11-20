---
title: Azure Cosmos DB sorgu dilinde DEĞIŞTIR
description: Azure Cosmos DB 'da SQL sistem işlevi DEĞIŞTIRME hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2cb7d82efd010fd7c3395a4f6a9217370d9e5779
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349590"
---
# <a name="replace-azure-cosmos-db"></a>DEĞIŞTIR (Azure Cosmos DB)
 Belirtilen dize değeri tüm oluşumlarını başka bir dize değeri ile değiştirir.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*str_expr1*  
   , Aranacak dize ifadesidir.  
  
*str_expr2*  
   , Bulunan dize ifadesidir.  
  
*str_expr3*  
   , *Str_expr1*içindeki *str_expr2* oluşumlarını değiştirecek dize deyimidir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir dize ifadesi döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, bir sorguda `REPLACE` ' ın nasıl kullanılacağını gösterir.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
