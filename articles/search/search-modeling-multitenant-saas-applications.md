---
title: Çok kiracılı ve içerik yalıtımı
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama kullanırken çok kiracılı SaaS uygulamaları için ortak tasarım desenleri hakkında bilgi edinin.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d37abd1b5d212c3d920cb68b6236029b2112ae24
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113264"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Çok kiracılı SaaS uygulamaları ve Azure Bilişsel Arama için tasarım desenleri
Çok kiracılı bir uygulama, başka bir kiracının verilerini görebilen veya paylaşabilen birçok kiracı için aynı hizmetleri ve özellikleri sağlayan bir uygulamadır. Bu belgede, Azure Bilişsel Arama ile oluşturulan çok kiracılı uygulamalar için kiracı yalıtımı stratejileri ele alınmaktadır.

## <a name="azure-cognitive-search-concepts"></a>Azure Bilişsel Arama kavramları
Bir hizmet olarak arama çözümü olan Azure Bilişsel Arama, geliştiricilerin herhangi bir altyapıyı yönetmeksizin veya bilgi alma konusunda uzman hale gelmeden uygulamalara zengin arama deneyimleri eklemesine olanak tanır. Veriler hizmete yüklenir ve ardından bulutta depolanır. Azure Bilişsel Arama API 'sine yönelik basit istekleri kullanarak veriler daha sonra değiştirilebilir ve aranabilir. [Bu makalede](https://aka.ms/whatisazsearch)hizmete genel bakış bulabilirsiniz. Tasarım düzenlerini tartışmadan önce, Azure Bilişsel Arama 'de bazı kavramların anlaşılması önemlidir.

### <a name="search-services-indexes-fields-and-documents"></a>Hizmetleri, dizinleri, alanları ve belgeleri arayın
Azure Bilişsel Arama kullanırken bir *arama hizmetine*abone olur. Veriler Azure Bilişsel Arama karşıya yüklendiği için arama hizmeti içindeki bir *dizinde* depolanır. Tek bir hizmet içinde çok sayıda dizin olabilir. Veritabanlarının tanıdık kavramlarını kullanmak için, bir hizmet içindeki dizinler bir veritabanı içindeki tablolara beğenilirken arama hizmeti bir veritabanına eklenebilir.

Bir arama hizmeti içindeki her dizin kendi şemasına sahiptir ve bu bir dizi özelleştirilebilir *alan*tarafından tanımlanır. Veriler bir Azure Bilişsel Arama dizinine tek tek *Belgeler*biçiminde eklenir. Her belge belirli bir dizine yüklenmelidir ve bu dizinin şemasına sığması gerekir. Azure Bilişsel Arama kullanarak veri ararken tam metin arama sorguları belirli bir dizine göre verilir.  Bu kavramları bir veritabanınızla karşılaştırmak için, alanlar bir tablodaki sütunlara eklenebilir ve belgeler satırlara eklenebilir.

### <a name="scalability"></a>Ölçeklenebilirlik
Standart [fiyatlandırma katmanındaki](https://azure.microsoft.com/pricing/details/search/) herhangi bir Azure bilişsel arama hizmeti, iki boyutta ölçeklendirilebilir: depolama ve kullanılabilirlik.

* Bir arama hizmetinin depolanmasını artırmak için *bölümler* eklenebilir.
* Bir arama hizmetinin işleyebileceği isteklerin verimini artırmak için bir hizmete *çoğaltmalar* eklenebilir.

' De bölüm ve çoğaltmaları ekleme ve kaldırma, arama hizmeti kapasitesinin, uygulamanın talep aldığı veri ve trafik miktarıyla büyümesine izin verir. Bir arama hizmetinin bir okuma [SLA 'sı](https://azure.microsoft.com/support/legal/sla/search/v1_0/)elde etmek için iki çoğaltma gerekir. Bir hizmetin bir okuma-yazma [SLA 'sı](https://azure.microsoft.com/support/legal/sla/search/v1_0/)kullanabilmesi için, üç çoğaltma gerektirir.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Azure Bilişsel Arama hizmet ve Dizin sınırları
Azure Bilişsel Arama 'de birkaç farklı [fiyatlandırma katmanı](https://azure.microsoft.com/pricing/details/search/) vardır. her katmanda farklı [sınırlara ve Kotalar](search-limits-quotas-capacity.md)vardır. Bu limitlerden bazıları hizmet düzeyindedir, bazıları dizin düzeyindedir ve bazıları bölüm düzeyinde bulunur.

|  | Temel | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Hizmet başına en fazla çoğaltma |3 |12 |12 |12 |12 |
| Hizmet başına en fazla bölüm |1 |12 |12 |12 |3 |
| Hizmet başına en fazla arama birimi (çoğaltmalar * bölüm) |3 |36 |36 |36 |36 (en fazla 3 Bölüm) |
| Hizmet başına en fazla depolama alanı |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Bölüm başına en fazla depolama alanı |2 GB |25 GB |100 GB |200 GB |200 GB |
| Hizmet başına en fazla dizin |5 |50 |200 |200 |3000 (en fazla 1000 dizin/bölüm) |

#### <a name="s3-high-density"></a>S3 yüksek yoğunluklu '
Azure Bilişsel Arama S3 fiyatlandırma katmanında, çok kiracılı senaryolar için özel olarak tasarlanan yüksek yoğunluklu (HD) mod için bir seçenek vardır. Birçok durumda, basitlik ve maliyet verimliliği avantajlarından yararlanmak için tek bir hizmet altında çok sayıda daha küçük kiracılar desteklemek gereklidir.

S3 HD, çok sayıda küçük dizinin tek bir hizmette daha fazla dizin barındırmanıza olanak sağlamak için bölümleri kullanarak Dizin ölçekleme yeteneği sunarak tek bir arama hizmetinin yönetimi altına paketlenebilmesini sağlar.

Bir S3 hizmetinde, her ikisi de 1.400.000.000 belge barındırabileceğiniz 1 ile 200 arasında dizin olabilir. Diğer yandan bir S3 HD, tek tek dizinlerin yalnızca 1.000.000 belge kullanmasına izin verebilir, ancak 600.000.000 bölüm başına en fazla 1000 dizini işleyebilir (hizmet başına 3000 en fazla 200.000.000).

## <a name="considerations-for-multitenant-applications"></a>Çok kiracılı uygulamalar için dikkat edilmesi gerekenler
Çok kiracılı uygulamalar, çeşitli kiracılar arasında bazı gizlilik düzeyini korurken, kaynakları kiracılar arasında verimli bir şekilde dağıtmalıdır. Bu tür bir uygulama için mimari tasarlarken bazı önemli noktalar vardır:

* *Kiracı yalıtımı:* Uygulama geliştiricilerinin, kiracının diğer kiracıların verilerine yetkisiz veya istenmeyen erişim sahibi olmadığından emin olmak için uygun önlemleri almanız gerekir. Veri gizliliği perspektifinin ötesinde, kiracı yalıtım stratejileri, gürültülü komşulardan paylaşılan kaynakların ve korumanın etkili bir şekilde yönetilmesini gerektirir.
* *Bulut kaynağı maliyeti:* Diğer uygulamalarda olduğu gibi, yazılım çözümlerinin de çok kiracılı bir uygulamanın bir bileşeni olarak maliyet rekabetçi kalması gerekir.
* *Işlem kolaylığı:* Çok kiracılı bir mimari geliştirilirken, uygulamanın işlemleri ve karmaşıklığı üzerindeki etki önemli bir konudur. Azure Bilişsel Arama [% 99,9 SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)'ya sahiptir.
* *Global ayak izi:* Çok kiracılı uygulamaların dünya genelinde dağıtılan kiracılara etkin bir şekilde sunması gerekebilir.
* *Ölçeklenebilirlik:* Uygulama geliştiricilerinin, en düşük düzeyde uygulama karmaşıklığı bulundurma ve uygulamayı kiracı sayısıyla ve kiracıların verilerinin ve iş yükünün boyutuyla ölçeklenebilecek şekilde tasarlama arasındaki uzlaşmaları göz önünde bulundurmanız gerekir.

Azure Bilişsel Arama, kiracıların verilerini ve iş yükünü yalıtmak için kullanılabilecek birkaç sınır sunar.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Azure Bilişsel Arama ile çok kiracılı Modellendirme
Çok kiracılı bir senaryo söz konusu olduğunda, uygulama geliştiricisi bir veya daha fazla arama hizmeti kullanır ve kiracılarını hizmetler, dizinler veya her ikisi arasında böler. Azure Bilişsel Arama, çok kiracılı bir senaryo modellemesi sırasında bazı yaygın desenlere sahiptir:

1. *Kiracı başına Dizin:* Her kiracının, diğer kiracılar ile paylaşılan bir arama hizmeti içinde kendi dizini vardır.
2. *Kiracı başına hizmet:* Her kiracının, en yüksek düzeyde veri ve iş yükü ayrımı sunan kendi adanmış Azure Bilişsel Arama hizmeti vardır.
3. *Her Ikisinin karışımı:* Daha büyük, daha etkin kiracılara, daha küçük kiracılar paylaşılan hizmetler içinde tekil dizinler atandığında, daha fazla etkin kiracılar adanmış hizmetler atanır.

## <a name="1-index-per-tenant"></a>1. kiracı başına Dizin
![Kiracı başına Dizin modeline ait bir portrayal](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Kiracı başına Dizin modelinde birden çok kiracı, her kiracının kendi dizini olduğu tek bir Azure Bilişsel Arama hizmeti kaplar.

Tüm arama istekleri ve belge işlemleri Azure Bilişsel Arama bir dizin düzeyinde verildiği için kiracılar veri yalıtımı elde ediyor. Uygulama katmanında, tüm kiracılarda hizmet düzeyinde kaynakları yönetirken çeşitli kiracıların trafiğinin doğru dizinlere yönlendirilmesi için bir tanıma ihtiyacı vardır.

Kiracı başına dizin oluşturma modelinin anahtar özniteliği, uygulama geliştiricisinin uygulamanın kiracılar arasında bir arama hizmeti kapasitesini fazla bir şekilde abone olma yeteneğidir. Kiracılar iş yükünün düzensiz bir şekilde dağıtılmasını kullanıyorsa, kiracının en iyi kombinasyonu, uzun bir süre daha az olan çok sayıda etkin, yoğun kaynak yoğunluklu kiracılara uyum sağlamak için bir arama hizmetinin dizinlerine dağıtılabilir etkin kiracılar. Bu, her kiracının eşzamanlı olarak etkin olduğu durumları işlemek için modelden bir denge sağlar.

Kiracı başına Dizin modeli, Azure Bilişsel Arama hizmetinin tamamının satın aldığı ve daha sonra kiracılar ile doldurulmuş bir değişken maliyet modelinin temelini sağlar. Bu, deneme ve ücretsiz hesaplar için kullanılmayan kapasitenin belirlenmesi için izin verir.

Genel bir ayak izine sahip uygulamalarda, kiracı başına dizin oluşturma en etkili olmayabilir. Bir uygulamanın kiracılar dünya genelinde dağıtılırsa her bir bölge için ayrı bir hizmet gerekebilir ve bunların her birinde maliyetleri çoğaltabilir.

Azure Bilişsel Arama, her iki dizinin ölçeğinin ve toplam dizin sayısının büyümesine izin verir. Uygun bir fiyatlandırma katmanı seçilirse, hizmet içindeki tek bir dizin depolama veya trafik açısından çok büyük büyüdüğünde tüm arama hizmetine bölümler ve çoğaltmalar eklenebilir.

Toplam dizin sayısı tek bir hizmet için çok büyük büyürse, yeni kiracıları barındırmak için başka bir hizmetin sağlanması gerekir. Yeni hizmetler eklendikçe, Dizin arama hizmetleri arasında taşınmaları gerekiyorsa, Azure Bilişsel Arama bir dizinin taşınmasına izin vermediği için dizindeki verilerin bir dizinden diğerine el ile kopyalanması gerekir.

## <a name="2-service-per-tenant"></a>2. kiracı başına hizmet
![Kiracı başına hizmet modeline ait bir portrayal](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Her kiracının hizmet başına bir mimaride kendi arama hizmeti vardır.

Bu modelde, uygulama kiracılar için en yüksek yalıtım düzeyine erişir. Her hizmette, arama isteğini ve ayrı API anahtarlarını işlemek için ayrılmış depolama ve aktarım hızı vardır.

Her kiracının büyük bir parmak izine sahip olduğu veya iş yükünün kiracıya kiracıya çok az değişkenlik olduğu uygulamalar için, kaynaklar çeşitli kiracıların iş yükleri arasında paylaşılmadığından, kiracı başına hizmet modeli etkili bir seçenektir.

Her kiracı modeli için bir hizmet, öngörülebilir, sabit bir maliyet modelinin avantajını de sunmaktadır. Tüm arama hizmetinde, bir kiracı olana kadar bir ön ödeme yoktur, ancak kiracı başına maliyet, kiracı başına bir modelden daha yüksektir.

Kiracı başına hizmet modeli, küresel bir parmak izine sahip uygulamalar için verimli bir seçimdir. Coğrafi olarak dağıtılan kiracılar sayesinde, her kiracının hizmeti uygun bölgede kolayca bulunur.

Bu düzenin ölçeklendirildiği sorunlar, tek tek kiracılar kendi hizmetini büyürken oluşur. Azure Bilişsel Arama, bir arama hizmetinin fiyatlandırma katmanını yükseltmeyi desteklememektedir, bu nedenle tüm verilerin yeni bir hizmete el ile kopyalanması gerekir.

## <a name="3-mixing-both-models"></a>3. her iki modeli de karıştırma
Birden çok kiracının modellenmesi için başka bir model, kiracı başına dizin ve hizmet kiracıya yönelik stratejileri karıştırmalıdır.

İki deseni karıştırarak, uygulamanın en büyük kiracılar, uzun süre daha az etkin olan, daha küçük kiracılar paylaşılan bir hizmette Dizin kaplayabilirler. Bu model, tüm gürültülü komşulardan daha küçük kiracılar korumaya yardımcı olurken en büyük kiracıların hizmetten sürekli olarak yüksek performansa sahip olmasını sağlar.

Ancak, bu stratejiyi uygulamak, hangi kiracıların adanmış bir hizmette bir dizin ve bir paylaşılan hizmette Dizin gerektirdiğini tahmin etmek için de yararlanır. Bu çok kiracılı modellerin her ikisini de yönetme ihtiyacı ile uygulama karmaşıklığı artar.

## <a name="achieving-even-finer-granularity"></a>Daha da ayrıntılı ayrıntı düzeyi
Bilişsel Arama Azure 'da çok kiracılı senaryolar modellenmesini sağlayan yukarıdaki tasarım desenleri, her kiracının bir uygulamanın bir örneği olduğu Tekdüzen bir kapsam olduğunu varsayar. Ancak, uygulamalar bazen birçok küçük kapsamı işleyebilir.

Kiracı başına hizmet ve kiracı başına Dizin modelleri yeterince küçük kapsamlar değilse, bir dizini modelleyebilir ve daha da fazla ayrıntı düzeyi elde edebilirsiniz.

Tek bir dizinin farklı istemci uç noktaları için farklı davranması için, bir dizine, olası her istemci için belirli bir değeri atayan bir alan eklenebilir. Bir istemcinin bir dizini sorgulamak veya değiştirmek için Azure Bilişsel Arama her çağırdığında, istemci uygulamasındaki kod, sorgu sırasında Azure Bilişsel Arama [filtre](https://msdn.microsoft.com/library/azure/dn798921.aspx) özelliğini kullanarak bu alan için uygun değeri belirtir.

Bu yöntem ayrı kullanıcı hesaplarının işlevselliğini, farklı izin düzeylerini ve hatta tamamen ayrı uygulamaları elde etmek için kullanılabilir.

> [!NOTE]
> Birden çok kiracıyı karşılamak üzere tek bir dizin yapılandırmak için yukarıda açıklanan yaklaşımın kullanılması, arama sonuçlarının uygunluğunu etkiler. Arama ilgi puanları, kiracı düzeyindeki bir kapsamda değil, dizin düzeyindeki bir kapsamda hesaplanır. bu nedenle, tüm kiracıların verileri, dönem sıklığı gibi temel istatistiklere dahil edilir.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
Azure Bilişsel Arama birçok uygulama için etkileyici bir seçimdir. Çok kiracılı uygulamalar için çeşitli tasarım düzenlerini değerlendirirken, Azure Bilişsel Arama en iyi şekilde her boyuttaki uygulama iş yüklerine ve mimarilerine sığması için [çeşitli fiyatlandırma katmanlarını](https://azure.microsoft.com/pricing/details/search/) ve ilgili [hizmet sınırlarını](search-limits-quotas-capacity.md) göz önünde bulundurun.

Azure Bilişsel Arama ve çok kiracılı senaryolar hakkında sorular azuresearch_contact@microsoft.comyönlendirilebilir.

