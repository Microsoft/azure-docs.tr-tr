---
title: Azure SYNAPSE Analytics (eski adıyla SQL DW) sık sorulan sorular
description: Bu makalede, müşteriler ve geliştiricilerden Azure SYNAPSE Analytics (eski adıyla SQL DW) hakkında sık sorulan sorular listelenmektedir
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f7b4f926bb9611d87c67276f754a6b596850b59d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645599"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure SYNAPSE Analytics (eski adıyla SQL DW) sık sorulan sorular

## <a name="general"></a>Genel

S. Azure SYNAPSE nedir?

A. Azure SYNAPSE, veri depolama ve büyük veri analizlerini birlikte getiren sınırsız bir analiz hizmetidir. Aynı zamanda sunucusuz isteğe bağlı veya sağlanmış kaynakları kullanarak koşullarınızda verileri sorgulama özgürlüğü sunar. Azure SYNAPSE, bu iki çalışma LDS 'yi, anında ve makine öğrenimi ihtiyaçları için veri alma, hazırlama, yönetme ve sunma konularında birleştirilmiş bir deneyimle birlikte sunar. Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics nedir](sql-data-warehouse-overview-what-is.md).

S. Azure SQL veri ambarı 'na ne oldu?

A. Azure SYNAPSE, Azure SQL veri ambarı (SQL DW) gelişmiştir. Aynı sektörde önde gelen veri ambarını, yeni bir performans ve özellik düzeyinin tamamına gerçekleştirdik. Mevcut veri ambarı iş yüklerinizi Azure SYNAPSE ile bugün çalıştırmaya devam edebilir ve önizleme aşamasında olan yeni yeteneklerden otomatik olarak yararlanabilirsiniz. Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics nedir](sql-data-warehouse-overview-what-is.md).

S. SQL Analytics nedir?

A. SQL Analytics, Azure SYNAPSE ile genel kullanıma sunulan kurumsal veri ambarı özelliklerini ifade eder. Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics nedir](sql-data-warehouse-overview-what-is.md).

S. Azure SYNAPSE kullanmaya başlama Nasıl yaparım??

A. [Daha fazla bilgi Için](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html) [Azure Ücretsiz hesabıyla](https://azure.microsoft.com/free/sql-data-warehouse/) çalışmaya başlayabilir veya satışlarla iletişim kurun. 

S. Azure SYNAPSE veri güvenliği için ne sunuyor?

A. Azure SYNAPSE, TDE ve denetimi gibi verileri korumak için çeşitli çözümler sunar. Daha fazla bilgi için bkz. [güvenlik].

S. Azure SYNAPSE 'in hangi yasal veya iş standartlarından uyumlu olduğunu nereden bulabilirim?

A. SOC ve ISO gibi ürüne göre çeşitli uyumluluk teklifleri için [Microsoft uyumluluğu] sayfasını ziyaret edin. Öncelikle uyumluluk başlığına göre ' yi seçin, ardından sayfanın sağ tarafındaki Microsoft 'un kapsam içi bulut Hizmetleri bölümünde Azure ' ı genişleterek Azure SYNAPSE uyumlu olduğunu görün.

S. Power BI bağlanabilir miyim?

A. Evet! Power BI Azure SYNAPSE ile doğrudan sorguyu desteklediğinden, bu, çok sayıda kullanıcı veya gerçek zamanlı veriler için tasarlanmamıştır. Power BI performansını daha iyi hale getirebilmenizi sağlamak için, Azure Analysis Services veya Analysis Service IaaS üzerinde Power BI kullanmayı göz önünde bulundurun.

S. SQL Analytics kapasite sınırları nelerdir?

A. Geçerli [Kapasite limitleri] sayfamıza bakın. 

S. Neden Ölçeklendirmeliyim/Duraklat/devam eden uzun sürüyor?

A. Çeşitli faktörler, işlem yönetimi işlemleri için zamanı etkileyebilir. Uzun süre çalışan işlemler için ortak bir durum işlem geri alma işlemi olur. Bir ölçek veya duraklatma işlemi başlatıldığında, tüm gelen oturumlar engellenir ve sorgular kaldırılır. Sistemin kararlı durumda kalmasını sağlamak için, bir işlemin bir işlem yapılmadan önce geri alınması gerekir. İşlemin ne kadar büyük olması ve günlük boyutunun daha büyük olması, işlemin daha uzun olması sistem kararlı duruma geri yüklenmeye karşı daha uzundur.

## <a name="user-support"></a>Kullanıcı desteği

S. Bir özellik isteğim var ve nereden gönderebilirim?

A. Bir özellik isteğiniz varsa [UserVoice] sayfamıza gönderebilirsiniz

S. X 'i nasıl yapabilirim?

A. Azure SYNAPSE ile geliştirmeye yönelik yardım için [Stack Overflow] sayfamızda soru sorabilirsiniz. 

S. Nasıl yaparım? bir destek bileti mi gönder?

A. [Destek biletleri] , Azure Portal aracılığıyla dosyalanır.

## <a name="sql-languagefeature-support"></a>SQL dil/özellik desteği 

S. Hangi veri türleri destekleniyor?

A. Bkz. [veri türleri].

S. Hangi tablo özelliklerini destekliyoruz?

A. Birçok özellik desteklenir, desteklenenler desteklenmez ve [Desteklenmeyen tablo özellikleri]belgelenmiştir.

## <a name="tooling-and-administration"></a>Araç ve yönetim

S. Visual Studio 'da veritabanı projelerini destekliyoruz.

A. Şu anda Visual Studio 'da veritabanı projelerini desteklemiyoruz. Bu özelliği almak için oy dönüştürmek isterseniz, kullanıcı sesli [Veritabanı projeleri özellik isteği]ziyaret edin.

S. SQL Analytics REST API 'Leri destekliyor mu?

A. Evet. SQL veritabanıyla birlikte kullanılabilecek çoğu REST işlevi de SQL Analytics ile kullanılabilir. API bilgilerini, REST belge sayfaları veya [MSDN]içinde bulabilirsiniz.


## <a name="loading"></a>Yükleniyor

S. Hangi istemci sürücülerini destekliyoruz?

A. DW 'in sürücü desteği [bağlantı dizeleri] sayfasında bulunabilir

S: PolyBase tarafından hangi dosya biçimleri destekleniyor?

Y: Orc, RC, Parquet ve düz olarak sınırlandırılmış metin

S: PolyBase 'i kullanarak hangi veri kaynaklarına bağlanabilirim? 

Y: [Azure Data Lake Store] ve [Azure depolama Blobları]

S: Azure Storage Bloblarına veya ADLS 'e bağlanılırken hesaplama Işlemi mümkün midir? 

Y: Hayır, PolyBase yalnızca depolama bileşenleriyle etkileşime girer. 

S: HDI 'ye bağlanabilir miyim?

Y: HDI,,, bir ya da. Bu durumda, bu verileri SQL DW 'ye yükleyebilirsiniz. Ancak, HDI örneğine aşağı itme hesaplaması oluşturamazsınız. 

## <a name="next-steps"></a>Sonraki adımlar
Azure SYNAPSE hakkında bir bütün olarak daha fazla bilgi için bkz. [genel bakış] sayfası.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Bağlantı Dizeleri]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Destek biletleri]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Güvenlik]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft uyumluluğu]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[Kapasite limitleri]: ./sql-data-warehouse-service-capacity-limits.md
[veri türleri]: ./sql-data-warehouse-tables-data-types.md
[Desteklenmeyen tablo özellikleri]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure depolama Blobları]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Veritabanı projeleri özellik isteği]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Genel Bakış]: ./sql-data-warehouse-overview-faq.md
