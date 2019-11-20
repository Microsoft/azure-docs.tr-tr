---
title: Yerleşik Dizin oluşturucular kullanarak büyük veri kümesini dizin oluşturma
titleSuffix: Azure Cognitive Search
description: Toplu iş modu, kaynak kaynağı oluşturma ve zamanlanan, paralel ve Dağıtılmış dizin oluşturma teknikleri aracılığıyla büyük veri dizinleme veya hesaplama açısından yoğun dizin oluşturma stratejileri.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bd158eaf22025a64d7464c632d3f0fa510a4b5a3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793756"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Azure Bilişsel Arama büyük veri kümelerini dizin oluşturma

Veri birimlerinin büyümesi veya işlenmesi değiştikçe, basit veya varsayılan dizin oluşturma stratejilerinin artık pratik olmadığını görebilirsiniz. Azure Bilişsel Arama için, bir veri yükleme isteğini nasıl yapılandıracağınıza, zamanlanmış ve dağıtılmış iş yükleri için kaynağa özel bir dizin oluşturucunun kullanılmasına kadar, daha büyük veri kümelerine sahip olmak için çeşitli yaklaşımlar vardır.

Aynı teknikler de uzun süreli süreçler için de geçerlidir. Özellikle, [paralel dizin oluşturma](#parallel-indexing) bölümünde özetlenen adımlar, bir [AI zenginleştirme ardışık düzeninde](cognitive-search-concept-intro.md)görüntü analizi veya doğal dil işleme gibi yoğun hesaplama gerektiren dizin oluşturma için yararlıdır.

Aşağıdaki bölümlerde, büyük miktarlarda veri dizinlemeye yönelik üç teknik araştırılamaz.

## <a name="option-1-pass-multiple-documents"></a>Seçenek 1: birden çok belge geçirme

Daha büyük bir veri kümesini dizinlemeye yönelik en basit mekanizmalardan biri, tek bir istekte birden fazla belge veya kayıt gönderkullanmaktır. Tüm yükün 16 MB altında olduğu sürece, bir istek toplu karşıya yükleme işleminde en fazla 1000 belge işleyebilir. Bu sınırlar, .NET SDK 'da [belge ekle REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) veya [Dizin yöntemi](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) kullanıp kullanmayacağınızı uygular. Her iki API için de, her isteğin gövdesinde 1000 belge paketlemeyi yapmanız gerekir.

Toplu dizin oluşturma, REST veya .NET kullanan tek istekler veya Dizin oluşturucular aracılığıyla uygulanır. Birkaç Dizin Oluşturucu farklı limitlerde çalışır. Özellikle, Azure Blob dizinlemesi, 10 belge üzerinde toplu iş boyutunu, büyük ortalama belge boyutunu tanımaya göre ayarlar. [Create Indexer REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)temel alan Dizin oluşturucular için, bu ayarı özelleştirmek üzere `BatchSize` bağımsız değişkenini, verilerinizin özellikleriyle daha iyi eşleşecek şekilde ayarlayabilirsiniz. 

> [!NOTE]
> Belge boyutunu aşağı tutmak için, sorgulanabilir olmayan verileri bir dizine eklemekten kaçının. Görüntüler ve diğer ikili veriler doğrudan aranabilir değildir ve dizinde depolanmamalıdır. Sorgulanabilir olmayan verileri arama sonuçlarıyla bütünleştirmek için, kaynağa bir URL başvurusu depolayan aranabilir olmayan bir alan tanımlamalısınız.

## <a name="option-2-add-resources"></a>2\. seçenek: kaynak ekleme

[Standart fiyatlandırma katmanlarından](search-sku-tier.md) birinde sağlanan hizmetler genellikle hem depolama hem de iş yükleri (sorgular ya da dizin oluşturma) için yetersiz kapasiteye sahiptir ve bu sayede [bölüm ve çoğaltmanın artması](search-capacity-planning.md) için açık bir çözüm sayılır daha büyük veri kümelerini koname. En iyi sonuçları elde etmek için, her iki kaynağa de ihtiyacınız vardır: depolama için bölümler ve veri alımı iş çoğaltmaları.

Çoğaltmaları ve bölümleri artırmak, maliyetinizi artıran etkinliklerdir, ancak en fazla yük altında sürekli olarak dizin oluşturmadığınız sürece, dizin oluşturma işlemi süresince ölçek ekleyebilir ve ardından kaynak düzeylerini dizin oluşturma işleminin süresi boyunca aşağı doğru ayarlayabilirsiniz bitirdikten.

## <a name="option-3-use-indexers"></a>Seçenek 3: Dizin oluşturucular kullanma

[Dizin oluşturucular](search-indexer-overview.md) , aranabilir içerik Için desteklenen Azure veri kaynaklarını gezinmek üzere kullanılır. Özellikle büyük ölçekli dizin oluşturma için özel bir yöntem olmasa da, birkaç dizin oluşturucu özelliği özellikle daha büyük veri kümelerine göz sallarken yararlı olur:

+ Zamanlayıcılar, zaman içinde yayabilmeniz için dizin oluşturmayı düzenli aralıklarla kullanmanıza olanak sağlar.
+ Zamanlanan dizin oluşturma, bilinen son durdurma noktasında sürdürülür. Bir veri kaynağı 24 saatlik bir pencerede tam olarak gezinilmemişse, Dizin Oluşturucu, her yerde iki gün boyunca Dizin oluşturmayı sürdürür.
+ Verileri daha küçük tekil veri kaynaklarına bölümlemek paralel işleme etkinleştirilir. Kaynak verileri Azure Blob depolamada birden çok kapsayıcı gibi daha küçük bileşenlere bölebilir ve ardından, paralel olarak dizinlenebilir Azure Bilişsel Arama karşılık gelen, birden fazla [veri kaynağı nesnesi](https://docs.microsoft.com/rest/api/searchservice/create-data-source) oluşturabilirsiniz.

> [!NOTE]
> Dizin oluşturucular veri kaynağına özgüdür, bu nedenle bir Dizin Oluşturucu yaklaşımı kullanmak yalnızca Azure 'daki seçili veri kaynakları için geçerlidir: [SQL veritabanı](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [BLOB depolama](search-howto-indexing-azure-blob-storage.md), [Tablo depolama](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Zamanlanan dizin oluşturma

Dizin Oluşturucu zamanlaması, büyük veri kümelerini işlemeye yönelik önemli bir mekanizmadır ve bilişsel arama işlem hattındaki görüntü analizi gibi yavaş çalışan işlemleri. Dizin Oluşturucu işleme, 24 saatlik bir pencere içinde çalışır. İşlem, 24 saat içinde sonlanamazsa, Dizin Oluşturucu zamanlamalarının davranışları avantajınıza çalışabilir. 

Tasarıma göre, zamanlanan dizin oluşturma işlemi, genellikle zamanlanan bir sonraki aralıkta devam etmeden önce tamamlanan bir iş ile belirli aralıklarla başlar. Ancak, işlem Aralık içinde tamamlanmazsa, Dizin Oluşturucu duraklar (zaman aşımına uğradığından). Sonraki aralıkta, işleme en son kaldığınız yerden devam eder ve sistem nerede olursa olduğu gibi izler. 

Pratik koşullarda, birkaç güne yayılan Dizin yükleri için Dizin oluşturucuyu 24 saatlik bir zamanlamaya yerleştirebilirsiniz. Sonraki 24 saat boyunca yeniden çalışmaya devam ederse, en son bilinen iyi belgede yeniden başlatılır. Bu şekilde, bir Dizin Oluşturucu, tüm işlenmemiş belgeler işlenene kadar bir dizi gün boyunca bir belge biriktirme listesi aracılığıyla çalışmasını sağlayabilir. Bu yaklaşım hakkında daha fazla bilgi için bkz. [Azure Blob depolamada büyük veri kümelerini dizine alma](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Zamanlamaları genel olarak ayarlama hakkında daha fazla bilgi için bkz. [Dizin Oluşturucu oluşturma REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax) veya [Azure bilişsel arama için Dizin oluşturucuyu zamanlama](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Paralel dizin oluşturma

Paralel dizin oluşturma stratejisi, her veri kaynağı tanımının verilerin bir alt kümesini belirttiği, uyum içinde birden çok veri kaynağını dizine almayı temel alır. 

Bir bilişsel arama işlem hattındaki taranmış belgelerde OCR gibi rutin olmayan, yoğun şekilde yoğun dizin oluşturma gereksinimleri, görüntü analizi veya doğal dil işleme için, paralel bir dizin oluşturma stratejisi genellikle bir uzun süre çalışan işlem en kısa sürede. Sorgu isteklerini ortadan kaldırabilir veya azaltabiliyorsanız, sorguları eşzamanlı olarak işleme olmayan bir hizmette Paralel dizin oluşturma, büyük bir yavaş işleme içeriği ile çalışmaya yönelik en iyi strateji seçeneğidir. 

Paralel işleme şu öğelere sahiptir:

+ Kaynak verileri birden çok kapsayıcı veya aynı kapsayıcı içindeki birden çok sanal klasör arasında alt bölümlere ayır. 
+ Her bir mini veri kümesini kendi [veri kaynağına](https://docs.microsoft.com/rest/api/searchservice/create-data-source)eşleyin ve kendi [Dizin oluşturucula](https://docs.microsoft.com/rest/api/searchservice/create-indexer)eşleştirilmiş olarak eşleştirin.
+ Bilişsel arama için, her bir Dizin Oluşturucu tanımında aynı [beceri](https://docs.microsoft.com/rest/api/searchservice/create-skillset) başvurun.
+ Aynı hedef arama dizinine yazın. 
+ Tüm dizin oluşturucularının aynı anda çalışmasını zamanlayın.

> [!NOTE]
> Azure Bilişsel Arama, çoğaltmaları veya bölümleri belirli iş yüklerine ayırabilirsiniz. Ağır eşzamanlı dizin oluşturma riski, sisteminizin sorgu performansının üstesinden gelme riskini ortadan kaldırır. Bir sınama ortamınız varsa, bu avantajları anlamak için öncelikle Paralel dizin oluşturma uygulayın.

### <a name="how-to-configure-parallel-indexing"></a>Paralel dizin oluşturmayı yapılandırma

Dizin oluşturucular için, işlem kapasitesi, arama hizmetiniz tarafından kullanılan her bir hizmet birimi (SU) için bir Dizin Oluşturucu alt sistemine bağlıdır. En az iki çoğaltmaya sahip temel veya Standart katmanlarda sağlanan Azure Bilişsel Arama hizmetlerinde birden çok eş zamanlı Dizin Oluşturucu mümkündür. 

1. [Azure Portal](https://portal.azure.com), arama hizmeti panonuz **genel bakış** sayfasında, **fiyatlandırma katmanını** denetleyerek Paralel dizin oluşturma işleminin uyumlu olduğunu doğrulayın. Hem temel hem de Standart katmanlar birden çok çoğaltma sunar.

2. **Ayarlar** > **ölçeğinde**, paralel işleme için [çoğaltmaları artırın](search-capacity-planning.md) : her bir dizin oluşturucu iş yükü için bir ek çoğaltma. Mevcut sorgu birimi için yeterli bir sayı bırakın. Dizin oluşturma için sorgu iş yüklerinin Fede iyi bir zorunluluğunu getirir değildir.

3. Azure Bilişsel Arama dizin oluşturucularının ulaşabileceği bir düzeyde verileri birden çok kapsayıcıya dağıtın. Bu, Azure SQL veritabanı 'nda birden çok tablo, Azure Blob depolamada birden çok kapsayıcı veya birden çok koleksiyon olabilir. Her tablo veya kapsayıcı için bir veri kaynağı nesnesi tanımlayın.

4. Paralel olarak çalışmak için birden çok Dizin Oluşturucu oluşturun ve zamanlayın:

   + Altı çoğaltmalarla bir hizmet varsayın. Her biri, veri kümesinin tamamının 6 yönlü bölünmesi için veri kümesinin altıncı bir kısmını içeren bir veri kaynağıyla eşleştirilmiş altı Dizin oluşturucuyu yapılandırın. 

   + Her dizin oluşturucuyu aynı dizine işaret edin. Bilişsel arama iş yükleri için, her bir dizin oluşturucuyu aynı beceri işaret edin.

   + Her bir Dizin Oluşturucu tanımı içinde aynı çalışma zamanı yürütme modelini zamanlayın. Örneğin, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }`, tüm dizin oluşturucularda sekiz saatlik aralıklarla çalışan, 2018-05-15 üzerinde bir zamanlama oluşturur.

Zamanlanan zamanda, tüm dizin oluşturucular yürütme, veri yükleme, zenginleştirme uygulama (bilişsel arama işlem hattı yapılandırdıysanız) ve dizine yazma işlemini başlatır. Azure Bilişsel Arama, güncelleştirme dizinini kilitlemez. İlk denemede belirli bir yazma işlemi başarısız olursa, eşzamanlı yazma işlemleri yönetilir.

> [!Note]
> Çoğaltmaları arttırdığınızda, dizin boyutu önemli ölçüde artırmak için yansıtıldıysanız bölüm sayısını artırmayı göz önünde bulundurun. Bölümler, dizinli içeriğin dilimlerini depolar; ne kadar çok bölüm varsa, dilimin her birinin depolanması gerekir.

## <a name="see-also"></a>Ayrıca bkz.

+ [Dizin Oluşturucu’ya genel bakış](search-indexer-overview.md)
+ [Portalda dizin oluşturma](search-import-data-portal.md)
+ [Azure SQL veritabanı Dizin Oluşturucu](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB dizinleyici](search-howto-index-cosmosdb.md)
+ [Azure Blob Depolama dizin oluşturucu](search-howto-indexing-azure-blob-storage.md)
+ [Azure Tablo Depolama dizin oluşturucu](search-howto-indexing-azure-tables.md)
+ [Azure Bilişsel Arama güvenliği](search-security-overview.md)
