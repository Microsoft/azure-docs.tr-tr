---
title: İçerik başvurusunu yapılandırma & Yönet
description: Azure SQL veritabanı 'nı yapılandırıp yönetmenizi sağlayan içeriklere bir başvuru bulun.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: 19cec1deeef344ea5897061db0fec88066b90b05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92791775"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>İçerik başvurusunu yapılandırma ve yönetme-Azure SQL veritabanı
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu makalede, Azure SQL veritabanınızı yönetmenize ve yapılandırmanıza yardımcı olabilecek çeşitli kılavuzların, betiklerin ve açıklamalarının içerik başvurusunu bulabilirsiniz. 

## <a name="load-data"></a>Veri yükleme

- [SQL Veritabanına geçiş](migrate-to-database-from-sql-server.md)
- [Geçişten sonra SQL veritabanını yönetmeyi](manage-data-after-migrating-to-database.md)öğrenin.
- [Veritabanı kopyalama](database-copy.md)
- [BACPAC’ten veritabanını içeri aktarma](database-import.md)
- [Veritabanını BACPAC’e dışarı aktarma](database-export.md)
- [BCP ile veri yükleme](../load-from-csv-with-bcp.md)
- [ADF ile veri yükleme](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>Yapılandırma özellikleri

- [Azure Active Directory (Azure AD) kimlik doğrulamasını yapılandırma](authentication-aad-configure.md)
- [Koşullu erişimi yapılandırma](conditional-access-configure.md)
- [Çok faktörlü Azure AD kimlik doğrulaması](authentication-mfa-ssms-overview.md)
- [Multi-Factor Authentication Yapılandır](authentication-mfa-ssms-configure.md)
- [Zamana bağlı saklama ilkesini yapılandırma](temporal-tables-retention-policy.md)
- [KAG ile TDE Yapılandırması](transparent-data-encryption-byok-configure.md)
- [TDE KAG anahtarlarını döndürme](transparent-data-encryption-byok-key-rotation.md)
- [TDE koruyucusunu kaldırma](transparent-data-encryption-byok-remove-tde-protector.md)
- [Bellek içi OLTP’yi yapılandırma](../in-memory-oltp-configure.md)
- [Azure Otomasyonu 'Nu yapılandırma](automation-manage.md)
- Tarih veritabanlarını veritabanları arasında çoğaltmak için [İşlemsel çoğaltmayı yapılandırın](replication-to-sql-database.md) .
- Azure SQL veritabanı 'nın SQL ekleme veya şüpheli konumlardan erişim gibi şüpheli etkinlikleri belirlemesine izin vermek için [tehdit algılamayı yapılandırın](threat-detection-configure.md) .
- Gizli verilerinizi korumak için [dinamik veri maskeleme yapılandırın](dynamic-data-masking-configure-portal.md) .
- Yedeklemelerinizi Azure Blob depolama alanı üzerinde tutmak için bir veritabanı için [yedekleme bekletmesini yapılandırın](long-term-backup-retention-configure.md) . 
- [Coğrafi çoğaltmayı](active-geo-replication-overview.md) , veritabanınızın bir çoğaltmasını başka bir bölgede tutmak için yapılandırın.
- [Coğrafi çoğaltmalar için güvenliği yapılandırın](active-geo-replication-security-configure.md).

## <a name="monitor-and-tune-your-database"></a>Veritabanınızı izleme ve ayarlama

- [El ile ayarlama](performance-guidance.md)
- [Performansı izlemek için DMV’leri kullanma](monitoring-with-dmvs.md)
- [Performansı izlemek için Sorgu deposunu kullanma](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- Azure SQL veritabanı 'nın iş yükünüzün performansını iyileştirmelerine izin vermek için [otomatik ayarlamayı etkinleştirin](automatic-tuning-enable.md) .
- Ayarlama önerileri hakkında bilgi almak için [otomatik ayarlama için e-posta bildirimlerini etkinleştirin](automatic-tuning-email-notifications-configure.md) .
- [Performans önerilerini uygulayın](database-advisor-find-recommendations-portal.md) ve veritabanınızı iyileştirin.
- Azure SQL veritabanından bildirimleri almak için [uyarılar oluşturun](alerts-insights-configure-portal.md) .
- Uygulamalar ve veritabanı arasında bazı bağlantı sorunlarıyla karşılaşırsanız [bağlantı sorunlarını giderin](troubleshoot-common-errors-issues.md) . [Bağlantı sorunları için kaynak durumu](resource-health-to-troubleshoot-connectivity.md)de kullanabilirsiniz.
- [Akıllı İçgörüler ile performans sorunlarını giderme](intelligent-insights-troubleshoot-performance.md)
- Veritabanınızdaki depolama kullanımını izlemek için [Dosya alanını yönetin](file-space-manage.md) .
- [Akıllı İçgörüler tanılama günlüğünü kullanma](intelligent-insights-use-diagnostics-log.md)
- [Bellek İçi OLTP alanını izleme](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Genişletilmiş olaylar

- [Genişletilmiş olaylar](xevent-db-diff-from-svr.md)
- [Genişletilmiş olayları olay dosyasına depola](xevent-code-event-file.md)
- [Genişletilmiş olayları halka arabelleğine depola](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>Sorgu dağıtılmış verileri

- [Dikey olarak bölümlenmiş verileri](elastic-query-getting-started-vertical.md) birden çok veritabanı arasında sorgulayın.
- [Ölçeği genişletilmiş veri katmanı üzerinden raporla](elastic-query-horizontal-partitioning.md).
- [Farklı şemalarla tablolar arasında sorgu](elastic-query-vertical-partitioning.md).

### <a name="data-sync"></a>Veri eşitleme

- [SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md)
- [Veri Eşitleme Aracısı](sql-data-sync-agent-overview.md)
- [Şema değişikliklerini çoğaltma](sql-data-sync-update-sync-schema.md)
- [OMS ile izleme](./monitor-tune-overview.md)
- [Data Sync için en iyi deneyimler](sql-data-sync-best-practices.md)
- [Data Sync’de sorun giderme](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>Elastik Veritabanı işleri

- [Oluşturma ve yönetme](elastic-jobs-powershell-create.md) PowerShell kullanarak elastik veritabanı Işleri.
- [Oluşturma ve yönetme](elastic-jobs-tsql-create-manage.md) Transact-SQL kullanan elastik veritabanı Işleri.
- [Eski elastik Işten geçiş yapın](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Veritabanı parçalama

- [Elastik veritabanı istemci kitaplığını yükseltin](elastic-scale-upgrade-client-library.md).
- Parçalı [uygulama oluşturun](elastic-scale-get-started.md).
- [Yatay olarak parçalı verileri sorgulayın](elastic-query-getting-started.md).
- [Çok parçalı sorgular](elastic-scale-multishard-querying.md)çalıştırın.
- Parçalı [verileri taşıyın](elastic-scale-configure-deploy-split-and-merge.md).
- Veritabanı parçaları 'nda [güvenliği yapılandırma](elastic-scale-split-merge-security-configuration.md) .
- Geçerli veritabanı parçaları kümesine parça [ekleyin](elastic-scale-add-a-shard.md) .
- Parça [eşleme sorunlarını giderin](elastic-database-recovery-manager.md).
- Parçalı [DB 'Yi geçirin](elastic-convert-to-use-elastic-tools.md).
- [Sayaç oluşturun](elastic-database-perf-counters.md).
- Parçalı verileri sorgulamak için [Entity Framework 'Ü kullanın](elastic-scale-use-entity-framework-applications-visual-studio.md) .
- Parçalı verileri sorgulamak için [kaber çerçevesini kullanın](elastic-scale-working-with-dapper.md) .

## <a name="develop-applications"></a>Uygulama geliştirme

- [Bağlantı](connect-query-content-reference-guide.md#libraries)
- [Spark Bağlayıcısı kullanma](spark-connector.md)
- [Uygulamanın kimliğini doğrulama](application-authentication-get-client-id-keys.md)
- [Daha iyi performans için toplu işlem kullanma](../performance-improve-use-batching.md)
- [Bağlantı kılavuzu](troubleshoot-common-connectivity-issues.md)
- [DNS diğer adları](dns-alias-overview.md)
- [DNS diğer ad PowerShell 'i ayarla](dns-alias-powershell-create.md)
- [Bağlantı noktaları - ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C ve C++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>Uygulamaları tasarlama

- [Olağanüstü durum kurtarmaya yönelik tasarım](designing-cloud-solutions-for-disaster-recovery.md)
- [Elastik havuzlara yönelik tasarım](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Uygulama yükseltmelerine yönelik tasarım](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a>Çok kiracılı hizmet olarak yazılım (SaaS) uygulamaları tasarlama

- [SaaS tasarım desenleri](saas-tenancy-app-design-patterns.md)
- [SaaS video dizinleyicisi](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS uygulama güvenliği](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL yönetilen örneği Için nasıl yapılır kılavuzlarından](../managed-instance/how-to-content-reference-guide.md) daha fazla bilgi edinin