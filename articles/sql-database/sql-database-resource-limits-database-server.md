---
title: Sunucu kaynak sınırları
description: Bu makalede, tek veritabanları ve elastik havuzlar için Azure SQL veritabanı sunucusu kaynak sınırlarına genel bir bakış sunulmaktadır. Ayrıca, bu kaynak limitleri isabet edildiğinde veya aşıldığında ne olacağı hakkında bilgi de sağlar.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/14/2019
ms.openlocfilehash: 52e7a3408c231ba8a38fdc22c2fcac65ee26bb82
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082517"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Azure SQL veritabanı sunucusu için SQL veritabanı kaynak sınırları

Bu makalede, tek veritabanlarını ve elastik havuzları yöneten bir SQL veritabanı sunucusu için SQL veritabanı kaynak sınırlarına genel bir bakış sunulmaktadır. Ayrıca, bu kaynak limitleri isabet edildiğinde veya aşıldığında ne olacağı hakkında bilgi de sağlar.

> [!NOTE]
> Yönetilen örnek sınırları için bkz. [yönetilen örnekler Için SQL veritabanı kaynak sınırları](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>En fazla kaynak sınırı

| Kaynak | Sınır |
| :--- | :--- |
| Sunucu başına veritabanları | 5000 |
| Herhangi bir bölgedeki abonelik başına varsayılan sunucu sayısı | 20 |
| Herhangi bir bölgedeki abonelik başına en fazla sunucu sayısı | 200 |  
| Sunucu başına DTU/eDTU kotası | 54,000 |  
| sunucu/örnek başına sanal çekirdek kotası | 540 |
| Sunucu başına en fazla havuz | DTU sayısıyla veya sanal çekirdekler ile sınırlıdır. Örneğin, her havuz 1000 DTU ise, bir sunucu 54 havuzlarını destekleyebilir.|
|||

> [!NOTE]
> Varsayılan miktardan daha fazla DTU/eDTU kotası, vCore kotası veya daha fazla sunucu almak için, "Quota" sorun türüyle abonelik için Azure portal yeni bir destek isteği gönderilebilir. Sunucu başına DTU/eDTU kotası ve veritabanı sınırı, sunucu başına elastik havuz sayısını kısıtlar.
> [!IMPORTANT]
> Veritabanlarının sayısı SQL veritabanı sunucusu başına sınıra yaklaşırsa, şunlar meydana gelebilir:
>
> - Ana veritabanına karşı sorguları çalıştırırken gecikme süresini artırma.  Bu, sys. resource_stats gibi kaynak kullanımı istatistiklerinin görünümlerini içerir.
> - Sunucuda veritabanlarının listesini oluşturmayı içeren Yönetim işlemlerinde ve işleme portalı görüntüleme noktalarında gecikme süresini artırma.

### <a name="storage-size"></a>Depolama boyutu

- Tek veritabanı kaynak depolama boyutları için, fiyatlandırma katmanı başına depolama boyutu sınırları için [DTU tabanlı kaynak sınırlarına](sql-database-dtu-resource-limits-single-databases.md) veya [sanal çekirdek tabanlı kaynak sınırlarına](sql-database-vcore-resource-limits-single-databases.md) bakın.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Veritabanı kaynak sınırlarına ulaşıldığında ne olur?

### <a name="compute-dtus-and-edtus--vcores"></a>Hesaplama (DTU 'Lar ve eDTU 'lar/sanal çekirdekler)

Veritabanı işlem kullanımı (DTU 'lar ve eDTU 'lar ya da sanal çekirdekler) yüksek hale geldiğinde sorgu gecikmesi artar ve hatta zaman aşımına uğrabilirler. Bu koşullar altında, sorgular hizmet tarafından sıraya alınmış ve kaynak ücretsiz hale geldiğinde yürütme için kaynaklar sağlanabilir.
Yüksek işlem kullanımı ile karşılaşıldığında, risk azaltma seçenekleri şunlardır:

- Veritabanını daha fazla işlem kaynağı sağlamak için veritabanının veya elastik havuzun işlem boyutunu artırma. Bkz. [tek veritabanı kaynaklarını ölçeklendirme](sql-database-single-database-scale.md) ve [elastik havuz kaynaklarını ölçeklendirme](sql-database-elastic-pool-scale.md).
- Her bir sorgunun kaynak kullanımını azaltmak için sorguları en iyi duruma getirme. Daha fazla bilgi için bkz. [sorgu ayarlama/Ipuçcu](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Depolama

Kullanılan veritabanı alanı en büyük boyut sınırına ulaştığında, veri boyutunu artıran veritabanı eklemeleri ve güncelleştirmeleri [hata iletisi](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)alır. Veritabanı SEÇILIR ve sıler, başarılı olmaya devam eder.

Yüksek alan kullanımıyla karşılaşdığınızda, risk azaltma seçenekleri şunlardır:

- Veritabanının veya elastik havuzun en büyük boyutunu artırma veya daha fazla depolama alanı ekleme. Bkz. [tek veritabanı kaynaklarını ölçeklendirme](sql-database-single-database-scale.md) ve [elastik havuz kaynaklarını ölçeklendirme](sql-database-elastic-pool-scale.md).
- Veritabanı elastik bir havuztasa alternatif olarak, depolama alanı diğer veritabanlarıyla paylaşılmaması için veritabanı havuzun dışına taşınabilir.
- Kullanılmayan alanı geri kazanmak için bir veritabanını daraltın. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Oturumlar ve çalışanlar (istekler)

En fazla oturum ve çalışan sayısı, hizmet katmanı ve işlem boyutu (DTU 'Lar ve eDTU 'lar) tarafından belirlenir. Oturum veya çalışan sınırlarına ulaşıldığında yeni istekler reddedilir ve istemciler bir hata iletisi alır. Kullanılabilir bağlantı sayısı uygulama tarafından denetlenebileceği sürece, eşzamanlı çalışanların sayısı genellikle tahmine ve denetime göre daha zordur. Bu, özellikle veritabanı kaynak sınırlarına ulaşıldığında ve çalışanlar daha uzun süre çalışan sorgular nedeniyle, yoğun yük dönemlerinde geçerlidir.

Yüksek oturum veya çalışan kullanımı ile karşılaşıldığında, risk azaltma seçenekleri şunlardır:

- Veritabanı veya elastik havuzun hizmet katmanını veya işlem boyutunu artırma. Bkz. [tek veritabanı kaynaklarını ölçeklendirme](sql-database-single-database-scale.md) ve [elastik havuz kaynaklarını ölçeklendirme](sql-database-elastic-pool-scale.md).
- Artan çalışan kullanımının nedeni, işlem kaynakları için çekişme nedeniyle, her bir sorgunun kaynak kullanımını azaltmak için sorguları en iyi duruma getirme. Daha fazla bilgi için bkz. [sorgu ayarlama/Ipuçcu](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="transaction-log-rate-governance"></a>İşlem günlüğü oranı Idare

İşlem günlüğü oranı yönetimi, toplu ekleme, seçme ve dizin oluşturma gibi iş yüklerinin yüksek alım tarifelerinin sınırlandırılmasına yönelik Azure SQL veritabanı 'nda kullanılan bir işlemdir. Bu sınırlar, veri dosyalarına göre kaç IOs yayımlanabileceğine bakılmaksızın iş verimini sınırlayan, alt ikinci düzeyde günlük kaydı oluşturma hızına göre izlenir ve zorlanır.  İşlem günlüğü oluşturma ücretleri Şu anda, sanal çekirdek satın alma modeliyle maksimum günlük hızı 96 MB/sn olmasına izin verilen, donanıma bağımlı olan bir noktaya göre ölçeklendirilmeye göre ölçeklenebilir.

> [!NOTE]
> Gerçek fiziksel IOs işlem günlüğü dosyalarına yönetilmez veya sınırlı değildir.

Günlük ücretleri, çeşitli senaryolarda elde edilebilecekleri ve sürekli olarak kullanılabilir, ancak genel sistem kendi işlevlerini Kullanıcı yüküne en aza indirilerek koruyabileceğinden bu şekilde ayarlanır. Günlük hızı yönetimi, işlem günlüğü yedeklemelerinin yayımlanan kurtarmalar içinde kalmasını sağlar.  Bu idare, ikincil çoğaltmalarda aşırı biriktirme listesini de önler.

Günlük kayıtları üretildiğinden, her işlem değerlendirilir ve en fazla istenen günlük hızının (saniyede MB/s) korunması için geciktirilmesi gerekip gerekmediğini değerlendirirler. Günlük kayıtları depoya boşaltıldığınızda gecikmeler eklenmez, bunun yerine günlük hızı oluşturma sırasında günlük hızı Yönetimi uygulanır.

Çalışma zamanında uygulanan gerçek günlük oluşturma ücretleri ayrıca, geri bildirim mekanizmalarından etkilenerek, sistemin sabitlenebilmesi için izin verilen günlük hızlarını geçici olarak azaltabilirsiniz. Günlük dosyası alanı yönetimi, oturum açma alanı koşullarını ve kullanılabilirlik grubu çoğaltma mekanizmalarını çalıştırmanın genel sistem sınırlarını geçici olarak azalmasını önleme.

Günlük hızı idarecisi trafik şekillendirme, aşağıdaki bekleme türleri aracılığıyla ortaya çıkarılır ( [sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV içinde gösterilir):

| Bekleme türü | Notlar |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Veritabanı sınırlandırma |
| POOL_LOG_RATE_GOVERNOR | Havuz sınırlandırma |
| INSTANCE_LOG_RATE_GOVERNOR | Örnek düzeyi sınırlandırma |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Geri bildirim denetimi, Premium 'da kullanılabilirlik grubu fiziksel çoğaltma ve İş Açısından Kritik güncel değil |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Bir günlük alanı koşulunu önlemek için geri bildirim denetimi, oranları sınırlandırma |
|||

İstenen ölçeklenebilirliği gösteren bir günlük hızı sınırı ile karşılaşıldığında, aşağıdaki seçenekleri göz önünde bulundurun:

- Maksimum 96 MB/sn günlük hızını almak için ölçeği daha büyük bir katmana ölçeklendirin.
- Yüklenen veriler geçicidir, yani bir ETL işleminde veri hazırlama işlemi, tempdb 'ye yüklenebilir (en az bir şekilde günlüğe kaydedilir).
- Analitik senaryolar için, kümelenmiş bir columnstore kapsamına alınmış bir tabloya yükleyin. Bu, sıkıştırma nedeniyle gerekli günlük oranını azaltır. Bu teknik, CPU kullanımını artırır ve yalnızca kümelenmiş columnstore dizinlerinden faydalan veri kümelerine uygulanabilir.

## <a name="next-steps"></a>Sonraki adımlar

- Genel Azure limitleri hakkında daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-subscription-service-limits.md).
- DTU 'Lar ve eDTU 'lar hakkında daha fazla bilgi için bkz. [DTU ve eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Tempdb boyut limitleri hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
