---
title: Sık sorulan sorular (SSS)
titleSuffix: Azure Cognitive Search
description: Microsoft Azure üzerinde bulut barındırılan arama hizmeti olan Microsoft Azure Bilişsel Arama hizmeti hakkında sık sorulan sorulara yanıtlar alın.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fee74cb6ec5acd5fa0f171eab9769a833f04ad66
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792905"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure Bilişsel Arama-sık sorulan sorular (SSS)

 Azure Bilişsel Arama ile ilgili kavramlar, kod ve senaryolar hakkında sık sorulan soruların yanıtlarını bulun.

## <a name="platform"></a>Platform

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Azure Bilişsel Arama, DBMS 'imde tam metin aramasından farklı midir?

Azure Bilişsel Arama birden çok veri kaynağını, [birçok dil için dil analizini](https://docs.microsoft.com/rest/api/searchservice/language-support), [ilginç ve olağandışı veri girişlerinin özel analizini](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), [Puanlama profilleri](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)aracılığıyla arama derecelendirme denetimleri ve Kullanıcı deneyimini destekler typeahead, isabet vurgulama ve çok yönlü gezinme gibi özellikler. Ayrıca, eş anlamlılar ve zengin sorgu söz dizimi gibi diğer özellikleri de içerir, ancak bunlar genellikle özellikleri farklılaştırmaz.

### <a name="what-is-the-difference-between-azure-cognitive-search-and-elasticsearch"></a>Azure Bilişsel Arama ve Elakıa araması arasındaki fark nedir?

Arama teknolojilerini karşılaştırırken, müşteriler, Azure Bilişsel Arama 'nin Elau araması ile nasıl Karşılaştırıldığı hakkında daha fazla ayrıntı ister. Önemli bir görevi daha kolay hale yaptığımız veya diğer Microsoft teknolojileriyle yerleşik tümleştirmeye gerek duyduğumuz için, Azure Bilişsel Arama, arama uygulaması projelerine yönelik olarak Azure 'yi seçen müşteriler genellikle bunu yaptı.

+ Azure Bilişsel Arama,% 99,9 hizmet düzeyi sözleşmeleri (SLA) ile tam olarak yönetilen bir bulut hizmetidir (okuma erişimi için 2 çoğaltma, okuma-yazma için üç çoğaltma).
+ Microsoft 'un [doğal dil işlemcileri](https://docs.microsoft.com/rest/api/searchservice/language-support) önde gelen Edge dil analizi sunar.  
+ [Azure bilişsel arama Dizin oluşturucular](search-indexer-overview.md) , ilk ve artımlı dizin oluşturma Için çeşitli Azure veri kaynaklarına gezinebilirler.
+ Sorgu veya dizin oluşturma birimlerindeki dalgalanmalara hızlı yanıt almanız gerekiyorsa Azure portal [kaydırıcı denetimlerini](search-manage.md#scale-up-or-down) kullanabilir veya bir [PowerShell betiği](search-manage-powershell.md)çalıştırarak doğrudan yönetimi doğrudan atlayarak yönetebilirsiniz.  
+ [Puanlama ve ayarlama özellikleri](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) , arama altyapısının tek başına sağlayabildiklerinin ötesinde, arama sıra puanlarını etkileyen yolları sağlar.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Azure Bilişsel Arama hizmetini duraklatabilir ve faturalandırmayı durdurabilir miyim?

Hizmeti duraklatırsınız. Hesaplama ve depolama kaynakları, hizmet oluşturulduğunda özel kullanım için ayrılır. Bu kaynakları isteğe bağlı olarak serbest bırakmak ve geri kazanmak mümkün değildir.

## <a name="indexing-operations"></a>Dizin oluşturma Işlemleri

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Dizinleri veya dizin anlık görüntülerini taşıyın, yedekleyin ve geri yükleyin

Geliştirme aşamasında, dizininizi arama hizmetleri arasında taşımak isteyebilirsiniz. Örneğin, dizininizin geliştirilmesi için temel veya ücretsiz bir fiyatlandırma katmanı kullanabilir ve daha sonra üretim kullanımı için standart veya daha yüksek bir katmana taşımak isteyebilirsiniz. 

Ya da, daha sonra geri yüklemek için kullanılabilecek dosyalara bir dizin anlık görüntüsü yedeklemek isteyebilirsiniz. 

Bu [Azure bilişsel arama .NET örnek deposu](https://github.com/Azure-Samples/azure-search-dotnet-samples)' nda **Dizin-yedekleme-geri yükleme** örnek kodu ile her şeyi yapabilirsiniz. 

Azure Bilişsel Arama REST API kullanarak istediğiniz zaman [bir dizin tanımı da alabilirsiniz](https://docs.microsoft.com/rest/api/searchservice/get-index) .

Şu anda Azure portal yerleşik Dizin ayıklama, anlık görüntü veya yedekleme-geri yükleme özelliği yoktur. Bununla birlikte, yedekleme ve geri yükleme işlevlerini gelecek bir sürüme eklemeyi düşünüyoruz. Bu özellik için desteğiniz gösterilmesini istiyorsanız, [Kullanıcı sesine](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index)bir oy atayın.

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Dizin veya hizmetmi silindikten sonra geri yükleyebilir miyim?

Hayır, bir Azure Bilişsel Arama dizini veya hizmeti silerseniz, bu, kurtarılamaz. Bir Azure Bilişsel Arama hizmetini sildiğinizde, hizmette bulunan tüm dizinler kalıcı olarak silinir. Bir veya daha fazla Azure Bilişsel Arama hizmeti içeren bir Azure kaynak grubunu silerseniz, tüm hizmetler kalıcı olarak silinir.  

Dizinler, Dizin oluşturucular, veri kaynakları ve becerileri gibi kaynakların yeniden oluşturulması, bunları koddan yeniden oluşturmanızı gerektirir. 

Bir dizini yeniden oluşturmak için dış kaynaklardaki verileri yeniden dizinetmeniz gerekir. Bu nedenle, Azure SQL veritabanı veya Cosmos DB gibi başka bir veri deposunda orijinal verilerin bir ana kopyasını veya yedeklemesini tutmanız önerilir.

Alternatif olarak, bir dizin tanımını ve dizin anlık görüntüsünü bir dizi JSON dosyasına yedeklemek için bu [Azure bilişsel arama .NET örnek](https://github.com/Azure-Samples/azure-search-dotnet-samples) kodundaki **Dizin-yedekleme-geri yükleme** örnek kodunu kullanabilirsiniz. Daha sonra, gerekirse, dizini geri yüklemek için aracını ve dosyalarını kullanabilirsiniz.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>SQL veritabanı Çoğaltmalarından dizin oluşturabilir miyim ( [Azure SQL veritabanı Dizin oluşturucular](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers)için geçerlidir)

Sıfırdan bir dizin oluştururken bir veri kaynağı olarak birincil veya ikincil çoğaltmaların kullanılmasına yönelik bir kısıtlama yoktur. Ancak, artımlı güncelleştirmeler (değiştirilen kayıtlara göre) ile bir dizini yenilemek, birincil çoğaltmayı gerektirir. Bu gereksinim yalnızca birincil çoğaltmalarda değişiklik izlemeyi garanti eden SQL veritabanından gelir. Bir dizin yenileme iş yükü için ikincil çoğaltmaları kullanmayı denerseniz, tüm verileri alacağınız garanti yoktur.

## <a name="search-operations"></a>Işlemleri ara

### <a name="can-i-search-across-multiple-indexes"></a>Birden çok dizin arasında arama yapabilir miyim?

Hayır, bu işlem desteklenmiyor. Arama her zaman tek bir dizine eklenir.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Kullanıcı kimliğine göre arama dizini erişimini kısıtlayabilirim miyim?

`search.in()` filtresiyle [Güvenlik filtreleri](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) uygulayabilirsiniz. Filtre, arama sonuçlarını tanımlanan Kullanıcı grubu üyeliğine göre kırpmak için [Azure Active Directory (AAD) gibi kimlik yönetimi hizmetleri](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) ile iyi bir bileşim sağlar.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Neden geçerli olduğunu bildiğiniz koşullarda hiç sıfır eşleşme var mı?

En yaygın durum, her bir sorgu türünün farklı arama davranışlarını ve dil analizlerinin düzeylerini desteklediğini bilmektir. Baskın iş yükü olan tam metin araması, kök formlara koşulları kesen bir dil analizi aşaması içerir. Bu sorgu ayrıştırma özelliği, belirteçlerin çok sayıda çeşitle eşleştiğinden, olası eşleşmelerin daha geniş bir kısmını yayınlar.

Ancak, joker karakter, benzer ve Regex sorguları, normal terim veya tümcecik sorguları gibi çözümlenmez ve sorgu, arama dizininde bulunan sözcüğün çözümlenme formuyla eşleşmezse kötü bir geri aramaya yol açabilir. Sorgu ayrıştırma ve çözümleme hakkında daha fazla bilgi için bkz. [sorgu mimarisi](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Joker karakter aramalarım yavaş.

Önek, benzer ve Regex gibi çoğu joker karakter arama sorguları, arama dizininde eşleşen koşullara göre dahili olarak yeniden yazılır. Arama dizinini taramanın bu ek işlemesi gecikme süresine eklenir. Daha fazla terim `a*` gibi büyük arama sorguları çok daha yavaş olabilir. Performanslı joker karakter aramaları için [özel bir çözümleyici](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)tanımlamayı düşünün.

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Arama neden her isabet için bir sabit veya eşit puanı 1,0 olarak derecelendirmelidir?

Varsayılan olarak, arama sonuçları, [eşleşen koşulların istatistiksel özelliklerine](search-lucene-query-architecture.md#stage-4-scoring)göre puanlanır ve sonuç kümesinde yüksek ile düşük sıralanır. Ancak, bazı sorgu türleri (joker karakter, ön ek, Regex) her zaman genel belge puanına bir sabit puanı katkıda bulunur. Bu davranış tasarım gereğidir. Azure Bilişsel Arama, sorgu genişletmesi aracılığıyla bulunan eşleşmelerin, derecelendirmeyi etkilemeden, sonuçlara dahil edilmesini sağlamak için sabit bir puan uygular.

Örneğin, joker karakter aramasında "Tur" girişinin "turları", "touretes" ve "Tourmaline" ile eşleşmeleri olduğunu varsayalım. Bu sonuçların doğası göz önüne alındığında, hangi koşulların diğerlerinden daha değerli olduğunu makul bir şekilde çıkarmanın bir yolu yoktur. Bu nedenle, Puanlama joker karakter, ön ek ve normal ifade türündeki sorgularda sonuçladığı zaman terim sıklıklarını yok saydık. Kısmi girişe dayalı arama sonuçlarına, beklenmedik potansiyel eşleşmelerin önüne geçmek için sabit bir puan verilir.

## <a name="design-patterns"></a>Tasarım desenleri

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Yerelleştirilmiş arama uygulamak için en iyi yaklaşım nedir?

Çoğu müşteri, aynı dizinde farklı yerel ayarları (diller) desteklemeye geldiğinde bir koleksiyon üzerinde adanmış alanlar seçer. Yerel ayara özgü alanlar, uygun bir çözümleyici atanmasını olanaklı kılar. Örneğin, Microsoft Fransızca Çözümleyicisi 'ni Fransızca dizeleri içeren bir alana atama. Ayrıca filtrelemeyi basitleştirir. Bir fr-fr sayfasında bir sorgunun başlatıldığını biliyorsanız, arama sonuçlarını bu alanla sınırlayabilirsiniz. Veya alana daha fazla ağırlık sağlamak için bir [Puanlama profili](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) oluşturun. Azure Bilişsel Arama, arasından seçim yapmak için 50 ' den fazla [dil Çözümleyicileri](https://docs.microsoft.com/azure/search/search-language-support) destekler.

## <a name="next-steps"></a>Sonraki adımlar

Sorunuz, eksik bir özellik veya işlevlerle ilgili mi? Özelliği [Kullanıcı Voice Web sitesinde](https://feedback.azure.com/forums/263029-azure-search)isteyin.

## <a name="see-also"></a>Ayrıca bkz.

 [StackOverflow: Azure Bilişsel Arama](https://stackoverflow.com/questions/tagged/azure-search)   
 [Tam metin aramasının Azure 'da nasıl çalıştığı Bilişsel Arama](search-lucene-query-architecture.md)  
 [Azure Bilişsel Arama nedir?](search-what-is-azure-search.md)
