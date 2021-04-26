---
title: Kaynak sınırları
titleSuffix: Azure SQL Managed Instance
description: Bu makalede, Azure SQL yönetilen örneği için kaynak sınırlarına genel bir bakış sunulmaktadır.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: bonova
ms.author: bonova
ms.reviewer: sstein, jovanpop, sachinp
ms.date: 09/14/2020
ms.openlocfilehash: ee0c673027892e6d3ee55b6ec5f67a30d88be683
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780353"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Azure SQL Yönetilen Örneği kaynak sınırlarına genel bakış
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Bu makalede, Azure SQL yönetilen örneği için teknik özelliklere ve kaynak sınırlarına genel bir bakış sağlanır ve bu sınırlara bir artış isteme hakkında bilgi sağlanır.

> [!NOTE]
> Desteklenen özellikler ve T-SQL deyimlerindeki farklar için bkz. [özellik farklılıkları](../database/features-comparison.md) ve [t-SQL deyimi desteği](transact-sql-tsql-differences-sql-server.md). Azure SQL veritabanı ve SQL yönetilen örneği için hizmet katmanları arasındaki genel farklılıklar için bkz. [hizmet katmanı karşılaştırması](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Donanım oluşturma özellikleri

SQL yönetilen örneği, temel altyapıyı ve mimarisine bağlı olan özelliklere ve kaynak sınırlarına sahiptir. SQL yönetilen örneği iki donanım nesile dağıtılabilir: 4. nesil ve 5. nesil. Aşağıdaki tabloda açıklandığı gibi donanım nesilleri farklı özelliklere sahiptir:

|   | **4. nesil** | **5. nesil** |
| --- | --- | --- |
| **Donanım** | Intel® E5-2673 v3 (Haswell) 2,4 GHz işlemcileri, ekli SSD sanal çekirdek = 1 PP (fiziksel çekirdek) | Intel® E5-2673 v4 (geniş) 2,3 GHz, Intel® SP-8160 (ufuk Gölü) ve Intel® 8272CL (Cascade Lake) 2,5 GHz işlemcileri, Fast NVMe SSD, vCore = 1 LP (hiper iş parçacığı) |
| **Sanal çekirdek sayısı** | 8, 16, 24 sanal çekirdek | 4, 8, 16, 24, 32, 40, 64, 80 Vçekirdekler |
| **Maksimum bellek (bellek/çekirdek oranı)** | Sanal çekirdek başına 7 GB<br/>Daha fazla bellek almak için daha fazla sanal çekirdek ekleyin. | vCore başına 5,1 GB<br/>Daha fazla bellek almak için daha fazla sanal çekirdek ekleyin. |
| **En büyük In-Memory OLTP belleği** | Örnek sınırı: vCore başına 1-1,5 GB| Örnek sınırı: vCore başına 0,8-1,65 GB |
| **En büyük örnek ayrılmış depolama alanı** |  Genel Amaçlı: 8 TB<br/>İş Açısından Kritik: 1 TB | Genel Amaçlı: 8 TB<br/> Çekirdek sayısına bağlı olarak 1 TB, 2 TB veya 4 TB İş Açısından Kritik |

> [!IMPORTANT]
> - 4. nesil donanım kullanıma alınıyor ve Yeni dağıtımlar için artık kullanılamıyor. SQL yönetilen örneğinin tüm yeni örnekleri 5. nesil donanımında dağıtılmalıdır.
> - Daha geniş bir sanal çekirdek ve depolama ölçeklenebilirliği, hızlandırılmış ağ, en iyi GÇ performansı ve en düşük gecikme süresine sahip olmak için [SQL yönetilen örneği örneğinizi Gen 5 donanımına taşımayı](../database/service-tiers-vcore.md) düşünün.

### <a name="in-memory-oltp-available-space"></a>Bellek içi OLTP kullanılabilir alanı 

[İş açısından kritik](../database/service-tier-business-critical.md) hizmet katmanındaki bellek içi OLTP alanı miktarı, sanal çekirdekler ve donanım oluşturma sayısına bağlıdır. Aşağıdaki tabloda, bellek Içi OLTP nesneleri için kullanılabilecek bellek sınırları listelenmektedir.

| Bellek içi OLTP alanı  | **5. nesil** | **4. nesil** |
| --- | --- | --- |
| 4 sanal çekirdek  | 3,14 GB | |   
| 8 sanal çekirdek  | 6,28 GB | 8 GB |
| 16 sanal çekirdek | 15,77 GB | 20 GB |
| 24 sanal çekirdek | 25,25 GB | 36 GB |
| 32 sanal çekirdekler | 37,94 GB | |
| 40 sanal çekirdekler | 52,23 GB | |
| 64 sanal çekirdekler | 99,9 GB    | |
| 80 sanal çekirdekler | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Hizmet katmanı özellikleri

SQL yönetilen örneği iki hizmet katmanına sahiptir: [genel amaçlı](../database/service-tier-general-purpose.md) ve [iş açısından kritik](../database/service-tier-business-critical.md). Bu katmanlar, aşağıdaki tabloda açıklandığı gibi [farklı yetenekler](../database/service-tiers-general-purpose-business-critical.md)sağlar.

> [!Important]
> İş Açısından Kritik hizmet katmanı, salt okunurdur iş yükü için kullanılabilecek SQL yönetilen örneği 'nin (ikincil çoğaltma) ek yerleşik bir kopyasını sağlar. Okuma-yazma sorgularını ve salt okunurdur/analitik/raporlama sorgularını ayırabiliyorsanız, aynı fiyata ait sanal çekirdekler ve belleğin iki katına alınması gerekir. İkincil çoğaltma, birincil örneğinin arkasında birkaç saniye geciktelebilirler, bu nedenle, tam olarak geçerli veri durumunun gerekli olmadığı raporlama/analiz iş yüklerini boşaltmak üzere tasarlanmıştır. Aşağıdaki tabloda, salt yazılır **sorgular** , ikincil çoğaltmada yürütülen sorgulardır.

| **Özellik** | **Genel Amaçlı** | **İş Açısından Kritik** |
| --- | --- | --- |
| Sanal çekirdek sayısı\* | 4. nesil: 8, 16, 24<br/>5. nesil: 4, 8, 16, 24, 32, 40, 64, 80 | 4. nesil: 8, 16, 24 <br/> 5. nesil: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Salt okuma sorguları için aynı sayıda sanal çekirdek ayrılmış. |
| Maksimum bellek | 4. nesil: 56 GB-168 GB (7GB/sanal çekirdek)<br/>5. nesil: 20,4 GB-408 GB (5.1 GB/sanal çekirdek)<br/>Daha fazla bellek almak için daha fazla sanal çekirdek ekleyin. | 4. nesil: 56 GB-168 GB (7GB/sanal çekirdek)<br/>Okuma-yazma sorguları için 5. nesil: 20,4 GB-408 GB (5.1 GB/vCore)<br/>+ salt okuma sorguları için + ek 20,4 GB-408 GB (5.1 GB/vCore).<br/>Daha fazla bellek almak için daha fazla sanal çekirdek ekleyin. |
| En büyük örnek depolama boyutu (ayrılmış) | 4 sanal çekirdek için-2 TB (yalnızca 5. nesil)<br/>-8 TB diğer boyutlar için | 4. nesil: 1 TB <br/> 5. nesil <br/>-1 TB, 4, 8, 16 sanal çekirdek<br/>-2 TB, 24 sanal çekirdek için<br/>-4 TB 32, 40, 64, 80 sanal çekirdekler |
| Maks. veritabanı boyutu | Şu anda kullanılabilir örnek boyutu (sanal çekirdek sayısına bağlı olarak en fazla 2 TB-8 TB). | Şu anda kullanılabilir örnek boyutu (sanal çekirdek sayısına bağlı olarak en fazla 1 TB-4 TB). |
| En fazla tempDB boyutu | 24 GB/sanal çekirdek (96-1.920 GB) ile sınırlıdır ve şu anda kullanılabilir örnek depolama boyutu.<br/>Daha fazla TempDB alanı almak için daha fazla sanal çekirdek ekleyin.<br/> Günlük dosyası boyutu 120 GB ile sınırlıdır.| Şu anda kullanılabilir örnek depolama boyutuna kadar. |
| Örnek başına en fazla veritabanı sayısı | 100 kullanıcı veritabanları, örnek depolama boyutu sınırına ulaşılmadığı müddetçe. | 100 kullanıcı veritabanları, örnek depolama boyutu sınırına ulaşılmadığı müddetçe. |
| Örnek başına en fazla veritabanı dosyası sayısı | Örnek depolama boyutu veya [Azure Premium disk depolama ayırma alanı](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) sınırına ulaşılmadığı takdirde 280 'e kadar. | örnek depolama boyutu sınırına ulaşılmadığı takdirde veritabanı başına 32.767 dosya. |
| En büyük veri dosyası boyutu | Şu anda kullanılabilir örnek depolama boyutu (en fazla 2 TB-8 TB) ve [Azure Premium disk depolama alanı ayırma alanı](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files)ile sınırlıdır. | Şu anda kullanılabilir örnek depolama boyutuyla sınırlıdır (en fazla 1 TB-4 TB). |
| Günlük dosyası boyutu üst sınırı | 2 TB ile sınırlıdır ve şu anda kullanılabilir örnek depolama boyutu. | 2 TB ile sınırlıdır ve şu anda kullanılabilir örnek depolama boyutu. |
| Veri/günlük ıOPS (yaklaşık) | Örnek başına en fazla 30-40 K ıOPS *, 500-dosya başına 7500<br/>\*[Daha fazla ıOPS almak için dosya boyutunu artırın](#file-io-characteristics-in-general-purpose-tier)| 16 k-320 K (4000 ıOPS/vCore)<br/>Daha iyi GÇ performansı almak için daha fazla sanal çekirdek ekleyin. |
| Günlük yazma verimlilik sınırı (örnek başına) | Sanal çekirdek başına 3 MB/s<br/>Örnek başına en fazla 120 MB/sn<br/>VERITABANı başına 22-65 MB/s<br/>\*[Daha iyi GÇ performansı almak için dosya boyutunu artırın](#file-io-characteristics-in-general-purpose-tier) | vCore başına 4 MB/s<br/>En fazla 96 MB/sn |
| Veri işleme (yaklaşık) | dosya başına 100-250 MB/s<br/>\*[Daha iyi GÇ performansı almak için dosya boyutunu artırın](#file-io-characteristics-in-general-purpose-tier) | Sınırlı değildir. |
| Depolama GÇ gecikmesi (yaklaşık) | 5-10 MS | 1-2 MS |
| Bellek içi OLTP | Desteklenmez | Kullanılabilir, [Boyut sanal çekirdek sayısına bağlıdır](#in-memory-oltp-available-space) |
| En fazla oturum sayısı | 30000 | 30000 |
| Maksimum eş zamanlı çalışan (istek) | 4\. Nesil: 210 * sanal çekirdek sayısı + 800<br>5\. Nesil: 105 * sanal çekirdek sayısı + 800 | 4\. Nesil: 210 * sanal çekirdek sayısı + 800<br>5\. Nesil: 105 * sanal çekirdek sayısı + 800 |
| [Salt okuma çoğaltmaları](../database/read-scale-out.md) | 0 | 1 (fiyata dahildir) |
| İşlem yalıtımı | Genel Amaçlı örnekleri diğer örneklerle fiziksel donanımı paylaşaolabileceğinden 5. nesil desteklenmez<br/>Kullanımdan kaldırılması nedeniyle 4. nesil desteklenmiyor|5. nesil<br/>-40, 64, 80 sanal çekirdekler için desteklenir<br/>-diğer boyutlar için desteklenmez<br/><br/>Kullanımdan kaldırılması nedeniyle 4. nesil desteklenmiyor|


Birkaç ek dikkat edin: 

- **Şu anda kullanılabilir örnek depolama boyutu** , ayrılmış örnek boyutu ve kullanılan depolama alanı arasındaki farktır.
- Kullanıcı ve sistem veritabanlarındaki veri ve günlük dosyası boyutu, en büyük depolama boyutu sınırı ile karşılaştırılan örnek depolama boyutuna dahildir. Veritabanlarına göre kullanılan toplam alanı öğrenmek için [sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) sistem görünümünü kullanın. Hata günlükleri kalıcı değil ve boyutuna dahil edilmez. Yedeklemeler depolama boyutuna dahil değildir.
- Genel Amaçlı katmanındaki aktarım hızı ve ıOPS, SQL yönetilen örneği tarafından açıkça sınırlı olmayan [dosya boyutuna](#file-io-characteristics-in-general-purpose-tier) da bağlıdır.
  [Otomatik yük devretme gruplarını](../database/auto-failover-group-configure.md) kullanarak farklı bir Azure bölgesinde başka bir okunabilir çoğaltma oluşturabilirsiniz
- En büyük örnek ıOPS, iş yükünün dosya düzenine ve dağıtımına bağlıdır. Örnek olarak, her biri 500 ıOPS ile en fazla 5 k ıOPS ve 7 küçük dosya (128 GB 'tan küçük) ile 7 x 1TB dosya oluşturursanız, iş yükünüz tüm dosyaları kullanıyorsa örnek başına 38500 ıOPS (7x5000 + 7x500) alabilirsiniz. Bazı ıOPS 'nin otomatik yedeklemeler için de kullanıldığını unutmayın.

[Bu MAKALEDEKI SQL yönetilen örnek havuzlarındaki kaynak limitleri](instance-pools-overview.md#resource-limitations)hakkında daha fazla bilgi bulabilirsiniz.

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Genel Amaçlı katmanındaki dosya GÇ özellikleri

Genel Amaçlı hizmet katmanında her veritabanı dosyası, dosya boyutuna bağlı olan adanmış ıOPS ve aktarım hızını alır. Daha büyük dosyalar daha fazla ıOPS ve aktarım hızı alır. Veritabanı dosyalarının GÇ özellikleri aşağıdaki tabloda gösterilmiştir:

| Dosya boyutu | >= 0 ve <= 128 GiB | >128 ve <= 512 GiB | >0,5 ve <= 1 TiB    | >1 ve <= 2 TiB    | >2 ve <= 4 TiB | >4 ve <= 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Dosya başına ıOPS       | 500   | 2300              | 5000              | 7.500              | 7.500              | 12,500   |
| Dosya başına aktarım hızı | 100 MIB/sn | 150 MIB/sn | 200 MIB/sn | 250 MiB/sn | 250 MiB/sn | 480 MIB/sn | 

Bazı veritabanı dosyasında yüksek GÇ gecikme süresi fark ederseniz veya ıOPS/aktarım hızının sınıra ulaşdığına görürseniz, [dosya boyutunu artırarak](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)performansı artırabilirsiniz.

Ayrıca, en büyük günlük yazma aktarım hızına (örneğin, 22 MB/sn) ait bir örnek düzeyi sınırı vardır. bu nedenle, örnek aktarım hızı sınırına ulaştığınız için günlük dosyasında en fazla dosya ile iletişime geçemeyebilirsiniz.

## <a name="supported-regions"></a>Desteklenen bölgeler

SQL yönetilen örneği yalnızca [desteklenen bölgelerde](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)oluşturulabilir. Şu anda desteklenmeyen bir bölgede SQL yönetilen örneği oluşturmak için [Azure Portal aracılığıyla bir destek isteği gönderebilirsiniz](../database/quota-increase-request.md).

## <a name="supported-subscription-types"></a>Desteklenen abonelik türleri

SQL yönetilen örneği şu anda yalnızca aşağıdaki abonelik türlerinde dağıtımı desteklemektedir:

- [Kurumsal Anlaşma (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Kullandıkça öde](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Bulut hizmeti sağlayıcısı (CSP)](/partner-center/csp-documents-and-learning-resources)
- [Kurumsal Geliştirme ve Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Kullandıkça Öde Geliştirme ve Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio aboneleri için aylık Azure kredisine sahip abonelikler](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Bölgesel kaynak sınırlamaları

> [!Note]
> Abonelikler için bölge kullanılabilirliği hakkında en son bilgiler için önce [bir bölge seçin](https://aka.ms/sqlcapacity)' i işaretleyin.

Desteklenen Abonelik türleri, bölge başına sınırlı sayıda kaynak içerebilir. SQL yönetilen örneği, Azure bölgesi başına iki varsayılan sınıra sahiptir (bir abonelik türü türüne göre Azure portal özel bir [destek isteği](../database/quota-increase-request.md) oluşturarak isteğe bağlı olarak artırılabilir:

- **Alt ağ sınırı**: SQL yönetilen örnek örneklerinin tek bir bölgede dağıtıldığı alt ağların en fazla sayısı.
- **Vcore birim sınırı**: tek bir bölgedeki tüm örneklerde dağıtılabilecek en fazla Vcore birimi sayısı. Bir GP sanal çekirdeği bir vCore birimi kullanır ve bir BC sanal çekirdek 4 sanal çekirdek birimi alır. Toplam örnek sayısı, sanal çekirdek birim sınırının içinde olduğu sürece sınırlı değildir.

> [!Note]
> Bu sınırlar varsayılan ayarlar değildir ve teknik sınırlamalardır. Geçerli bölgede daha fazla örneğe ihtiyaç duyuyorsanız, Azure portal özel bir [destek isteği](../database/quota-increase-request.md) oluşturularak sınırlar artırılabilir. Alternatif olarak, destek istekleri göndermeden başka bir Azure bölgesinde SQL yönetilen örneğinin yeni örneklerini oluşturabilirsiniz.

Aşağıdaki tabloda desteklenen Abonelik türleri için **varsayılan bölgesel sınırlar** gösterilmektedir (varsayılan sınırlar aşağıda açıklanan destek isteği kullanılarak genişletilebilir):

|Abonelik türü| Maksimum SQL yönetilen örnek alt ağı sayısı | Maksimum vCore birimi sayısı * |
| :---| :--- | :--- |
|Kullandıkça öde|3|320|
|CSP |8 (bazı bölgelerde 15 * *)|960 (bazı bölgelerde 1440 * *)|
|Kullandıkça Öde geliştirme ve test|3|320|
|Kurumsal Geliştirme ve Test|3|320|
|EA|8 (bazı bölgelerde 15 * *)|960 (bazı bölgelerde 1440 * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional ve MSDN Platformları|2|32|

\* Dağıtımları planlama bölümünde, lütfen İş Açısından Kritik (BC) hizmet katmanının dört (4) kat daha fazla sanal çekirdek kapasitesi Genel Amaçlı (GP) hizmet katmanından gerektirdiğini göz önünde bulundurun. Örneğin: 1 GP vCore = 1 sanal çekirdek birim ve 1 BC sanal çekirdek = 4 sanal çekirdek. Tüketim analizinizi varsayılan sınırlara karşı basitleştirmek için, SQL yönetilen örneğinin dağıtıldığı bölgedeki tüm alt ağlarda vCore birimlerini özetleyin ve sonuçları abonelik türü için örnek birim sınırlarıyla karşılaştırın. Bir bölgedeki her abonelik için **en fazla vCore birimi** sınırı geçerlidir. Birden çok alt ağ arasında dağıtılan tüm sanal çekirdekler toplamı, **en fazla sanal çekirdek birimi sayısına** eşit veya daha düşük olmalıdır.

\*\* Daha büyük alt ağ ve sanal çekirdek limitleri şu bölgelerde kullanılabilir: Avustralya Doğu, Doğu ABD, Doğu ABD 2, Kuzey Avrupa, Orta Güney ABD, Güneydoğu Asya, UK Güney, Batı Avrupa, Batı ABD 2.

> [!IMPORTANT]
> VCore ve alt ağ sınırlarınızın 0 olması durumunda, abonelik türü için varsayılan bölgesel sınırın ayarlanmayacağı anlamına gelir. Aynı yordamın yanı sıra gerekli sanal çekirdek ve alt ağ değerlerini sağlayan belirli bir bölgede abonelik erişimi almak için kota artışı isteği 'ni de kullanabilirsiniz.

## <a name="request-a-quota-increase"></a>Kota artışı iste

Geçerli Bölgelerinizdeki daha fazla örneğe ihtiyacınız varsa, Azure portal kullanarak kotayı uzatmak için bir destek isteği gönderin. Daha fazla bilgi için bkz. [Azure SQL veritabanı Için istek kotası artışları](../database/quota-increase-request.md).

## <a name="next-steps"></a>Sonraki adımlar

- SQL yönetilen örneği hakkında daha fazla bilgi için bkz. [SQL yönetilen örneği nedir?](sql-managed-instance-paas-overview.md).
- Fiyatlandırma bilgileri için bkz. [SQL yönetilen örnek fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- İlk SQL yönetilen örneğinizi oluşturmayı öğrenmek için [hızlı başlangıç kılavuzuna](instance-create-quickstart.md)bakın.
