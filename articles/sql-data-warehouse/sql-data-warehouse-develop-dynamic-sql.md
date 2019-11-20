---
title: Dinamik SQL kullanma
description: Azure SQL veri ambarı 'nda çözüm geliştirmek için dinamik SQL kullanmaya yönelik ipuçları.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 87320128537a235e8047a2f826b0e59c08aef76b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692851"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL veri ambarı 'nda dinamik SQL
Azure SQL veri ambarı 'nda çözüm geliştirmek için dinamik SQL kullanmaya yönelik ipuçları.

## <a name="dynamic-sql-example"></a>Dinamik SQL örneği

SQL veri ambarı için uygulama kodu geliştirirken, esnek, genel ve modüler çözümler sunmaya yardımcı olması için dinamik SQL kullanmanız gerekebilir. SQL veri ambarı Şu anda blob veri türlerini desteklemiyor. Blob veri türleri hem varchar (max) hem de nvarchar (max) türlerini içerdiğinden, blob veri türlerini desteklememe, dizelerin boyutunu sınırlayabilir. Büyük dizeler oluşturmak için uygulama kodunuzda bu türleri kullandıysanız, kodu parçalara ayırmak ve bunun yerine EXEC ifadesini kullanmanız gerekir.

Basit bir örnek:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Dize kısaysa, [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) öğesini normal olarak kullanabilirsiniz.

> [!NOTE]
> Dinamik SQL olarak yürütülen deyimler, tüm TSQL doğrulama kurallarına tabi olmaya devam edecektir.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).

