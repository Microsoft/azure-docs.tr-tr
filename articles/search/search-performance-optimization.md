---
title: Dağıtım ve performans iyileştirmesi
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama performansını ayarlamaya ve optimum ölçeği yapılandırmaya yönelik teknikleri ve en iyi yöntemleri öğrenin.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4ff6779843546bd41423c140ef40de6a7e24b026
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113254"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-cognitive-search"></a>Azure Bilişsel Arama performansını iyileştirmek için dağıtım stratejileri ve en iyi uygulamalar

Bu makalede ölçeklenebilirlik ve kullanılabilirlik için gelişmiş gereksinimlere sahip gelişmiş senaryolar için en iyi yöntemler açıklanmaktadır. 

## <a name="develop-baseline-numbers"></a>Taban çizgisi numaraları geliştirme
Arama performansı için iyileştirirken, sorgu yürütme süresini azaltmaya odaklanmanız gerekir. Bunu yapmak için tipik bir sorgu yükünün nasıl göründüğünü bilmeniz gerekir. Aşağıdaki kılavuzlar, taban çizgisi sorgu numaralarına ulaşgetirmenize yardımcı olabilir.

1. Tipik bir arama isteğinin tamamlanması için gereken bir hedef gecikme süresi (veya en fazla süre) seçin.
2. Bu gecikme sürelerini ölçmek için gerçekçi bir veri kümesiyle, arama hizmetinize yönelik gerçek bir iş yükü oluşturun ve test edin.
3. Saniye başına az sayıda sorgu (QPS) ile başlayın ve sorgu gecikmesi tanımlanan hedef gecikme süresinin altına düşene kadar testte yürütülen sayıyı kademeli olarak artırın. Bu, uygulamanızın kullanımda büyüdüğü sürece ölçek planlaması yapmanıza yardımcı olan önemli bir kıyaslamaya yöneliktir.
4. Mümkün olan yerlerde HTTP bağlantılarını yeniden kullanın. Azure Bilişsel Arama .NET SDK kullanıyorsanız bu, bir örneği veya [Searchındexclient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) örneğini yeniden kullanmanız gerektiği anlamına gelir ve REST API kullanıyorsanız, tek bir HttpClient kullanmanız gerekir.
5. Sorgunun farklı bölümlerinin üzerinde gerçekleşmesini sağlamak için sorgu isteklerinin bu şekilde çeşitini farklılık gösterir. Aynı arama isteklerini sürekli olarak yürütüyorsa, değişim önemlidir çünkü sürekli olarak aynı arama isteklerini yürütüyorsa, verilerin önbelleğe alınması, daha farklı bir sorgu kümesiyle olabileceği gibi performansı daha iyi hale getirmek için başlar.
6. Farklı sorgu türlerini alabilmeniz için sorgu isteklerinin yapısını farklılık gösterir. Her arama sorgusu aynı düzeyde uygulanmaz. Örneğin, bir belge arama veya arama önerisi genellikle önemli sayıda model ve filtre içeren bir sorgudan daha hızlıdır. Test kompozisyonu, kabaca, üretimde bekleeceğiniz oranlar halinde çeşitli sorgular içermelidir.  

Bu test iş yüklerini oluştururken aklınızda bulundurmanız gereken bazı Azure Bilişsel Arama özellikleri vardır:

+ Tek seferde çok fazla arama sorgusu göndererek hizmetinizi aşırı yükleme olasılığı vardır. Bu durumda, HTTP 503 yanıt kodları görürsünüz. Test sırasında 503 önlemek için, daha fazla arama isteği eklerken gecikme hızlarındaki farkları görmek üzere çeşitli arama istekleri aralıklarıyla başlayın.

+ Azure Bilişsel Arama, dizin oluşturma görevlerini arka planda çalıştırmaz. Hizmetiniz sorgu ve dizin oluşturma iş yüklerini eşzamanlı olarak işlerinizde, sorgu testleriniz için dizin oluşturma işleri sunarak ya da yoğun saatlerde dizin oluşturma işlerini çalıştırmaya yönelik seçenekleri inceleyerek bunu hesaba sunun.

> [!NOTE]
> [Visual Studio yük testi](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) , Azure bilişsel arama karşı sorgu yürütmek için ihtiyaç duyduğunuz ve isteklerin PARALELLEŞTIRILMESINI sağlayan http isteklerini yürütmenize olanak tanıdığından, kıyaslama testlerinizi gerçekleştirmeye yönelik oldukça iyi bir yoldur.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Yüksek sorgu hacmi ve kısıtlanmış istekler için ölçekleme
Çok fazla kısıtlanmış istek alırken veya artan bir sorgu yüküyle hedef gecikme hızlarınızı aşarsanız, gecikme ücretlerini iki şekilde azaltabilirsiniz:

1. **Çoğaltmaları artırma:**  Çoğaltma, Azure Bilişsel Arama 'ın birden fazla kopyaya karşı yük dengelemesi yapmasına izin veren verilerinizin bir kopyasına benzer.  Tüm yük dengeleme ve verilerin çoğaltmalar arasında çoğaltılması Azure Bilişsel Arama tarafından yönetilir ve hizmetiniz için ayrılan kopyaların istediğiniz zaman sayısını değiştirebilirsiniz.  Standart bir arama hizmetinde 12 ' ye kadar çoğaltma ve temel bir arama hizmetinde 3 çoğaltma ayırabilirsiniz. Çoğaltmalar [Azure Portal](search-create-service-portal.md) ya da [PowerShell](search-manage-powershell.md)'den ayarlanabilir.
2. **Arama katmanını artır:**  Azure Bilişsel Arama [birkaç katmanda](https://azure.microsoft.com/pricing/details/search/) gelir ve bu katmanların her biri farklı performans düzeyleri sunar.  Bazı durumlarda, üzerinde yaptığınız katmanda çoğaltmalar ne zaman bir miktar düşük gecikme süresine sahip olsa bile çok sayıda sorguya sahip olabilirsiniz. Bu durumda, çok sayıda belge ve çok yüksek sorgu iş yüklerine sahip senaryolar için iyi uygun olan Azure Bilişsel Arama S3 katmanı gibi daha yüksek arama katmanlarından birini kullanmayı düşünmek isteyebilirsiniz.

## <a name="scaling-for-slow-individual-queries"></a>Yavaş tek sorgular için ölçekleme
Yüksek gecikme oranları için bir diğer neden, tek bir sorgunun tamamlanmasını çok uzun sürüyor. Bu durumda, çoğaltmaları eklemek yardımcı olmayacaktır. Aşağıdakileri içeren olası iki seçenek vardır:

1. **Bölümleri artırma** Bölüm, verilerinizi ek kaynaklar arasında bölmek için bir mekanizmadır. İkinci bölüm ekleme, verileri ikiye böler, üçüncü bir bölüm onu üç olarak böler ve bu şekilde devam eder. Bir pozitif yan etki, daha yavaş sorguların bazen paralel bilgi işlem nedeniyle daha hızlı bir şekilde gerçekleştirilemesidir. Çok sayıda belgeyle eşleşen sorgular veya çok sayıda belge üzerinde sayı sağlayan modeller gibi düşük seçiciliği sorgularında paralelleştirme belirtiyoruz. Belgelerin yeniden dengeliğine veya belge numaralarını saymaya yönelik önemli bir hesaplama gerektiğinden, ek bölümler eklemek sorguların daha hızlı tamamlanmasını sağlar.  
   
   Temel arama hizmetinde standart arama hizmetinde ve 1 bölümde en fazla 12 bölüm olabilir.  Bölümler [Azure Portal](search-create-service-portal.md) ya da [PowerShell](search-manage-powershell.md)'den ayarlanabilir.

2. **Yüksek kardinalite alanlarını sınırla:** Yüksek bir kardinalite alanı, önemli sayıda benzersiz değere sahip çok yönlü veya filtrelenebilir bir alandan oluşur ve sonuç olarak sonuçları hesaplarken önemli kaynakları tüketir. Örneğin, bir ürün KIMLIĞI veya açıklama alanını çok yönlü tablo/filtre olarak ayarlamak, belgedeki değerlerin çoğu benzersiz olduğundan yüksek önem düzeyi olarak sayılır. Mümkün olan yerlerde, yüksek kardinalite alanlarının sayısını sınırlandırın.

3. **Arama katmanını artır:**  Daha yüksek bir Azure Bilişsel Arama katmanına taşımak, yavaş sorguların performansını artırmanın başka bir yolu olabilir. Her iki katman da daha hızlı CPU ve daha fazla bellek sağlar ve bunların her ikisi de sorgu performansı üzerinde olumlu bir etkiye sahiptir.

## <a name="scaling-for-availability"></a>Kullanılabilirlik için ölçeklendirme
Çoğaltmalar yalnızca sorgu gecikmesini azaltmaya yardımcı olmakla kalmaz yüksek kullanılabilirliğe de izin verebilir. Tek bir çoğaltmayla, yazılım güncelleştirmelerinden sonra veya gerçekleşen diğer bakım olayları için sunucu yeniden başlatılması nedeniyle dönemsel kapalı kalma süresi beklemelisiniz.  Sonuç olarak, uygulamanızın çok yüksek düzeyde aramalar (sorgular) ve yazmaları (Dizin oluşturma olayları) gerektirip gerektirmediğini göz önünde bulundurmanız önemlidir. Azure Bilişsel Arama, tüm ücretli arama tekliflerindeki SLA seçeneklerini aşağıdaki özniteliklerle sunar:

* salt okuma iş yüklerinin yüksek kullanılabilirliği (sorgular) için 2 çoğaltma
* okuma/yazma iş yüklerinin yüksek kullanılabilirliği için 3 veya daha fazla çoğaltma (sorgular ve dizin oluşturma)

Bunun hakkında daha fazla bilgi için lütfen [Azure Bilişsel Arama hizmet düzeyi sözleşmesi](https://azure.microsoft.com/support/legal/sla/search/v1_0/)ziyaret edin.

Çoğaltmalar verilerinizin kopyaları olduğundan, birden fazla kopyaya sahip olmak Azure Bilişsel Arama 'nin makine yeniden başlatmaları ve bakım yapmasına izin verirken, sorgu yürütmeye diğer çoğaltmalara karşı devam etmesine izin verir. Buna karşılık, çoğaltmaları dışarıda bırakırsanız, bu çoğaltmaların bir veya daha fazla kaynak olduğu varsayıldığında sorgu performansı düşüşüne tabi olursunuz.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Coğrafi olarak dağıtılan iş yükleri ve coğrafi yedeklilik için ölçekleme
Coğrafi olarak dağıtılan iş yükleri için, Azure Bilişsel Arama barındıran veri merkezi 'nden uzak olarak bulunan kullanıcıların gecikme süresi daha yüksektir. Bir hafifletme, bölgelere bu kullanıcılara daha yakından yaklaşarak birden çok arama hizmeti sağlamak. Azure Bilişsel Arama, bölge genelinde Azure Bilişsel Arama dizinlerinin coğrafi olarak çoğaltılmasının otomatik bir yöntemini sağlamıyor, ancak bu işlemi uygulamak ve yönetmek için basit hale getirmek üzere kullanılabilecek bazı teknikler vardır. Bunlar, sonraki birkaç bölümde özetlenmiştir.

Coğrafi olarak dağıtılmış bir arama hizmetleri kümesinin amacı, bir kullanıcının Azure Bilişsel Arama hizmetine yönlendirildiği iki veya daha fazla bölgenin, bu örnekte görüldüğü gibi en düşük gecikme süresini sağlayan iki veya daha fazla dizine sahip olmaktır:

   ![Bölgeye göre hizmetlerin çapraz sekmesi][1]

### <a name="keeping-data-in-sync-across-multiple-azure-cognitive-search-services"></a>Birden çok Azure Bilişsel Arama hizmeti arasında verileri eşitlenmiş halde tutma
Dağıtılmış arama hizmetlerinizin [azure bilişsel arama Dizin oluşturucuyu](search-indexer-overview.md) veya anında iletme API 'Sini ( [Azure bilişsel arama REST API](https://docs.microsoft.com/rest/api/searchservice/)olarak da bilinir) kullanmaktan oluşan eşzamanlı olarak tutulması için kullanabileceğiniz iki seçenek vardır.  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Birden çok hizmet üzerinde içerik güncelleştirmek için Dizin oluşturucular kullanma

Bir hizmette zaten Dizin Oluşturucu kullanıyorsanız, ikinci bir hizmette ikinci bir Dizin Oluşturucu yapılandırarak aynı veri kaynağı nesnesini kullanarak aynı konumdan veri çekmesini sağlayabilirsiniz. Her bölgedeki her bir hizmetin kendi Dizin Oluşturucusu ve bir hedef dizini vardır (arama dizininiz paylaşılmaz, bu da veriler yinelenir), ancak her Dizin Oluşturucu aynı veri kaynağına başvurur.

Mimarinin nasıl görüneceğine ilişkin üst düzey bir görsel aşağıda verilmiştir.

   ![Dağıtılmış Dizin Oluşturucu ve hizmet bileşimleri içeren tek veri kaynağı][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Birden çok hizmete içerik güncelleştirmelerini göndermek için REST API 'Lerini kullanma
Azure [bilişsel arama dizininizdeki içeriği göndermek](https://docs.microsoft.com/rest/api/searchservice/update-index)için azure bilişsel arama REST API kullanıyorsanız, her bir güncelleştirme gerektiğinde tüm arama hizmetlerine değişiklikleri göndererek çeşitli arama hizmetlerinizi eşitlenmiş halde tutabilirsiniz. Kodunuzda, bir arama hizmetine yapılan bir güncelleştirmenin başarısız olduğu ancak diğer arama hizmetleri için başarılı olduğu durumları işlediğinizden emin olun.

## <a name="leverage-azure-traffic-manager"></a>Azure Traffic Manager 'ten yararlanın
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) , istekleri birden çok arama hizmeti tarafından desteklenen, coğrafi olarak bulunan birden çok Web sitesine yönlendirmenize olanak tanır. Traffic Manager avantajlarından biri, kullanılabilir olmasını sağlamak için Azure Bilişsel Arama araştırma yapabilir ve kapalı kalma durumunda kullanıcıları alternatif arama hizmetleri 'ne yönlendirebilir. Ayrıca, Azure Web siteleri aracılığıyla arama isteklerini yönlendirçalışıyorsanız Azure Traffic Manager, Web sitesinin en fazla Azure Bilişsel Arama olmadığı, Yük Dengeleme durumlarını yüklemenize izin verir. Traffic Manager yararlanan mimarinin bir örneği aşağıda verilmiştir.

   ![Merkezi Traffic Manager ile bölgelere göre hizmetlerin çapraz sekmesi][3]

## <a name="next-steps"></a>Sonraki adımlar
Her biri için fiyatlandırma katmanları ve hizmet limitleri hakkında daha fazla bilgi edinmek için bkz. [Azure bilişsel arama hizmet limitleri](search-limits-quotas-capacity.md).

Bölüm ve çoğaltma birleşimleri hakkında daha fazla bilgi edinmek için [Kapasite planlamasını](search-capacity-planning.md) ziyaret edin.

Performans hakkında daha fazla ayrıntıya gitme ve bu makalede ele alınan iyileştirmelerin nasıl uygulanacağı hakkında bazı gösteriler hakkında daha fazla bilgi için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
