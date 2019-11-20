---
title: Azure Cosmos DB sorgu dilinde sistem işlevleri
description: Azure Cosmos DB 'deki SQL sistem işlevleri hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 98a087f02035545b828cfcf11c58d03d02a5e76a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349183"
---
# <a name="system-functions-azure-cosmos-db"></a>Sistem işlevleri (Azure Cosmos DB)

 Cosmos DB, çok sayıda yerleşik SQL işlevleri sağlar. Yerleşik işlevler kategorileri aşağıda listelenmiştir.  
  
|İşlev grubu|Açıklama|İşlemler|  
|--------------|-----------------|-----------------| 
|[Dizi işlevleri](sql-query-array-functions.md)|Dizi işlevler bir dizi giriş değeri ve dönüş sayısal, Boole değeri veya dizi değeri üzerinde bir işlem gerçekleştirir. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Tarih ve saat işlevleri](sql-query-date-time-functions.md)|Tarih ve saat işlevleri, geçerli UTC Tarih ve saatini iki şekilde almanızı sağlar; değeri, milisaniye cinsinden UNIX dönemi olan sayısal bir zaman damgası veya ISO 8601 biçimine uygun bir dize olarak. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Matematik işlevleri](sql-query-mathematical-functions.md)|Matematik işlevleri her, genellikle bağımsız değişken olarak sağlanan ve sayısal bir değer döndürmesi giriş değerlerine göre bir hesaplama gerçekleştirir. | [ABS](sql-query-abs.md), [ACOS](sql-query-acos.md), [ASİN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [TAVAN](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [DERECE](sql-query-degrees.md), [EXP](sql-query-exp.md), [FLOOR](sql-query-floor.md), [LOG](sql-query-log.md), [LOG10](sql-query-log10.md), [PI](sql-query-pi.md), [ÜS](sql-query-power.md), [RADYAN](sql-query-radians.md), [S_SAYI_ÜRET](sql-query-rand.md), [YUVARLAK](sql-query-round.md), [İŞARET](sql-query-sign.md), [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [KARE](sql-query-square.md), [TAN](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[Uzamsal İşlevler](sql-query-spatial-functions.md)|Uzamsal İşlevler, uzamsal nesne giriş değeri bir işlem gerçekleştirmek ve bir sayısal veya Boolean değeri döndürür. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Dize işlevleri](sql-query-string-functions.md)|Dize işlevleri, bir dize giriş değeri bir işlem gerçekleştirmek ve bir dize, sayısal veya Boolean değeri döndürür. | [Concat](sql-query-concat.md), [CONTAINS](sql-query-contains.md), [EndsWith](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [sol](sql-query-left.md), [uzunluk](sql-query-length.md), [alt](sql-query-lower.md), [LTrim](sql-query-ltrim.md), [Değiştir](sql-query-replace.md), [Çoğalt](sql-query-replicate.md), [ters](sql-query-reverse.md), [sağ](sql-query-right.md), [RTrim](sql-query-rtrim.md), [ STARTSWITH](sql-query-startswith.md), [stringtoarray](sql-query-stringtoarray.md), [stringtoboolean](sql-query-stringtoboolean.md), [stringtonull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [stringtoobject](sql-query-stringtoobject.md), [SUBSTRING](sql-query-substring.md), [ToString](sql-query-tostring.md), [TRIM](sql-query-trim.md), [Upper](sql-query-upper.md) |
|[Tür denetimini işlevleri](sql-query-type-checking-functions.md)|Tür denetimi işlevleri SQL sorgusu içindeki bir ifadenin türü denetlemenizi sağlar. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Yerleşik ve Kullanıcı tanımlı Işlevler (UDF 'ler)

Şu anda yerleşik bir işlevin artık kullanılabildiği Kullanıcı tanımlı bir işlev (UDF) kullanıyorsanız, ilgili yerleşik işlevin çalıştırılması daha hızlı ve daha verimli olacaktır.

## <a name="built-in-versus-ansi-sql-functions"></a>Yerleşik ANSI SQL işlevleri

Cosmos DB işlevleri ve ANSI SQL işlevleri arasındaki temel fark, Cosmos DB işlevlerin şemasız ve karışık şema verileri ile sorunsuz çalışacak şekilde tasarlandıkları bir çalışmadır. Örneğin, bir özellik eksikse veya `unknown` gibi sayısal olmayan bir değere sahipse, öğe bir hata döndürmek yerine atlanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB giriş](introduction.md)
- [Dizi işlevleri](sql-query-array-functions.md)
- [Tarih ve saat işlevleri](sql-query-date-time-functions.md)
- [Matematik işlevleri](sql-query-mathematical-functions.md)
- [Uzamsal İşlevler](sql-query-spatial-functions.md)
- [Dize işlevleri](sql-query-string-functions.md)
- [Tür denetimini işlevleri](sql-query-type-checking-functions.md)
- [Kullanıcı tanımlı Işlevler](sql-query-udfs.md)
- [Toplar](sql-query-aggregates.md)
