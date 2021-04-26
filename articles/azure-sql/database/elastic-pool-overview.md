---
title: Elastik havuzlarla birden çok veritabanını yönetme
description: Azure SQL veritabanında birden çok veritabanını yönetin ve ölçeklendirin (yüzlerce ve binlerce) elastik havuzlar kullanarak. Gerektiğinde dağıtabileceğiniz kaynaklar için bir fiyat.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, sstein
ms.date: 12/9/2020
ms.openlocfilehash: c478edf95ae345d64da630400fbf63ac613b73a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653644"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>Elastik havuzlar Azure SQL veritabanı 'nda birden çok veritabanını yönetmenize ve ölçeklendirmenize yardımcı olur
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL veritabanı elastik havuzları, değişen ve öngörülemeyen kullanım taleplerine sahip birden çok veritabanını yönetmeye ve ölçeklendirmeye yönelik basit, ekonomik bir çözümdür. Elastik havuzdaki veritabanları tek bir sunucuda bulunur ve ayarlanan fiyata bir dizi kaynağı paylaşır. Azure SQL Veritabanındaki elastik havuzlar, SaaS geliştiricilerinin bir veritabanı grubuna ait fiyat performansını belirtilen bütçe dahilinde iyileştirmesini ve aynı zamanda her veritabanı için performans Elastikliği sunmasını sağlar.

## <a name="what-are-sql-elastic-pools"></a>SQL elastik havuzları nelerdir

SaaS geliştiricileri, birden fazla veritabanından oluşan büyük ölçekli veri katmanlarının üzerinde uygulamalar oluşturur. Her müşteri için tek veritabanı sağlanması yaygın bir uygulama modelidir. Ancak farklı müşteriler genellikle değişen ve öngörülemeyen kullanım düzenlerine sahiptir ve her veritabanı kullanıcısının kaynak gereksinimlerini tahmin etmek zordur. Geleneksel olarak iki seçeneğiniz vardır:

- Yoğun kullanımı ve ödeme üzerinden kaynakları daha fazla sağlayın veya
- Maliyet tasarrufu sağlamak için, en yüksek performans ve müşteri memnuniyetini harcamadan maliyeti tasarruf edin.

Elastik havuzlar, veritabanlarının ihtiyaç duydukları performans kaynaklarını almasını sağlayarak bu sorunu çözebilir. Bunlar, tahmin edilebilir bir bütçe içinde basit bir kaynak ayırma mekanizması sağlar. Elastik havuzları kullanan SaaS uygulamalarının tasarım desenleri hakkında daha fazla bilgi edinmek için bkz. [Azure SQL Veritabanı kullanan Çok Kiracılı SaaS Uygulamaları için Tasarım Desenleri](saas-tenancy-app-design-patterns.md).
>
> [!IMPORTANT]
> Elastik havuzlar için veritabanı başına ücret alınmaz. Kullanımdan veya havuzun bir saatten daha az etkin olup olmamasına bakılmaksızın, en yüksek eDTU veya sanal çekirdekler için bir havuzun bulunduğu her saat için faturalandırılırsınız.

Elastik havuzlar, geliştiricilerin birden çok veritabanı tarafından paylaşılan bir havuz için kaynak satın almasını etkinleştirerek, bireysel veritabanları tarafından öngörülemeyen kullanım süreleri vardır. Havuzun kaynak için [DTU tabanlı satın alma modeli](service-tiers-dtu.md) veya [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md)temelinde yapılandırma yapabilirsiniz. Havuzun kaynak gereksinimi, veritabanlarının toplam kullanımına göre belirlenir. Havuzun kullanabileceği kaynak miktarı, geliştirici bütçesi tarafından denetlenir. Geliştirici, veritabanlarını havuza ekler, isteğe bağlı olarak veritabanları için en düşük ve en yüksek kaynakları (kaynak alma modelinize bağlı olarak minimum ve maksimum DTU 'lar ya da en düşük ya da en yüksek sanal çekirdekler) ayarlar ve ardından bu havuzun kaynak kaynaklarını bütçesine göre ayarlar. Geliştirici, hizmetini zayıf bir başlangıçtan sürekli artan ölçekte olgun bir işletmeye sorunsuzca büyütmek için havuzları kullanabilir.

Havuz içerisinde tek tek veritabanlarına belirli parametreler içinde otomatik olarak ölçeklendirme esnekliği tanınır. Yoğun yük altında bir veritabanı talebi karşılamak için daha fazla kaynak tüketebilir. Hafif yükler altındaki veritabanları daha az tüketir ve yük olmadığında veritabanları hiçbir kaynak tüketmez. Tek tek veritabanları yerine tüm havuz için kaynak sağlamak, yönetim görevlerinizi basitleştirir. Ayrıca, havuz için öngörülebilir bir bütçeniz vardır. Mevcut bir havuza, en az kapalı kalma süresine sahip ek kaynaklar eklenebilir. Benzer şekilde, ek kaynaklara artık ihtiyaç duyulmuyorsa, herhangi bir zamanda mevcut bir havuzdan de kaldırılabilirler. Ve havuzdan veritabanı ekleyebilir veya havuzdan veritabanları kaldırabilirsiniz. Bir veritabanı kaynakları tahmin edilebilir bir şekilde normalden az kullanıyorsa bu veritabanını havuzdan çıkarın.

> [!NOTE]
> Veritabanları, elastik bir havuzun içine veya dışına taşınırken, veritabanı bağlantıları bırakıldığında işlemin sonundaki kısa bir süre (saniye cinsinden) dışında bir kesinti olmaz.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>SQL veritabanı elastik havuzunu ne zaman düşünmeniz gerekir?

Havuzlar, belirli kullanım düzenlerine sahip çok sayıda veritabanı bulunan durumlar için çok uygundur. Söz konusu kullanım düzeni, belirli bir veritabanı için ortalama düşük düzeyde kullanım ile nispeten nadir zamanlarda kullanımın ani olarak artması şeklindedir. Buna karşılık, sürekli orta yüksek kullanıma sahip birden çok veritabanı aynı elastik havuza yerleştirilmemelidir.

Bir havuza ekleyebileceğiniz veritabanı sayısı arttıkça, tasarruflarınız artar. Uygulamanızın kullanım düzenine bağlı olarak, iki S3 veritabanı kadar az sayıda tasarruf sağlamak mümkündür.

Aşağıdaki bölümler veritabanı koleksiyonunuzun bir havuzda olmasının yararlarını nasıl değerlendireceğini anlamanıza yardımcı olabilir. Örneklerde Standart havuzlar kullanılmaktadır, ancak aynı ilkeler Temel ve Premium havuzlar için de geçerlidir.

### <a name="assessing-database-utilization-patterns"></a>Veritabanı kullanım modellerini değerlendirme

Aşağıdaki şekilde zamanın büyük bölümünü boşta geçiren, ancak düzenli olarak ani etkinlikler sergileyen bir veritabanı örneğini göstermektedir. Bu model bir havuz için uygun olan kullanım modelidir:

   ![havuz için uygun bir tek veritabanı](./media/elastic-pool-overview/one-database.png)

Grafik, her veri noktasında 1 dakikalık ayrıntı düzeyi olduğunda, 12:00 ile 1:00 arasında bir 1 saatlik süre boyunca DTU kullanımını gösterir. 12:10 Dlik 90 'e kadar DB1, ancak genel ortalama kullanımı beş DTU 'dan daha az. Bu iş yükünü tek bir veritabanında çalıştırmak için S3 işlem boyutu gerekir, ancak bu, düşük etkinlik dönemlerinde kaynakların çoğunu kullanılmamış olarak bırakır.

Havuz bu kullanılmayan DTU’ların birden fazla veritabanında paylaşılmasına olanak tanır ve böylece gereken DTU ile genel maliyeti azaltır.

Önceki örnekten devam ederek, Veritabanı1 ile benzer kullanım modellerine sahip ek veritabanları olduğunu varsayalım. Aşağıdaki iki resimde, dört veritabanı ve 20 veritabanı kullanımı, DTU tabanlı satın alma modeli kullanarak zaman içinde kullanımlarının çakışmayan yapısını göstermek için aynı grafiğe katmanlıdır:

   ![bir havuz için uygun kullanım modeli ile dört veritabanı](./media/elastic-pool-overview/four-databases.png)

   ![bir havuz için uygun kullanım modeli ile yirmi veritabanı](./media/elastic-pool-overview/twenty-databases.png)

20 veritabanının tamamındaki toplam DTU kullanımı, önceki şekilde siyah çizgi ile gösterilmiştir. Bu şekil, toplam DTU kullanımının 100 DTU’yu hiçbir zaman aşmadığını ve 20 veritabanının bu süre boyunca 100 eDTU’yu paylaşabileceğini gösterir. Bu, her bir veritabanını tek veritabanları için S3 işlem boyutlarına yerleştirmekten kaynaklanan DTU 'Lar ve 13x fiyat azaltmasıyla sonuçlanır.

Bu örnek aşağıdaki nedenlerle idealdir:

- Bir veritabanındaki en yüksek kullanım ile ortalama kullanım arasında büyük farklar mevcuttur.
- Bir veritabanının en yüksek kullanımı zamanın farklı noktalarında gerçekleşir.
- eDTU'lar birden fazla veritabanı arasında paylaşılır.

DTU satın alma modelinde, havuzun fiyatı, havuz eDTU 'larının bir işlevidir. Bir havuzun eDTU birim fiyatı, tek veritabanının DTU birim fiyatından 1,5 kat fazladır. Bununla birlikte **havuz eDTU'ları çok sayıda veritabanı tarafından paylaşılabilir ve toplam eDTU sayısı gereklidir**. Fiyatlandırma ve eDTU paylaşımındaki bu farklılıklar, havuzların sağlayabileceği tasarruf potansiyelinin temelini oluşturur.

Sanal çekirdek satın alma modelinde, elastik havuzlar için sanal çekirdek birim fiyatı, tek veritabanlarının sanal çekirdek birim fiyatıyla aynıdır.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Doğru havuz boyutunu Nasıl yaparım? seçin

Bir havuz için en iyi boyut, havuzdaki tüm veritabanları için gereken toplam kaynaklara bağlıdır. Bu, aşağıdakilerin belirlenmesi içerir:

- Havuzdaki tüm veritabanları tarafından kullanılan en fazla işlem kaynağı.  İşlem kaynakları, satın alma modelinize bağlı olarak eDTU 'lar ya da sanal çekirdekler tarafından dizinlenir.
- Havuzdaki tüm veritabanları tarafından kullanılan en fazla depolama baytı sayısı.

Her satın alma modelindeki hizmet katmanları ve kaynak sınırları için bkz. [DTU tabanlı satın](service-tiers-dtu.md) alma modeli veya [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md).

Aşağıdaki adımlar bir havuzun tek veritabanlarından daha uygun maliyetli olup olmadığını tahmin etmenize yardımcı olabilir:

1. Havuz için gereken eDTU 'ları veya sanal çekirdekleri aşağıdaki gibi tahmin edin:
   - DTU tabanlı satın alma modeli için:
     - En fazla (  <&times; *veritabanı başına ortalama DTU kullanımı* sayısı>, <her veritabanı için *aynı anda* &times; *en yüksek DTU kullanımı* sayısı>)
   - Sanal çekirdek tabanlı satın alma modeli için:
     - En fazla ( *<* &times; *veritabanı başına ortalama ortalama sanal çekirdek kullanımı*>, <her veritabanı için *aynı anda* &times; *en yüksek sanal çekirdek kullanımı* sayısı>)
2. Havuzdaki tüm veritabanları için gereken veri boyutunu ekleyerek havuz için gereken toplam depolama alanını tahmin edin. DTU satın alma modeli için, bu depolama miktarını sağlayan eDTU havuz boyutunu saptayın.
3. DTU tabanlı satın alma modeli için 1. ve 2. adım 'daki eDTU tahminlerinin daha büyük bir kısmını alın. Sanal çekirdek tabanlı satın alma modeli için 1. adımdaki sanal çekirdek tahminini alın.
4. [SQL Veritabanı fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/sql-database/) bakın ve 3. adımdaki tahminden daha büyük olan en küçük havuz boyutunu bulun.
5. 5. adımdaki havuz fiyatını, tek veritabanları için uygun işlem boyutlarını kullanma fiyatına göre karşılaştırın.

> [!IMPORTANT]
> Bir havuzdaki veritabanlarının sayısı desteklenen en yüksek sayıya yaklaşırsa, [yoğun elastik havuzlardaki kaynak yönetimini](elastic-pool-resource-management.md)göz önünde bulundurduğunuzdan emin olun.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Esnek havuzlarla diğer SQL veritabanı özelliklerini kullanma

### <a name="elastic-jobs-and-elastic-pools"></a>Elastik işler ve elastik havuzlar

Bir havuz kullanılarak **[esnek işlerde](elastic-jobs-overview.md)** betik çalıştırma yoluyla yönetim görevleri kolaylaştırılır. Elastik iş, çok sayıda veritabanından kaynaklanan sorunların çoğunu ortadan kaldırır.

Birden fazla veritabanıyla çalışmak için kullanılabilen diğer veritabanı araçları hakkında daha fazla bilgi için bkz. [Azure SQL Veritabanı ile ölçek genişletme](elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Elastik havuzdaki veritabanları için iş sürekliliği seçenekleri

Havuza alınan veritabanları genellikle tek veritabanları için kullanılabilen [iş sürekliliği özelliklerinin](business-continuity-high-availability-disaster-recover-hadr-overview.md) aynılarını destekler.

- **Belirli bir noktaya geri yükleme**

  Belirli bir noktaya geri yükleme, havuzdaki bir veritabanını zaman içinde belirli bir noktaya kurtarmak için otomatik veritabanı yedeklemeleri kullanır. Bkz. [Belirli Bir Noktaya Geri Yükleme](recovery-using-backups.md#point-in-time-restore)

- **Coğrafi geri yükleme**

  Coğrafi geri yükleme, veritabanının barındırıldığı bölgedeki bir olay nedeniyle kullanılabilir olmadığında varsayılan kurtarma seçeneğini sağlar. Bkz. [Bir Azure SQL Veritabanını geri yükleme veya ikincil veritabanına yük devretme](disaster-recovery-guidance.md)

- **Etkin coğrafi çoğaltma**

  Coğrafi geri yüklemenin sunabileceği daha fazla agresif kurtarma gereksinimlerine sahip uygulamalar için, [etkin coğrafi çoğaltma](active-geo-replication-overview.md) veya [otomatik yük devretme grubunu](auto-failover-group-overview.md)yapılandırın.

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Azure portal kullanarak yeni bir SQL veritabanı elastik havuzu oluşturma

Azure portal esnek havuz oluşturabileceğiniz iki yol vardır.

1. Elastik havuz oluşturmak için [Azure Portal](https://portal.azure.com) gidin. **Azure SQL** araması yapın ve seçin.
2. **+ Ekle** ' yı seçerek **SQL dağıtım seçeneğini seçin** sayfasını açın. **Veritabanları** kutucuğunda **Ayrıntıları göster** ' i seçerek elastik havuzlarla ilgili ek bilgileri görüntüleyebilirsiniz.
3. **Veritabanları** kutucuğunda **kaynak türü** açılan listesinde **Esnek havuz** ' ı seçin ve ardından **Oluştur**' u seçin.

   ![Elastik havuz oluşturma](./media/elastic-pool-overview/create-elastic-pool.png)

4. Alternatif olarak, var olan bir sunucuya gidip **+ yeni havuz** ' a tıklayarak bir elastik havuz oluşturarak bu sunucuya doğrudan bir havuz oluşturabilirsiniz.

> [!NOTE]
> Sunucuda birden çok havuz oluşturabilirsiniz, ancak farklı sunuculardan aynı havuza veritabanı ekleyemezsiniz.

Havuzun hizmet katmanı, havuzdaki elastik için kullanılabilen özellikleri ve her bir veritabanı için kullanılabilir maksimum kaynak miktarını belirler. Ayrıntılar için bkz. [DTU modelindeki](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)elastik havuzlar için kaynak sınırları. Elastik havuzlara yönelik sanal çekirdek tabanlı kaynak sınırları için bkz. [sanal çekirdek tabanlı kaynak limitleri-elastik havuzlar](resource-limits-vcore-elastic-pools.md).

Havuzun kaynaklarını ve fiyatlandırmasını yapılandırmak için **havuzu Yapılandır**' a tıklayın. Ardından bir hizmet katmanı seçin, havuza veritabanları ekleyin ve havuz ve veritabanları için kaynak sınırlarını yapılandırın.

Havuzu yapılandırmayı tamamladıktan sonra ' Uygula 'ya tıklayabilir, havuzu değiştirebilir ve ' Tamam 'a tıklayarak havuzu oluşturabilirsiniz.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Elastik havuz ve veritabanlarını izleme

Azure portal, elastik havuzun ve bu havuzun içindeki veritabanlarının kullanımını izleyebilirsiniz. Aynı zamanda esnek havuzunuzdaki bir değişiklik kümesi de oluşturabilir ve tüm değişiklikleri aynı anda gönderebilirsiniz. Bu değişiklikler veritabanlarını ekleme veya kaldırma, elastik havuz ayarlarınızı değiştirme veya veritabanı ayarlarınızı değiştirme içerir.

Yerleşik [performans izleme](./performance-guidance.md) ve [Uyarı araçlarını](./alerts-insights-configure-portal.md), performans derecelendirmeleri ile birlikte kullanabilirsiniz.  Ayrıca, SQL veritabanı daha kolay izleme için [ölçümleri ve kaynak günlüklerini yayabilir](./metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal) .

## <a name="customer-case-studies"></a>Müşteri örnek olay incelemeleri

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart, Azure SQL veritabanı ile esnek havuzlar kullanarak, aylık 1.000 yeni Azure SQL veritabanı hızında iş hizmetlerini hızla genişletin.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Dönerek, bulutta binlerce kiracı için Hizmetleri hızlıca sağlamak ve ölçeklendirmek için Azure SQL veritabanı ile elastik havuzlar kullanır.

- [Davxko/CSı](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   Dadxko/CSı, geliştirme döngüsünü hızlandırmak ve müşteri hizmetlerini ve performansını geliştirmek için Azure SQL veritabanı ile elastik havuzlar kullanır.

## <a name="next-steps"></a>Sonraki adımlar

- Fiyatlandırma bilgileri için bkz. [elastik havuz fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Elastik havuzları ölçeklendirmek için bkz. [elastik havuzları ölçekleme](elastic-pool-scale.md) ve [elastik havuz ölçekleme-örnek kod](scripts/monitor-and-scale-pool-powershell.md)
- Elastik havuzları kullanan SaaS uygulamalarının tasarım desenleri hakkında daha fazla bilgi edinmek için bkz. [Azure SQL Veritabanı kullanan Çok Kiracılı SaaS Uygulamaları için Tasarım Desenleri](saas-tenancy-app-design-patterns.md).
- Elastik havuzlar kullanan bir SaaS öğreticisi için bkz. [Wingtip SaaS uygulamasına giriş](saas-dbpertenant-wingtip-app-overview.md).
- Birçok veritabanı ile elastik havuzlardaki kaynak yönetimi hakkında bilgi edinmek için bkz. [yoğun elastik havuzlarda kaynak yönetimi](elastic-pool-resource-management.md).
