---
title: Azure Cosmos DB sorgu dilinde ACOS
description: Azure Cosmos DB 'deki ACOS (arccosice) SQL sistem işlevinin, kosinüsü belirtilen sayısal ifade olan radyan cinsinden açısını nasıl döndürdüğünü öğrenin
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ed5adaad6c0ee160fd55341e1d7c48fcf3e49a28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93332823"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Kosinüsü belirtilen sayısal ifade olan radyan cinsinden açıyı döndürür; Arkkosinüs da denir.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek `ACOS` -1 ' i döndürür.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi dizinden yararlanmayacak.

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
