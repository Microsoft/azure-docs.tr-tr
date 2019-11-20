---
title: Azure Cosmos DB sorgu dilinde IS_DEFINED
description: Azure Cosmos DB 'de SQL sistem işlevi IS_DEFINED hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2c3193262a41b3c6772d4fe29c78a132bc51bbd8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349880"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Özellik değeri atanıp atanmadığını gösteren bir Boole değeri döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*ifadeyi*  
   Herhangi bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir Boolean ifadesi döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, bir özelliği belirtilen JSON belgesi içinde varlığını denetler. İlk "a" var, ancak "b" eksik olduğundan ikinci false döndürürse bu yana true döndürür.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Tür denetimi işlevleri Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
