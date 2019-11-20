---
title: Kaynak sınırları-yönetilen örnek
description: Bu makalede yönetilen örnekler için Azure SQL veritabanı kaynak sınırlarına genel bir bakış sunulmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 10/02/2019
ms.openlocfilehash: 08e378c4b5f6d8e17ac67a80401edf69611c0a13
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823331"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Azure SQL veritabanı yönetilen örneği kaynak sınırlarına genel bakış

Bu makalede, Azure SQL veritabanı yönetilen örneği için teknik özelliklere ve kaynak sınırlarına genel bir bakış sağlanır ve bu sınırlara bir artış isteme hakkında bilgi sağlanır.

> [!NOTE]
> Desteklenen özellikler ve T-SQL deyimlerindeki farklar için bkz. [özellik farklılıkları](sql-database-features.md) ve [t-SQL deyimi desteği](sql-database-managed-instance-transact-sql-information.md). Tek veritabanı ve yönetilen örnekteki hizmet katmanları arasında genel farklıara için bkz. [hizmet katmanı karşılaştırması](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Donanım oluşturma özellikleri

Yönetilen örnek, temel altyapıyı ve mimarisine bağlı olan özelliklere ve kaynak sınırlarına sahiptir. Azure SQL veritabanı yönetilen örneği iki donanım nesile dağıtılabilir: 4. nesil ve 5. nesil. Aşağıdaki tabloda açıklandığı gibi donanım nesilleri farklı özelliklere sahiptir:

|   | **4. nesil** | **5. nesil** |
| --- | --- | --- |
| Donanım | Intel E5-2673 v3 (Haswell) 2,4-GHz işlemciler, ekli SSD sanal çekirdek = 1 PP (fiziksel çekirdek) | Intel E5-2673 v4 (çok Iyi) 2,3-GHz işlemcileri, Fast NVMe SSD, sanal çekirdek = 1 LP (hiper iş parçacığı) |
| Sanal çekirdek sayısı | 8, 16, 24 sanal çekirdek | 4, 8, 16, 24, 32, 40, 64, 80 Vçekirdekler |
| Maksimum bellek (bellek/çekirdek oranı) | Sanal çekirdek başına 7 GB<br/>Daha fazla bellek almak için daha fazla sanal çekirdek ekleyin. | vCore başına 5,1 GB<br/>Daha fazla bellek almak için daha fazla sanal çekirdek ekleyin. |
| Maks. bellek Içi OLTP belleği | Örnek sınırı: vCore başına 1-1,5 GB| Örnek sınırı: vCore başına 0,8-1,65 GB |
| En büyük örnek ayrılmış depolama alanı |  Genel Amaçlı: 8 TB<br/>İş Açısından Kritik: 1 TB | Genel Amaçlı: 8 TB<br/> Çekirdek sayısına bağlı olarak 1 TB, 2 TB veya 4 TB İş Açısından Kritik |

> [!IMPORTANT]
> - 4\. nesil donanım kullanıma alınıyor. 5. nesil donanımında yeni yönetilen örnekler dağıtmanız önerilir.
> - 4\. nesil donanım Şu anda yalnızca şu bölgelerde kullanılabilir: Kuzey Avrupa, Batı Avrupa, Doğu ABD, Orta Güney ABD, Orta Kuzey ABD, Batı ABD 2, Orta ABD, Kanada Orta, Güney Hindistan, Güneydoğu Asya ve Kore Orta.

### <a name="in-memory-oltp-available-space"></a>Bellek içi OLTP kullanılabilir alanı 

[İş açısından kritik](sql-database-service-tier-business-critical.md) hizmet katmanındaki bellek içi OLTP alanı miktarı, sanal çekirdekler ve donanım oluşturma sayısına bağlıdır. Aşağıdaki tabloda, bellek Içi OLTP nesneleri için kullanılabilecek bellek sınırları listelenmiştir.

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

Yönetilen örnekte iki hizmet katmanı vardır: [genel amaçlı](sql-database-service-tier-general-purpose.md) ve [iş açısından kritik](sql-database-service-tier-business-critical.md). Bu katmanlar, aşağıdaki tabloda açıklandığı gibi [farklı yetenekler](sql-database-service-tiers-general-purpose-business-critical.md)sağlar.

> [!Important]
> İş Açısından Kritik hizmet katmanı, salt okuma iş yükü için kullanılabilen örneğin ek yerleşik kopyasını (ikincil çoğaltma) sağlar. Okuma-yazma sorgularını ve salt okunurdur/analitik/raporlama sorgularını ayırabiliyorsanız, aynı fiyat için iki sanal çekirdek ve bellek elde edersiniz. İkincil çoğaltma, birincil örnekten birkaç saniye daha fazla gecikme gösterebilir ve bu nedenle, tam olarak geçerli veri durumunu gerektirmeyen raporlama/analiz iş yükünü devretmek üzere tasarlanmıştır. Aşağıdaki tabloda, salt yazılır **sorgular** , ikincil çoğaltmada yürütülen sorgulardır.

| **Özellik** | **Genel Amaçlı** | **İş Açısından Kritik** |
| --- | --- | --- |
| Sanal çekirdek sayısı\* | 4\. nesil: 8, 16, 24<br/>5\. nesil: 4, 8, 16, 24, 32, 40, 64, 80 | 4\. nesil: 8, 16, 24 <br/> 5\. nesil: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*aynı sayıda sanal çekirdek salt okuma sorguları için ayrılmıştır. |
| Maksimum bellek | 4\. nesil: 56 GB-168 GB (7GB/sanal çekirdek)<br/>5\. nesil: 20,4 GB-408 GB (5.1 GB/sanal çekirdek)<br/>Daha fazla bellek almak için daha fazla sanal çekirdek ekleyin. | 4\. nesil: 56 GB-168 GB (7GB/sanal çekirdek)<br/>Okuma-yazma sorguları için 5. nesil: 20,4 GB-408 GB (5.1 GB/vCore)<br/>+ salt okuma sorguları için + ek 20,4 GB-408 GB (5.1 GB/vCore).<br/>Daha fazla bellek almak için daha fazla sanal çekirdek ekleyin. |
| En büyük örnek depolama boyutu (ayrılmış) | 4 sanal çekirdek için-2 TB (yalnızca 5. nesil)<br/>-8 TB diğer boyutlar için | 4\. nesil: 1 TB <br/> 5\. nesil <br/>-1 TB, 4, 8, 16 sanal çekirdek<br/>-2 TB, 24 sanal çekirdek için<br/>-4 TB 32, 40, 64, 80 sanal çekirdekler |
| Maks. veritabanı boyutu | Şu anda kullanılabilir örnek boyutu (sanal çekirdek sayısına bağlı olarak en fazla 2 TB-8 TB). | Şu anda kullanılabilir örnek boyutu (sanal çekirdek sayısına bağlı olarak en fazla 1 TB-4 TB). |
| En fazla tempDB boyutu | 24 GB/sanal çekirdek (96-1.920 GB) ile sınırlıdır ve şu anda kullanılabilir örnek depolama boyutu.<br/>Daha fazla TempDB alanı almak için daha fazla sanal çekirdek ekleyin.<br/> Günlük dosyası boyutu 120 GB ile sınırlıdır.| Şu anda kullanılabilir örnek depolama boyutuna kadar. |
| Örnek başına en fazla veritabanı sayısı | 100, örnek depolama boyutu sınırına ulaşılmadığı müddetçe. | 100, örnek depolama boyutu sınırına ulaşılmadığı müddetçe. |
| Örnek başına en fazla veritabanı dosyası sayısı | Örnek depolama boyutu veya [Azure Premium disk depolama ayırma alanı](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files) sınırına ulaşılmadığı takdirde 280 'e kadar. | örnek depolama boyutu sınırına ulaşılmadığı takdirde veritabanı başına 32.767 dosya. |
| En büyük veri dosyası boyutu | Şu anda kullanılabilir örnek depolama boyutu (en fazla 2 TB-8 TB) ve [Azure Premium disk depolama alanı ayırma alanı](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files)ile sınırlıdır. | Şu anda kullanılabilir örnek depolama boyutuyla sınırlıdır (en fazla 1 TB-4 TB). |
| Günlük dosyası boyutu üst sınırı | 2 TB ile sınırlıdır ve şu anda kullanılabilir örnek depolama boyutu. | 2 TB ile sınırlıdır ve şu anda kullanılabilir örnek depolama boyutu. |
| Veri/günlük ıOPS (yaklaşık) | Örnek başına en fazla 30-40 K ıOPS *, 500-dosya başına 7500<br/>[daha fazla IOPS almak için dosya boyutunu \*artırın](#file-io-characteristics-in-general-purpose-tier)| 5,5 k-110 K (1375 ıOPS/sanal çekirdek)<br/>Daha iyi GÇ performansı almak için daha fazla sanal çekirdek ekleyin. |
| Günlük yazma verimlilik sınırı (örnek başına) | Sanal çekirdek başına 3 MB/s<br/>En fazla 22 MB/sn | vCore başına 4 MB/s<br/>En fazla 48 MB/sn |
| Veri işleme (yaklaşık) | dosya başına 100-250 MB/s<br/>[daha ıyı GÇ performansı almak için dosya boyutunu \*artırın](#file-io-characteristics-in-general-purpose-tier) | Sınırlı değildir. |
| Depolama GÇ gecikmesi (yaklaşık) | 5-10 MS | 1-2 MS |
| Bellek içi OLTP | Desteklenmiyor | Kullanılabilir, [Boyut sanal çekirdek sayısına bağlıdır](#in-memory-oltp-available-space) |
| En fazla oturum sayısı | 30000 | 30000 |
| [Salt okuma çoğaltmaları](sql-database-read-scale-out.md) | 0 | 1 (fiyata dahildir) |

> [!NOTE]
> - **Şu anda kullanılabilir örnek depolama boyutu** , ayrılmış örnek boyutu ve kullanılan depolama alanı arasındaki farktır.
> - Kullanıcı ve sistem veritabanlarındaki veri ve günlük dosyası boyutu, en büyük depolama boyutu sınırı ile karşılaştırılan örnek depolama boyutuna dahildir. Veritabanlarına göre kullanılan toplam alanı öğrenmek için <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys. master_files</a> sistem görünümünü kullanın. Hata günlükleri kalıcı değil ve boyutuna dahil edilmez. Yedeklemeler depolama boyutuna dahil değildir.
> - Genel Amaçlı katmanındaki aktarım hızı ve ıOPS, yönetilen örnekle açıkça sınırlı olmayan [dosya boyutuna](#file-io-characteristics-in-general-purpose-tier) da bağlıdır.
> - Farklı Azure bölgesinde, otomatik yük devretme grupları kullanarak başka bir okunabilir çoğaltma oluşturabilirsiniz.
> - En büyük örnek ıOPS, iş yükünün dosya düzenine ve dağıtımına bağlıdır. Örnek olarak, her biri 500 ıOPS ile en fazla 5 k ıOPS ve 7 küçük dosya (128 GB 'tan küçük) ile 7 x 1GB dosyalar oluşturursanız, iş yükünüz tüm dosyaları kullanıyorsa örnek başına 38500 ıOPS (7x500 0 + 7x500) alabilirsiniz. Bazı ıOPS miktarının otomatik yedeklemeler için de kullanıldığını unutmayın.

> [!NOTE]
> [Bu makaledeki yönetilen örnek havuzlardaki kaynak limitleri](sql-database-instance-pools.md#instance-pools-resource-limitations)hakkında daha fazla bilgi bulabilirsiniz.

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Genel Amaçlı katmanındaki dosya GÇ özellikleri

Genel Amaçlı hizmet katmanında her veritabanı dosyası, dosya boyutuna bağlı olarak adanmış ıOPS ve aktarım hızı alıyor. Daha büyük dosyalar daha fazla ıOPS ve aktarım hızı alıyor. Veritabanı dosyalarının GÇ özellikleri aşağıdaki tabloda gösterilmiştir:

| Dosya boyutu           | 0-128 GiB | 128-256 GiB | 256-512 GiB | 0,5-1 TiB    | 1-2 TiB    | 2-4 TiB | 4-8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Dosya başına ıOPS       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12,500   |
| Dosya başına aktarım hızı | 100 MIB/sn | 125 MIB/sn | 150 MIB/sn | 200 MIB/sn | 250 MIB/sn | 250 MIB/sn | 480 MIB/sn | 

Bazı veritabanı dosyasında yüksek GÇ gecikme süresi fark ederseniz veya ıOPS/aktarım hızının sınıra ulaşdığına görürseniz, [dosya boyutunu artırarak](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)performansı artırabilirsiniz.

Ayrıca, en fazla günlük yazma miktarı 22 MB/sn gibi örnek düzeyi sınırlar da vardır. bu nedenle, örnek işleme sınırına ulaştığınız için günlük dosyası genelinde dosyaya ulaşamamanız mümkün olmayabilir.

## <a name="supported-regions"></a>Desteklenen bölgeler

Yönetilen örnekler yalnızca [desteklenen bölgelerde](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)oluşturulabilir. Şu anda desteklenmeyen bir bölgede yönetilen bir örnek oluşturmak için, [Azure Portal aracılığıyla bir destek isteği gönderebilirsiniz](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Desteklenen abonelik türleri

Yönetilen örnek şu anda yalnızca aşağıdaki abonelik türlerinde dağıtımı desteklemektedir:

- [Kurumsal Anlaşma (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Kullandıkça öde](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Bulut hizmeti sağlayıcısı (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Kurumsal Geliştirme ve Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Kullandıkça Öde Geliştirme ve Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio aboneleri için aylık Azure kredisine sahip abonelikler](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Bölgesel kaynak sınırlamaları

Desteklenen Abonelik türleri, bölge başına sınırlı sayıda kaynak içerebilir. Yönetilen örnek, Azure bölgesi başına iki varsayılan sınıra sahiptir (Bu, Azure portal) bir abonelik türü türüne bağlı olarak bir özel [destek isteği](#obtaining-a-larger-quota-for-sql-managed-instance)oluşturarak isteğe bağlı olarak artırılabilir:

- **Alt ağ sınırı**: yönetilen örneklerin tek bir bölgede dağıtıldığı alt ağların en fazla sayısı.
- **Vcore birim sınırı**: tek bir bölgedeki tüm örneklerde dağıtılabilecek en fazla Vcore birimi sayısı. Bir GP sanal çekirdeği bir vCore birimi kullanır ve bir BC sanal çekirdek 4 sanal çekirdek birimi alır. Toplam örnek sayısı, sanal çekirdek birim sınırının içinde olduğu sürece sınırlı değildir.

> [!Note]
> Bu sınırlar varsayılan ayarlar değildir ve teknik sınırlamalardır. Geçerli bölgede daha fazla yönetilen örneğe ihtiyacınız varsa Azure portal özel bir [destek isteği](#obtaining-a-larger-quota-for-sql-managed-instance) oluşturularak sınırlar artırılabilir. Alternatif olarak, destek istekleri göndermeden başka bir Azure bölgesinde yeni yönetilen örnekler oluşturabilirsiniz.

Aşağıdaki tabloda desteklenen Abonelik türleri için **varsayılan bölgesel sınırlar** gösterilmektedir (varsayılan sınırlar aşağıda açıklanan destek isteği kullanılarak genişletilebilir):

|Abonelik türü| En fazla yönetilen örnek alt ağ sayısı | Maksimum vCore birimi sayısı * |
| :---| :--- | :--- |
|Kullandıkça öde|3|320|
|CSP |8 (bazı bölgelerde 15 * *)|960 (bazı bölgelerde 1440 * *)|
|Kullandıkça Öde geliştirme ve test|3|320|
|Kurumsal Geliştirme ve Test|3|320|
|UC|8 (bazı bölgelerde 15 * *)|960 (bazı bölgelerde 1440 * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional ve MSDN Platformları|2|32|

\* dağıtımlarda, lütfen İş Açısından Kritik (BC) hizmet katmanının dört (4) kat daha fazla sanal çekirdek kapasitesi (Genel Amaçlı (GP) hizmet katmanını gerektirdiğini göz önünde bulundurun. Örneğin: 1 GP vCore = 1 sanal çekirdek birim ve 1 BC sanal çekirdek = 4 sanal çekirdek birimi. Tüketim analizinizi varsayılan sınırlara karşı basitleştirmek için, yönetilen örneklerin dağıtıldığı bölgedeki tüm alt ağlarda vCore birimlerini özetleyin ve sonuçları abonelik türü için örnek birim sınırlarıyla karşılaştırın. Bir bölgedeki her abonelik için **en fazla vCore birimi** sınırı geçerlidir. Birden çok alt ağ arasında dağıtılan tüm sanal çekirdekler toplamı, **en fazla sanal çekirdek birimi sayısına**eşit veya daha düşük olmalıdır.

\*\* daha büyük alt ağ ve sanal çekirdek limitleri şu bölgelerde kullanılabilir: Avustralya Doğu, Doğu ABD, Doğu ABD 2, Kuzey Avrupa, Orta Güney ABD, Güneydoğu Asya, UK Güney, Batı Avrupa, Batı ABD 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>SQL yönetilen örneği için daha büyük bir kota alma

Geçerli Bölgelerinizdeki daha fazla yönetilen örneğe ihtiyacınız varsa, Azure portal kullanarak kotayı uzatmak için bir destek isteği gönderin.
Daha büyük bir kota elde etme işlemini başlatmak için:

1. **Yardım ve destek**' i açın ve **Yeni destek isteği**' ne tıklayın.

   ![Yardım ve destek](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Yeni destek isteği için temel bilgiler sekmesinde:
   - **Sorun türü**için **hizmet ve abonelik sınırları (kotalar)** öğesini seçin.
   - **Abonelik** bölümünde aboneliğinizi seçin.
   - **Kota türü**Için, **SQL veritabanı yönetilen örneği**' ni seçin.
   - **Destek planı**için destek planınızı seçin.

     ![Sorun türü kotası](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. **İleri**’ye tıklayın.
4. Yeni destek isteği için **sorun sekmesinde** :
   - **Önem derecesi**için sorunun önem derecesini seçin.
   - **Ayrıntılar**için, sorununuz hakkında hata iletileri de dahil olmak üzere ek bilgiler sağlayın.
   - **Karşıya dosya yükleme**için, daha fazla bilgi içeren bir dosya ekleyin (4 MB 'a kadar).

     ![Sorun ayrıntıları](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Geçerli bir istek şunları içermelidir:
     > - Abonelik sınırının artırılması gereken bölge.
     > - Kota artırdıktan sonra mevcut alt ağlardaki hizmet katmanı başına gereken sanal çekirdek sayısı (mevcut alt ağlardan herhangi birinin genişletilmesi gerekiyorsa).
     > - Yeni alt ağlar içinde gerekli yeni alt ağ sayısı ve hizmet katmanı başına toplam Vcore sayısı (yeni alt ağlarda yönetilen örnekler dağıtmanız gerekiyorsa).

5. **İleri**’ye tıklayın.
6. Yeni destek isteği için kişi bilgileri sekmesinde, tercih edilen iletişim yöntemini (e-posta veya telefon) ve iletişim ayrıntılarını girin.
7. **Oluştur**'a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- Yönetilen örnek hakkında daha fazla bilgi için bkz. [yönetilen örnek nedir?](sql-database-managed-instance.md).
- Fiyatlandırma bilgileri için bkz. [SQL veritabanı yönetilen örnek fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- İlk yönetilen örneğinizi oluşturmayı öğrenmek için [hızlı başlangıç kılavuzuna](sql-database-managed-instance-get-started.md)bakın.
