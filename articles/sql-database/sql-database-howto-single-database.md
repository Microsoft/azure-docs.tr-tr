---
title: Tek bir veritabanını yapılandırma
description: Azure SQL veritabanı 'nı yapılandırma ve yönetme hakkında bilgi edinin-tek veritabanı
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 02/08/2019
ms.openlocfilehash: 3c03797c97a354eb06b6c3b1d76ba5c39d512c18
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811215"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Azure SQL veritabanı 'nda tek bir veritabanı kullanma

Bu bölümde, Azure SQL veritabanı 'nda tek veritabanınızı yönetmenize ve yapılandırmanıza yardımcı olabilecek çeşitli kılavuzlar, betikler ve açıklamalar bulabilirsiniz

## <a name="migrate"></a>Geçiş

- [SQL veritabanına](sql-database-single-database-migrate.md) geçiş – yönetilen bir örneğe geçiş için önerilen geçiş işlemi ve araçları hakkında bilgi edinin.
- [Geçişten sonra SQL veritabanını yönetmeyi](sql-database-manage-after-migration.md)öğrenin.

## <a name="configure-features"></a>Yapılandırma özellikleri

- Tarih veritabanlarını veritabanları arasında çoğaltmak için [İşlemsel çoğaltmayı yapılandırın](replication-to-sql-database.md) .
- Azure SQL veritabanı 'nın SQL ekleme veya şüpheli konumlardan erişim gibi şüpheli etkinlikleri belirlemesine izin vermek için [tehdit algılamayı yapılandırın](sql-database-threat-detection.md) .
- Gizli verilerinizi korumak için [dinamik veri maskeleme yapılandırın](sql-database-dynamic-data-masking-get-started-portal.md) .
- Yedeklemelerinizi Azure Blob depolama alanı üzerinde tutmak için bir veritabanı için [yedekleme bekletmesini yapılandırın](sql-database-long-term-backup-retention-configure.md) . Alternatif olarak, [Azure Kasası (kullanım dışı) yaklaşımını kullanarak yedekleme bekletmesini yapılandırın](sql-database-long-term-backup-retention-configure-vault.md) .
- [Coğrafi çoğaltmayı](sql-database-geo-replication-portal.md) , veritabanınızın bir çoğaltmasını başka bir bölgede tutmak için yapılandırın.
- [Coğrafi çoğaltmalar için güvenliği yapılandırın](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Veritabanınızı izleme ve ayarlama

- Azure SQL veritabanı 'nın iş yükünüzün performansını iyileştirmelerine izin vermek için [otomatik ayarlamayı etkinleştirin](sql-database-automatic-tuning-enable.md) .
- Ayarlama önerileri hakkında bilgi almak için [otomatik ayarlama için e-posta bildirimlerini etkinleştirin](sql-database-automatic-tuning-email-notifications.md) .
- [Performans önerilerini uygulayın](sql-database-advisor-portal.md) ve veritabanınızı iyileştirin.
- Azure SQL veritabanından bildirimleri almak için [uyarılar oluşturun](sql-database-insights-alerts-portal.md) .
- Uygulamalar ve veritabanı arasında bazı bağlantı sorunlarıyla karşılaşırsanız [bağlantı sorunlarını giderin](sql-database-troubleshoot-common-connection-issues.md) . [Bağlantı sorunları için kaynak durumu](sql-database-resource-health.md)de kullanabilirsiniz.
- Veritabanınızdaki depolama kullanımını izlemek için [Dosya alanını yönetin](sql-database-file-space-management.md) .

## <a name="query-distributed-data"></a>Sorgu dağıtılmış verileri

- [Dikey olarak bölümlenmiş verileri](sql-database-elastic-query-getting-started-vertical.md) birden çok veritabanı arasında sorgulayın.
- [Ölçeği genişletilmiş veri katmanı üzerinden raporla](sql-database-elastic-query-horizontal-partitioning.md).
- [Farklı şemalarla tablolar arasında sorgu](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Elastik Veritabanı İşleri

- [Oluşturma ve yönetme](elastic-jobs-powershell.md) PowerShell kullanarak elastik veritabanı Işleri.
- [Oluşturma ve yönetme](elastic-jobs-tsql.md) Transact-SQL kullanan elastik veritabanı Işleri.
- [Eski elastik Işten geçiş yapın](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Veritabanı parçalama

- [Elastik veritabanı istemci kitaplığını yükseltin](sql-database-elastic-scale-upgrade-client-library.md).
- Parçalı [uygulama oluşturun](sql-database-elastic-scale-get-started.md).
- [Yatay olarak parçalı verileri sorgulayın](sql-database-elastic-query-getting-started.md).
- [Çok parçalı sorgular](sql-database-elastic-scale-multishard-querying.md)çalıştırın.
- Parçalı [verileri taşıyın](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- Veritabanı parçaları 'nda [güvenliği yapılandırma](sql-database-elastic-scale-split-merge-security-configuration.md) .
- Geçerli veritabanı parçaları kümesine parça [ekleyin](sql-database-elastic-scale-add-a-shard.md) .
- Parça [eşleme sorunlarını giderin](sql-database-elastic-database-recovery-manager.md).
- Parçalı [DB 'Yi geçirin](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Sayaç oluşturun](sql-database-elastic-database-perf-counters.md).
- Parçalı verileri sorgulamak için [Entity Framework 'Ü kullanın](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) .
- Parçalı verileri sorgulamak için [kaber çerçevesini kullanın](sql-database-elastic-scale-working-with-dapper.md) .

## <a name="next-steps"></a>Sonraki adımlar
- [Yönetilen örnek Için nasıl yapılır kılavuzlarından](sql-database-howto-managed-instance.md) daha fazla bilgi edinin
