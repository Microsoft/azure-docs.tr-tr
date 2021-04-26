---
title: Azure Cosmos DB sorgu dilindeki tür denetimi işlevleri
description: Azure Cosmos DB 'de SQL sistem işlevlerini denetleme türü hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2becc9216d847dfe26d8fd3a433993112fff7980
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546361"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Tür denetimi işlevleri (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tür denetimi işlevleri bir SQL sorgusu içindeki bir ifadenin türünü denetlemenizi sağlar. Değişken ya da bilinmiyor olduğunda, işlemler içindeki öğelerin içindeki özellik türlerini belirleyebilmek için tür denetimi işlevlerini kullanabilirsiniz. 

## <a name="functions"></a>İşlevler

Desteklenen yerleşik tür denetleme işlevlerinin bir tablosu aşağıda verilmiştir:

Aşağıdaki işlevler, giriş değerlerine karşı tür denetimini destekler ve her biri bir Boole değeri döndürür.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
- [Kullanıcı tanımlı Işlevler](sql-query-udfs.md)
- [Toplamlar](sql-query-aggregate-functions.md)
