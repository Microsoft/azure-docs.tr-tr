---
title: Azure 'da veri ambarı geliştirme kaynakları
description: Geliştirme kavramları, tasarım kararları, öneriler ve SQL veri ambarı için kodlama teknikleri.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a6cb7937bdd5dea9eb1a48b2b350db9077431fe0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645609"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>SQL veri ambarı için tasarım kararları ve kodlama teknikleri
Temel tasarım kararlarını, önerileri ve SQL veri ambarı için kodlama tekniklerini daha iyi anlamak için bu geliştirme makalelerine göz atın.

## <a name="key-design-decisions"></a>Anahtar tasarım kararları
Aşağıdaki makalelerde SQL veri ambarı kullanılarak dağıtılmış bir veri ambarı geliştirmeye yönelik kavramlar ve tasarım kararları vurgulanacak:

* [bağlantının][connections]
* [zamanlı][concurrency]
* [hareket][transactions]
* [Kullanıcı tanımlı şemalar][user-defined schemas]
* [Tablo dağıtımı][table distribution]
* [tablo dizinleri][table indexes]
* [Tablo bölümleri][table partitions]
* [CTAS][CTAS]
* [istatistikler][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Geliştirme önerileri ve kodlama teknikleri
Bu makaleler, SQL veri Ambarınızı geliştirmeye yönelik belirli kodlama tekniklerini, ipuçlarını ve önerilerini vurgular:

* [saklı yordamlar][stored procedures]
* [etikete][labels]
* [Görünümler][views]
* [geçici tablolar][temporary tables]
* [dinamik SQL][dynamic SQL]
* [döngüye][looping]
* [seçeneklere göre Gruplandır][group by options]
* [değişken ataması][variable assignment]

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla başvuru bilgisi için bkz. [SQL veri ambarı T-SQL deyimleri](sql-data-warehouse-reference-tsql-statements.md).

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
