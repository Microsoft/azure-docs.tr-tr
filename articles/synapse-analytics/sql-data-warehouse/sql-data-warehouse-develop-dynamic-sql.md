---
title: Dinamik SQL kullanma
description: Azure SYNAPSE Analytics 'te adanmış SQL havuzları için dinamik SQL kullanan geliştirme çözümleri ipuçları.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 18dcdbf5a4840fda3c2689cc3c8c003470bf1c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98679610"
---
# <a name="dynamic-sql-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzları için dinamik SQL

Bu makalede, adanmış SQL havuzlarında dinamik SQL kullanan geliştirme çözümleri için ipuçları yer almaktadır.

## <a name="dynamic-sql-example"></a>Dinamik SQL örneği

Adanmış SQL havuzları için uygulama kodu geliştirirken, esnek, genel ve modüler çözümler sunmaya yardımcı olması için dinamik SQL kullanmanız gerekebilir. Adanmış SQL havuzları Şu anda blob veri türlerini desteklemez.

Blob veri türleri hem varchar (max) hem de nvarchar (max) türlerini içerdiğinden, blob veri türlerini desteklememe, dizelerin boyutunu sınırlayabilir.

Büyük dizeler oluşturmak için uygulama kodunuzda bu türleri kullandıysanız, kodu parçalara ayırmak ve bunun yerine EXEC ifadesini kullanmanız gerekir.

Basit bir örnek:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Dize kısaysa, [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) normal olarak kullanabilirsiniz.

> [!NOTE]
> Dinamik SQL olarak yürütülen deyimler, tüm T-SQL doğrulama kurallarına tabi olmaya devam edecektir.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).
