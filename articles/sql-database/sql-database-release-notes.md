---
title: Sürüm Notları
description: Azure SQL veritabanı hizmetindeki ve Azure SQL veritabanı belgelerindeki yeni özellikler ve geliştirmeler hakkında bilgi edinin
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sstein
ms.openlocfilehash: 5c70d1d7d62b41965d4c81e8bbe0e5b2dc3cbb92
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166263"
---
# <a name="sql-database-release-notes"></a>SQL veritabanı sürüm notları

Bu makalede, şu anda genel önizleme aşamasında olan SQL veritabanı özellikleri listelenmektedir. SQL veritabanı güncelleştirmeleri ve geliştirmeleri için bkz. [SQL Database Service Updates](https://azure.microsoft.com/updates/?product=sql-database). Diğer Azure hizmetlerine yönelik güncelleştirmeler ve geliştirmeler için bkz. [hizmet güncelleştirmeleri](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Genel önizlemede Özellikler

### <a name="single-databasetabsingle-database"></a>[Tek veritabanı](#tab/single-database)

| Özellik | Ayrıntılar |
| ---| --- |
| Yeni Fsv2 serisi ve d serisi donanım nesilleri| Bilgi için bkz. [donanım nesilleri](sql-database-service-tiers-vcore.md#hardware-generations).|
| [Azure özel bağlantısı](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Özel Bağlantı, ağ mimarisini yalınlaştırır ve verileri Azure ağında tutarak internete maruz kalma riskini ortadan kaldırıp Azure uç noktaları arasındaki bağlantıyı güvenli bir hale getirir. Ayrıca Özel Bağlantı sayesinde Azure’da kendi hizmetlerinizi oluşturabilirsiniz. |
| Tek veritabanları ve elastik havuzlarla hızlandırılmış veritabanı kurtarma | Bilgi için bkz. [hızlandırılmış veritabanı kurtarma](sql-database-accelerated-database-recovery.md).|
|Yaklaşık sayı farklı|Daha fazla bilgi için bkz. [yaklaşık Count DISTINCT](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Rowstore 'da Batch modu (uyumluluk düzeyi 150 altında)|Bilgi için bkz. [rowstore 'Da Batch modu](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Veri bulma ve sınıflandırma  |Bilgi için bkz. [Azure SQL veritabanı ve SQL veri ambarı veri bulma & sınıflandırması](sql-database-data-discovery-and-classification.md).|
| Esnek veritabanı işleri | Bilgi için bkz. [elastik Işler oluşturma, yapılandırma ve yönetme](elastic-jobs-overview.md). |
| Esnek sorgular | Daha fazla bilgi için bkz. [elastik sorguya genel bakış](sql-database-elastic-query-overview.md). |
| Elastik işlemler | [Bulut veritabanları arasında dağıtılmış işlemler](sql-database-elastic-transactions-overview.md). |
|Bellek Izni geri bildirimi (satır modu) (uyumluluk düzeyi 150 altında)|Bilgi için bkz. [bellek verme geri bildirimi (satır modu)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Azure portal sorgu Düzenleyicisi |Bilgi için bkz. [Azure Portal SQL sorgu düzenleyicisini kullanarak bağlanma ve veri sorgulama](sql-database-connect-query-portal.md).|
| Tek veritabanları ve elastik havuzlarla R Hizmetleri/makine öğrenimi |Bilgi için bkz. [Azure SQL veritabanı 'nda Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|SQL Analytics|Bilgi için bkz. [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Tablo değişkeni ertelenmiş derlemesi (uyumluluk düzeyi 150 altında)|Bilgi için bkz. [tablo değişkeni ertelenmiş derleme](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Yönetilen örnek](#tab/managed-instance)

| Özellik | Ayrıntılar |
| ---| --- |
| <a href="/azure/sql-database/sql-database-managed-instance-connectivity-architecture#service-aided-subnet-configuration-public-preview-in-east-us-and-west-us">Hizmet destekli alt ağ yapılandırması</a> | Alt ağ yapılandırmasını yönetmenin güvenli ve kolay bir yolu. |
| <a href="/azure/sql-database/sql-database-instance-pools">Örnek havuzları</a> | Daha küçük SQL örneklerini buluta geçirmek için kullanışlı ve uygun maliyetli bir yol. |
| <a href="https://aka.ms/managed-instance-tde-byok">Kendi Anahtarını Getir (BYOK) ile saydam veri şifrelemesi (TDE)</a> |Bilgi için, bkz. [Azure SQL Saydam Veri Şifrelemesi Azure Key Vault müşteri tarafından yönetilen anahtarlar: kendi anahtarını getir desteği](transparent-data-encryption-byok-azure-sql.md).|
| <a href="https://aka.ms/managed-instance-aadlogins">Örnek düzeyi Azure AD sunucu sorumluları (oturum açmalar)</a> | <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Dış SAĞLAYıCıDAN oturum oluştur</a> ekstresini kullanarak sunucu düzeyinde oturumlar oluşturun. |
| [İşlem Çoğaltması](sql-database-managed-instance-transactional-replication.md) | Tablolardaki değişiklikleri yönetilen örneklere, tek veritabanlarına veya SQL Server örneklerine yerleştirilmiş diğer veritabanlarına çoğaltın veya diğer yönetilen örneklerde veya SQL Server örneğinde bazı satırlar değiştirildiğinde tablolarınızı güncelleştirin. Daha fazla bilgi için bkz. [Azure SQL veritabanı yönetilen örnek veritabanında çoğaltmayı yapılandırma](replication-with-sql-database-managed-instance.md). |
| Tehdit algılama |Daha fazla bilgi için bkz. [Azure SQL veritabanı yönetilen örneği 'nde tehdit algılamayı yapılandırma](sql-database-managed-instance-threat-detection.md).|
| Yönetilen örneklerle bırakılan veritabanlarını yeniden oluşturma |Daha fazla bilgi için bkz. [Azure SQL yönetilen örneği 'nde bırakılan veritabanlarını yeniden oluşturma](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="new-features"></a>Yeni özellikler

### <a name="managed-instance-h2-2019-updates"></a>Yönetilen örnek H2 2019 güncelleştirmeleri

- [Otomatik yük devretme grupları](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) , birincil örnekten diğer bir bölgedeki ikincil örneğe tüm veritabanlarını çoğaltmanıza olanak sağlar.
- Yönetilen örnek davranışınızı [Genel izleme bayraklarıyla](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)yapılandırın.

### <a name="managed-instance-h1-2019-updates"></a>Yönetilen örnek H1 2019 güncelleştirmeleri

Aşağıdaki özellikler, H1 2019 ' de yönetilen örnek dağıtım modelinde etkinleştirilmiştir:
  - <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Visual Studio aboneleri için aylık Azure kredisi</a> ve artan [bölge sınırları](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)ile abonelikler için destek.
  - <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 ve SharePoint 2019 </a> ile <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a> için destek
  - <a href="https://aka.ms/managed-instance-collation">Sunucu düzeyi harmanlama</a> ve tercih ettiğiniz <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">zaman dilimini</a> içeren örnekler oluşturun.
  - Yönetilen örnekler artık <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">yerleşik güvenlik duvarıyla</a>korunuyor.
  - Örnekleri, [genel uç noktaları](sql-database-managed-instance-public-endpoint-configure.md)kullanacak şekilde yapılandırın, <a href="https://aka.ms/four-cores-sql-mi-update">5. nesil donanım oluşturma konusunda</a> daha iyi ağ performansı elde etmek için [proxy geçersiz kılma](sql-database-connectivity-architecture.md#connection-policy) bağlantısı yapın veya en son nokta geri yükleme için <a href="https://aka.ms/managed-instance-configurable-backup-retention">35 güne kadar yedekleme tutma işlemini yapılandırın</a> . Uzun süreli yedek saklama (10 yıla kadar) hala etkin değildir, bu nedenle <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">yalnızca kopya yedeklemeleri</a> alternatif olarak kullanabilirsiniz.
  - Yeni işlevler, <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">PowerShell kullanarak veritabanınızı başka bir veri merkezine coğrafi olarak geri yükleme</a>, [veritabanını yeniden adlandırma](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [sanal kümeyi silme](sql-database-managed-instance-delete-virtual-cluster.md)olanağı sağlar.
  - Yeni yerleşik [örnek katılımcısı rolü](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) , güvenlik ilkelerine ve kurumsal standartlarla uyumluluğa sahip vergi (SOD) uyumluluğuna izin verebilir.
  - Yönetilen örnek, aşağıdaki Azure Kamu bölgelerinde (US Gov Teksas, US Gov Arizona) ve Çin Kuzey 2 ve Çin Doğu 2 ' de kullanılabilir. Ayrıca, şu ortak bölgelerde de mevcuttur: Avustralya Orta, Avustralya Orta 2, Brezilya Güney, Fransa Güney, BAE Orta, BAE Kuzey, Güney Afrika Kuzey, Güney Afrika Batı.

## <a name="fixed-known-issues"></a>Bilinen sorunlar düzeltildi

- **2019 Ağu** -kapsanan veritabanları yönetilen örnekte tam olarak desteklenmektedir.
- Adım **2019** -yerleşik zaman içinde, kaynak veritabanı bellek içi OLTP nesneleri içeriyorsa İş Açısından Kritik katmanından genel amaçlı katmanına geri yükleme başarısız olur.
- **Kas 2019** -veritabanı tutarlılığı, Azure Blob depolamadan veritabanını geri yükledikten sonra `DBCC CHECKDB` kullanılarak doğrulanır.

## <a name="updates"></a>Güncelleştirmeler

SQL veritabanı güncelleştirmelerinin ve geliştirmelerin bir listesi için bkz. [SQL Database Service Updates](https://azure.microsoft.com/updates/?product=sql-database).

Tüm Azure hizmetleri için güncelleştirmeler ve geliştirmeler için bkz. [hizmet güncelleştirmeleri](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>İçeriğe katkıda bulunma

Azure SQL veritabanı belgelerine katkıda bulunmak için [docs katılımcısı Kılavuzu](https://docs.microsoft.com/contribute/)' na bakın.
