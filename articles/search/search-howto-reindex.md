---
title: Bir arama dizinini yeniden derle
titleSuffix: Azure Cognitive Search
description: Bir Azure Bilişsel Arama dizinini yenilemek için yeni öğeler ekleyin, var olan öğeleri veya belgeleri güncelleştirin veya tam yeniden oluşturma veya kısmi artımlı dizin oluşturma içindeki eski belgeleri silin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b14c153f52e0427e289afeccdfd22d6510e4ace1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112957"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Azure Bilişsel Arama bir dizini yeniden oluşturma

Bu makalede bir Azure Bilişsel Arama dizininin nasıl yeniden oluşturulduğu, yeniden oluşturmanın gerekli olduğu koşullar ve devam eden sorgu isteklerinde yeniden oluşturma işlemlerinin etkisini azaltmaya yönelik öneriler açıklanmaktadır.

*Yeniden oluşturma* , tüm alan tabanlı ters dizinler de dahil olmak üzere bir dizinle ilişkili fiziksel veri yapılarını bırakmayı ve yeniden oluşturmayı gösterir. Azure Bilişsel Arama ' de tek tek alanları bırakamaz ve yeniden oluşturamazsınız. Bir dizini yeniden derlemek için, tüm alan depolamanın silinmesi, var olan veya düzeltilmiş bir dizin şemasına göre yeniden oluşturulması ve sonra dizine gönderilen veya dış kaynaklardan çekilmiş verilerle yeniden doldurulması gerekir. Geliştirme sırasında dizinleri yeniden oluşturmak yaygındır, ancak karmaşık türler ekleme veya öneri araçları 'ye alan ekleme gibi yapısal değişikliklere uyum sağlamak için üretim düzeyinde bir dizini yeniden oluşturmanız gerekebilir.

Bir dizini çevrimdışı duruma alan yeniden oluşturma 'nın aksine, *veri yenileme* bir arka plan görevi olarak çalışır. Sorgu yüklerini genellikle daha uzun süreseler de, sorguları sorgulamak için en az kesintiye sahip belgeleri ekleyebilir, kaldırabilir ve değiştirebilirsiniz. Dizin içeriğini güncelleştirme hakkında daha fazla bilgi için bkz. [belge ekleme, güncelleştirme veya silme](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Koşulları yeniden oluştur

| Koşul | Açıklama |
|-----------|-------------|
| Alan tanımını değiştirme | Bir alan adını, veri türünü veya belirli [Dizin özniteliklerini](https://docs.microsoft.com/rest/api/searchservice/create-index) (aranabilir, filtrelenebilir, sıralanabilir, çok yönlü tablo) yeniden düzeltme için tam yeniden oluşturma gerekir. |
| Bir alana çözümleyici atama | [Çözümleyiciler](search-analyzers.md) bir dizinde tanımlanır ve alanlara atanır. Dizine dilediğiniz zaman yeni bir çözümleyici tanımı ekleyebilirsiniz, ancak alan oluşturulduğunda yalnızca bir çözümleyici *atayabilirsiniz* . Bu, hem **çözümleyici** hem de **ındexanalyzer** özellikleri için geçerlidir. **SearchAnalyzer** özelliği bir özel durumdur (Bu özelliği var olan bir alana atayabilirsiniz). |
| Bir dizindeki çözümleyici tanımını güncelleştirme veya silme | Tüm dizini yeniden oluşturmadığınız müddetçe, dizinde var olan bir çözümleyici yapılandırmasını (çözümleyici, belirteç Oluşturucu, belirteç filtresi veya char filtresi) silemez veya değiştiremezsiniz. |
| Öneri aracı bir alan ekleme | Bir alan zaten varsa ve onu bir [Öneri araçları](index-add-suggesters.md) yapısına eklemek istiyorsanız, dizini yeniden oluşturmanız gerekir. |
| Bir alanı silme | Bir alanın tüm izlemelerini fiziksel olarak kaldırmak için, dizini yeniden oluşturmanız gerekir. Hemen yeniden oluşturma pratik olmadığında, "silinen" alanına erişimi devre dışı bırakmak için uygulama kodunu değiştirebilirsiniz. Fiziksel olarak, söz konusu alanı atlayacağınız bir şemayı uyguladığınızda, alan tanımı ve içerikleri, sonraki yeniden oluşturmaya kadar dizinde kalır. |
| Katmanları Değiştir | Daha fazla kapasiteye ihtiyacınız varsa Azure portal yerinde yükseltme yoktur. Yeni bir hizmet oluşturulmalıdır ve yeni hizmette dizinlerin sıfırdan oluşturulması gerekir. Bu işlemin otomatikleştirilmesine yardımcı olmak için, bu [Azure bilişsel arama .NET örnek](https://github.com/Azure-Samples/azure-search-dotnet-samples)depodaki **Dizin-yedekleme-geri yükleme** örnek kodunu kullanabilirsiniz. Bu uygulama, dizininizi bir dizi JSON dosyasına yedekleyecek ve sonra belirttiğiniz bir arama hizmetinde dizini yeniden oluşturacak.|

Diğer herhangi bir değişiklik, mevcut fiziksel yapıları etkilemeden yapılabilir. Özellikle, aşağıdaki değişiklikler dizin yeniden *oluşturma gerektirmez:*

+ Yeni alan ekle
+ Varolan bir alanda **alınabilir** özniteliği ayarla
+ Mevcut bir alanda **searchAnalyzer** ayarlama
+ Dizine yeni bir çözümleyici tanımı ekleme
+ Puanlama profilleri ekleme, güncelleştirme veya silme
+ CORS ayarlarını ekleme, güncelleştirme veya silme
+ Eş anlamlı haritaları ekleme, güncelleştirme veya silme

Yeni bir alan eklediğinizde, var olan dizinli belgelere yeni alan için null değer verilir. Gelecekteki bir veri yenilemesinde, dış kaynak verilerinden alınan değerler Azure Bilişsel Arama tarafından eklenen null değerleri değiştirir. Dizin içeriğini güncelleştirme hakkında daha fazla bilgi için bkz. [belge ekleme, güncelleştirme veya silme](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="partial-indexing"></a>Kısmi dizin oluşturma

Azure Bilişsel Arama ' de, alan temelinde Dizin oluşturmayı denetleyemezsiniz, belirli alanları silmeyi veya yeniden oluşturmayı seçebilirsiniz. Benzer şekilde, [ölçütlere göre belgeleri dizine alma](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents)için yerleşik bir mekanizma yoktur. Ölçüt temelli dizin oluşturma için sahip olduğunuz tüm gereksinimlere özel kod aracılığıyla ulaşılması gerekir.

Ancak, kolayca yapabilecekleriniz, bir dizindeki *belgeleri yeniler* . Birçok arama çözümü için dış kaynak verileri geçici olur ve kaynak verilerle arama dizini arasındaki eşitleme ortak bir uygulamadır. Kod içinde, [belge ekleme, güncelleştirme veya silme](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) işlemini veya dizin içeriğini güncelleştirmek veya yeni bir alana değer eklemek için [.net eşdeğerini](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) çağırın.

## <a name="partial-indexing-with-indexers"></a>Dizin oluşturucularla kısmi dizin oluşturma

[Dizin oluşturucular](search-indexer-overview.md) veri yenileme görevini basitleştirir. Dizin Oluşturucu, dış veri kaynağında yalnızca bir tablo veya görünümün dizinini oluşturabilir. Birden çok tabloyu indekslemek için en basit yaklaşım, tabloları ve projeleri dizine eklemek istediğiniz sütunları birleştiren bir görünüm oluşturmaktır. 

Dış veri kaynaklarını gezen Dizinleyicileri kullanırken, kaynak verilerde "yüksek su işareti" sütununu kontrol edin. Varsa, yalnızca yeni veya düzeltilmiş içerik içeren bir satırları seçerek artımlı değişiklik algılama için kullanabilirsiniz. [Azure Blob depolama](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)için bir `lastModified` alanı kullanılır. [Azure Tablo Depolaması](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection)'nda `timestamp`, aynı amaca hizmet eder. Benzer şekilde, hem [Azure SQL veritabanı Dizin Oluşturucusu](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) hem de [Azure Cosmos DB Indexer](search-howto-index-cosmosdb.md#indexing-changed-documents) satır güncelleştirmelerinin bayrak eklemek için alanlar vardır 

Dizin oluşturucular hakkında daha fazla bilgi için bkz. [dizin oluşturucuya genel bakış](search-indexer-overview.md) ve [Dizin Oluşturucu REST API sıfırlama](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)

## <a name="how-to-rebuild-an-index"></a>Bir dizini yeniden derleme

Dizin şemaları Flox durumunda olduğunda, sık sık, etkin geliştirme sırasında tam yeniden oluşturma planlayın. Zaten üretimde olan uygulamalar için, sorgu kapalı kalma süresini önlemek için mevcut bir dizini yan yana çalıştıran yeni bir dizin oluşturmanız önerilir.

Dizin güncelleştirmeleri için hizmet düzeyinde okuma-yazma izinleri gereklidir. 

Portalda bir dizini yeniden oluşturamazsınız. Programlı olarak, tam yeniden oluşturmak için [güncelleştirme dizini REST API](https://docs.microsoft.com/rest/api/searchservice/update-index) veya [eşdeğer .NET API 'leri](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) çağırabilirsiniz. Güncelleştirme dizini isteği, [dizin REST API oluşturma](https://docs.microsoft.com/rest/api/searchservice/create-index)ile aynıdır, ancak farklı bir içeriğe sahiptir.

Aşağıdaki iş akışı REST API 'e göre belirlenir, ancak .NET SDK 'ya eşit olarak uygulanır.

1. Bir dizin adını yeniden kullanırken, [var olan dizini bırakın](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Dizini hedefleyen tüm sorgular hemen bırakılır. Bir dizinin silinmesi geri alınamaz, alanlar koleksiyonu ve diğer yapılar için fiziksel depolamayı yok edilir. Bırakmadan önce bir dizini silmenin etkilerine ilişkin etkileri açık olduğunuzdan emin olun. 

2. Hizmet uç noktanızla, API anahtarınızla ve bir [yönetici anahtarıyla](https://docs.microsoft.com/azure/search/search-security-api-keys)bir [güncelleştirme dizini](https://docs.microsoft.com/rest/api/searchservice/update-index) isteği oluşturma. Yazma işlemleri için yönetici anahtarı gereklidir.

3. İsteğin gövdesinde, değiştirilmiş veya değiştirilmiş alan tanımlarına sahip bir dizin şeması sağlayın. İstek gövdesi, Dizin şemasını, ayrıca Puanlama profilleri, çözümleyiciler, öneri araçları ve CORS seçenekleri için yapıları içerir. Şema gereksinimleri [create INDEX](https://docs.microsoft.com/rest/api/searchservice/create-index)bölümünde belgelenmiştir.

4. Azure Bilişsel Arama üzerindeki dizinin fiziksel ifadesini yeniden oluşturmak için bir [güncelleştirme dizin](https://docs.microsoft.com/rest/api/searchservice/update-index) isteği gönderin. 

5. Dizini harici bir kaynaktaki [belgelerle yükleyin](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) .

Dizini oluştururken, her bir aranabilir alan için bir ters dizin oluşturulan dizin şemasındaki her bir alan için fiziksel depolama ayrılır. Aranabilir olmayan alanlar, filtrelerde veya ifadelerde kullanılabilir, ancak ters dizinler içermez ve tam metin veya benzer şekilde aranabilir değildir. Bir dizin yeniden derlemede, bu ters dizinler silinir ve sağladığınız dizin şemasına göre yeniden oluşturulur.

Dizini yüklediğinizde, her alanın ters çevrilmiş dizini her belgedeki benzersiz ve simgeleştirilmiş sözcüklerin tümü ile doldurulur ve buna karşılık gelen belge kimliklerine eşlenir. Örneğin, bir oteller veri kümesini dizinlerken, şehir alanı için oluşturulan ters dizin Seattle, Portland ve benzeri terimleri içerebilir. Şehir alanına Seattle veya Portland içeren belgeler, dönem içinde belge KIMLIĞINE göre listelenmiş olacaktır. Herhangi bir [ekleme, güncelleştirme veya silme](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) işleminde, hüküm ve belge kimliği listesi buna göre güncelleştirilir.

> [!NOTE]
> Katı SLA gereksinimleriniz varsa, geliştirme ve dizin oluşturma ile bir üretim dizininden tam yalıtımıyla oluşan yeni bir hizmet sağlamayı düşünebilirsiniz. Ayrı bir hizmet kendi donanımında çalışır ve kaynak çakışması olasılığını ortadan kaldırır. Geliştirme tamamlandığında, yeni dizini yerinde bırakabilir, sorguları yeni uç noktaya ve dizine yönlendirebilir ya da özgün Azure Bilişsel Arama hizmetinizde düzeltilmiş bir dizin yayımlamak için tamamlanmış kodu çalıştırırsınız. Kullanıma kullanım dışı bir dizini başka bir hizmete taşımak için şu anda hiçbir mekanizma yoktur.

## <a name="view-updates"></a>Güncelleştirmeleri görüntüle

İlk belge yüklendikten hemen sonra bir dizini sorgulamaya başlayabilirsiniz. Bir belgenin KIMLIĞINI biliyorsanız, [Arama belgesi REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) belirli belgeyi döndürür. Daha geniş bir test için, Dizin tam olarak yüklenene kadar beklemeniz gerekir ve ardından görmek istediğiniz bağlamı doğrulamak için sorguları kullanabilirsiniz.

## <a name="see-also"></a>Ayrıca bkz.

+ [Dizin Oluşturucu’ya genel bakış](search-indexer-overview.md)
+ [Büyük veri kümelerini ölçeğe göre dizine at](search-howto-large-index.md)
+ [Portalda dizin oluşturma](search-import-data-portal.md)
+ [Azure SQL veritabanı Dizin Oluşturucu](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB dizinleyici](search-howto-index-cosmosdb.md)
+ [Azure Blob Depolama dizin oluşturucu](search-howto-indexing-azure-blob-storage.md)
+ [Azure Tablo Depolama dizin oluşturucu](search-howto-indexing-azure-tables.md)
+ [Azure Bilişsel Arama güvenliği](search-security-overview.md)