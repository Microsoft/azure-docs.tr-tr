---
title: İçerik başvurusunu yapılandırma & Yönet
titleSuffix: Azure SQL Managed Instance
description: Azure SQL yönetilen örneğini yapılandırmayı ve yönetmeyi öğretir içerik Kılavuzu.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 04/16/2019
ms.openlocfilehash: b7f2f060f32cf3bf92660a2e8b75a45b7ec76b82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779773"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Azure SQL yönetilen örnek içerik başvurusu
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Bu makalede, Azure SQL yönetilen örneğini yönetmenize ve yapılandırmanıza yardımcı olan çeşitli kılavuzlar, betikler ve açıklamalar için bir İçerik başvurusu bulabilirsiniz.

## <a name="load-data"></a>Veri yükleme

- [Azure SQL yönetilen örneği 'Ne geçirme](migrate-to-instance-from-sql-server.md): Azure SQL yönetilen örneği 'ne geçiş için önerilen geçiş işlemi ve araçları hakkında bilgi edinin.
- [TDE CERT 'Yi Azure SQL yönetilen örneği 'Ne geçirme](tde-certificate-migrate.md): SQL Server veritabanınız saydam veri şifrelemesi (tde) ile KORUNUYORSA, SQL yönetilen örneğinin Azure 'da geri yüklemek istediğiniz yedeğin şifresini çözmek için kullanabileceği sertifikayı geçirmeniz gerekir.
- [BACPAC’ten veritabanını içeri aktarma](../database/database-import.md)
- [Veritabanını BACPAC’e dışarı aktarma](../database/database-export.md)
- [BCP ile veri yükleme](../load-from-csv-with-bcp.md)
- [Azure Data Factory verileri yükleme](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Ağ yapılandırması

- [Alt ağ boyutunu belirleme](vnet-subnet-determine-size.md): SQL yönetilen örneği dağıtıldıktan sonra alt ağ yeniden boyutlandırılamadığı için, alt ağa dağıtmayı planladığınız yönetilen örnek sayısı ve türleri IÇIN hangi IP adresi aralığının gerektiğini hesaplamanız gerekir. 
- [Yeni VNET ve alt ağ oluşturma](virtual-network-subnet-create-arm-template.md): sanal ağı ve alt ağı [ağ gereksinimlerine](connectivity-architecture-overview.md#network-requirements)göre yapılandırın. 
- [Mevcut bir sanal ağı ve alt ağı yapılandırma](vnet-existing-add-subnet.md): SQL yönetilen örneği dağıtmak için ağ gereksinimlerini doğrulayın ve var olan sanal ağınızı ve alt ağınızı yapılandırın. 
- [Özel DNS yapılandırma](custom-dns-configure.md): özel DNS 'YI, SQL yönetilen örneğinden gelen ve bir DB posta profillerinin bağlı sunucusu aracılığıyla özel etki alanlarına erişim izni verecek şekilde yapılandırın. 
- [Eşitleme ağı yapılandırması](azure-app-sync-network-configuration.md): [uygulamanızı bir Azure sanal ağı ile tümleştirdikten](../../app-service/web-sites-integrate-with-vnet.md)sonra bağlantı kuramazsa ağ yapılandırma planını yenileyin.
- [Yönetim uç noktası IP adresini bul](management-endpoint-find-ip-address.md): SQL Managed Instance 'ın yönetim amacıyla kullandığı genel uç noktasını belirleme. 
- [Yerleşik güvenlik duvarı korumasını doğrulayın](management-endpoint-verify-built-in-firewall.md): SQL yönetilen örneğinin yalnızca gerekli bağlantı noktalarında trafiğe izin verdiğini ve diğer yerleşik güvenlik duvarı kurallarını doğrulayın. 
- [Uygulamaları bağlama](connect-application-instance.md): uygulamaları SQL yönetilen örneğine bağlamak için farklı desenler hakkında bilgi edinin.

## <a name="feature-configuration"></a>Özellik yapılandırması

- [Azure AD kimlik doğrulamasını yapılandırma](../database/authentication-aad-configure.md)
- [Koşullu erişim yapılandırma](../database/conditional-access-configure.md)
- [Çok faktörlü Azure AD kimlik doğrulaması](../database/authentication-mfa-ssms-overview.md)
- [Çok faktörlü kimlik doğrulamasını yapılandırma](../database/authentication-mfa-ssms-configure.md)
- [Zamana bağlı bekletme ilkesi yapılandırma](../database/temporal-tables-retention-policy.md)
- [KAG ile TDE Yapılandırması](../database/transparent-data-encryption-byok-configure.md)
- [TDE KAG anahtarlarını döndürme](../database/transparent-data-encryption-byok-key-rotation.md)
- [TDE koruyucuyu kaldırma](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Bellek içi OLTP’yi yapılandırma](../in-memory-oltp-configure.md)
- [Azure Otomasyonu 'Nu yapılandırma](../database/automation-manage.md)
- [İşlemsel çoğaltma](replication-between-two-instances-configure-tutorial.md) , verilerinizi yönetilen örnekler arasında veya şirket içi SQL Server Ile SQL yönetilen örneği arasında çoğaltmanıza olanak sağlar ve tam tersi de geçerlidir.
- [Tehdit algılamayı yapılandırma](threat-detection-configure.md) – [TEHDIT algılama](../database/threat-detection-overview.md) , SQL ekleme veya şüpheli konumlardan erişim gibi çeşitli olası saldırıları ALGıLAYAN yerleşik bir Azure SQL yönetilen örnek özelliğidir. 
- [Uyarı oluşturma](alerts-create.md) , SQL yönetilen ÖRNEĞI için CPU kullanımı, depolama alanı TÜKETIMI, IOPS ve diğerleri gibi izlenen ölçümler üzerinde uyarılar ayarlamanıza olanak sağlar. 

## <a name="monitoring-and-tuning"></a>İzleme ve ayarlama

- [El ile ayarlama](../database/performance-guidance.md)
- [Performansı izlemek için DMV’leri kullanma](../database/monitoring-with-dmvs.md)
- [Performansı izlemek için sorgu deposunu kullanma](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Akıllı İçgörüler ile performans sorunlarını giderme](../database/intelligent-insights-troubleshoot-performance.md)
- [Akıllı İçgörüler tanılama günlüğünü kullanın](../database/intelligent-insights-use-diagnostics-log.md)
- [In-Memory OLTP alanını izleme](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Genişletilmiş olaylar

- [Genişletilmiş olaylar](../database/xevent-db-diff-from-svr.md)
- [Genişletilmiş olayları bir olay dosyasına depolayın](../database/xevent-code-event-file.md)
- [Genişletilmiş olayları bir halka arabelleğine depolayın](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>Uyarı

- [Yönetilen örnek üzerinde uyarı oluşturma](alerts-create.md)

## <a name="operations"></a>Operations

- [SQL Yönetilen Örneği'nde kullanıcı tarafından başlatılan el ile yük devretme](user-initiated-failover.md)

## <a name="develop-applications"></a>Uygulama geliştirme

- [Bağlantı](../database/connect-query-content-reference-guide.md#libraries)
- [Spark Bağlayıcısı kullanma](../../cosmos-db/spark-connector.md)
- [Uygulamanın kimliğini doğrulama](../database/application-authentication-get-client-id-keys.md)
- [Daha iyi performans için toplu işlem kullanma](../performance-improve-use-batching.md)
- [Bağlantı kılavuzu](../database/troubleshoot-common-connectivity-issues.md)
- [DNS diğer adları](../database/dns-alias-overview.md)
- [PowerShell kullanarak bir DNS diğer adı ayarlama](../database/dns-alias-powershell-create.md)
- [Bağlantı noktaları - ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C ve C++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Uygulamaları tasarlama

- [Olağanüstü durum kurtarmaya yönelik tasarım](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Elastik havuzlara yönelik tasarım](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Uygulama yükseltmelerine yönelik tasarım](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Çok kiracılı SaaS uygulamaları tasarlama

- [SaaS tasarım desenleri](../database/saas-tenancy-app-design-patterns.md)
- [SaaS video dizinleyicisi](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS uygulama güvenliği](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Sonraki adımlar

[SQL yönetilen örneği dağıtarak](instance-create-quickstart.md)çalışmaya başlayın.