---
title: GGenel amaçlı ve iş açısından kritik
description: Bu makalede, sanal çekirdek tabanlı satın alma modelindeki genel amaçlı ve iş açısından kritik hizmet katmanları ele alınmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/01/2019
ms.openlocfilehash: 85b610969f9e5e85d014f89b90cfd862731aaf0c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821072"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL veritabanı hizmet katmanları

Azure SQL veritabanı, bir altyapı hatası olsa bile yüzde 99,99 kullanılabilirlik sağlamak için bulut ortamı için ayarlanmış SQL Server veritabanı altyapısı mimarisini temel alır. Azure SQL veritabanı 'nda, her biri farklı bir mimari modeliyle üç hizmet katmanı kullanılır. Bu hizmet katmanları şunlardır:

- Bütçe odaklı iş yükleri için tasarlanan [genel amaçlı](sql-database-service-tier-general-purpose.md).
- Yüksek düzeyde ölçeklenebilir depolama, okuma ölçeği genişletme ve hızlı veritabanı geri yükleme özellikleri sağlayan, iş yükleri için tasarlanan [hiper ölçek](sql-database-service-tier-hyperscale.md).
- Hatalara ve hızlı yük devretmeyle yüksek dayanıklılık sağlayan düşük gecikmeli iş yükleri için tasarlanan [iş açısından kritik](sql-database-service-tier-business-critical.md).

Bu makalede, sanal çekirdek tabanlı satın alma modelindeki genel amaçlı ve iş açısından kritik hizmet katmanlarına yönelik hizmet katmanlarını, depolama ve yedekleme konularını doldurma konusunda farklar anlatılmaktadır.

## <a name="service-tier-comparison"></a>Hizmet katmanı karşılaştırması

Aşağıdaki tabloda, en son nesil (5. nesil) için hizmet katmanları arasındaki temel farklılıklar açıklanmaktadır. Hizmet katmanı özelliklerinin Tek Veritabanı ve yönetilen örnekte farklı olabileceğini unutmayın.

| | Kaynak türü | Genel Amaçlı |  Hiper Ölçek | İş Açısından Kritik |
|:---:|:---:|:---:|:---:|:---:|
| **En iyisi** | |  Bütçe odaklı dengeli işlem ve depolama seçenekleri sunar. | Birçok iş yükü. Depolama boyutunu 100 TB 'a kadar otomatik ölçeklendirme, sıvı dikey ve yatay işlem ölçekleme, hızlı veritabanı geri yükleme. | Yüksek işlem hızına sahip OLTP uygulamaları ve düşük GÇ gecikme süresi. Birden çok zaman uyumlu olarak güncellenen çoğaltmaları kullanarak hatalara en yüksek esnekliği ve hızlı yük devretme olanağı sunar.|
|  **Kaynak türünde kullanılabilir:** ||Tek veritabanı/elastik havuz/yönetilen örnek | Tek veritabanı | Tek veritabanı/elastik havuz/yönetilen örnek |
| **İşlem boyutu**|Tek veritabanı/elastik havuz | 1-80 sanal çekirdek | 1-80 sanal çekirdek | 1-80 sanal çekirdek |
| | Yönetilen örnek | 4, 8, 16, 24, 32, 40, 64, 80 Vçekirdekler | Yok | 4, 8, 16, 24, 32, 40, 64, 80 Vçekirdekler |
| | Yönetilen örnek havuzları | 2, 4, 8, 16, 24, 32, 40, 64, 80 Vçekirdekler | Yok | Yok |
| **Depolama türü** | Tümü | Premium uzak depolama (örnek başına) | Yerel SSD Önbelleği (örnek başına) ile birlikte bağlanmış depolama | Süper hızlı yerel SSD depolaması (örnek başına) |
| **Veritabanı boyutu** | Tek veritabanı/elastik havuz | 5 GB – 4 TB | 100 TB 'a kadar | 5 GB – 4 TB |
| | Yönetilen örnek  | 32 GB – 8 TB | Yok | 32 GB – 4 TB |
| **Depolama boyutu** | Tek veritabanı/elastik havuz | 5 GB – 4 TB | 100 TB 'a kadar | 5 GB – 4 TB |
| | Yönetilen örnek  | 32 GB – 8 TB | Yok | 32 GB – 4 TB |
| **TempDB boyutu** | Tek veritabanı/elastik havuz | [vCore başına 32 GB](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [vCore başına 32 GB](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [vCore başına 32 GB](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Yönetilen örnek  | [vCore başına 24 GB](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Yok | En fazla 4 TB- [sınırlı depolama boyutuna göre](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Günlüğe yazma aktarım hızı** | Tek veritabanı | [vCore başına 1,875 MB/s (en fazla 30 MB/sn)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [vCore başına 6 MB/s (en fazla 96 MB/sn)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Yönetilen örnek | [Sanal çekirdek başına 3 MB/s (en fazla 22 MB/sn)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Yok | [sanal çekirdek başına 4 MB/s (en fazla 48 MB/sn)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Kullanılabilirlik**|Tümü| %99,99 |  [bir ikincil çoğaltmayla% 99,95, daha fazla çoğaltmayla% 99,99](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | %99,99 <br/> [bölge yedekli tek veritabanı ile% 99,995](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Lerine**|Tümü|RA-GRS, 7-35 gün (varsayılan olarak 7 gün)| RA-GRS, 7 gün, sabit zaman zaman aşımı kurtarma (sür) | RA-GRS, 7-35 gün (varsayılan olarak 7 gün) |
|**Bellek içi OLTP** | | Yok | Yok | Kullanılabilir |
|**Salt okuma çoğaltmaları**| | 0  | 0 - 4 | 1 (yerleşik, fiyata dahildir) |
|**Fiyatlandırma/faturalandırma** | Tek veritabanı | [sanal çekirdek, ayrılmış depolama ve yedekleme depolaması](https://azure.microsoft.com/pricing/details/sql-database/single/) ücretlendirilir. <br/>IOPS ücretlendirilmez. | [her çoğaltma Için sanal çekirdek ve kullanılan depolama alanı](https://azure.microsoft.com/pricing/details/sql-database/single/) ücretlendirilir. <br/>IOPS henüz ücretlendirilmedi. | [sanal çekirdek, ayrılmış depolama ve yedekleme depolaması](https://azure.microsoft.com/pricing/details/sql-database/single/) ücretlendirilir. <br/>IOPS ücretlendirilmez. |
|| Yönetilen Örnek | [sanal çekirdek ve ayrılmış depolama](https://azure.microsoft.com/pricing/details/sql-database/managed/) ücretlendirilir. <br/>IOPS ücretlendirilmez.<br/>Yedekleme depolaması henüz ücretlendirilmez. | Yok | [sanal çekirdek ve ayrılmış depolama](https://azure.microsoft.com/pricing/details/sql-database/managed/) ücretlendirilir. <br/>IOPS ücretlendirilmez.<br/>Yedekleme depolaması henüz ücretlendirilmez. | 
|**İndirim modelleri**| | [Ayrılmış örnekler](sql-database-reserved-capacity.md)<br/>[Azure hibrit avantajı](sql-database-azure-hybrid-benefit.md) (geliştirme ve test aboneliklerinde kullanılamaz)<br/>[Kurumsal](https://azure.microsoft.com/offers/ms-azr-0148p/) ve [Kullandıkça Öde](https://azure.microsoft.com/offers/ms-azr-0023p/) geliştirme ve test abonelikleri| [Azure hibrit avantajı](sql-database-azure-hybrid-benefit.md) (geliştirme ve test aboneliklerinde kullanılamaz)<br/>[Kurumsal](https://azure.microsoft.com/offers/ms-azr-0148p/) ve [Kullandıkça Öde](https://azure.microsoft.com/offers/ms-azr-0023p/) geliştirme ve test abonelikleri| [Ayrılmış örnekler](sql-database-reserved-capacity.md)<br/>[Azure hibrit avantajı](sql-database-azure-hybrid-benefit.md) (geliştirme ve test aboneliklerinde kullanılamaz)<br/>[Kurumsal](https://azure.microsoft.com/offers/ms-azr-0148p/) ve [Kullandıkça Öde](https://azure.microsoft.com/offers/ms-azr-0023p/) geliştirme ve test abonelikleri|

Daha fazla bilgi için [tek veritabanındaki hizmet katmanları (sanal çekirdek)](sql-database-vcore-resource-limits-single-databases.md), [tek veritabanı havuzları (sanal çekirdek)](sql-database-dtu-resource-limits-single-databases.md), [tek veritabanı (DTU)](sql-database-dtu-resource-limits-single-databases.md), [tek veritabanı havuzları (DTU)](sql-database-dtu-resource-limits-single-databases.md)ve [yönetilen örnek](sql-database-managed-instance-resource-limits.md) arasındaki ayrıntılı farklar bölümüne bakın sayfaları.

> [!NOTE]
> Sanal çekirdek tabanlı satın alma modelindeki hiper ölçek hizmet katmanı hakkında bilgi için bkz. [hiper ölçekli hizmet katmanı](sql-database-service-tier-hyperscale.md). DTU tabanlı satın alma modeliyle sanal çekirdek tabanlı satın alma modeli karşılaştırması için bkz. [Azure SQL veritabanı satın alma modelleri ve kaynakları](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Veri ve günlük depolama

Aşağıdaki faktörler, veri ve günlük dosyaları için kullanılan depolama miktarını etkiler ve Genel Amaçlı ve İş Açısından Kritik için geçerlidir. Hiper ölçekte veri ve günlük depolama hakkındaki ayrıntılar için bkz. [hyperscale hizmet katmanı](sql-database-service-tier-hyperscale.md).

- Ayrılan depolama alanı, veri dosyaları (MDF) ve günlük dosyaları (LDF) tarafından kullanılır.
- Her tek veritabanı işlem boyutu, varsayılan en büyük boyut olan 32 GB olan en büyük veritabanı boyutunu destekler.
- Gerekli tek veritabanı boyutunu (MDF dosyasının boyutu) yapılandırdığınızda, LDF dosyalarını desteklemek için yüzde 30 daha fazla ek depolama alanı otomatik olarak eklenir.
- SQL veritabanı yönetilen örneği için depolama boyutu 32 GB 'ın katları olarak belirtilmelidir.
- 10 GB ve desteklenen maksimum boyut arasında tek bir veritabanı boyutu seçebilirsiniz.
  - Standart veya genel amaçlı hizmet katmanlarında depolama için, boyutu 10 GB 'lik artışlarla artırın veya azaltın.
  - Premium veya iş açısından kritik hizmet katmanlarında depolama için boyutu 250 GB 'lik artışlarla artırın veya azaltın.
- Genel amaçlı hizmet katmanında `tempdb` eklenmiş bir SSD kullanır ve bu depolama maliyeti sanal çekirdek fiyatına dahildir.
- İş açısından kritik hizmet katmanında `tempdb`, eklenen SSD 'yi MDF ve LDF dosyalarıyla paylaşır ve `tempdb` depolama maliyeti sanal çekirdek fiyatına dahildir.

> [!IMPORTANT]
> MDF ve LDF dosyaları için ayrılan toplam depolama alanı için ücretlendirilirsiniz.

MDF ve LDF dosyalarınızın geçerli toplam boyutunu izlemek için [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)kullanın. Bireysel MDF ve LDF dosyalarının geçerli boyutunu izlemek için [sys. database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)kullanın.

> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Yedeklemeler ve depolama

Veritabanı yedeklemeleri için depolama, SQL veritabanı 'nın zaman içinde geri yükleme (ıNR) ve [uzun süreli saklama (LTR)](sql-database-long-term-retention.md) özelliklerini desteklemek için ayrılır. Bu depolama, her veritabanı için ayrı ayrı ayrılır ve iki ayrı veritabanı başına ücret olarak faturalandırılır.

- **Sür**: ayrı veritabanı yedeklemeleri, [Okuma Erişimli Coğrafi olarak yedekli (RA-GRS) depolamaya](../storage/common/storage-designing-ha-apps-with-ragrs.md) otomatik olarak kopyalanır. Yeni yedeklemeler oluşturuldıkça depolama boyutu dinamik olarak artar. Depolama, haftalık tam yedeklemeler, günlük değişiklik yedeklemeleri ve 5 dakikada bir kopyalanmış olan işlem günlüğü yedeklemeleri tarafından kullanılır. Depolama alanı tüketimi, veritabanının değişim hızına ve yedeklemeler için Bekletme süresine bağlıdır. Her veritabanı için 7 ila 35 gün arasında ayrı bir bekletme süresi yapılandırabilirsiniz. Veritabanı boyutunun yüzde 100 ' una (1x) eşit olan minimum depolama miktarı ek bir ücret ödemeden sunulmaktadır. Çoğu veritabanı için, bu miktar 7 günlük yedeklemeleri depolamak için yeterlidir.
- **LTR**: SQL veritabanı, 10 yıla kadar tam yedeklemelerin uzun süreli bekletmesini yapılandırma seçeneği sunar. Bir LTR ilkesi ayarlarsanız, bu yedeklemeler RA-GRS depolama alanında otomatik olarak depolanır, ancak yedeklemelerin ne sıklıkla kopyalanacağını kontrol edebilirsiniz. Farklı uyumluluk gereksinimlerini karşılamak için haftalık, aylık ve/veya yıllık yedeklemeler için farklı saklama süreleri seçebilirsiniz. Seçtiğiniz yapılandırma, LTR yedeklemeleri için ne kadar Depolama kullanılacağını belirler. LTR depolama maliyetini tahmin etmek için, LTR Fiyatlandırma hesaplayıcısı ' nı kullanabilirsiniz. Daha fazla bilgi için bkz. [SQL veritabanı uzun süreli saklama](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Sonraki adımlar

- Genel amaçlı ve iş açısından kritik hizmet katmanlarında tek bir veritabanı için kullanılabilen belirli işlem boyutları ve depolama boyutları hakkında ayrıntılı bilgi için bkz. [SQL veritabanı sanal çekirdek tabanlı kaynak sınırları tek veritabanları için](sql-database-vcore-resource-limits-single-databases.md).
- Genel amaçlı ve iş açısından kritik hizmet katmanlarında elastik havuzlar için kullanılabilen belirli işlem boyutları ve depolama boyutları hakkında daha fazla bilgi için bkz. [elastik havuzlar Için SQL veritabanı sanal çekirdek tabanlı kaynak sınırları](sql-database-vcore-resource-limits-elastic-pools.md).
