---
title: Elastik havuzlarla birden çok veritabanını yönetme
description: Birden çok SQL veritabanını yönetme ve ölçeklendirme-yüzlerce ve binlerce-elastik havuzlar kullanılarak. Gerektiğinde dağıtabileceğiniz kaynaklar için bir fiyat.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
ms.date: 08/06/2019
ms.openlocfilehash: 1b3ee75d1b24cab8211337cf1cdbd053327e16b8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823826"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Elastik havuzlar birden çok Azure SQL veritabanını yönetmenize ve ölçeklendirmenize yardımcı olur

SQL Veritabanı elastik havuzları, kullanım talepleri değişken olan ve öngörülmeyen birden çok veritabanını yönetmeye ve ölçeklendirmeye yönelik kolay ve ekonomik bir çözümdür. Elastik havuzdaki veritabanları tek bir Azure SQL veritabanı sunucusunda bulunur ve ayarlanan fiyata bir dizi kaynağı paylaşır. Azure SQL Veritabanındaki elastik havuzlar, SaaS geliştiricilerinin bir veritabanı grubuna ait fiyat performansını belirtilen bütçe dahilinde iyileştirmesini ve aynı zamanda her veritabanı için performans Elastikliği sunmasını sağlar.

## <a name="what-are-sql-elastic-pools"></a>SQL elastik havuzları nelerdir

SaaS geliştiricileri, birden fazla veritabanından oluşan büyük ölçekli veri katmanlarının üzerinde uygulamalar oluşturur. Her müşteri için tek veritabanı sağlanması yaygın bir uygulama modelidir. Ancak, farklı müşteriler genellikle değişen ve tahmin edilemeyen kullanım modellerine sahiptir ve her veritabanı kullanıcısının kaynak gereksinimlerini tahmin etmek zordur. Geleneksel olarak iki seçeneğiniz vardır:

- Yoğun kullanımı ve ödeme üzerinden kaynakları daha fazla sağlayın veya
- Maliyet tasarrufu sağlamak için, en yüksek performans ve müşteri memnuniyetini harcamadan maliyeti tasarruf edin.

Elastik havuzlar, veritabanlarının ihtiyaç duydukları performans kaynaklarını almasını sağlayarak bu sorunu çözebilir. Bunlar, tahmin edilebilir bir bütçe içinde basit bir kaynak ayırma mekanizması sağlar. Elastik havuzları kullanan SaaS uygulamalarının tasarım desenleri hakkında daha fazla bilgi edinmek için bkz. [Azure SQL Veritabanı kullanan Çok Kiracılı SaaS Uygulamaları için Tasarım Desenleri](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Elastik havuzlar için veritabanı başına ücret alınmaz. Kullanımdan veya havuzun bir saatten daha az etkin olup olmamasına bakılmaksızın, en yüksek eDTU veya sanal çekirdekler için bir havuzun bulunduğu her saat için faturalandırılırsınız.

Elastik havuzlar, geliştiricilerin birden çok veritabanı tarafından paylaşılan bir havuz için kaynak satın almasını etkinleştirerek, bireysel veritabanları tarafından öngörülemeyen kullanım süreleri vardır. Havuzun kaynak için [DTU tabanlı satın alma modeli](sql-database-service-tiers-dtu.md) veya [sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md)temelinde yapılandırma yapabilirsiniz. Havuzun kaynak gereksinimi, veritabanlarının toplam kullanımına göre belirlenir. Havuzun kullanabileceği kaynak miktarı, geliştirici bütçesi tarafından denetlenir. Geliştirici, veritabanlarını havuza ekler, veritabanları için en düşük ve en yüksek değerleri (kaynak alma modelinize bağlı olarak minimum ve maksimum DTU 'lar ya da en düşük veya en yüksek sanal çekirdekler) ayarlar ve sonra havuzun kaynaklarını bütçe. Geliştirici, hizmetini zayıf bir başlangıçtan sürekli artan ölçekte olgun bir işletmeye sorunsuzca büyütmek için havuzları kullanabilir.

Havuz içerisinde tek tek veritabanlarına belirli parametreler içinde otomatik olarak ölçeklendirme esnekliği tanınır. Yoğun yük altında bir veritabanı talebi karşılamak için daha fazla kaynak tüketebilir. Hafif yükler altındaki veritabanları daha az tüketir ve yük olmadığında veritabanları hiçbir kaynak tüketmez. Tek tek veritabanları yerine tüm havuz için kaynak sağlamak, yönetim görevlerinizi basitleştirir. Ayrıca, havuz için öngörülebilir bir bütçeniz vardır. Mevcut bir havuza, veritabanı kapalı kalma süresi olmadan ek kaynaklar eklenebilir, ancak yeni eDTU ayırması için ek bilgi işlem kaynakları sağlamak üzere veritabanlarının taşınması gerekebilir. Benzer şekilde, ek kaynaklara artık ihtiyaç duyulmuyorsa, herhangi bir zamanda mevcut bir havuzdan de kaldırılabilirler. Ayrıca havuza veritabanları ekleyebilir veya havuzdan veritabanları kaldırabilirsiniz. Bir veritabanı kaynakları tahmin edilebilir bir şekilde normalden az kullanıyorsa bu veritabanını havuzdan çıkarın.

> [!NOTE]
> Veritabanları, elastik bir havuzun içine veya dışına taşınırken, veritabanı bağlantıları bırakıldığında işlemin sonundaki kısa bir süre (saniye cinsinden) dışında bir kesinti olmaz.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>SQL veritabanı elastik havuzunu ne zaman düşünmeniz gerekir?

Havuzlar, belirli kullanım düzenlerine sahip çok sayıda veritabanı bulunan durumlar için çok uygundur. Söz konusu kullanım düzeni, belirli bir veritabanı için ortalama düşük düzeyde kullanım ile nispeten nadir zamanlarda kullanımın ani olarak artması şeklindedir.

Bir havuza ekleyebileceğiniz veritabanı sayısı arttıkça, tasarruflarınız artar. Uygulama kullanım modelinize bağlı olarak, yalnızca iki S3 veritabanı ile tasarruf edildiğini görmek mümkündür.

Aşağıdaki bölümler veritabanı koleksiyonunuzun bir havuzda olmasının yararlarını nasıl değerlendireceğini anlamanıza yardımcı olabilir. Örneklerde Standart havuzlar kullanılmaktadır, ancak aynı ilkeler Temel ve Premium havuzlar için de geçerlidir.

### <a name="assessing-database-utilization-patterns"></a>Veritabanı kullanım modellerini değerlendirme

Aşağıdaki şekilde zamanın büyük bölümünü boşta geçiren, ancak düzenli olarak ani etkinlikler sergileyen bir veritabanı örneğini göstermektedir. Bu model bir havuz için uygun olan kullanım modelidir:

   ![havuz için uygun bir tek veritabanı](./media/sql-database-elastic-pool/one-database.png)

Gösterilen beş dakikalık süre boyunca Veritabanı1, 90 DTU’ya kadar yükselir, ancak genel ortalama kullanım beş DTU’dan azdır. Bu iş yükünü tek bir veritabanında çalıştırmak için S3 işlem boyutu gerekir, ancak bu, düşük etkinlik dönemlerinde kaynakların çoğunu kullanılmamış olarak bırakır.

Havuz bu kullanılmayan DTU’ların birden fazla veritabanında paylaşılmasına olanak tanır ve böylece gereken DTU ile genel maliyeti azaltır.

Önceki örnekten devam ederek, Veritabanı1 ile benzer kullanım modellerine sahip ek veritabanları olduğunu varsayalım. Aşağıdaki iki resimde, dört veritabanı ve 20 veritabanı kullanımı, DTU tabanlı satın alma modeli kullanarak zaman içinde kullanımlarının çakışmayan yapısını göstermek için aynı grafiğe katmanlıdır:

   ![bir havuz için uygun kullanım modeli ile dört veritabanı](./media/sql-database-elastic-pool/four-databases.png)

   ![bir havuz için uygun kullanım modeli ile yirmi veritabanı](./media/sql-database-elastic-pool/twenty-databases.png)

20 veritabanının tamamındaki toplam DTU kullanımı, önceki şekilde siyah çizgi ile gösterilmiştir. Bu şekil, toplam DTU kullanımının 100 DTU’yu hiçbir zaman aşmadığını ve 20 veritabanının bu süre boyunca 100 eDTU’yu paylaşabileceğini gösterir. Bu, her bir veritabanını tek veritabanları için S3 işlem boyutlarına yerleştirmekten kaynaklanan DTU 'Lar ve 13x fiyat azaltmasıyla sonuçlanır.

Bu örnek aşağıdaki nedenlerle idealdir:

- Bir veritabanındaki en yüksek kullanım ile ortalama kullanım arasında büyük farklar mevcuttur.
- Bir veritabanının en yüksek kullanımı zamanın farklı noktalarında gerçekleşir.
- eDTU'lar birden fazla veritabanı arasında paylaşılır.

Bir havuzun fiyatı, havuz eDTU'larının bir işlevidir. Bir havuzun eDTU birim fiyatı, tek veritabanının DTU birim fiyatından 1,5 kat fazladır. Bununla birlikte **havuz eDTU'ları çok sayıda veritabanı tarafından paylaşılabilir ve toplam eDTU sayısı gereklidir**. Fiyatlandırma ve eDTU paylaşımındaki bu farklılıklar, havuzların sağlayabileceği tasarruf potansiyelinin temelini oluşturur.

Veritabanı sayısı ve veritabanı kullanımı ile ilgili olan Thumb 'in aşağıdaki kuralları, bir havuzun tek veritabanları için işlem boyutlarını kullanmaya kıyasla daha düşük maliyetli bir maliyet sunarak emin olmaya yardımcı olur.

### <a name="minimum-number-of-databases"></a>En az veritabanı sayısı

Tek veritabanlarına ait kaynakların toplam miktarı 1,5 x 'ten daha fazla ise, havuz için gereken kaynaklar daha fazla maliyetli olur.

***DTU tabanlı satın alma modeli örneği***<br>
100 eDTU havuzunun, tek veritabanları için işlem boyutları kullanmaktan daha uygun maliyetli olması için en az iki S3 veritabanı veya en az 15 S0 veritabanı gereklidir.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Eşzamanlı olarak en üst seviyeye çıkan en fazla veritabanı sayısı

Kaynakları paylaşarak, havuzdaki tüm veritabanları aynı anda kaynakları tek veritabanları için kullanılabilen sınıra kadar eşzamanlı olarak kullanabilir. Eşzamanlı olarak en yüksek olan veritabanları, havuz kaynakları ayarlanabilir ve havuz daha uygun maliyetli hale gelebilir. Genel olarak, 2/3 (veya %67) değil Havuzdaki veritabanlarının kaynakları için aynı anda en fazla olması gerekir.

***DTU tabanlı satın alma modeli örneği***

200 eDTU içeren bir havuzdaki üç S3 veritabanının maliyetlerini azaltmak için, bu veritabanlarının en fazla iki tanesi kullanım sırasında en üst seviyeye çıkabilir. Aksi takdirde, bu dört S3 veritabanının ikiden fazlası eşzamanlı olarak en üst seviyeye çıkarsa, havuzun boyutu 200 eDTU’dan fazla olmak zorundadır. Havuz 200 eDTU 'dan daha fazlasına yeniden boyutlandırılırsa, tek veritabanlarının işlem boyutundan daha düşük olması için havuza daha fazla S3 veritabanının eklenmesi gerekir.

Bu örnek, havuzdaki diğer veritabanlarının kullanımını dikkate almaz. Herhangi bir zamanda tüm veritabanlarının kullanımı aynı olursa, veritabanlarının 2/3’ünden (veya %67) daha azı eşzamanlı olarak en üst seviyeye çıkabilir.

### <a name="resource-utilization-per-database"></a>Veritabanı başına kaynak kullanımı

Bir veritabanının en yüksek ile ortalama kullanımı arasında büyük bir fark olması, uzun süreli düşük kullanımı ve kısa süreli yüksek kullanımı ifade eder. Bu kullanım modeli, veritabanları arasında kaynakların paylaşılması için idealdir. Bir veritabanının en yüksek kullanımı ortalama kullanımından 1,5 kat fazla olduğunda, veritabanı havuz için düşünülmelidir.

**DTU tabanlı satın alma modeli örneği**: 100 dtu ve 67 Ortalama ile Ilgili bir S3 veritabanı, bir havuzdaki eDTU 'ları paylaşmak için iyi bir adaydır. Alternatif olarak, en yüksek kullanımı 20 DTU’ya varan ve ortalama olarak en fazla 13 DTU kullanan bir S1 veritabanı da havuz için iyi bir adaydır.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Doğru havuz boyutunu Nasıl yaparım? seçin

Bir havuz için en iyi boyut, havuzdaki tüm veritabanları için gereken toplam kaynaklara bağlıdır. Bu, aşağıdakilerin belirlenmesi içerir:

- Havuzdaki tüm veritabanları tarafından kullanılan en fazla kaynak (kaynak alma modelinize bağlı olarak maksimum DTU 'lar ya da maksimum sanal çekirdek).
- Havuzdaki tüm veritabanları tarafından kullanılan en fazla depolama baytı sayısı.

Her kaynak modeli için kullanılabilir hizmet katmanları için, [DTU tabanlı satın alma modeli](sql-database-service-tiers-dtu.md) veya [sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md)' ne bakın.

Araçları kullanamadığınız durumlarda aşağıdaki adım adım yönergeler bir havuzun tek veritabanlarından daha uygun maliyetli olup olmadığını tahmin etmenize yardımcı olabilir:

1. Havuz için gereken eDTU 'ları veya sanal çekirdekleri aşağıdaki gibi tahmin edin:

   DTU tabanlı satın alma modeli için: MAX (< VERITABANı başına*Toplam DBs* X *Ortalama DTU kullanımı*sayısı >,<br>  
   <*Eşzamanlı olarak en üst seviyeye çıkan veritabanı sayısı* X *Veritabanı başına en yüksek DTU kullanımı*)

   Sanal çekirdek tabanlı satın alma modeli için: MAX (< VERITABANı başına*Toplam DBs* X *Ortalama sanal çekirdek kullanımı*sayısı >,<br>  
   Her VERITABANı için *aynı anda* *en üst düzey sanal çekirdek kullanımı*<sayısı

2. Havuzdaki tüm veritabanları için gereken bayt sayısını ekleyerek havuz için gereken depolama alanını tahmin edin. Ardından, bu depolama miktarını sağlayan eDTU havuz boyutunu belirleyin.
3. DTU tabanlı satın alma modeli için 1. ve 2. adım 'daki eDTU tahminlerinin daha büyük bir kısmını alın. Sanal çekirdek tabanlı satın alma modeli için 1. adımdaki sanal çekirdek tahminini alın.
4. [SQL Veritabanı fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/sql-database/) bakın ve 3. adımdaki tahminden daha büyük olan en küçük havuz boyutunu bulun.
5. 5\. adımdaki havuz fiyatını, tek veritabanları için uygun işlem boyutlarını kullanma fiyatına göre karşılaştırın.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Esnek havuzlarla diğer SQL veritabanı özelliklerini kullanma

### <a name="elastic-jobs-and-elastic-pools"></a>Elastik işler ve elastik havuzlar

Bir havuz kullanılarak **[esnek işlerde](elastic-jobs-overview.md)** betik çalıştırma yoluyla yönetim görevleri kolaylaştırılır. Elastik iş, çok sayıda veritabanından kaynaklanan sorunların çoğunu ortadan kaldırır.

Birden fazla veritabanıyla çalışmak için kullanılabilen diğer veritabanı araçları hakkında daha fazla bilgi için bkz. [Azure SQL Veritabanı ile ölçek genişletme](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Elastik havuzdaki veritabanları için iş sürekliliği seçenekleri

Havuza alınan veritabanları genellikle tek veritabanları için kullanılabilen [iş sürekliliği özelliklerinin](sql-database-business-continuity.md) aynılarını destekler.

- **Zaman içindeki bir noktaya geri yükleme**

  Belirli bir noktaya geri yükleme, havuzdaki bir veritabanını zaman içinde belirli bir noktaya kurtarmak için otomatik veritabanı yedeklemeleri kullanır. Bkz. [Belirli Bir Noktaya Geri Yükleme](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Coğrafi geri yükleme**

  Coğrafi geri yükleme, veritabanının barındırıldığı bölgedeki bir olay nedeniyle kullanılabilir olmadığında varsayılan kurtarma seçeneğini sağlar. Bkz. [Bir Azure SQL Veritabanını geri yükleme veya ikincil veritabanına yük devretme](sql-database-disaster-recovery.md)

- **Etkin coğrafi çoğaltma**

  Coğrafi geri yüklemenin sunabileceği daha fazla agresif kurtarma gereksinimlerine sahip uygulamalar için, [etkin coğrafi çoğaltma](sql-database-active-geo-replication.md) veya [otomatik yük devretme grubunu](sql-database-auto-failover-group.md)yapılandırın.

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Azure portal kullanarak yeni bir SQL veritabanı elastik havuzu oluşturma

Azure portal esnek havuz oluşturabileceğiniz iki yol vardır.

1. Azure portal sol taraftaki menüden **Azure SQL** ' i seçin. Azure SQL listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna *Azure SQL* yazın.
2. **+ Ekle** ' yı seçerek **SQL dağıtım seçeneğini seçin** sayfasını açın. **Veritabanları** kutucuğunda **Ayrıntıları göster** ' i seçerek elastik havuzlarla ilgili ek bilgileri görüntüleyebilirsiniz.
3. **Veritabanları** kutucuğunda **kaynak türü** açılan listesinde **Esnek havuz** ' ı seçin ve ardından **Oluştur**' u seçin.

   ![Elastik havuz oluşturma](./media/sql-database-elastic-pool/create-elastic-pool.png)


1. Alternatif olarak, var olan bir Azure SQL sunucusuna gidip **+ yeni havuz** ' a tıklayarak bir elastik havuz oluşturarak bu sunucuya doğrudan bir havuz oluşturabilirsiniz.

> [!NOTE]
> Sunucuda birden çok havuz oluşturabilirsiniz, ancak farklı sunuculardan aynı havuza veritabanı ekleyemezsiniz.

Havuzun hizmet katmanı, havuzdaki elastik için kullanılabilen özellikleri ve her bir veritabanı için kullanılabilir maksimum kaynak miktarını belirler. Ayrıntılar için bkz. [DTU modelindeki](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)elastik havuzlar için kaynak sınırları. Elastik havuzlara yönelik sanal çekirdek tabanlı kaynak sınırları için bkz. [sanal çekirdek tabanlı kaynak limitleri-elastik havuzlar](sql-database-vcore-resource-limits-elastic-pools.md).

Havuzun kaynaklarını ve fiyatlandırmasını yapılandırmak için **havuzu Yapılandır**' a tıklayın. Ardından bir hizmet katmanı seçin, havuza veritabanları ekleyin ve havuz ve veritabanları için kaynak sınırlarını yapılandırın.

Havuzu yapılandırmayı tamamladıktan sonra ' Uygula 'ya tıklayabilir, havuzu değiştirebilir ve ' Tamam 'a tıklayarak havuzu oluşturabilirsiniz.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Elastik havuz ve veritabanlarını izleme

Azure portal, elastik havuzun ve bu havuzun içindeki veritabanlarının kullanımını izleyebilirsiniz. Aynı zamanda esnek havuzunuzdaki bir değişiklik kümesi de oluşturabilir ve tüm değişiklikleri aynı anda gönderebilirsiniz. Bu değişiklikler veritabanlarını ekleme veya kaldırma, elastik havuz ayarlarınızı değiştirme veya veritabanı ayarlarınızı değiştirme içerir.

Elastik havuzunuzu izlemeye başlamak için portalda bir elastik havuz bulun ve açın. İlk olarak, elastik havuzunuzun durumuna ilişkin genel bir bakış sunan bir ekran görürsünüz. Buna aşağıdakiler dahildir:

- Esnek havuzun kaynak kullanımını gösteren grafikleri izleme
- Elastik havuz için, varsa son uyarılar ve öneriler

Aşağıdaki grafikte örnek bir elastik havuz gösterilmektedir:

![Havuz görünümü](./media/sql-database-elastic-pool-manage-portal/basic.png)

Havuz hakkında daha fazla bilgi edinmek istiyorsanız, bu genel bakışta bulunan bilgilerden herhangi birine tıklayabilirsiniz. **Kaynak kullanımı** grafiğine tıklamak sizi grafikte gösterilen ölçümleri ve zaman penceresini özelleştirebileceğiniz Azure izleme görünümüne götürür. Kullanılabilir bildirimlere tıkladığınızda, bu uyarının veya önerinin tüm ayrıntılarını gösteren bir dikey pencereye gönderilir.

Havuzunuzun içindeki veritabanlarını izlemek isterseniz, sol taraftaki kaynak menüsündeki **izleme** bölümünde **veritabanı kaynak kullanımı** ' na tıklayabilirsiniz.

![Veritabanı kaynak kullanımı sayfası](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Grafik görüntüsünü özelleştirmek için

Grafik ve ölçüm sayfasını, CPU yüzdesi, veri GÇ yüzdesi ve kullanılan günlük GÇ yüzdesi gibi diğer ölçümleri görüntüleyecek şekilde düzenleyebilirsiniz.

**Grafik Düzenle** formunda, bir sabit zaman aralığı seçebilir veya son iki hafta içinde herhangi bir 24 saatlik pencere seçmek için **özel** ' e tıklayabilir ve ardından izlenecek kaynakları seçebilirsiniz.

### <a name="to-select-databases-to-monitor"></a>İzlenecek veritabanlarını seçmek için

Varsayılan olarak, **veritabanı kaynak kullanımı** dikey penceresindeki grafik, en iyi 5 veritabanını DTU veya CPU 'ya göre gösterir (hizmet katmanınıza bağlı olarak). Soldaki onay kutularını kullanarak, grafiğin altındaki listeden veritabanlarını seçip seçerek bu grafikteki veritabanlarını değiştirebilirsiniz.

Veritabanlarının performansının daha kapsamlı bir görünümünü sağlamak için bu veritabanı tablosunda yan yana görüntülemek üzere daha fazla ölçüm de seçebilirsiniz.

Daha fazla bilgi için bkz. [Azure Portal SQL veritabanı uyarıları oluşturma](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Müşteri örnek olay incelemeleri

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart, Azure SQL veritabanı ile esnek havuzlar kullanarak, aylık 1.000 yeni Azure SQL veritabanı hızında iş hizmetlerini hızla genişletin.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Dönerek, bulutta binlerce kiracı için Hizmetleri hızlıca sağlamak ve ölçeklendirmek için Azure SQL veritabanı ile elastik havuzlar kullanır.

- [Davxko/CSı](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   Dadxko/CSı, geliştirme döngüsünü hızlandırmak ve müşteri hizmetlerini ve performansını geliştirmek için Azure SQL veritabanı ile elastik havuzlar kullanır.   

## <a name="next-steps"></a>Sonraki adımlar

- Elastik havuzları ölçeklendirmek için bkz. [elastik havuzları ölçekleme](sql-database-elastic-pool-scale.md) ve [elastik havuz ölçekleme-örnek kod](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Bir video için bkz. [Microsoft sanal akademik My video Kurumu Azure SQL veritabanı elastik özellikleri](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Elastik havuzları kullanan SaaS uygulamalarının tasarım desenleri hakkında daha fazla bilgi edinmek için bkz. [Azure SQL Veritabanı kullanan Çok Kiracılı SaaS Uygulamaları için Tasarım Desenleri](sql-database-design-patterns-multi-tenancy-saas-applications.md).
- Elastik havuzlar kullanan bir SaaS öğreticisi için bkz. [Wingtip SaaS uygulamasına giriş](sql-database-wtp-overview.md).
