---
title: Azure Cosmos DB sorgu dilinde uzunluk
description: Azure Cosmos DB 'de SQL sistem işlev uzunluğu hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aa430152415b1662a73a388a03ba6d4721c730f0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349762"
---
# <a name="length-azure-cosmos-db"></a>Uzunluk (Azure Cosmos DB)
 Belirtilen dize ifadesinin karakter sayısını döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*str_expr*  
   Değerlendirilecek dize ifadesi.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek bir dizenin uzunluğunu döndürür.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"len": 3}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
