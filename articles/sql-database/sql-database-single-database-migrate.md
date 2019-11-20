---
title: Tek/havuza alınmış bir veritabanına veritabanı geçişini SQL Server
description: Azure SQL veritabanı 'nda tek bir veritabanına veya elastik bir havuza veritabanı geçişi SQL Server hakkında bilgi edinin.
keywords: veritabanı geçişi,sql server veritabanı geçişi,veritabanı taşıma araçları,veritabanı taşıma,sql veritabanı geçişi
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/11/2019
ms.openlocfilehash: df1ef21da43bc74809bd9fd71b5dde3906cdb343
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820968"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Azure SQL veritabanı 'na SQL Server veritabanı geçişi

Bu makalede, SQL Server 2005 veya üzeri bir veritabanını Azure SQL veritabanı 'nda tek veya havuza alınmış bir veritabanına geçirmeye yönelik birincil yöntemler hakkında bilgi edineceksiniz. Yönetilen bir örneğe geçiş hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı yönetilen örneği 'ne SQL Server örneğine geçirme](sql-database-managed-instance-migrate.md). Diğer platformlardan geçiş hakkında geçiş bilgileri için bkz. [Azure veritabanı geçiş kılavuzu](https://datamigration.microsoft.com/).

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Tek bir veritabanına veya havuza alınmış bir veritabanına geçirme

SQL Server 2005 veya üzeri bir veritabanını Azure SQL veritabanı 'nda tek veya havuza alınmış bir veritabanına geçirmek için iki birincil yöntem vardır. İlk yöntem basittir, ancak geçiş sırasında önemli olabilecek bazı kapalı kalma sürelerine neden olabilir. İkinci yöntem daha karmaşık olmasına karşın, geçiş sırasında kapalı kalma süresini önemli ölçüde ortadan kaldırır.

Her iki durumda da, [Data Migration Yardımcısı (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)kullanarak kaynak VERITABANıNıN Azure SQL veritabanı ile uyumlu olduğundan emin olmanız gerekir. SQL Veritabanı V12, sunucu düzeyi ve veritabanları arası işlemlerle ilgili sorunlardan başka SQL Server [özellik eşliği](sql-database-features.md) ile yaklaşılıyor. [Kısmen desteklenen veya desteklenmeyen işlevleri](sql-database-transact-sql-information.md) kullanan veritabanları ve uygulamalar için SQL Server veritabanının geçirilebilmesi için [bu uyumsuzlukların giderilmesi amacıyla yeniden mühendislik](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues) işlemlerinin yapılması gerekir.

> [!NOTE]
> Microsoft Access, Sybase, MySQL Oracle ve DB2 olmak üzere SQL Server harici veritabanlarını Azure SQL Veritabanına geçirmek için bkz. [SQL Server Geçiş Yardımcısı](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Yöntem 1: Geçiş sırasında kapalı kalma süresi ile geçiş

 Bir süre kapalı kalma süresi kazandırabilmeniz veya daha sonra geçiş için bir üretim veritabanının test geçişini gerçekleştiriyorsanız, tek veya havuza alınmış bir veritabanına geçiş yapmak için bu yöntemi kullanın. Bir öğretici için bkz. [SQL Server veritabanını geçirme](../dms/tutorial-sql-server-to-azure-sql.md).

Aşağıdaki liste, bu yöntemi kullanarak tek veya havuza alınmış bir veritabanının SQL Server veritabanı geçişinin genel iş akışını içerir. Yönetilen örneğe geçiş için bkz. [yönetilen bir örneğe geçiş](sql-database-managed-instance-migrate.md).

  ![VSSSDT geçiş şeması](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Data Migration Yardımcısı (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)en son sürümünü kullanarak veritabanını uyumluluk için [değerlendirin](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) .
2. Transact-SQL betikleri halinde tüm gerekli düzeltmeleri hazırlayın.
3. Geçirilen kaynak veritabanının işlemsel olarak tutarlı bir kopyasını oluşturun veya geçiş gerçekleşirken kaynak veritabanında gerçekleştirilen yeni işlemleri durdurur. Bu ikinci seçeneği yerine getirmek için yöntemler istemci bağlantısını devre dışı bırakmayı veya bir [veritabanı anlık görüntüsü](https://msdn.microsoft.com/library/ms175876.aspx)oluşturmayı içerir. Geçişten sonra, geçirilen veritabanlarını geçiş için kesme noktası sonrasında gerçekleşen değişikliklerle güncelleştirmek için işlemsel çoğaltmayı kullanabilirsiniz. Bkz. [Işlem geçişini kullanarak geçirme](sql-database-single-database-migrate.md#method-2-use-transactional-replication).  
4. Düzeltmeleri veritabanı kopyasına uygulamak için Transact-SQL betiklerini dağıtın.
5. Data Migration Yardımcısı kullanarak veritabanı kopyasını yeni bir Azure SQL veritabanına [geçirin](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) .

> [!NOTE]
> DMA kullanmak yerine BACPAC dosyasını da kullanabilirsiniz. Bkz. [BACPAC dosyasını yeni bir Azure SQL veritabanına aktarma](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Geçiş sırasında veri aktarımı performansını en iyi duruma getirme

Aşağıdaki liste, içeri aktarma işlemi sırasında en iyi performans için öneriler içerir.

- Bütçenizin aktarım performansını en üst düzeye çıkarması için izin verdiği en yüksek hizmet katmanını ve işlem boyutunu seçin. Geçiş tamamlandıktan sonra paradan tasarruf etmek için ölçeği azaltabilirsiniz.
- BACPAC dosyanız ile hedef veri merkezi arasındaki mesafeyi en aza indirin.
- Geçiş sırasında otomatik istatistikleri devre dışı bırakma
- Tabloları ve dizinleri bölümleme
- Dizini oluşturulmuş görünümleri bırakma ve tamamlandıktan sonra yeniden oluşturma
- Nadiren sorgulanan geçmiş verileri başka bir veritabanına kaldırın ve bu geçmiş verileri ayrı bir Azure SQL veritabanına geçirin. Daha sonra bu geçmiş verileri [esnek sorgular](sql-database-elastic-query-overview.md) kullanarak sorgulayabilirsiniz.

### <a name="optimize-performance-after-the-migration-completes"></a>Geçiş tamamlandıktan sonra performansı en iyi duruma getirme

Geçiş tamamlandıktan sonra tam tarama ile [istatistikleri güncelleştirin](https://msdn.microsoft.com/library/ms187348.aspx).

## <a name="method-2-use-transactional-replication"></a>Yöntem 2: İşlem Çoğaltma Kullanma

Geçiş gerçekleşirken SQL Server veritabanınızı üretimden kaldırmak kabul edilebilir bir durum değilse, geçiş çözümü olarak SQL Server işlem çoğaltmayı kullanabilirsiniz. Bu yöntemi kullanmak için, kaynak veritabanının [işlem çoğaltma gereksinimlerini](https://msdn.microsoft.com/library/mt589530.aspx) karşılaması ve Azure SQL Veritabanı ile uyumlu olması gerekir. Her zaman açık olan SQL çoğaltma hakkında daha fazla bilgi için bkz. [Always on kullanılabilirlik grupları Için çoğaltmayı yapılandırma (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Bu çözümü kullanmak için, Azure SQL Veritabanınızı, geçirmek istediğiniz SQL Server örneğine abone olacak şekilde yapılandırabilirsiniz. Yeni işlemler gerçekleşmeye devam ederken, işlem çoğaltma dağıtıcısı, veritabanındaki eşitlenecek verileri eşitler (yayımcı).

İşlem çoğaltma ile verilerinizde veya şemanıza yapılan tüm değişiklikler Azure SQL Veritabanınızda gösterilir. Eşitleme tamamlandıktan sonra geçişe hazır olduğunuzda, uygulamalarınızın bağlantı dizesini Azure SQL Veritabanınıza işaret edecek şekilde değiştirin. İşlem çoğaltma özelliği kaynak veritabanınızda kalan tüm değişiklikleri boşalttığında ve tüm uygulamalarınız Azure DB’yi işaret ettiğinde, işlem çoğaltma özelliğini kaldırabilirsiniz. Azure SQL Veritabanınız artık üretim sisteminizdir.

 ![SeedCloudTR diyagramı](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> İşlem çoğaltmayı, kaynak veritabanınızın bir alt kümesini geçirmek için de kullanabilirsiniz. Azure SQL Veritabanına çoğalttığınız yayın, çoğaltmakta olduğunuz veritabanındaki bir tablo alt kümesiyle sınırlanabilir. Çoğaltılmakta olan her tablo için verileri bir satır alt kümesi ve/veya sütun alt kümesi ile sınırlayabilirsiniz.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>İşlem Çoğaltma iş akışı kullanılarak SQL Veritabanına geçiş

> [!IMPORTANT]
> Microsoft Azure ve SQL Veritabanı güncelleştirmeleriyle aynı özelliklere sahip olması için SQL Server Management Studio’nun en son sürümünü kullanın. SQL Server Management Studio’nun eski sürümleri, SQL Veritabanını abone olarak ayarlayamaz. [SQL Server Management Studio’yu güncelleyin](https://msdn.microsoft.com/library/mt238290.aspx).

1. Dağıtımı Ayarlama
   - [SQL Server Management Studio (SSMS) kullanma](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   - [Transact-SQL kullanma](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)

2. Yayın Oluşturma
   - [SQL Server Management Studio (SSMS) kullanma](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   - [Transact-SQL kullanma](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Abonelik Oluşturma
   - [SQL Server Management Studio (SSMS) kullanma](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   - [Transact-SQL kullanma](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

SQL veritabanına geçiş için bazı ipuçları ve farklılıklar

- Yerel dağıtıcı kullanma
  - Bunun yapılması sunucuda bir performans etkisi oluşmasına neden olur.
  - Performans etkisi kabul edilemez boyuttaysa başka bir sunucu kullanabilirsiniz, ancak bunun yapılması yönetimi daha karmaşık hale getirir.
- Bir anlık görüntü klasörü seçerken, seçtiğiniz klasörün çoğaltmak istediğiniz her tabloya ait BCP’yi saklayacak kadar büyük olduğundan emin olun.
- Anlık görüntü oluşturma işlemi tamamlanana kadar ilişkili tabloları kilitler, bu nedenle anlık görüntünüzü uygun şekilde zamanlayın.
- Azure SQL Veritabanında yalnızca iletme abonelikleri desteklenir. Aboneleri yalnızca kaynak veritabanından ekleyebilirsiniz.

## <a name="resolving-database-migration-compatibility-issues"></a>Veritabanı geçişi uyumluluk sorunlarını çözme

Kaynak veritabanındaki SQL Server sürümüne ve geçirdiğiniz veritabanının karmaşıklığına bağlı olarak karşılaşabileceğiniz çok çeşitli uyumluluk sorunları vardır. Eski SQL Server sürümlerinde daha fazla uyumluluk sorunları algılanabilir. Aşağıdaki kaynakları kullanabilir ve ek olarak istediğiniz arama motorunu kullanarak hedefli bir İnternet araması yapabilirsiniz:

- [Azure SQL Veritabanında desteklenmeyen SQL Server veritabanı özellikleri](sql-database-transact-sql-information.md)
- [SQL Server 2016'da Artık Sağlanmayan Veritabanı Altyapısı İşlevleri](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [SQL Server 2014'te Artık Sağlanmayan Veritabanı Altyapısı İşlevleri](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [SQL Server 2012'de Artık Sağlanmayan Veritabanı Altyapısı İşlevleri](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [SQL Server 2008 R2'de Artık Sağlanmayan Veritabanı Altyapısı İşlevleri](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [SQL Server 2005'te Artık Sağlanmayan Veritabanı Altyapısı İşlevleri](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

İnternet aramasına ve bu kaynaklara ek olarak [MSDN SQL Server topluluk forumlarını](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) veya [StackOverflow](https://stackoverflow.com/) sitesini de kullanabilirsiniz.

> [!IMPORTANT]
> SQL veritabanı yönetilen örneği, mevcut bir SQL Server örneğini ve veritabanlarını bir uyumluluk sorunu olmadan en düşük düzeyde geçirmenize olanak sağlar. Bkz. [yönetilen örnek nedir](sql-database-managed-instance.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Geçiş sırasında tempdb kullanımını izlemek](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/) için Azure SQL EMEA Mühendisleri blogundaki betiği kullanın.
- [Geçiş devam ederken veritabanınızın işlem günlüğü alanını izlemek](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0) için Azure SQL EMEA Mühendisleri blogundaki betiği kullanın.
- BACPAC dosyalarını kullanarak geçiş hakkında bir SQL Server Müşteri Danışmanlık Ekibi blogu için bkz. [BACPAC Dosyalarını kullanarak SQL Server’dan Azure SQL Veritabanına Geçiş](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Geçişten sonra UTC saati ile çalışma hakkında daha fazla bilgi için bkz. [Yerel saat diliminiz için varsayılan saat dilimini değiştirme](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
- Geçişten sonra veritabanının varsayılan dilini değiştirme hakkında daha fazla bilgi için bkz. [Azure SQL Veritabanının varsayılan dilini değiştirme](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).
