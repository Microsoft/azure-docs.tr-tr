---
title: Azure Cosmos DB sorgu dilindeki tür denetimi işlevleri
description: Azure Cosmos DB 'de SQL sistem işlevlerini denetleme türü hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349070"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Tür denetimi işlevleri (Azure Cosmos DB)

Tür denetimi işlevleri bir SQL sorgusu içindeki bir ifadenin türünü denetlemenizi sağlar. Değişken ya da bilinmiyor olduğunda, işlemler içindeki öğelerin içindeki özellik türlerini belirleyebilmek için tür denetimi işlevlerini kullanabilirsiniz. 

## <a name="functions"></a>Functions

Desteklenen yerleşik tür denetleme işlevlerinin bir tablosu aşağıda verilmiştir:

Tür denetimini karşı giriş değerleri aşağıdaki işlevleri destekler ve her bir Boole değeri döndürür.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Sonraki adımlar

- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
- [Kullanıcı tanımlı Işlevler](sql-query-udfs.md)
- [Toplar](sql-query-aggregates.md)
