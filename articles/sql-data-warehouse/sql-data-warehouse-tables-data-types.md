---
title: Veri türlerini tanımlama
description: Azure SQL veri ambarı 'nda tablo veri türlerini tanımlamaya yönelik öneriler.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a1d88d8fabd5ff32cc1ca5cca4e2a57a86c8dcb3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692494"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'ndaki tablo veri türleri
Azure SQL veri ambarı 'nda tablo veri türlerini tanımlamaya yönelik öneriler. 

## <a name="what-are-the-data-types"></a>Veri türleri nelerdir?

SQL veri ambarı en yaygın kullanılan veri türlerini destekler. Desteklenen veri türlerinin bir listesi için, bkz. CREATE TABLE deyimindeki [veri türleri](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) . 

## <a name="minimize-row-length"></a>Satır uzunluğunu Küçült
Veri türlerinin boyutunu en aza indirmek, satır uzunluğunun kısaltaştırarak daha iyi sorgu performansına yol açar. Verileriniz için en küçük veri türünü kullanın. 

- Büyük bir varsayılan uzunluğa sahip karakter sütunları tanımlamaktan kaçının. Örneğin, en uzun değer 25 karakter ise sütununuzu VARCHAR (25) olarak tanımlayın. 
- Yalnızca VARCHAR gerektiren [NVARCHAR] [NVARCHAR] kullanmaktan kaçının.
- Mümkün olduğunda, NVARCHAR (MAX) veya VARCHAR (MAX) yerine NVARCHAR (4000) veya VARCHAR (8000) kullanın.

Tablolarınızı yüklemek için PolyBase dış tabloları kullanıyorsanız, tablo satırının tanımlanan uzunluğu 1 MB 'ı aşamaz. Değişken uzunluklu verileri olan bir satır 1 MB 'yi aştığında satırı BCP ile yükleyebilirsiniz ancak PolyBase ile birlikte kullanamazsınız.

## <a name="identify-unsupported-data-types"></a>Desteklenmeyen veri türlerini tanımla
Veritabanınızı başka bir SQL veritabanından geçiriyorsanız SQL veri ambarı 'nda desteklenmeyen veri türleriyle karşılaşabilirsiniz. Mevcut SQL şemanızda desteklenmeyen veri türlerini saptamak için bu sorguyu kullanın.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Desteklenmeyen veri türleri için geçici çözümler

Aşağıdaki listede SQL veri ambarı 'nın desteklemediği veri türleri ve desteklenmeyen veri türleri yerine kullanabileceğiniz alternatifler verilmiştir.

| Desteklenmeyen veri türü | Geçici çözüm |
| --- | --- |
| [geometrisi](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[ikili](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Coğrafya](/sql/t-sql/spatial-geography/spatial-types-geography) |[ikili](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [görüntüyle](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[ikili](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [metinleri](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [n](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Sütunu kesin olarak belirlenmiş birkaç sütuna Böl. |
| [tablosundan](/sql/t-sql/data-types/table-transact-sql) |Geçici tablolara Dönüştür. |
| [ilişkin](/sql/t-sql/data-types/date-and-time-types) |[Datetime2](/sql/t-sql/data-types/datetime2-transact-sql) ve [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) işlevini kullanmak için yeniden çalışma kodu. Varsayılanlar olarak yalnızca sabitler desteklenir, bu nedenle current_timestamp varsayılan kısıtlama olarak tanımlanamaz. Zaman damgasıyla yazılmış bir sütundan satır sürümü değerlerini geçirmeniz gerekiyorsa, NULL veya NULL satır sürüm değerleri için [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) veya [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) kullanın. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [Kullanıcı tanımlı tür](/sql/relational-databases/native-client/features/using-user-defined-types) |Mümkün olduğunda yerel veri türüne geri dönüştür. |
| Varsayılan değerler | Varsayılan değerler yalnızca sabit değerleri ve sabitleri destekler. |


## <a name="next-steps"></a>Sonraki adımlar
Tablo geliştirme hakkında daha fazla bilgi için bkz. [tabloya genel bakış](sql-data-warehouse-tables-overview.md).
