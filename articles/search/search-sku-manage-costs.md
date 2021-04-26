---
title: Maliyetleri tahmin etme
titleSuffix: Azure Cognitive Search
description: Faturalandırılabilir olaylar, fiyatlandırma modeli ve bir Bilişsel Arama hizmeti çalıştırma maliyetini yönetme ipuçları hakkında bilgi edinin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: e2ab7a007a92bd2306a7a97781af5653f932d0b7
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580612"
---
# <a name="how-to-estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Azure Bilişsel Arama hizmeti 'nin maliyetlerini tahmin etme ve yönetme

Bu makalede, fiyatlandırma modeli, faturalandırılabilir olaylar ve bir Azure Bilişsel Arama hizmeti çalıştırma maliyetini yönetmeye yönelik ipuçları hakkında bilgi edinin.

## <a name="pricing-model"></a>Fiyatlandırma modeli

Azure Bilişsel Arama 'de ölçeklenebilirlik mimarisi, daha fazla sorgu veya dizin oluşturma gücü olmasına bağlı olarak kapasiteyi değiştirebilmeniz ve yalnızca ihtiyacınız olanlar için ücret ödemenizi sağlayacak şekilde çoğaltmalar ve bölümlerin esnek birleşimlerine dayanır.

Arama hizmetiniz tarafından kullanılan, hizmet katmanı tarafından belirlenen faturalandırma ücretine göre çarpılan kaynak miktarı, hizmeti çalıştırmanın maliyetini belirler. Maliyetler ve kapasite sıkı bir şekilde bağlanır. Maliyet tahmini yaparken, dizin oluşturma ve sorgu iş yüklerinizi çalıştırmak için gereken kapasiteyi anlamak, öngörülen maliyetlerin ne olacağı konusunda en iyi fikir verir.

Faturalama amacıyla, bilmeniz için iki basit formül vardır:

| Formül | Açıklama |
|---------|-------------|
| **R x P = SU** | Kullanılan çoğaltma sayısı, kullanılan bölüm sayısıyla çarpılır, bir hizmet tarafından kullanılan *arama birimi* (su) miktarına eşittir. SU bir kaynak birimidir ve bir bölüm ya da çoğaltma olabilir. |
| **SU * faturalandırma oranı = aylık harcama** | SUs sayısı, hizmeti sağladığınız katmanın faturalama oranı ile çarpılır. genel aylık faturanız birincil olarak belirlenir. Bazı özellikler veya iş yükleri, diğer Azure hizmetlerinde bağımlılıklara sahiptir ve bu, çözümünüzün maliyetini abonelik düzeyinde artırabilir. Aşağıdaki faturalanabilir olaylar bölümü faturanızda ekleyebileceğini özellikleri tanımlar. |

Her hizmet bir SU (bir çoğaltma bir bölümden çarpılarak) en düşük düzeyde başlar. Her hizmet için en fazla 36 SUs olur. Bu en fazla iki şekilde erişilebilir: 6 Bölüm x 6 çoğaltma veya 3 bölümden oluşan x 12 çoğaltma. Toplam kapasitenin (örneğin, 3 çoğaltma, 3 bölümlü bir hizmetin 9 ' da faturalandırılan) kullanılması yaygındır. Geçerli birleşimler için [bölüm ve çoğaltma birleşimleri](search-capacity-planning.md#chart) grafiğine bakın.

Fatura ücreti SU başına saatlik olarak belirlenir. Her katmanda aşamalı olarak daha yüksek bir hız vardır. Daha yüksek katmanlar daha büyük ve speedileyici bölümleri ile gelir ve bu, bu katman için genel olarak saatlik bir fiyat sağlar. [Fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/search/) sayfasında her bir katmanın oranlarını görüntüleyebilirsiniz.

Çoğu müşteri, kalanı ayrılmış olarak tutarak toplam kapasitenin yalnızca bir kısmını çevrimiçi duruma getirir. Faturalandırma için, çevrimiçi duruma getirdiğiniz bölüm ve çoğaltmaların sayısı, SU formülü tarafından hesaplandığınız, saatlik olarak ne ödediklerinizi belirler. 

## <a name="billable-events"></a>Faturalandırılabilir olaylar

Azure Bilişsel Arama 'de oluşturulan bir çözüm, aşağıdaki yollarla maliyette ücret alabilir:

+ [Hizmetin](#service-costs) , en düşük düzeyde (bir bölüm ve çoğaltma), temel fiyata 7/24 çalışan maliyeti. Bunu, hizmeti çalıştırmanın sabit maliyeti olarak düşünebilirsiniz.

+ Maliyetlerin faturalandırılabilir orandaki artışlarla artması kapasitesi (çoğaltmalar veya bölümler) ekleme. Yüksek kullanılabilirlik bir iş gereksinimidir, 3 çoğaltmalara sahip olmanız gerekir.

+ Bant genişliği ücretleri (giden veri aktarımı)

+ Belirli yetenekler veya özellikler için gereken eklenti hizmetleri:

  + AI zenginleştirme (bilişsel [Hizmetler](https://azure.microsoft.com/pricing/details/cognitive-services/)gerektirir)
  + bilgi deposu ( [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)gerektirir)
  + Artımlı zenginleştirme ( [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)gerektırır, AI zenginleştirme için geçerlidir)
  + Müşteri tarafından yönetilen anahtarlar ve çift şifreleme ( [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)gerekir)
  + İnternet erişimi olmayan bir model için özel uç noktalar ( [Azure özel bağlantısı](https://azure.microsoft.com/pricing/details/private-link/)gerekir)

### <a name="service-costs"></a>Hizmet maliyetleri

Ücretlendirmeden kaçınmak için sanal makinelerden veya "duraklatılmış" olabilecek diğer kaynaklardan farklı olarak Azure Bilişsel Arama hizmeti, özel kullanım için ayrılmış donanımlar üzerinde her zaman kullanılabilir. Bu nedenle, hizmet oluşturma hizmeti oluştururken başlayan faturalandırılabilir bir olaydır ve hizmeti sildiğinizde sona erer. 

Minimum ücret, faturalandırılabilir fiyata ilk arama birimidir (bir çoğaltma x bir bölüm). Hizmet bu yapılandırmadan daha küçük bir süre içinde çalışmadığından hizmetin kullanım ömrü için bu minimum değer düzeltildi. 

En düşük değerin ötesinde, çoğaltmaları ve bölümleri birbirinden bağımsız olarak ekleyebilirsiniz. Çoğaltmalar ve bölümler aracılığıyla kapasitenin artımlı artışları, faturanızı aşağıdaki formüle göre artırır: **(çoğaltmalar x Partitions x faturalandırma oranı)**; burada, ücretlendirilebilen oran seçtiğiniz fiyatlandırma katmanına bağlıdır.

Bir arama çözümünün maliyetini tahmin ediyorsanız, fiyatlandırma ve kapasitenin doğrusal değil (katlama kapasitesi aynı katmandaki maliyetin iki katına çıkar) olduğunu aklınızda bulundurun. Ayrıca, bazı bir noktada, daha yüksek bir katmana geçiş yapmak kabaca aynı fiyat noktasında daha iyi ve daha hızlı performans sağlayabilir. Daha fazla bilgi ve bir örnek için bkz. [Standart S2 katmanına yükseltme](search-performance-tips.md#tip-upgrade-to-a-standard-s2-tier).

### <a name="bandwidth-charges"></a>Bant genişliği ücretleri

Azure veri kaynağı Azure Bilişsel Arama farklı bir bölgedeyse, [Dizin oluşturucular](search-indexer-overview.md) kullanmak faturalandırmayı etkileyebilir. Bu senaryoda, giden verileri Azure veri kaynağından Azure Bilişsel Arama taşıma maliyeti olabilir. Ayrıntılar için, söz konusu Azure veri platformunun fiyatlandırma sayfalarına bakın.

Azure Bilişsel Arama hizmetini verileriniz ile aynı bölgede oluşturursanız, veri çıkış ücretlerini tamamen ortadan kaldırabilirsiniz. [Bant genişliği fiyatlandırma sayfasından](https://azure.microsoft.com/pricing/details/bandwidth/)bazı bilgiler aşağıda verilmiştir:

+ Gelen veriler: Microsoft, Azure 'daki herhangi bir hizmete gelen veriler için ücret ödemez. 

+ Giden veriler: giden veriler sorgu sonuçlarına başvurur. Bilişsel Arama giden veriler için ücret alınmaz, ancak hizmetler farklı bölgelerde olduğunda Azure 'daki giden ücretler mümkündür.

  Bu ücretler aslında Azure Bilişsel Arama faturanızın bir parçası değildir. Bunlar burada bahsedildikleri için, diğer bölgelere veya Azure olmayan uygulamalara sonuç gönderiyorsanız, bu maliyetlerin genel faturanızda yansıtıldığını görebilirsiniz.

### <a name="ai-enrichment-with-cognitive-services"></a>Bilişsel hizmetler ile AI zenginleştirme

[AI zenginleştirme](cognitive-search-concept-intro.md)için, Kullandıkça Öde Işleme için S0 fiyatlandırma katmanında Azure bilişsel arama ile aynı bölgede [faturalandırılabilir bir Azure bilişsel hizmetler kaynağı eklemeyi](cognitive-search-attach-cognitive-services.md)planlamalısınız. Bilişsel hizmetler ekleme ile ilişkili sabit bir maliyet yoktur. Yalnızca ihtiyacınız olan işleme için ödeme yaparsınız.

| İşlem | Faturalama etkisi |
|-----------|----------------|
| Belge çözme, metin ayıklama | Ücretsiz |
| Belge çözme, görüntü ayıklama | Belgelerinizden ayıklanan görüntü sayısına göre faturalandırılır. Bir [Dizin Oluşturucu yapılandırmasında](/rest/api/searchservice/create-indexer#indexer-parameters) **ımageaction** , görüntü ayıklamayı tetikleyen parametredir. **Imageaction** "none" (varsayılan) olarak ayarlandıysa, görüntü ayıklama için ücretlendirilmezsiniz. Görüntü ayıklama oranı, Azure Bilişsel Arama için [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/search/) sayfasında belgelenmiştir.|
| [Yerleşik bilişsel beceriler](cognitive-search-predefined-skills.md) | Bilişsel hizmetler 'i doğrudan kullanarak görevi gerçekleştirdiyseniz aynı hızda faturalandırılır. |
| Özel beceriler | Özel bir beceri sağladığınız işlevsellikdir. Özel bir beceri kullanmanın maliyeti, tamamen özel kodun diğer ölçülen Hizmetleri çağırarak çağrılmayacağı konusunda farklılık gösterir. |

[Artımlı zenginleştirme (Önizleme)](cognitive-search-incremental-indexing-conceptual.md) özelliği, dizin oluşturucunun yalnızca beceri ' yi daha sonra değiştirmeniz gereken bilişsel becerileri çalıştırmaya ve zamandan ve paradan tasarruf etmenize olanak sağlayan bir önbellek sağlamanıza olanak tanır.

## <a name="tips-for-managing-costs"></a>Maliyetleri yönetmeye yönelik ipuçları

Aşağıdaki kılavuz, maliyetleri düşürmenize veya maliyetleri daha etkin bir şekilde yönetmenize yardımcı olabilir:

+ Bant genişliği ücretlerini en aza indirmek veya ortadan kaldırmak için, aynı bölgedeki veya mümkün olduğunca az sayıda bölgede tüm kaynakları oluşturun.

+ Tüm hizmetleri Azure Bilişsel Arama, bilişsel hizmetler ve çözümünüzde kullanılan diğer Azure hizmetleri gibi tek bir kaynak grubunda birleştirin. Azure portal, kaynak grubunu bulun ve gerçek ve tahmini harcama hakkında bilgi edinmek için **maliyet yönetimi** komutlarını kullanın.

+ İstek ve yanıtların veri merkezi sınırının içinde kalması için, ön uç uygulamanız için Azure Web App 'i göz önünde bulundurun.

+ Dizin oluşturma gibi yoğun kaynak gerektiren işlemlere göre ölçeklendirin ve normal sorgu iş yükleri için hemen aşağı doğru. Azure Bilişsel Arama için en düşük yapılandırma (bir bölümden ve bir çoğaltmayla bir SU) ile başlayın ve ardından daha fazla kapasiteye ihtiyacı olan kullanım düzenlerini belirlemek için Kullanıcı etkinliğini izleyin. Tahmin edilebilir bir model varsa, ölçeklendirmeyi etkinlikle eşitlenebilir (Bunu otomatikleştirmek için kod yazmanız gerekir).

+ Maliyet yönetimi, Azure altyapısında yerleşiktir. Maliyetleri, araçları ve API 'Leri izleme hakkında daha fazla bilgi için [faturalandırma ve maliyet yönetimini](../cost-management-billing/cost-management-billing-overview.md) gözden geçirin.

Arama hizmetini geçici olarak kapatmak mümkün değildir. Adanmış kaynaklar her zaman çalışır ve hizmetinizin kullanım ömrü boyunca özel kullanım için ayrılır. Bir hizmetin silinmesi kalıcıdır ve ayrıca ilişkili verileri de silinir.

Hizmetin kendisi açısından, faturanızı düşürmenin tek yolu, çoğaltmaları ve bölümleri, kabul edilebilir performans ve [SLA uyumluluğu](https://azure.microsoft.com/support/legal/sla/search/v1_0/)sağlayan bir düzeye düşürmeniz veya daha düşük bir katmanda bir hizmet oluşturmanız (S1 saatlik fiyatlar S2 veya S3 oranlarından daha düşüktür). Hizmetinizi yük projeksiyonlarınızın alt ucunda sağladığınızda, hizmeti büyüyerek ikinci bir daha büyük katmanlı hizmet oluşturabilir, dizinlerinizi ikinci hizmette yeniden oluşturabilir ve sonra birincisini silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure aboneliğinizde maliyetleri izlemeyi ve yönetmeyi öğrenin.

> [!div class="nextstepaction"]
> [Azure Maliyet Yönetimi ve Faturalandırma belgeleri](../cost-management-billing/cost-management-billing-overview.md)