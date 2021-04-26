---
title: Azure Cosmos DB sorgu dilinde ASIN
description: Azure Cosmos DB 'teki arksinüs (ASIN) SQL sistem işlevinin, sinüsü belirtilen sayısal ifade olan radyan cinsinden açısını nasıl döndürdüğünü öğrenin
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 69862df5809bffe7c975b0340993b8e42aa47337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93332483"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Sinüsü belirtilen sayısal ifade olan radyan cinsinden açıyı döndürür. Buna ark sinüs da denir.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek `ASIN` -1 ' i döndürür.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi dizinden yararlanmayacak.

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
