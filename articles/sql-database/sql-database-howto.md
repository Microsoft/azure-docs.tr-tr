---
title: Yapılandırma ve yönetme
description: Azure SQL veritabanı 'nı yapılandırmayı ve yönetmeyi öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 11/14/2019
ms.openlocfilehash: e45baba74d7bf752718e5e36a148a91987cd3ffb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082424"
---
# <a name="how-to-use-azure-sql-database"></a>Azure SQL veritabanı 'nı kullanma

Bu bölümde, Azure SQL veritabanınızı yönetmenize ve yapılandırmanıza yardımcı olabilecek çeşitli kılavuzlar, betikler ve açıklamalar bulabilirsiniz. Ayrıca, [tek veritabanı](sql-database-howto-single-database.md) ve [yönetilen örnek](sql-database-howto-managed-instance.md)için özel nasıl yapılır kılavuzlarını bulabilirsiniz.

## <a name="load-data"></a>Veri yükleme

- [Azure 'da tek bir veritabanını veya havuza alınmış bir veritabanını kopyalama](sql-database-copy.md)
- [BACPAC 'den bir VERITABANıNı içeri aktarma](sql-database-import.md)
- [Bir VERITABANıNı BACPAC 'ye aktarma](sql-database-export.md)
- [BCP ile veri yükleme](sql-database-load-from-csv-with-bcp.md)
- [ADF ile veri yükleme](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Veri eşitleme

- [SQL Data Sync](sql-database-sync-data.md)
- [Veri eşitleme Aracısı](sql-database-data-sync-agent.md)
- [Şema değişikliklerini Çoğalt](sql-database-update-sync-schema.md)
- [OMS ile izleme](sql-database-sync-monitor-oms.md)
- [Veri eşitleme için en iyi uygulamalar](sql-database-best-practices-data-sync.md)
- [Veri eşitleme sorunlarını giderme](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>İzleme ve ayarlama

- [El ile ayarlama](sql-database-performance-guidance.md)
- [Performansı izlemek için DMVs 'yi kullanma](sql-database-monitoring-with-dmvs.md)
- [Performansı izlemek için sorgu deposunu kullanma](sql-database-operate-query-store.md)
- [Akıllı İçgörüler performans sorunlarını giderme](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Akıllı İçgörüler tanılama günlüğünü kullanın](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Bellek Içi OLTP alanını izleme](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Genişletilmiş olaylar

- [Genişletilmiş olaylar](sql-database-xevent-db-diff-from-svr.md)
- [Genişletilmiş olayları olay dosyasına depola](sql-database-xevent-code-event-file.md)
- [Genişletilmiş olayları halka arabelleğine depola](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Yapılandırma özellikleri

- [Azure AD kimlik doğrulamasını yapılandırma](sql-database-aad-authentication-configure.md)
- [Koşullu erişimi yapılandırma](sql-database-conditional-access.md)
- [Multi-Factor AAD kimlik doğrulaması](sql-database-ssms-mfa-authentication.md)
- [Multi-Factor auth yapılandırma](sql-database-ssms-mfa-authentication-configure.md)
- [Zamana bağlı saklama ilkesini yapılandırma](sql-database-temporal-tables-retention-policy.md)
- [BYOK ile TDE yapılandırma](transparent-data-encryption-byok-azure-sql-configure.md)
- [TDE BYOK anahtarlarını döndür](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [TDE koruyucuyu kaldır](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Bellek içi OLTP’yi yapılandırma](sql-database-in-memory-oltp-migration.md)
- [Azure Otomasyonu 'Nu yapılandırma](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Uygulama geliştirme

- [Bağlantı](sql-database-libraries.md)
- [Spark bağlayıcısını kullanma](sql-database-spark-connector.md)
- [Uygulamanın kimliğini doğrulama](sql-database-client-id-keys.md)
- [Daha iyi performans için toplu işlem kullanma](sql-database-use-batching-to-improve-performance.md)
- [Bağlantı kılavuzu](sql-database-connectivity-issues.md)
- [DNS diğer adları](dns-alias-overview.md)
- [DNS diğer ad PowerShell 'i ayarla](dns-alias-powershell.md)
- [Bağlantı noktaları - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C ve C++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Uygulamaları tasarlama

- [Olağanüstü durum kurtarma tasarımı](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Elastik havuzlar için tasarım](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Uygulama yükseltmeleri için tasarım](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Çok kiracılı SaaS uygulamaları tasarlama

- [SaaS tasarım desenleri](saas-tenancy-app-design-patterns.md)
- [SaaS video Dizin Oluşturucu](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS uygulama güvenliği](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Yönetilen örnekler Için nasıl yapılır kılavuzlarından](sql-database-howto-managed-instance.md)daha fazla bilgi edinin.
- [Tek veritabanları Için nasıl yapılır kılavuzlarından](sql-database-howto-single-database.md)daha fazla bilgi edinin.
