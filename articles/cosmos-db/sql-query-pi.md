---
title: Azure Cosmos DB sorgu dilinde PI
description: Azure Cosmos DB 'de SQL sistem işlevi PI hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349659"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 PI sayısının sabit değerini döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek `PI` değerini döndürür.  
  
```sql
SELECT PI() AS pi 
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
