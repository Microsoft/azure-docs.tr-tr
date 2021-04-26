---
title: SYNAPSE SQL özellikleri geliştirme kaynakları
description: SYNAPSE SQL için geliştirme kavramları, tasarım kararları, öneriler ve kodlama teknikleri.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4d842414d3046692c982ca3203957a96f8a01b37
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377339"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te SYNAPSE SQL özellikleri için tasarım kararları ve kodlama teknikleri
Bu makalede, Özel SQL havuzu ve SYNAPSE SQL 'in sunucusuz SQL havuzu işlevlerine yönelik kaynakların bir listesini bulacaksınız. Önerilen makaleler iki bölüme ayrılır: temel tasarım kararları ve geliştirme ve kodlama teknikleri.

Bu makalelerin amacı, Azure SYNAPSE Analytics 'te SYNAPSE SQL bileşenleri için en iyi teknik yaklaşımı geliştirmenize yardımcı olmaktır.

## <a name="key-design-decisions"></a>Anahtar tasarım kararları
Aşağıdaki makalelerde SYNAPSE SQL geliştirme için kavramlar ve tasarım kararları vurgulanacak:

| Makale | adanmış SQL havuzu | sunucusuz SQL havuzu |
| ------- | -------- | ------------- |
| [Bağlantılar](connect-overview.md)                    | Yes | Yes |
| [Kaynak sınıfları ve eşzamanlılık](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Yes    | Hayır |
| [İşlemler](develop-transactions.md)              | Yes | Hayır |
| [Kullanıcı tanımlı şemalar](develop-user-defined-schemas.md) | Yes | Yes |
| [Tablo dağıtımı](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Yes | Hayır |
| [Tablo dizinleri](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Yes | Hayır |
| [Tablo bölümleri](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Yes | Hayır |
| [İstatistikler](develop-tables-statistics.md)            | Yes | Yes |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Yes | Hayır |
| [Dış tablolar](develop-tables-external-tables.md) | Yes | Yes |
| [CETAS](develop-tables-cetas.md)                     | Yes | Yes |


## <a name="recommendations"></a>Öneriler

Aşağıda, geliştirmeye yönelik belirli kodlama tekniklerini, ipuçlarını ve önerilerini vurgulayacak önemli makaleler bulacaksınız:

| Makale | adanmış SQL havuzu | sunucusuz SQL havuzu |
| ------- | -------- | ------------- |
| [Saklı yordamlar](develop-stored-procedures.md)  | Yes                | Yes                      |
| [Etiketler](develop-label.md)                           | Yes                | Hayır                      |
| [Görünümler](develop-views.md)                             | Yes                | Yes                     |
| [Geçici tablolar](develop-tables-temporary.md)       | Yes                | Yes                     |
| [Dinamik SQL](develop-dynamic-sql.md)                 | Yes                | Yes                     |
| [Döngü](develop-loops.md)                         | Yes                | Yes                     |
| [Seçeneklere göre gruplandırma](develop-group-by-options.md)       | Yes                | Hayır                      |
| [Değişken ataması](develop-variable-assignment.md) | Yes                | Evet                     |

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla başvuru bilgisi için bkz. [SQL Pool T-SQL deyimleri](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

