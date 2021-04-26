---
title: Kullanılabilirlik ve süreklilik
titleSuffix: Azure Cognitive Search
description: bir arama hizmetini yüksek oranda kullanılabilir hale getirme, dönem kesintilerini veya hatta çok zararlı hatalara karşı dayanıklı bir şekilde öğrenin.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: 493f6759f63f023572f38647076e04425acf9d6a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581532"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Azure Bilişsel Arama kullanılabilirlik ve iş sürekliliği

Bilişsel Arama, kullanılabilirlik birden fazla çoğaltma aracılığıyla elde edilir, ancak iş sürekliliği (ve olağanüstü durum kurtarma) birden çok arama hizmeti aracılığıyla gerçekleştirilir. Bu makale, hem kullanılabilirlik hem de sürekli işlemler için iş gereksinimlerinizi karşılayan bir strateji geliştirmek üzere bir başlangıç noktası olarak kullanabileceğiniz rehberlik sağlar.

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>Yüksek kullanılabilirlik

Bilişsel Arama, çoğaltmalar dizininizdeki kopyalardır. Birden çok çoğaltmanın olması, Azure Bilişsel Arama 'nin makinenin yeniden başlatmalar ve bakım yapmasına olanak sağlarken, sorgu yürütme diğer çoğaltmalarda devam eder. Çoğaltmaları ekleme hakkında daha fazla bilgi için bkz. [çoğaltmaları ve bölümleri ekleme veya azaltma](search-capacity-planning.md#adjust-capacity).

Microsoft, her bir arama hizmeti için, şu ölçütlere uyan yapılandırmalarda en az% 99,9 kullanılabilirlik garantisi verir: 

+ Salt okuma iş yüklerinin yüksek kullanılabilirliği için iki çoğaltma (sorgular)

+ Okuma/yazma iş yüklerinin yüksek kullanılabilirliği için üç veya daha fazla çoğaltma (sorgular ve dizin oluşturma) 

Ücretsiz katman için SLA sağlanmaz. Daha fazla bilgi için bkz. [Azure bilişsel arama Için SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

<a name="availability-zones"></a>

## <a name="availability-zones"></a>Kullanılabilirlik Alanları

[Kullanılabilirlik alanları](../availability-zones/az-overview.md) , bir bölgenin veri merkezlerini, aynı bölgede yüksek kullanılabilirlik sağlamak üzere ayrı fiziksel konum gruplarına ayıran bir Azure platformu özelliğidir. Bilişsel Arama için Kullanılabilirlik Alanları kullanırsanız, bireysel çoğaltmalar bölge atamasının birimleridir. Bir arama hizmeti tek bir bölgede çalışır; çoğaltmaları farklı bölgelerde çalışır.

Arama hizmetinize iki veya daha fazla çoğaltma ekleyerek Azure Bilişsel Arama ile Kullanılabilirlik Alanları kullanabilirsiniz. Her çoğaltma, bölge içinde farklı bir kullanılabilirlik bölgesine yerleştirilir. Kullanılabilirlik Alanları daha fazla çoğaltmadıysanız çoğaltmalar, olabildiğince eşit Kullanılabilirlik Alanları dağıtılır. Bölüminizdeki belirli bir eylem yoktur, Kullanılabilirlik Alanları ve ardından hizmeti [birden çok çoğaltma kullanmak](search-capacity-planning.md#adjust-capacity)üzere yapılandırmak için [bir arama hizmeti oluşturun](search-create-service-portal.md) .

Azure Bilişsel Arama şu anda şu bölgelerden birinde oluşturulan Standart katman veya daha yüksek arama hizmetleri için Kullanılabilirlik Alanları desteklemektedir:

+ Avustralya Doğu (30 Ocak 2021 veya üzeri oluşturulanlar)
+ Kanada Orta (30 Ocak 2021 veya üzeri oluşturulanlar)
+ Orta ABD (4 Aralık 2020 veya üzeri)
+ Doğu ABD (27 Ocak 2021 veya üzeri oluşturulan)
+ Doğu ABD 2 (30 Ocak 2021 veya üzeri oluşturulanlar)
+ Fransa Orta (23 Ekim 2020 veya üzeri oluşturuldu)
+ Japonya Doğu (30 Ocak 2021 veya üzeri oluşturulanlar)
+ Kuzey Avrupa (28 Ocak 2021 veya üzeri oluşturulan)
+ Güney Doğu Asya (31 Ocak 2021 veya üzeri oluşturulan)
+ UK Güney (30 Ocak 2021 veya üzeri oluşturulanlar)
+ Batı Avrupa (29 Ocak 2021 veya üzeri oluşturulanlar)
+ Batı ABD 2 (30 Ocak 2021 veya üzeri oluşturulanlar)

Kullanılabilirlik Alanları [Azure Bilişsel Arama hizmet düzeyi sözleşmesi](https://azure.microsoft.com/support/legal/sla/search/v1_0/)etkilemez. Sorgu yüksek kullanılabilirlik için hala 3 veya daha fazla çoğaltma gerekir.

## <a name="multiple-services-in-separate-geographic-regions"></a>Ayrı coğrafi bölgelerde birden çok hizmet

Müşterilerin çoğu yalnızca bir hizmet kullansa da, işletim gereksinimleri arasında aşağıdakiler yer alıyorsa hizmet yedekliliği gerekebilir:

+ [İş sürekliliği ve olağanüstü durum kurtarma (BCDR)](../best-practices-availability-paired-regions.md) (bilişsel arama kesinti durumunda anlık yük devretme sağlamaz).
+ Küresel olarak dağıtılan uygulamalar. Sorgu ve dizin oluşturma istekleri dünyanın her yerinden geliyorsa, ana bilgisayar veri merkezine en yakın olan kullanıcılar daha hızlı performansa sahip olur. Bu kullanıcılara yakın yakınlığa sahip bölgelerde ek hizmetler oluşturmak, tüm kullanıcılar için performansı eşitleyebilirsiniz.
+ [Çok kiracılı mimariler](search-modeling-multitenant-saas-applications.md) bazen iki veya daha fazla hizmet için çağrı yapılır.

İki farklı arama hizmetine ihtiyacınız varsa, bunları farklı bölgelerde oluşturmak süreklilik ve kurtarmaya yönelik uygulama gereksinimlerini ve genel kullanıcı tabanı için daha hızlı yanıt sürelerini karşılayabilir.

Azure Bilişsel Arama, bölge genelinde coğrafi çoğaltma için arama dizinlerinin otomatik bir yöntemini sağlamıyor, ancak bu işlemi uygulamak ve yönetmek için basit hale getirmek üzere kullanılabilecek bazı teknikler vardır. Bunlar, sonraki birkaç bölümde özetlenmiştir.

Coğrafi olarak dağıtılan bir arama hizmeti kümesinin hedefi, iki veya daha fazla bölgede iki veya daha fazla dizine sahip olmak için bir kullanıcının Azure Bilişsel Arama hizmetine yönlendirildiği, en düşük gecikme süresini sağlayan iki veya daha fazla dizin olması gerekir:

   ![Bölgeye göre hizmetlerin çapraz sekmesi][1]

Bu mimariyi, birden çok hizmet oluşturarak ve veri eşitleme için bir strateji tasarlayarak uygulayabilirsiniz. İsteğe bağlı olarak, yönlendirme istekleri için Azure Traffic Manager gibi bir kaynak ekleyebilirsiniz. Daha fazla bilgi için bkz. [Arama hizmeti oluşturma](search-create-service-portal.md).

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>Verileri birden çok hizmet arasında eşitlenmiş durumda tut

[Azure bilişsel arama Dizin oluşturucuyu](search-indexer-overview.md) veya anında iletme API 'Sini ( [Azure bilişsel arama REST API](/rest/api/searchservice/)olarak da bilinir) kullanarak, iki veya daha fazla dağıtılmış arama hizmetini eşitlenmiş halde tutmak için kullanabileceğiniz iki seçenek vardır. 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>Seçenek 1: birden çok hizmet üzerinde içerik güncelleştirmek için Dizin oluşturucular kullanma

Bir hizmette zaten Dizin Oluşturucu kullanıyorsanız, ikinci bir hizmette ikinci bir Dizin Oluşturucu yapılandırarak aynı veri kaynağı nesnesini kullanarak aynı konumdan veri çekmesini sağlayabilirsiniz. Her bölgedeki her bir hizmetin kendi Dizin Oluşturucusu ve bir hedef dizini vardır (arama dizininiz paylaşılmaz, bu da veriler yinelenir), ancak her Dizin Oluşturucu aynı veri kaynağına başvurur.

Mimarinin nasıl görüneceğine ilişkin üst düzey bir görsel aşağıda verilmiştir.

   ![Dağıtılmış Dizin Oluşturucu ve hizmet bileşimleri içeren tek veri kaynağı][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>2. seçenek: içerik güncelleştirmelerini birden çok hizmete göndermek için REST API 'Lerini kullanma

[Arama dizininize içerik göndermek](tutorial-optimize-indexing-push-api.md)için Azure bilişsel arama REST API kullanıyorsanız, her bir güncelleştirme gerektiğinde tüm arama hizmetlerine değişiklikleri göndererek çeşitli arama hizmetlerinizi eşitlenmiş halde tutabilirsiniz. Kodunuzda, bir arama hizmetine yapılan bir güncelleştirmenin başarısız olduğu ancak diğer arama hizmetleri için başarılı olduğu durumları işlediğinizden emin olun.

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>İstekleri koordine etmek için Azure Traffic Manager kullanma

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) , istekleri birden çok arama hizmeti tarafından desteklenen, coğrafi olarak bulunan birden çok Web sitesine yönlendirmenize olanak tanır. Traffic Manager avantajlarından biri, kullanılabilir olmasını sağlamak için Azure Bilişsel Arama araştırma yapabilir ve kapalı kalma durumunda kullanıcıları alternatif arama hizmetleri 'ne yönlendirebilir. Ayrıca, Azure Web siteleri aracılığıyla arama isteklerini yönlendirçalışıyorsanız Azure Traffic Manager, Web sitesinin en fazla Azure Bilişsel Arama olmadığı, Yük Dengeleme durumlarını yüklemenize izin verir. Traffic Manager yararlanan mimarinin bir örneği aşağıda verilmiştir.

   ![Merkezi Traffic Manager ile bölgelere göre hizmetlerin çapraz sekmesi][3]

## <a name="disaster-recovery-and-service-outages"></a>Olağanüstü durum kurtarma ve hizmet kesintileri

Verilerinizi, veri merkezi düzeyinde bir kesinti varsa Azure Bilişsel Arama, hizmetin anında yük devretmesini sağlamaz. Veri merkezinde bir küme başarısız olursa, işlemler ekibi, hizmeti algılar ve geri yükleme işlemini çalışır. Hizmet geri yükleme sırasında kapalı kalma süresi yaşarsınız, ancak [hizmet düzeyi sözleşmesi (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)başına hizmet kullanım dışı kalması için hizmet kredileri isteyebilirsiniz. 

Microsoft 'un denetimi dışındaki çok sayıda hata oluşması durumunda sürekli hizmet gerekliyse, farklı bir bölgede [ek bir hizmet](search-create-service-portal.md) sağlayabilir ve dizinlerin tüm hizmetlerde tamamen yedekli olmasını sağlamak için coğrafi çoğaltma stratejisi uygulayabilirsiniz.

Dizinleri doldurmak ve yenilemek için [Dizin oluşturucular](search-indexer-overview.md) kullanan müşteriler, aynı veri kaynağından yararlanan coğrafi olarak özel Dizin oluşturucular aracılığıyla olağanüstü durum kurtarmayı işleyebilir. Her biri Dizin Oluşturucu çalıştıran farklı bölgelerdeki iki hizmet, coğrafi yedeklilik sağlamak için aynı veri kaynağını dizinlede olabilir. Aynı zamanda coğrafi olarak yedekli veri kaynaklarından dizin oluşturuyorsanız, Azure Bilişsel Arama Dizin oluşturucuların birincil çoğaltmalardan yalnızca artımlı dizin oluşturma (yeni, değiştirilen veya silinen belgelerden güncelleştirmeleri birleştirme) gerçekleştirebildiğinizden haberdar olun. Bir yük devretme olayında, Dizin oluşturucuyu yeni birincil çoğaltmaya yeniden işaret ettiğinizden emin olun. 

Dizin oluşturucular kullanmıyorsanız, nesneleri ve verileri farklı arama hizmetlerine paralel olarak göndermek için uygulama kodunuzu kullanabilirsiniz. Daha fazla bilgi için bkz. [verileri birden çok hizmet arasında eşitlenmiş halde tutma](#data-sync).

## <a name="back-up-and-restore-alternatives"></a>Yedekleme ve geri yükleme alternatifleri

Azure Bilişsel Arama bir birincil veri depolama çözümü olmadığından, Microsoft Self Servis yedekleme ve geri yükleme için resmi bir mekanizma sağlamaz. Ancak, Dizin tanımınızı ve anlık görüntüsünü bir dizi JSON dosyasına yedeklemek için bu [Azure bilişsel arama .NET örnek](https://github.com/Azure-Samples/azure-search-dotnet-samples) deposundaki **Dizin-yedekleme-geri yükleme** örnek kodunu kullanabilir ve gerekirse dizini geri yüklemek için bu dosyaları kullanabilirsiniz. Bu araç Ayrıca, dizinleri hizmet katmanları arasında taşıyabilir.

Aksi halde, dizin oluşturmak ve doldurmak için kullanılan uygulama kodunuz, yanlışlıkla bir dizini silerseniz geri yükleme ve geri yükleme seçeneğidir. Bir dizini yeniden oluşturmak için (varsa) onu siler, hizmette dizini yeniden oluşturun ve birincil veri deponuzdan verileri alarak yeniden yükleyin.

## <a name="next-steps"></a>Sonraki adımlar

Her biri için fiyatlandırma katmanları ve hizmet limitleri hakkında daha fazla bilgi edinmek için bkz. [hizmet limitleri](search-limits-quotas-capacity.md). Bölüm ve çoğaltma birleşimleri hakkında daha fazla bilgi edinmek için bkz. [kapasiteyi planlayın](search-capacity-planning.md) .

Bu makalede ele alınan tekniklerin performansı ve gösterileri hakkında bir tartışma için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png