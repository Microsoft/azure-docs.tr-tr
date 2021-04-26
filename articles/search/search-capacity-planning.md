---
title: Sorgu ve Dizin iş yükleri için kapasiteyi tahmin etme
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de, her kaynağın faturalandırılabilir arama birimlerinde fiyatlandırıldıkları bölüm ve çoğaltma bilgisayar kaynaklarını ayarlayın.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: b1f742c1de259f6c1c06d9b31a8788699f0b8426
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580037"
---
# <a name="estimate-and-manage-capacity-of-an-azure-cognitive-search-service"></a>Azure Bilişsel Arama hizmetinin kapasitesini tahmin edin ve yönetin

Belirli bir fiyatlandırma katmanında [bir arama hizmeti](search-create-service-portal.md) ve kilitlemeyi sağlamadan önce, kapasitenin nasıl çalıştığını ve iş yükü dalgalanmasına uyum sağlamak için çoğaltmaları ve bölümleri nasıl ayarlayabileceğinizi anlamak için birkaç dakikanızı yararlanın.

Kapasite, [hizmet katmanının](search-sku-tier.md)bir işlevidir, hizmet başına en fazla depolama alanı, bölüm başına ve oluşturabileceğiniz nesne sayısı üst sınırı. Temel katman, bir yüksek kullanılabilirlik yapılandırmasında (3 çoğaltma) çalışma olanağı sunan uygulamalar için tasarlanmıştır. Diğer katmanlar, çok kiracılı gibi belirli iş yükleri veya desenler için tasarlanmıştır. Dahili olarak, bu katmanlarda oluşturulan hizmetler bu senaryolara yardımcı olan donanımdan faydalanır.

Azure Bilişsel Arama 'de ölçeklenebilirlik mimarisi, daha fazla sorgu veya dizin oluşturma gücü olmasına bağlı olarak kapasiteyi değiştirebilmeniz için çoğaltmaların ve bölümlerin esnek birleşimlerine dayanır. Bir hizmet oluşturulduktan sonra çoğaltma veya bölümlerin sayısını bağımsız olarak artırabilir veya azaltabilirsiniz. Maliyetler her ek fiziksel kaynakla çalışmaya devam eder, ancak büyük iş yükleri tamamlandığında faturanızı düşürmek için ölçeği azaltabilirsiniz. Katmana ve ayarlamanın boyutuna bağlı olarak, kapasite ekleme veya azaltma, 15 dakikadan birkaç saate kadar herhangi bir zaman alabilir.

Kopyaların ve bölümlerin tahsisini değiştirirken Azure portal kullanmanızı öneririz. Portal, bir katmanın en fazla limitlerini takip eden izin verilen birleşimler üzerinde sınırlar uygular. Ancak, komut dosyası tabanlı veya kod tabanlı bir sağlama yaklaşımına ihtiyacınız varsa [Azure PowerShell](search-manage-powershell.md) veya [Yönetim REST API](/rest/api/searchmanagement/services) alternatif çözümlerdir.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Kavramlar: ara birimler, çoğaltmalar, bölümler, parçalar

Kapasite, esnek yapılandırmaların desteklenmesi için temel bir *parça* mekanizması kullanılarak, *bölüm* ve *çoğaltmaların* birleşimlerinde ayrılabilecek *arama birimlerinde* ifade edilir:

| Konsept  | Tanım|
|----------|-----------|
|*Arama birimi* | Toplam kullanılabilir kapasitenin tek bir artışı (36 birim). Ayrıca, bir Azure Bilişsel Arama hizmeti için faturalandırma birimidir. Hizmeti çalıştırmak için en az bir birim gereklidir.|
|*Çoğaltma* | Arama hizmeti örnekleri, öncelikli olarak sorgu işlemlerinin yükünü dengelemek için kullanılır. Her çoğaltma bir dizinin bir kopyasını barındırır. Üç çoğaltma ayırırsanız, sorgu isteklerine hizmet vermek için kullanılabilir bir dizinin üç kopyasına sahip olursunuz.|
|*Bölüm* | Okuma/yazma işlemleri için fiziksel depolama ve g/ç (örneğin, bir dizini yeniden oluşturma veya yenileme). Her bölümde toplam dizinin bir dilimi bulunur. Üç bölüm ayırırsanız, dizininiz üçe bölünür. |
|*Parça* | Bir dizinin öbeği. Azure Bilişsel Arama, bölümleri daha hızlı ekleme işlemini yapmak için her bir dizini parçalara ayırır (parçaları yeni arama birimlerine taşıyarak).|

Aşağıdaki diyagramda çoğaltmalar, bölümler, parçalar ve arama birimleri arasındaki ilişki gösterilmektedir. İki çoğaltma ve iki bölümden oluşan bir hizmette, tek bir dizinin dört arama birimi arasında nasıl yayıldığından bir örnek gösterir. Dört arama biriminin her biri, dizinin parçaları yalnızca yarısını depolar. Sol sütundaki arama birimleri ilk bölümden oluşan ilk parçayı depolarken, sağ sütundaki parçaların ikinci yarısını depolarken ikinci bölümden oluşan bir şekilde depolar. İki çoğaltma olduğundan, her bir dizin parçanın iki kopyası vardır. En üstteki satırdaki arama birimleri, ilk yinelemeyi kapsayan, alt satırdaki diğer bir kopyayı ikinci çoğaltmadan oluşan başka bir kopya depolarken bir kopya depolar.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Arama dizinleri bölümler arasında parçalı olarak bulunur.":::

Yukarıdaki diyagramda yalnızca bir örnek vardır. Birçok bölüm ve çoğaltma kombinasyonu, en fazla 36 toplam arama birimine kadar mümkündür.

Bilişsel Arama, parça yönetimi bir uygulama ayrıntısı ve yapılandırılamayan, ancak bir dizinin parçalı olduğunu bilmek, derecelendirme ve otomatik tamamlama davranışlarındaki zaman zaman anormallikleri anlamaya yardımcı olur:

+ Sıralama bozukluileri: arama puanları önce parça düzeyinde hesaplanır ve sonra tek bir sonuç kümesine toplanır. Parça içeriğinin özelliklerine bağlı olarak, bir parçadaki eşleşmeler, başka bir parçadaki eşleşmelerle daha yüksek bir şekilde derecelendirilir. Arama sonuçlarındaki sayaç sezgisel derecelendirmeme bildirimini fark ederseniz büyük olasılıkla, özellikle de dizinler küçük olduğunda parçalama etkileri yüksektir. [Puanları tüm dizinde küresel olarak hesaplama](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions)seçeneğini belirleyerek bu derecelendirme bozuklukilerini önleyebilirsiniz, ancak bunu yapmak bir performans cezası olur.

+ Otomatik tamamlama bozuklukları: kısmi olarak girilen bir terimin ilk birkaç karakterinin üzerinde eşleşme yapıldığında otomatik tamamlama sorguları, yazım içinde küçük sapmalar sağlayan benzer bir parametre kabul eder. Otomatik tamamlama için, belirsiz eşleştirme, geçerli parça içindeki koşullara göre kısıtlanmıştır. Örneğin, bir parça "Microsoft" ve kısmi "micor" terimi girilmişse, arama altyapısı bu parça içinde "Microsoft" ile eşleşir, ancak dizinin kalan bölümlerini tutan diğer parçalar üzerinde değildir.

## <a name="approaching-estimation"></a>Tahmine yaklaşıyoruz

Hizmetin kapasitesi ve maliyetleri, el ile çalışmaya devam ediyor. Katmanlar iki düzeyde sınırlar yapar: içerik (bir hizmetteki dizinlerin sayısı, örneğin, bir hizmet) ve depolama alanı. Her ikisini de göz önünde bulundurmanız önemlidir çünkü ilk ulaşılan sınır etkin limit.

Dizinlerin ve diğer nesnelerin sayısı genellikle iş ve mühendislik gereksinimlerine göre belirlenir. Örneğin, etkin geliştirme, test ve üretim için aynı dizinin birden fazla sürümüne sahip olabilirsiniz.

Depolama ihtiyaçları, derlemek istediğiniz dizinlerin boyutuna göre belirlenir. Tahminlerle ilgili olarak sağlam bir buluşsal yöntem veya genellik yoktur. Bir dizinin boyutunu belirlemenin tek yolu [derleme](search-what-is-an-index.md)amaçlıdır. Boyutu, içeri aktarılan verileri, metin analizini ve öneri araçları, filtrelemesini ve sıralamayı etkinleştirip etkinleştirmeyeceğinizi, dizin yapılandırmasını temel alır.

Tam metin arama için, birincil veri yapısı, kaynak verilerden farklı özelliklere sahip olan [ters bir dizin](https://en.wikipedia.org/wiki/Inverted_index) yapısıdır. Ters bir dizin için boyut ve karmaşıklık, içeriğe göre belirlenir, bu, içinde yer alan veri miktarına göre değildir. Yüksek artıklığa sahip büyük bir veri kaynağı, yüksek oranda değişken içerik içeren küçük bir veri kümesinden daha küçük bir dizin oluşmasına neden olabilir. Bu nedenle, özgün veri kümesinin boyutuna bağlı olarak dizin boyutunu çıkarsmak nadiren mümkündür.

Dizin üzerindeki, filtreleri ve sıralamayı etkinleştirme gibi öznitelikler, depolama gereksinimlerini etkiler. Öneri araçları 'in kullanımı, depolama etkilerine da sahiptir. Daha fazla bilgi için bkz. [öznitelikler ve dizin boyutu](search-what-is-an-index.md#index-size).

> [!NOTE]
> Dizinler ve depolama için gelecekteki ihtiyaçları tahmin etmek de tahmin etmek gibi görünse de bunun yapılması gerekir. Bir katmanın kapasitesi çok düşük olursa, daha yüksek bir katmanda yeni bir hizmet sağlamanız ve ardından [dizinlerinizi yeniden yüklemeniz](search-howto-reindex.md)gerekir. Bir hizmetin bir katmandan diğerine yerinde yükseltilmesi yoktur.
>

### <a name="estimate-with-the-free-tier"></a>Ücretsiz katman ile tahmin edin

Kapasiteyi tahmin etmek için bir yaklaşım ücretsiz katmanla başlamadır. Ücretsiz hizmetin üç Dizin, 50 MB depolama alanı ve 2 dakikalık dizin oluşturma süresi sunduğunu unutmayın. Tahmini bir dizin boyutunu bu kısıtlamalarla tahmin etmek zor olabilir, ancak bunlar şu adımlardan biri:

+ [Ücretsiz bir hizmet oluşturun](search-create-service-portal.md).
+ Küçük, temsili bir veri kümesi hazırlayın.
+ Bir dizin oluşturun ve verilerinizi yükleyin. Veri kümesi, Dizin oluşturucular tarafından desteklenen bir Azure veri kaynağında barındırılıyorsa, dizini oluşturmak ve yüklemek için [portalda veri alma Sihirbazı 'nı](search-get-started-portal.md) kullanabilirsiniz. Aksi takdirde, dizini oluşturmak ve verileri göndermek için REST ve [Postman](search-get-started-rest.md) veya [Visual Studio Code](search-get-started-vs-code.md) kullanmanız gerekir. Gönderim modeli, verilerin, belgedeki alanların dizindeki alanlarla karşılık geldiği JSON belgeleri biçiminde olmasını gerektirir.
+ Dizin hakkında boyut gibi bilgileri toplayın. Özellikler ve özniteliklerin depolama üzerinde bir etkisi vardır. Örneğin, öneri araçları (arama-yazma sorguları) ekleme, depolama gereksinimlerini artıracaktır. 

  Aynı veri kümesini kullanarak, Depolama gereksinimlerinin nasıl değişeceğini görmek için, her bir alanda farklı özniteliklere sahip bir dizinin birden çok sürümünü oluşturmayı deneyebilirsiniz. Daha fazla bilgi için [temel dizin oluşturma içindeki "depolama etkileri"](search-what-is-an-index.md#index-size)başlığına bakın.

El ile kabaca bir tahmin sayesinde, bu miktarı iki dizin (geliştirme ve üretim) için bütçeye katmanızı ve ardından katmanınızı uygun şekilde seçmenizi sağlayabilirsiniz.

### <a name="estimate-with-a-billable-tier"></a>Faturalandırılabilir katman ile tahmin

Adanmış kaynaklar, geliştirme sırasında dizin miktarının, boyutunun ve sorgu birimlerinin daha gerçekçi tahminleri için daha büyük örnekleme ve işleme sürelerine uyum sağlayabilir. Bazı müşteriler, faturalandırılabilir bir katmanda doğrudan geçiş yaparken geliştirme projesi olarak yeniden değerlendirilir.

1. Alt katmanların ihtiyacınız olan dizin sayısını destekleyip desteklemediğini öğrenmek için [Her katmandaki hizmet sınırlarını gözden geçirin](./search-limits-quotas-capacity.md#index-limits) . Temel, S1 ve S2 katmanlarında Dizin sınırları sırasıyla 15, 50 ve 200 ' dir. Depolama için Iyileştirilmiş katmanın, az sayıda çok büyük dizini destekleyecek şekilde tasarlandığından 10 Dizin sınırı vardır.

1. [Faturalanabilir katmanda bir hizmet oluşturun](search-create-service-portal.md):

    + Tahmini yük hakkında emin değilseniz, temel veya S1 ' te düşük ' ı başlatın.
    + Test, büyük ölçekli dizin oluşturma ve sorgu yüklerini içeriyorsa, S2 veya hatta S3 ' da yüksek bir başlangıç yapın.
    + Depolama ile en Iyileştirilmiş, L1 veya L2 ile başlayın; büyük miktarda veri dizinleniyor ve sorgu yükü görece düşükse, dahili bir iş uygulamasında olduğu gibi.

1. Kaynak verilerin bir dizine nasıl çevrilip dönüştürmediğine yönelik [bir başlangıç dizini oluşturun](search-what-is-an-index.md) . Bu, dizin boyutunu tahmin etmenin tek yoludur. 

1. Portalda [depolama, hizmet limitleri, sorgu hacmi ve gecikme süresini izleyin](search-monitor-usage.md) . Portal, saniye başına sorgular, kısıtlanmış sorgular ve arama gecikme süresi gösterir. Bu değerlerin tümü, doğru katmanı seçtiğinizden karar vermenize yardımcı olabilir.

1. Yüksek kullanılabilirliğe ihtiyacınız varsa veya yavaş sorgu performansına karşılaşıyorsanız çoğaltmalar ekleyin.

   Sorgu yüklerini karşılamak için kaç çoğaltmanın gerekli olduğu konusunda yönergeler yoktur. Sorgu performansı, sorgunun karmaşıklığına ve rekabet iş yüklerine bağlıdır. Çoğaltmaları ekleme işlemi daha iyi performansa neden olsa da, sonuç kesinlikle doğrusal değildir: üç çoğaltma eklemek Üçlü üretilen iş garantisi vermez. Çözümünüz için QPS tahmini konusunda rehberlik için bkz. performans ve [izleme sorguları](search-monitor-queries.md) [için ölçeklendirme](search-performance-optimization.md).

> [!NOTE]
> Hiçbir şekilde aranmayacak verileri dahil etmeniz halinde depolama gereksinimleri de düzlenebilir. İdeal olarak, belgeler yalnızca arama deneyimi için gereken verileri içerir. İkili veriler aranabilir değildir ve ayrı ayrı depolanmalıdır (Belki de bir Azure tablosu veya blob depolamada olabilir). Bir alan, dış verilere bir URL başvurusu tutmak için dizine eklenmelidir. Tek bir arama belgesinin en büyük boyutu 16 MB 'tır (veya bir istekte birden çok belgeyi toplu olarak karşıya yüklüyorsanız, daha az). Daha fazla bilgi için bkz. [Azure bilişsel arama hizmet limitleri](search-limits-quotas-capacity.md).
>

**Sorgu hacmi konuları**

Saniyedeki sorgu sayısı (QPS), performans ayarlama sırasında önemli bir ölçümdür, ancak genellikle bir katman, mantıksal olarak yüksek sorgu hacmi beklemeniz durumunda yalnızca bir katman önem taşımaktadır.

Standart katmanlar, çoğaltmaları ve bölümleri dengeleyebilir. Yük Dengeleme için çoğaltmalar ekleyerek veya paralel işleme için bölüm eklemek yoluyla sorgu kapatma işlemini artırabilirsiniz. Daha sonra hizmet sağlandıktan sonra performansı ayarlayabilirsiniz.

Outset 'ten yüksek hacimli sorgu birimleri düşünüyorsanız, daha güçlü donanımlar tarafından desteklenen, daha yüksek standart katmanları göz önünde bulundurmanız gerekir. Daha sonra, bu sorgu birimleri gerçekleşmese de bölümleri ve çoğaltmaları çevrimdışı duruma geçirebilir veya alt katman bir hizmete geçebilirsiniz. Sorgu verimini hesaplama hakkında daha fazla bilgi için bkz. [Azure bilişsel arama performansı ve iyileştirmesi](search-performance-optimization.md).

Depolama için Iyileştirilmiş katmanlar, büyük veri iş yükleri için yararlıdır ve sorgu gecikme süresi gereksinimleri daha az önemli olduğunda için daha fazla genel kullanılabilir dizin depolaması destekler. Yük Dengeleme için ek çoğaltmalar ve paralel işleme için ek bölümler kullanmanız gerekir. Daha sonra hizmet sağlandıktan sonra performansı ayarlayabilirsiniz.

**Hizmet düzeyi sözleşmeleri**

Ücretsiz katman ve Önizleme özellikleri, [hizmet düzeyi sözleşmeleri (SLA 'lar)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)kapsamında değildir. Tüm faturalanabilir katmanlar için, hizmet için yeterli artıklık sağladığınızda SLA 'Lar devreye girer. Sorgu (okuma) SLA 'Ları için iki veya daha fazla kopyaya sahip olmanız gerekir. Sorgu ve dizin oluşturma (okuma-yazma) SLA 'Ları için üç veya daha fazla kopyaya sahip olmanız gerekir. Bölüm sayısı SLA 'Ları etkilemez.

## <a name="tips-for-capacity-planning"></a>Kapasite planlama ipuçları

+ Ölçümlerin sorguları etrafında derleme yapmasına ve kullanım desenlerinde (iş saatleri sırasında sorgular, yoğun olmayan saatlerde dizin oluşturma) veri toplamasına izin verin. Hizmet sağlama kararlarını bilgilendirmek için bu verileri kullanın. Saatlik veya günlük temposunda pratik olmasa da, sorgu birimlerindeki planlı değişikliklere uyum sağlamak için bölümleri ve kaynakları dinamik olarak ayarlayabilirsiniz. Ayrıca, düzeylerin işlem yapmak için yeterince uzun sürmesi durumunda planlanmamış ancak sürekli değişikliklere de uyum sağlayabilirsiniz.

+ Sağlama altındaki tek alt tarafının, gerçek gereksinimler tahmine göre daha büyükse bir hizmeti bölmek zorunda kalmamanız gerektiğini unutmayın. Hizmet kesintisi yaşamamak için, daha yüksek bir katmanda yeni bir hizmet oluşturun ve tüm uygulamalar ve istekler yeni uç noktayı hedefleyerek yan yana çalıştırabilirsiniz.

## <a name="when-to-add-capacity"></a>Kapasitenin ne zaman ekleneceğini

Başlangıçta, bir hizmete bir bölümden ve bir çoğaltmadan oluşan en az düzeyde kaynak ayrılır. [Seçtiğiniz katman](search-sku-tier.md) bölüm boyutunu ve hızını belirler ve her katman çeşitli senaryolara uyan bir dizi özellik etrafında en iyi duruma getirilir. Daha yüksek bir katman seçerseniz S1 ile devam ediyorsanız daha az bölüm gerekebilir. Kendi kendine yönlendirilmiş test aracılığıyla yanıtlamanız gereken sorulardan biri, daha büyük ve daha pahalı bir bölümün daha düşük bir katmanda sağlanan bir hizmette iki ucuz bölümden daha iyi performans elde etmesinden biridir.

Tek bir hizmetin tüm iş yüklerini (Dizin oluşturma ve sorgular) işlemek için yeterli kaynak olması gerekir. Hiçbir iş yükü arka planda çalışır. Sorgu isteklerinin doğal olarak daha az sıklıkla olduğu durumlarda Dizin oluşturmayı zamanlayabilirsiniz, ancak hizmet diğer bir görevi başka bir görev için önceliklendirmez. Ayrıca, hizmetler veya düğümler dahili olarak güncelleştirilirken belirli bir artıklık miktarı sorgu performansını düzgünleştirir.

Kapasite eklenip eklenmeyeceğini belirlemek için bazı yönergeler şunlardır:

+ Hizmet düzeyi sözleşmesi için yüksek kullanılabilirlik ölçütlerini toplantısı
+ HTTP 503 hatalarının sıklığı artıyor
+ Büyük sorgu birimleri bekleniyor

Genel bir kural olarak, arama uygulamalarında, özellikle de hizmet işlemleri sorgu iş yüklerine yaklaşmanız durumunda bölümden daha fazla çoğaltma olması eğilimindedir. Her çoğaltma, dizinin bir kopyasıdır ve hizmetin birden fazla kopyaya karşı istek yükünü dengelemeye olanak tanır. Bir dizinin tüm yük dengelemesi ve çoğaltması Azure Bilişsel Arama tarafından yönetilir ve hizmetiniz için ayrılan çoğaltmaların sayısını dilediğiniz zaman değiştirebilirsiniz. Standart bir arama hizmetinde 12 ' ye kadar çoğaltma ve temel bir arama hizmetinde 3 çoğaltma ayırabilirsiniz. Çoğaltma ayırması [Azure Portal](search-create-service-portal.md) ya da programlama seçeneklerinden biri olabilir.

Neredeyse gerçek zamanlı veri yenileme gerektiren uygulamalarda, çoğaltmalara göre orantılı daha fazla bölüm gerekecektir. Bölüm ekleme okuma/yazma işlemlerini çok sayıda bilgi işlem kaynağına yayar. Ayrıca ek dizinleri ve belgeleri depolamak için daha fazla disk alanı sağlar.

Son olarak, daha büyük dizinler sorgu için daha uzun sürer. Bu nedenle, bölümlerdeki her artımlı artışın çoğaltmalarda daha küçük ancak orantılı bir artış gerektirdiğini fark edebilirsiniz. Sorgularınızın ve sorgu biriminizin karmaşıklığı, sorgu yürütmenin ne kadar hızlı bir şekilde bir şekilde çalıştığını çarpanlara katacaktır.

> [!NOTE]
> Daha fazla çoğaltma veya bölüm eklenmesi hizmeti çalıştırmanın maliyetini artırır ve sonuçların nasıl sıralandığından hafif Çeşitlemeler getirebilir. Daha fazla düğüm eklemenin faturalandırma etkilerini anlamak için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) denetlediğinizden emin olun. [Aşağıdaki grafik](#chart) , belirli bir yapılandırma için gereken arama birimi sayısına çapraz başvuru yapmanıza yardımcı olabilir. Ek çoğaltmaların sorgu işlemeyi etkisi hakkında daha fazla bilgi için bkz. [sonuçları sıralama](search-pagination-page-layout.md#ordering-results).

<a name="adjust-capacity"></a>

## <a name="add-or-reduce-replicas-and-partitions"></a>Çoğaltmaları ve bölümleri ekleme veya azaltma

1. [Azure Portal](https://portal.azure.com/) oturum açın ve arama hizmetini seçin.

1. **Ayarlar**' ın altında, çoğaltmaları ve bölümleri değiştirmek için **Ölçek** sayfasını açın. 

   Aşağıdaki ekran görüntüsünde, tek bir çoğaltma ve bölüm ile sağlanan temel bir standart gösterilmektedir. Alttaki formül, kaç arama birimi kullanıldığını gösterir (1). Birim fiyatı $100 (gerçek fiyat değil) ise, bu hizmeti çalıştırmanın aylık maliyeti, ortalama olarak $100 ' dir.

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="Geçerli değerleri gösteren sayfayı Ölçeklendir" border="true":::

1. Bölüm sayısını artırmak veya azaltmak için kaydırıcıyı kullanın. Alttaki formül, kaç tane arama birimi kullanıldığını gösterir. **Kaydet**’i seçin.

   Bu örnek, ikinci bir çoğaltma ve bölüm ekler. Arama Birimi sayısına dikkat edin; Fatura formülü bölümler (2 x 2) ile çarpıldığı için artık dördü vardır. Katlama kapasitesi, hizmeti çalıştırmanın maliyetini iki katına çıkarır. Arama birimi maliyeti $100 ise, yeni aylık fatura artık $400 olacaktır.

   Her katmanın birim maliyetlerine göre geçerli olan [fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/search/)ziyaret edin.

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="Çoğaltmalar ve bölümler ekleme" border="true":::

1. Kaydettikten sonra, başarılı olan eylemi onaylamak için bildirimleri kontrol edebilirsiniz.

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="Değişiklikleri Kaydet" border="true":::

   Kapasite değişikliklerinin tamamlanması 15 dakikadan birkaç saat kadar sürebilir. İşlem başladıktan sonra iptal edemezsiniz ve çoğaltma ve bölüm ayarlamaları için gerçek zamanlı izleme yoktur. Ancak, değişiklikler devam ederken aşağıdaki ileti görünür kalır.

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="Portalda durum iletisi" border="true":::

> [!NOTE]
> Bir hizmet sağlandıktan sonra, daha yüksek bir katmana yükseltilemez. Yeni katmanda bir arama hizmeti oluşturmanız ve dizinlerinizi yeniden yüklemeniz gerekir. Hizmet sağlama konusunda yardım için bkz. [portalda Azure bilişsel arama hizmeti oluşturma](search-create-service-portal.md) .
>
> Ayrıca, bölümler ve çoğaltmalar, hizmet tarafından özel olarak ve dahili olarak yönetilir. İşlemci benzeşimi kavramı veya belirli bir düğüme iş yükü atama yoktur.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Bölüm ve çoğaltma birleşimleri

Temel bir hizmet, en fazla üç SUs sınırı için tam olarak bir bölüme ve en fazla üç çoğaltmaya sahip olabilir. Yalnızca ayarlanabilir kaynak çoğaltmadır. Sorgularda yüksek kullanılabilirlik için en az iki çoğaltma gerekir.

Tüm standart ve depolama için Iyileştirilmiş arama hizmetleri, bu katmanlar için izin verilen 36-SU sınırına tabi olan aşağıdaki çoğaltma ve bölüm birleşimlerini kabul edebilir. 

|   | **1 Bölüm** | **2 bölüm** | **3 Bölüm** | **4 Bölüm** | **6 Bölüm** | **12 Bölüm** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 çoğaltma** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 çoğaltma** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 çoğaltma** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 çoğaltma** |4 SU |8 SU |12 SU |16 SU |24 SU |Yok |
| **5 çoğaltma** |5 SU |10 SU |15 SU |20 SU |30 SU |Yok |
| **6 çoğaltma** |6 SU |12 SU |18 SU |24 SU |36 SU |Yok |
| **12 çoğaltma** |12 SU |24 SU |36 SU |Yok |Yok |Yok |

SUs, fiyatlandırma ve kapasite Azure Web sitesinde ayrıntılı olarak açıklanmıştır. Daha fazla bilgi için bkz. [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Çoğaltma ve bölüm sayısı eşit olarak 12 ' ye bölünür (özellikle, 1, 2, 3, 4, 6, 12). Bunun nedeni, Azure Bilişsel Arama her bir dizini tüm bölümler arasında eşit kısımlara yayılabilecek şekilde 12 parçalara böler. Örneğin, hizmetinizin üç bölümü varsa ve bir dizin oluşturursanız, her bölüm dizin dört bölümden oluşur. Azure Bilişsel Arama Dizin oluşturma işlemi, sonraki sürümlerde değişikliğe tabi olan bir uygulama ayrıntısı. Sayı bugün 12 olsa da, bu sayının gelecekte her zaman 12 olması beklenmemelidir.
>

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Maliyetleri yönetme](search-sku-manage-costs.md)