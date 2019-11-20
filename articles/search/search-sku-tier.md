---
title: Fiyatlandırma Katmanı veya SKU seçin
titleSuffix: Azure Cognitive Search
description: "Azure Bilişsel Arama, bu SKU 'Larda sağlanabilir: ücretsiz, temel ve standart ve standart, çeşitli kaynak yapılandırmalarında ve kapasite düzeylerinde kullanılabilir."
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9caadddf281c6df29f6e36057c7b28abcef0e1c4
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112517"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Azure Bilişsel Arama için bir fiyatlandırma katmanı seçin

Bir Azure Bilişsel Arama hizmeti oluşturduğunuzda, hizmet ömrü boyunca düzeltilen bir fiyatlandırma katmanında (veya SKU) bir [kaynak oluşturulur](search-create-service-portal.md) . Katmanlar ücretsiz, temel, standart ve depolama için Iyileştirilmiş. Standart ve depolama için Iyileştirilmiş çeşitli yapılandırmalarda ve kapasitelerde kullanılabilir.

Çoğu müşteri, hizmeti değerlendirebilmeleri için ücretsiz katmanla başlar. Değerlendirme sonrası, geliştirme ve üretim dağıtımları için daha yüksek katmanlardan birinde ikinci bir hizmet oluşturmak yaygındır.

Ücretsiz katmanı da dahil olmak üzere tüm katmanların, genellikle özellik eşliği sunan, daha büyük iş yükleri daha yüksek katmanlar gereksinimini ortadan yabilse de. Örneğin, [AI zenginleştirme](cognitive-search-concept-intro.md) , veri kümesi küçük olmadığı sürece ücretsiz bir hizmette zaman aşımına uğrar uzun süreli yetenekler içerir.

> [!NOTE] 
> Özellik eşliği için özel durum, S3 HD 'de kullanılamayan [Dizin oluşturuculardır](search-indexer-overview.md).
>

<!-- For Basic tier and up, you can [adjust replica and partition resources](search-capacity-planning.md) to increase or decrease scale. You could start with one or two of each and then temporarily raise your computational power for a heavy indexing workload. The ability to tune resource levels within a tier adds flexibility, but also slightly complicates your analysis. You might have to experiment to see whether a lower tier with more resources/replicas offers better value and performance than a higher tier with fewer resources. To learn more about when and why you would adjust capacity, see [Performance and optimization considerations](search-performance-optimization.md). -->

## <a name="available-tiers"></a>Kullanılabilir Katmanlar

Katmanlar, hizmeti barındıran donanımın özelliklerini yansıtır (özellikler yerine) ve farklılaştırılabilir:

+ Oluşturabileceğiniz dizin ve dizin oluşturucularının miktarı
+ Bölümlerin boyutu ve hızı (fiziksel depolama)

Seçtiğiniz katman faturalandırılabilir ücreti belirler. Azure portal aşağıdaki ekran görüntüsünde, kullanılabilir katmanlar, eksi fiyatlandırma (portalda ve [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/search/)bulabilirsiniz) gösterilmektedir. **Ücretsiz**, **temel**ve **Standart** en yaygın katmanlardır.

**Free** , bir kümede, diğer aboneler ile paylaşılan sınırlı bir arama hizmeti oluşturur. Hızlı başlangıçler ve öğreticiler dahil olmak üzere küçük projeleri tamamlayabilirsiniz, ancak hizmeti ölçeklendirmez veya önemli iş yüklerini çalıştıramazsınız. **Temel** ve **Standart** , **Standart** olarak varsayılan değer olan en yaygın olarak kullanılan faturalanabilir katmanlardır.

![Azure Bilişsel Arama fiyatlandırma katmanları](media/search-sku-tier/tiers.png "Azure Bilişsel Arama fiyatlandırma katmanları")

Bazı katmanlar belirli iş türleri için iyileştirilmiştir. Örneğin, **Standart 3 yüksek yoğunluklu (S3 HD)** , S3 için bir *barındırma modudur* ; burada temeldeki donanım çok sayıda daha küçük dizinler için optimize edilmiştir ve çok kiracılı senaryolar için tasarlanmıştır. S3 HD, S3 ile aynı birim başına ücretine sahiptir, ancak donanım çok sayıda daha küçük dizin üzerinde hızlı dosya okuma için en iyi duruma getirilmiştir.

**Depolama Için iyileştirilmiş** katmanlar, standart katmanlardan TB başına daha büyük depolama kapasitesi sunar. Birincil zorunluluğunu getirir, belirli uygulama gereksinimleriniz için doğrulamanız gereken daha yüksek sorgu gecikmedir.  Bu katmanın performans konuları hakkında daha fazla bilgi için bkz. [performans ve iyileştirme konuları](search-performance-optimization.md).

[Fiyatlandırma sayfasındaki](https://azure.microsoft.com/pricing/details/search/)çeşitli katmanlar hakkında daha fazla bilgi edinmek için bkz. [Azure bilişsel arama 'deki hizmet sınırları](search-limits-quotas-capacity.md) ve hizmet sağlanırken Portal sayfası.

## <a name="billable-events"></a>Faturalandırılabilir olaylar

Azure Bilişsel Arama 'de oluşturulan bir çözüm, aşağıdaki yollarla maliyette ücret alabilir:

+ En düşük yapılandırmada hizmetin temel maliyeti (hizmet oluşturma)
+ Ölçeği artırma sırasında artımlı maliyet (çoğaltmalar veya bölümler ekleme)
+ Bant genişliği ücretleri (giden veri aktarımı) 
+ Bilişsel arama (AI zenginleştirme için bilişsel hizmetler, bilgi deposu için Azure depolama)

### <a name="service-costs"></a>Hizmet maliyetleri

Ücretlendirmeden kaçınmak için sanal makinelerden veya "duraklatılmış" olabilecek diğer kaynaklardan farklı olarak Azure Bilişsel Arama hizmeti, özel kullanım için ayrılmış donanımlar üzerinde her zaman kullanılabilir. Bu nedenle, hizmet oluşturma hizmeti oluştururken başlayan faturalandırılabilir bir olaydır ve hizmeti sildiğinizde sona erer. 

Minimum ücret, faturalandırılabilir fiyata ilk arama birimidir (bir çoğaltma x bir bölüm). Hizmet bu yapılandırmadan daha küçük bir süre içinde çalışmadığından hizmetin kullanım ömrü için bu minimum değer düzeltildi. En düşük değerin ötesinde, çoğaltmaları ve bölümleri birbirinden bağımsız olarak ekleyebilirsiniz. Çoğaltmalar ve bölümler aracılığıyla kapasitenin artımlı artışları, faturanızı aşağıdaki formüle göre artırır: [(çoğaltmalar x Partitions x oranı)](#search-units), burada ücretlendirilebilecek ücret seçtiğiniz fiyatlandırma katmanına bağlıdır.

Bir arama çözümünün maliyetini tahmin ediyorsanız, fiyatlandırma ve kapasitenin doğrusal değil olduğunu aklınızda bulundurun. (Katından fazla kapasite maliyeti iki katına çıkarır.) Formülün nasıl çalıştığına ilişkin bir örnek için bkz. [çoğaltmaları ve bölümleri ayırma](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Bant genişliği ücretleri

[Azure bilişsel arama dizin oluşturucularının](search-indexer-overview.md) kullanılması, hizmetlerinizin konumuna bağlı olarak faturalandırmayı etkileyebilir. Azure Bilişsel Arama hizmetini verileriniz ile aynı bölgede oluşturursanız, veri çıkış ücretlerini tamamen ortadan kaldırabilirsiniz. [Bant genişliği fiyatlandırma sayfasından](https://azure.microsoft.com/pricing/details/bandwidth/)bazı bilgiler aşağıda verilmiştir:

+ Microsoft, tüm gelen verileri Azure 'daki herhangi bir hizmete veya Azure Bilişsel Arama giden veriler için ücretlendirmez.
+ Çoklu hizmet çözümlerinde, tüm hizmetler aynı bölgedeyse, bu, kablo ile kesişen veriler için ücret alınmaz.

Hizmetler farklı bölgelerde olduğunda, giden veriler için ücretler uygulanır. Bu ücretler aslında Azure Bilişsel Arama faturanızın bir parçası değildir. Farklı bölgelerdeki verileri çekmek için veri veya AI zenginleştirilmiş dizinleyiciler kullanıyorsanız, maliyetleri genel faturanızda görürsünüz.

### <a name="ai-enrichment-with-cognitive-services"></a>Bilişsel hizmetler ile AI zenginleştirme

[AI zenginleştirme](cognitive-search-concept-intro.md)için, Kullandıkça Öde Işleme için S0 fiyatlandırma katmanında Azure bilişsel arama ile aynı bölgede [faturalandırılabilir bir Azure bilişsel hizmetler kaynağı eklemeyi](cognitive-search-attach-cognitive-services.md)planlamalısınız. Bilişsel hizmetler ekleme ile ilişkili sabit bir maliyet yoktur. Yalnızca ihtiyacınız olan işleme için ödeme yaparsınız.

| İşlem | Faturalama etkisi |
|-----------|----------------|
| Belge çözme, metin ayıklama | Ücretsiz |
| Belge çözme, görüntü ayıklama | Belgelerinizden ayıklanan görüntü sayısına göre faturalandırılır. Bir [Dizin Oluşturucu yapılandırmasında](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) **ımageaction** , görüntü ayıklamayı tetikleyen parametredir. **Imageaction** "none" (varsayılan) olarak ayarlandıysa, görüntü ayıklama için ücretlendirilmezsiniz. Görüntü ayıklama oranı, Azure Bilişsel Arama için [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/search/) sayfasında belgelenmiştir.|
| [Yerleşik bilişsel yetenekler](cognitive-search-predefined-skills.md) | Bilişsel hizmetler 'i doğrudan kullanarak görevi gerçekleştirdiyseniz aynı hızda faturalandırılır. |
| Özel beceriler | Özel bir beceri sağladığınız işlevsellikdir. Özel bir beceri kullanmanın maliyeti, tamamen özel kodun diğer ölçülen Hizmetleri çağırarak çağrılmayacağı konusunda farklılık gösterir. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Faturalandırma formülü (R x P = SU)

Azure Bilişsel Arama işlemlerinde anlaşılması için en önemli faturalandırma kavramı, *arama birimidir* (su). Azure Bilişsel Arama Dizin oluşturma ve sorgular için hem çoğaltmalara hem de bölümlere bağlı olduğundan, yalnızca bir veya başka bir fatura tarafından faturalandırılmıyor. Bunun yerine, faturalandırma her ikisinin de bir bileşimini temel alır.

SU, bir hizmet tarafından kullanılan *kopyaların* ve *bölümlerin* ürünüdür: **(R x P = su)** .

Her hizmet bir SU (bir çoğaltma bir bölümden çarpılarak) en düşük düzeyde başlar. Her hizmet için en fazla 36 SUs olur. Bu en fazla iki şekilde erişilebilir: 6 Bölüm x 6 çoğaltma veya 3 bölümden oluşan x 12 çoğaltma. Toplam kapasitenin (örneğin, 3 çoğaltma, 3 bölümlü bir hizmetin 9 ' da faturalandırılan) kullanılması yaygındır. Geçerli birleşimler için [bölüm ve çoğaltma birleşimleri](search-capacity-planning.md#chart) grafiğine bakın.

Fatura ücreti SU başına saatlik olarak belirlenir. Her katmanda aşamalı olarak daha yüksek bir hız vardır. Daha yüksek katmanlar daha büyük ve speedileyici bölümleri ile gelir ve bu, bu katman için genel olarak saatlik bir fiyat sağlar. [Fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/search/) sayfasında her bir katmanın oranlarını görüntüleyebilirsiniz.

Çoğu müşteri, kalanı ayrılmış olarak tutarak toplam kapasitenin yalnızca bir kısmını çevrimiçi duruma getirir. Faturalandırma için, çevrimiçi duruma getirdiğiniz bölüm ve çoğaltmaların sayısı, SU formülü tarafından hesaplandığınız, saatlik olarak ne ödediklerinizi belirler.

## <a name="how-to-manage-and-reduce-costs"></a>Maliyetleri yönetme ve azaltma

Aşağıdaki önerilere ek olarak [faturalandırma ve maliyet yönetimi](https://docs.microsoft.com/azure/billing/billing-getting-started)' ni ziyaret edin.

- Bant genişliği ücretlerini en aza indirmek veya ortadan kaldırmak için, aynı bölgedeki veya mümkün olduğunca az sayıda bölgede tüm kaynakları oluşturun.

- Tüm hizmetleri Azure Bilişsel Arama, bilişsel hizmetler ve çözümünüzde kullanılan diğer Azure hizmetleri gibi tek bir kaynak grubunda birleştirin. Azure portal, kaynak grubunu bulun ve gerçek ve tahmini harcama hakkında bilgi edinmek için **maliyet yönetimi** komutlarını kullanın.

- İstek ve yanıtların veri merkezi sınırının içinde kalması için, ön uç uygulamanız için Azure Web App 'i göz önünde bulundurun.

- Dizin oluşturma gibi yoğun kaynak gerektiren işlemlere göre ölçeklendirin ve normal sorgu iş yükleri için hemen aşağı doğru. Azure Bilişsel Arama için en düşük yapılandırma (bir bölümden ve bir çoğaltmayla bir SU) ile başlayın ve ardından daha fazla kapasiteye ihtiyacı olan kullanım düzenlerini belirlemek için Kullanıcı etkinliğini izleyin. Tahmin edilebilir bir model varsa, ölçeklendirmeyi etkinlikle eşitlenebilir (Bunu otomatikleştirmek için kod yazmanız gerekir).

Faturanızı azaltmak için bir arama hizmetini kapatamaz. Adanmış kaynaklar her zaman çalışır ve hizmetinizin kullanım ömrü boyunca özel kullanım için ayrılır. Hizmetin kendisi açısından, faturanızı düşürmenin tek yolu, çoğaltmaları ve bölümleri, kabul edilebilir performans ve [SLA uyumluluğu](https://azure.microsoft.com/support/legal/sla/search/v1_0/)sağlayan bir düzeye düşürmeniz veya daha düşük bir katmanda bir hizmet oluşturmanız (S1 saatlik fiyatlar S2 veya S3 oranlarından daha düşüktür). Hizmetinizi yük projeksiyonlarınızın alt ucunda sağladığınızda, hizmeti büyüyerek ikinci bir daha büyük katmanlı hizmet oluşturabilir, dizinlerinizi ikinci hizmette yeniden oluşturabilir ve sonra birincisini silebilirsiniz.

## <a name="how-to-evaluate-capacity-requirements"></a>Kapasite gereksinimlerini değerlendirme

Azure Bilişsel Arama, kapasite *çoğaltmalar* ve *bölümler*olarak yapılandırılır.

+ Çoğaltmalar, arama hizmetinin örnekleridir. Her çoğaltma, bir dizinin yük dengeli bir kopyasını barındırır. Örneğin, altı çoğaltmaya sahip bir hizmetin, hizmete yüklenen her dizinin altı kopyası vardır.

+ Bölümler depolar ve aranabilir verileri otomatik olarak böler. İki bölüm, dizininizi yarı bir şekilde böler, üç bölüm onu üçe ayırır ve bu şekilde devam eder. Kapasite açısından *bölüm boyutu* , katmanlar arasında birincil farklılaştırıcı özelliktir.

> [!NOTE]
> Tüm standart ve depolama için Iyileştirilmiş katmanlar, [çoğaltma ve bölümlerin esnek birleşimlerini](search-capacity-planning.md#chart) destekler, böylece dengeyi değiştirerek [sisteminizi hız veya depolama için iyileştirebilirsiniz](search-performance-optimization.md) . Temel katman, yüksek kullanılabilirlik için en fazla üç çoğaltma sunar, ancak yalnızca bir bölüme sahiptir. Ücretsiz katmanlar adanmış kaynaklar sağlamıyor: bilgi işlem kaynakları birden çok abone tarafından paylaşılır.

<!-- ## Consumption patterns

On the low and high ends, Basic and S3 HD are for important but atypical consumption patterns. Basic is for small production workloads. It offers SLAs, dedicated resources, and high availability, but it provides modest storage, topping out at 2 GB total. This tier was engineered for customers that consistently underutilize available capacity. At the high end, S3 HD is for workloads typical of ISVs, partners, [multitenant solutions](search-modeling-multitenant-saas-applications.md), or any configuration that calls for a large number of small indexes. It's often clear when Basic or S3 HD is the right tier. If you want confirmation, you can post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) or [contact Azure support](https://azure.microsoft.com/support/options/) for guidance.

The more commonly used standard tiers, S1 through S3, make up a progression of increasing levels of capacity. There are inflection points on partition size and limits on numbers of indexes, indexers, and corollary resources:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partition size|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index and indexer limits| 50 | 200 | 200 |  |  |  |  |

S1 is a common choice for customers that need dedicated resources and multiple partitions. S1 offers partitions of 25 GB and up to 12 partitions, providing a per-service limit of 300 GB if you maximize partitions over replicas. (See [Allocate partitions and replicas](search-capacity-planning.md#chart) for more balanced allocations.)

The portal and pricing pages put the focus on partition size and storage, but, for each tier, all compute capabilities (disk capacity, speed, CPUs) generally increase linearly with price. An S2 replica is faster than S1, and S3 is faster than S2. S3 tiers break from the linear compute-pricing pattern with disproportionately faster I/O. If you expect I/O to be the bottleneck, keep in mind that you can get much more IOPS with S3 than you can get with lower tiers.

S3 and S3 HD are backed by identical high-capacity infrastructure, but they reach their maximum limits in different ways. S3 targets a smaller number of very large indexes, so its maximum limit is resource-bound (2.4 TB for each service). S3 HD targets a large number of very small indexes. At 1,000 indexes, S3 HD reaches its limits in the form of index constraints. If you're an S3 HD customer and you need more than 1,000 indexes, contact Microsoft Support for information about how to proceed.

> [!NOTE]
> Document limits were a consideration at one time, but they're no longer applicable for new services. For information about conditions in which document limits still apply, see [Document limits](search-limits-quotas-capacity.md#document-limits).
>

Storage Optimized tiers, L1 and L2, are ideal for applications with large data requirements but a relatively low number of end users, when minimizing query latency isn't the top priority.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partition size|  1 TB | 2 TB |  |  |  |  |  |
| Index and indexer limits| 10 | 10 |  |  |  |  |  |

L2 offers twice the overall storage capacity of L1.  Choose your tier based on the maximum amount of data that you think your index needs. The L1 tier partitions scale up in 1-TB increments to a maximum of 12 TB. The L2 partitions increase by 2 TBs per partition up to a maximum of 24 TB. -->

### <a name="evaluating-capacity"></a>Kapasiteyi değerlendirme

Hizmetin kapasitesi ve maliyetleri, el ile çalışmaya devam ediyor. Katmanlar iki düzeyde sınırlar uygulayabilir: depolama ve kaynaklar. Her ikisi de düşünmeniz gerekir, çünkü ilk ulaşılan sınır etkin sınırdır.

İş gereksinimleri genellikle ihtiyaç duyacağınız dizin sayısını belirler. Örneğin, büyük bir belge deposu için genel bir dizine ihtiyacınız bulunabilir. Ya da bölge, uygulama veya iş kolu tabanlı birden çok dizine ihtiyacınız bulunabilir.

Bir dizinin boyutunu öğrenmek için [bir tane oluşturmanız](search-create-index-portal.md)gerekir. Azure Bilişsel Arama 'daki veri yapısı, birincil olarak kaynak verilerden farklı özelliklere sahip olan [ters çevrilmiş bir dizin](https://en.wikipedia.org/wiki/Inverted_index) yapısıdır. Ters bir dizin için boyut ve karmaşıklık, içeriğe göre belirlenir, bu, içinde yer alan veri miktarına göre değildir. Yüksek artıklığa sahip büyük bir veri kaynağı, yüksek oranda değişken içerik içeren küçük bir veri kümesinden daha küçük bir dizin oluşmasına neden olabilir. Bu nedenle, özgün veri kümesinin boyutuna bağlı olarak dizin boyutunu çıkarsmak nadiren mümkündür.

> [!NOTE] 
> Dizinler ve depolama için gelecekteki ihtiyaçları tahmin etmek de tahmin etmek gibi görünse de bunun yapılması gerekir. Bir katmanın kapasitesi çok düşük olursa, daha yüksek bir katmanda yeni bir hizmet sağlamanız ve ardından [dizinlerinizi yeniden yüklemeniz](search-howto-reindex.md)gerekir. Bir hizmetin bir SKU 'dan diğerine yerinde yükseltilmesi gerekmez.
>

### <a name="estimate-with-the-free-tier"></a>Ücretsiz katman ile tahmin edin

Kapasiteyi tahmin etmek için bir yaklaşım ücretsiz katmanla başlamadır. Ücretsiz hizmetin üç Dizin, 50 MB depolama alanı ve 2 dakikalık dizin oluşturma süresi sunduğunu unutmayın. Tahmini bir dizin boyutunu bu kısıtlamalarla tahmin etmek zor olabilir, ancak bunlar şu adımlardan biri:

+ [Ücretsiz bir hizmet oluşturun](search-create-service-portal.md).
+ Küçük, temsili bir veri kümesi hazırlayın.
+ [Portalda bir başlangıç dizini oluşturun](search-create-index-portal.md) ve boyutunu aklınızda edin. Özellikler ve özniteliklerin depolama üzerinde bir etkisi vardır. Örneğin, öneri araçları (typeahead) eklemek, depolama gereksinimlerini artıracaktır. Aynı veri kümesini kullanarak, Depolama gereksinimlerinin nasıl değişeceğini görmek için, her bir alanda farklı özniteliklere sahip bir dizinin birden çok sürümünü oluşturmayı deneyebilirsiniz. Daha fazla bilgi için [temel dizin oluşturma içindeki "depolama etkileri"](search-what-is-an-index.md#storage-implications)başlığına bakın.

El ile kabaca bir tahmin sayesinde, bu miktarı iki dizin (geliştirme ve üretim) için bütçeye katmanızı ve ardından katmanınızı uygun şekilde seçmenizi sağlayabilirsiniz.

### <a name="estimate-with-a-billable-tier"></a>Faturalandırılabilir katman ile tahmin

Adanmış kaynaklar, geliştirme sırasında dizin miktarının, boyutunun ve sorgu birimlerinin daha gerçekçi tahminleri için daha büyük örnekleme ve işleme sürelerine uyum sağlayabilir. Bazı müşteriler, faturalandırılabilir bir katmanda doğrudan geçiş yaparken geliştirme projesi olarak yeniden değerlendirilir.

1. Alt katmanların ihtiyacınız olan dizin sayısını destekleyip desteklemediğini öğrenmek için [Her katmandaki hizmet sınırlarını gözden geçirin](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) . Temel, S1 ve S2 katmanlarında Dizin sınırları sırasıyla 15, 50 ve 200 ' dir. Depolama için Iyileştirilmiş katmanın, az sayıda çok büyük dizini destekleyecek şekilde tasarlandığından 10 Dizin sınırı vardır.

1. [Faturalanabilir katmanda bir hizmet oluşturun](search-create-service-portal.md):

    + Tahmini yük hakkında emin değilseniz, temel veya S1 ' te düşük ' ı başlatın.
    + Büyük ölçekli dizin oluşturma ve sorgu yüklerine sahip olduğunuzu biliyorsanız, S2 veya hatta S3 ' da yüksek bir başlangıç yapın.
    + Depolama ile en Iyileştirilmiş, L1 veya L2 ile başlayın; büyük miktarda veri dizinleniyor ve sorgu yükü görece düşükse, dahili bir iş uygulamasında olduğu gibi.

1. Kaynak verilerin bir dizine nasıl çevrilip dönüştürmediğine yönelik [bir başlangıç dizini oluşturun](search-create-index-portal.md) . Bu, dizin boyutunu tahmin etmenin tek yoludur.

1. Portalda [depolama, hizmet limitleri, sorgu hacmi ve gecikme süresini izleyin](search-monitor-usage.md) . Portal, saniye başına sorgular, kısıtlanmış sorgular ve arama gecikme süresi gösterir. Bu değerlerin tümü, doğru katmanı seçtiğinizden karar vermenize yardımcı olabilir. 

Dizin numarası ve boyutu, analizinizi eşit derecede önemlidir. Bunun nedeni, en fazla depolama (bölüm) kullanımı veya kaynakların (dizinler, Dizin oluşturucular, vb.) en üst limitlerinin (hangisi önce geldiğini) tam olarak ulaşılmasını içerir. Portal, Genel Bakış sayfasında geçerli kullanımı ve en fazla sınırları yan yana göstererek her ikisini de izlemenize yardımcı olur.

> [!NOTE]
> Belgeler, fazlalık veriler içeriyorsa depolama gereksinimleri daraltılabilir. İdeal olarak, belgeler yalnızca arama deneyimi için gereken verileri içerir. İkili veriler aranabilir değildir ve ayrı ayrı depolanmalıdır (Belki de bir Azure tablosu veya blob depolamada olabilir). Bir alan, dış verilere bir URL başvurusu tutmak için dizine eklenmelidir. Tek bir belgenin en büyük boyutu 16 MB 'tır (veya bir istekte birden çok belgeyi toplu olarak karşıya yüklüyorsanız, daha az). Daha fazla bilgi için bkz. [Azure bilişsel arama hizmet limitleri](search-limits-quotas-capacity.md).
>

**Sorgu hacmi konuları**

Saniyedeki sorgu sayısı (QPS), performans ayarlama sırasında önemli bir ölçümdür, ancak genellikle bir katman, mantıksal olarak yüksek sorgu hacmi beklemeniz durumunda yalnızca bir katman önem taşımaktadır.

Standart katmanlar, çoğaltmaları ve bölümleri dengeleyebilir. Yük Dengeleme için çoğaltmalar ekleyerek veya paralel işleme için bölüm eklemek yoluyla sorgu kapatma işlemini artırabilirsiniz. Daha sonra hizmet sağlandıktan sonra performansı ayarlayabilirsiniz.

Outset 'ten yüksek hacimli sorgu birimleri düşünüyorsanız, daha güçlü donanımlar tarafından desteklenen, daha yüksek standart katmanları göz önünde bulundurmanız gerekir. Daha sonra, bu sorgu birimleri gerçekleşmese de bölümleri ve çoğaltmaları çevrimdışı duruma geçirebilir veya alt katman bir hizmete geçebilirsiniz. Sorgu verimini hesaplama hakkında daha fazla bilgi için bkz. [Azure bilişsel arama performansı ve iyileştirmesi](search-performance-optimization.md).

Depolama için Iyileştirilmiş katmanlar, büyük veri iş yükleri için yararlıdır ve sorgu gecikme süresi gereksinimleri daha az önemli olduğunda için daha fazla genel kullanılabilir dizin depolaması destekler. Yük Dengeleme için ek çoğaltmalar ve paralel işleme için ek bölümler kullanmanız gerekir. Daha sonra hizmet sağlandıktan sonra performansı ayarlayabilirsiniz.

**Hizmet düzeyi sözleşmeleri**

Ücretsiz katman ve Önizleme özellikleri [hizmet düzeyi sözleşmeleri (SLA 'lar)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)sağlamaz. Tüm faturalanabilir katmanlar için, hizmet için yeterli artıklık sağladığınızda SLA 'Lar devreye girer. Sorgu (okuma) SLA 'Ları için iki veya daha fazla kopyaya sahip olmanız gerekir. Sorgu ve dizin oluşturma (okuma-yazma) SLA 'Ları için üç veya daha fazla kopyaya sahip olmanız gerekir. Bölüm sayısı SLA 'Ları etkilemez.

## <a name="tips-for-tier-evaluation"></a>Katman değerlendirmesi için ipuçları

+ Ölçümlerin sorguları etrafında derleme yapmasına ve kullanım desenlerinde (iş saatleri sırasında sorgular, yoğun olmayan saatlerde dizin oluşturma) veri toplamasına izin verin. Hizmet sağlama kararlarını bilgilendirmek için bu verileri kullanın. Saatlik veya günlük temposunda pratik olmasa da, sorgu birimlerindeki planlı değişikliklere uyum sağlamak için bölümleri ve kaynakları dinamik olarak ayarlayabilirsiniz. Ayrıca, düzeylerin işlem yapmak için yeterince uzun sürmesi durumunda planlanmamış ancak sürekli değişikliklere de uyum sağlayabilirsiniz.

+ Düşük sağlamanın tek alt tarafının, gerçek gereksinimler tahmine göre daha büyükse bir hizmeti bölmek zorunda kalmamanız gerektiğini unutmayın. Hizmet kesintisi yaşamamak için, daha yüksek bir katmanda aynı abonelikte yeni bir hizmet oluşturun ve tüm uygulamalar ve istekler yeni uç noktayı hedefleyerek yan yana çalıştırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Ücretsiz bir katmanla başlayın ve özelliklerini anlamak için verilerinizin bir alt kümesini kullanarak bir başlangıç dizini oluşturun. Azure Bilişsel Arama veri yapısı ters bir dizin yapısıdır. Ters çevrilen bir dizinin boyutu ve karmaşıklığı içeriğe göre belirlenir. Yüksek oranda yedekli içeriğin çok sayıda düzensiz içerikten daha küçük bir dizine neden olduğunu unutmayın. Bu nedenle, veri kümesinin boyutu yerine içerik özellikleri dizin depolama gereksinimlerini belirlemektir.

Dizin boyutunuzu ilk tahmine sahip olduktan sonra, bu makalede ele alınan katmanlardan birinde [faturalandırılabilir bir hizmet sağlayın](search-create-service-portal.md) : temel, standart veya depolama için iyileştirilmiş. Veri boyutlandırmasına ilişkin yapay kısıtlamaları rahatlaın ve dizinizi, aranabilir olmasını istediğiniz tüm verileri içerecek şekilde [yeniden derleyin](search-howto-reindex.md) .

İhtiyaç duyduğunuz performans ve ölçeği almak için gereken [bölümleri ve çoğaltmaları ayırın](search-capacity-planning.md) .

Performans ve kapasite iyi durumdaysa, işiniz bitti demektir. Aksi takdirde, gereksinimlerinize göre daha yakından hizalanan bir arama hizmetini farklı bir katmanda yeniden oluşturun.

> [!NOTE]
> Sorularınız varsa, [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ' e gönderin veya [Azure desteği ile iletişime geçin](https://azure.microsoft.com/support/options/).
