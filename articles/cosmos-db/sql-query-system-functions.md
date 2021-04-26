---
title: Azure Cosmos DB sorgu dilinde sistem işlevleri
description: Azure Cosmos DB içinde yerleşik ve Kullanıcı tanımlı SQL sistem işlevleri hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 36ebe80671c77cc83ddba770e3259f6542472e58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526848"
---
# <a name="system-functions-azure-cosmos-db"></a>Sistem işlevleri (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Cosmos DB birçok yerleşik SQL işlevi sağlar. Yerleşik işlevlerin kategorileri aşağıda listelenmiştir.  
  
|İşlev grubu|Description|Operations|  
|--------------|-----------------|-----------------| 
|[Dizi işlevleri](sql-query-array-functions.md)|Dizi işlevleri dizi giriş değerinde bir işlem gerçekleştirir ve sayısal, Boolean veya dizi değeri döndürür. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Tarih ve saat işlevleri](sql-query-date-time-functions.md)|Tarih ve saat işlevleri, geçerli UTC Tarih ve saatini iki şekilde almanızı sağlar; değeri, milisaniye cinsinden UNIX dönemi olan sayısal bir zaman damgası veya ISO 8601 biçimine uygun bir dize olarak. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md), [GetCurrentTicks](sql-query-getcurrentticks.md) |
|[Matematik işlevleri](sql-query-mathematical-functions.md)|Matematik işlevlerinin her biri, genellikle bağımsız değişken olarak sunulan giriş değerlerine göre bir hesaplama gerçekleştirir ve sayısal bir değer döndürür. | [ABS](sql-query-abs.md), [Acos](sql-query-acos.md), [asin](sql-query-asin.md), [atan](sql-query-atan.md), [ATN2](sql-query-atn2.md), [tavan](sql-query-ceiling.md), [cos](sql-query-cos.md), [COT](sql-query-cot.md), [derece](sql-query-degrees.md), [Exp](sql-query-exp.md), [Floor](sql-query-floor.md), [log](sql-query-log.md), [log10](sql-query-log10.md), [PI](sql-query-pi.md), [üs](sql-query-power.md), [radyan](sql-query-radians.md), [S_SAYI_ÜRET](sql-query-rand.md), [round](sql-query-round.md), [SIGN](sql-query-sign.md), [sın](sql-query-sin.md), [sqrt](sql-query-sqrt.md), [kare](sql-query-square.md), [tan](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[Uzamsal işlevler](sql-query-spatial-functions.md)|Uzamsal işlevler, uzamsal nesne giriş değerinde bir işlem gerçekleştirir ve sayısal veya Boole değeri döndürür. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Dize işlevleri](sql-query-string-functions.md)|Dize işlevleri bir dize giriş değerinde bir işlem gerçekleştirir ve dize, sayısal veya Boole değeri döndürür. | [Concat](sql-query-concat.md), [CONTAINS](sql-query-contains.md), [EndsWith](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [Left](sql-query-left.md), [length](sql-query-length.md), [Lower](sql-query-lower.md), [LTrim](sql-query-ltrim.md), [regexmatch](sql-query-regexmatch.md)[Replace](sql-query-replace.md), [REPLICATE](sql-query-replicate.md), [Reverse](sql-query-reverse.md), [RIGHT](sql-query-right.md), [RTrim](sql-query-rtrim.md), [StartsWith](sql-query-startswith.md), [stringtoarray](sql-query-stringtoarray.md), [stringtoboolean](sql-query-stringtoboolean.md), [stringtonull](sql-query-stringtonull.md), [strtonumber](sql-query-stringtonumber.md) [,](sql-query-tostring.md)stringtoobject, [STRING](sql-query-substring.md) [](sql-query-stringtoobject.md) [](sql-query-trim.md) [](sql-query-upper.md) |
|[Tür denetimi işlevleri](sql-query-type-checking-functions.md)|Tür denetimi işlevleri SQL sorguları içindeki bir ifadenin türünü kontrol etmeniz için izin verir. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Yerleşik ve Kullanıcı tanımlı Işlevler (UDF 'ler)

Şu anda yerleşik bir işlevin artık kullanılabildiği Kullanıcı tanımlı bir işlev (UDF) kullanıyorsanız, ilgili yerleşik işlevin çalıştırılması daha hızlı ve daha verimli olacaktır.

## <a name="built-in-versus-ansi-sql-functions"></a>Yerleşik ANSI SQL işlevleri

Cosmos DB işlevleri ve ANSI SQL işlevleri arasındaki temel fark, Cosmos DB işlevlerin şemasız ve karışık şema verileri ile sorunsuz çalışacak şekilde tasarlandıkları bir çalışmadır. Örneğin, bir özellik eksikse veya gibi sayısal olmayan bir değer varsa `undefined` , öğe bir hata döndürmek yerine atlanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB'ye giriş](introduction.md)
- [Dizi işlevleri](sql-query-array-functions.md)
- [Tarih ve saat işlevleri](sql-query-date-time-functions.md)
- [Matematik işlevleri](sql-query-mathematical-functions.md)
- [Uzamsal işlevler](sql-query-spatial-functions.md)
- [Dize işlevleri](sql-query-string-functions.md)
- [Tür denetimi işlevleri](sql-query-type-checking-functions.md)
- [Kullanıcı tanımlı Işlevler](sql-query-udfs.md)
- [Toplamlar](sql-query-aggregate-functions.md)
