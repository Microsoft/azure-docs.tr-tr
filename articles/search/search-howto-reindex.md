---
title: Bir arama dizinini yeniden derle
titleSuffix: Azure Cognitive Search
description: Bir Azure Bilişsel Arama dizinini yenilemek için yeni öğeler ekleyin, var olan öğeleri veya belgeleri güncelleştirin veya bir tam yeniden oluşturma veya kısmi dizin oluşturma içindeki eski belgeleri silin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 47e9b80bb25b7ff14695cc67682265fe338ff76f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98119110"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Azure Bilişsel Arama bir dizini yeniden oluşturma

Bu makalede bir Azure Bilişsel Arama dizininin nasıl yeniden oluşturulduğu, yeniden oluşturmanın gerekli olduğu koşullar ve devam eden sorgu isteklerinde yeniden oluşturma işlemlerinin etkisini azaltmaya yönelik öneriler açıklanmaktadır.

*Yeniden oluşturma* , tüm alan tabanlı ters dizinler de dahil olmak üzere bir dizinle ilişkili fiziksel veri yapılarını bırakmayı ve yeniden oluşturmayı gösterir. Azure Bilişsel Arama ' de tek tek alanları bırakamaz ve yeniden oluşturamazsınız. Bir dizini yeniden derlemek için, tüm alan depolamanın silinmesi, var olan veya düzeltilmiş bir dizin şemasına göre yeniden oluşturulması ve sonra dizine gönderilen veya dış kaynaklardan çekilmiş verilerle yeniden doldurulması gerekir. 

Dizin tasarımını yineleirken geliştirme sırasında dizinlerin yeniden oluşturulması yaygındır, ancak karmaşık türler ekleme veya öneri araçları 'ye alan ekleme gibi yapısal değişikliklere uyum sağlamak için de üretim düzeyinde bir dizin yeniden oluşturmanız gerekebilir.

## <a name="rebuild-versus-refresh"></a>"Yeniden derle" ve "Yenile"

Yeni, değiştirilmiş veya silinen belgelerle bir dizinin içeriklerinin yenilenmesi ile yeniden oluşturma karıştırılmamalıdır. Bir aramanın yenilenmesi, her bir arama uygulamasında neredeyse dakikalık güncelleştirmeler (örneğin, bir arama Corp'in bir çevrimiçi satış uygulamasındaki envanter değişikliklerini yansıtması için ihtiyaç duyduğunda) gerekir.

Dizinin yapısını değiştirmiyorsanız, dizini ilk olarak yüklemek için kullandığınız tekniklerin aynısını kullanarak bir dizini yenileyebilirsiniz:

* Gönderme modundaki dizin oluşturma için, değişiklikleri bir dizine göndermek üzere [Belge Ekle, Güncelleştir veya Sil](/rest/api/searchservice/addupdate-or-delete-documents) ' i çağırın.

* Dizin oluşturucular için, [Dizin Oluşturucu yürütmeyi zamanlayabilir](search-howto-schedule-indexers.md) ve Delta tanımlamak için değişiklik izleme veya zaman damgalarını kullanabilirsiniz. Güncelleştirmelerin bir Scheduler 'ın yönetebileceğinden daha hızlı yansıtılması gerekiyorsa, bunun yerine gönderim modu dizin oluşturma kullanabilirsiniz.

## <a name="rebuild-conditions"></a>Koşulları yeniden oluştur

Aşağıdaki koşullardan herhangi biri doğru olduğunda bir dizini bırakıp yeniden oluşturun. 

| Koşul | Description |
|-----------|-------------|
| Alan tanımını değiştirme | Bir alan adını, veri türünü veya belirli [Dizin özniteliklerini](/rest/api/searchservice/create-index) (aranabilir, filtrelenebilir, sıralanabilir, çok yönlü tablo) yeniden düzeltme için tam yeniden oluşturma gerekir. |
| Bir alana çözümleyici atama | [Çözümleyiciler](search-analyzers.md) bir dizinde tanımlanır ve alanlara atanır. Dizine dilediğiniz zaman yeni bir çözümleyici tanımı ekleyebilirsiniz, ancak alan oluşturulduğunda yalnızca bir çözümleyici *atayabilirsiniz* . Bu, hem **çözümleyici** hem de **ındexanalyzer** özellikleri için geçerlidir. **SearchAnalyzer** özelliği bir özel durumdur (Bu özelliği var olan bir alana atayabilirsiniz). |
| Bir dizindeki çözümleyici tanımını güncelleştirme veya silme | Tüm dizini yeniden oluşturmadığınız müddetçe, dizinde var olan bir çözümleyici yapılandırmasını (çözümleyici, belirteç Oluşturucu, belirteç filtresi veya char filtresi) silemez veya değiştiremezsiniz. |
| Öneri aracı bir alan ekleme | Bir alan zaten varsa ve onu bir [Öneri araçları](index-add-suggesters.md) yapısına eklemek istiyorsanız, dizini yeniden oluşturmanız gerekir. |
| Bir alanı silme | Bir alanın tüm izlemelerini fiziksel olarak kaldırmak için, dizini yeniden oluşturmanız gerekir. Hemen yeniden oluşturma pratik olmadığında, "silinen" alanına erişimi devre dışı bırakmak için uygulama kodunu değiştirebilir veya sonuç kümesinde hangi alanların temsil edileceğini seçmek için [$Select sorgu parametresini](search-query-odata-select.md) kullanabilirsiniz. Fiziksel olarak, söz konusu alanı atlayacağınız bir şemayı uyguladığınızda, alan tanımı ve içerikleri, sonraki yeniden oluşturmaya kadar dizinde kalır. |
| Katmanları Değiştir | Daha fazla kapasiteye ihtiyacınız varsa Azure portal yerinde yükseltme yoktur. Yeni bir hizmet oluşturulmalıdır ve yeni hizmette dizinlerin sıfırdan oluşturulması gerekir. Bu işlemin otomatikleştirilmesine yardımcı olmak için, bu [Azure bilişsel arama .NET örnek](https://github.com/Azure-Samples/azure-search-dotnet-samples)depodaki **Dizin-yedekleme-geri yükleme** örnek kodunu kullanabilirsiniz. Bu uygulama, dizininizi bir dizi JSON dosyasına yedekleyecek ve sonra belirttiğiniz bir arama hizmetinde dizin oluşturacak.|

## <a name="update-conditions"></a>Güncelleştirme koşulları

Mevcut fiziksel yapıları etkilemeden birçok başka değişiklik yapılabilir. Özellikle, aşağıdaki değişiklikler dizin yeniden *oluşturma gerektirmez.* Bu değişiklikler için, yaptığınız değişikliklerle [bir dizin tanımını güncelleştirebilirsiniz](/rest/api/searchservice/update-index) .

+ Yeni alan ekleme
+ Varolan bir alanda **alınabilir** özniteliği ayarla
+ Mevcut bir alanda **searchAnalyzer** ayarlama
+ Dizine yeni bir çözümleyici tanımı ekleme
+ Puanlama profilleri ekleme, güncelleştirme veya silme
+ CORS ayarlarını ekleme, güncelleştirme veya silme
+ Eş anlamlı haritaları ekleme, güncelleştirme veya silme

Yeni bir alan eklediğinizde, var olan dizinli belgelere yeni alan için null değer verilir. Gelecekteki bir veri yenilemesinde, dış kaynak verilerinden alınan değerler Azure Bilişsel Arama tarafından eklenen null değerleri değiştirir. Dizin içeriğini güncelleştirme hakkında daha fazla bilgi için bkz. [belge ekleme, güncelleştirme veya silme](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Bir dizini yeniden derleme

Geliştirme sırasında, Dizin şeması sıklıkla değişir. Bunu, küçük bir temsili veri kümesiyle silinebilecek, yeniden oluşturulabilen ve yeniden yüklenmiş dizinler oluşturarak planlayabilirsiniz.

Zaten üretimde olan uygulamalar için, sorgu kapalı kalma süresini önlemek için mevcut bir dizini yan yana çalıştıran yeni bir dizin oluşturmanız önerilir. Uygulama kodunuz yeni dizine yeniden yönlendirme sağlar.

Dizin oluşturma arka planda çalışmaz ve hizmet, devam eden sorgulara karşı ek Dizin oluşturmayı dengeleyebilir. Dizin oluşturma sırasında, sorguların zamanında tamamlanmasını sağlamak için portaldaki [sorgu isteklerini izleyebilirsiniz](search-monitor-queries.md) .

1. Yeniden oluşturma gerekli olup olmadığını belirleme. Yalnızca alanlar ekliyorsanız veya alanların ilgisi olmayan bir bölümünü değiştirirseniz, tanımı silmeden, yeniden oluşturmadan ve tamamen yeniden yüklemeden [güncelleştirmeniz](/rest/api/searchservice/update-index) yeterlidir.

1. Daha sonra başvurmak üzere ihtiyacınız olduğunda [bir dizin tanımı alın](/rest/api/searchservice/get-index) .

1. Yeni ve eski dizinleri yan yana çalıştırdığınız varsayılarak, [var olan dizini bırakın](/rest/api/searchservice/delete-index). 

   Dizini hedefleyen tüm sorgular hemen bırakılır. Bir dizini silmenin geri alınamaz, alanlar koleksiyonu ve diğer yapılar için fiziksel depolamayı yok etmeyi unutmayın. Uygulamadan önce etkilerini düşünmek için duraklatın. 

1. İsteğin gövdesinin değiştirilen veya değiştirilen alan tanımlarını içerdiği [düzeltilmiş bir dizin oluşturun](/rest/api/searchservice/create-index).

1. Dizini harici bir kaynaktaki [belgelerle yükleyin](/rest/api/searchservice/addupdate-or-delete-documents) .

Dizini oluştururken, her bir aranabilir alan için bir ters dizin oluşturulan dizin şemasındaki her bir alan için fiziksel depolama ayrılır. Aranabilir olmayan alanlar, filtrelerde veya ifadelerde kullanılabilir, ancak ters dizinler içermez ve tam metin veya benzer şekilde aranabilir değildir. Bir dizin yeniden derlemede, bu ters dizinler silinir ve sağladığınız dizin şemasına göre yeniden oluşturulur.

Dizini yüklediğinizde, her alanın ters çevrilmiş dizini her belgedeki benzersiz ve simgeleştirilmiş sözcüklerin tümü ile doldurulur ve buna karşılık gelen belge kimliklerine eşlenir. Örneğin, bir oteller veri kümesini dizinlerken, şehir alanı için oluşturulan ters dizin Seattle, Portland ve benzeri terimleri içerebilir. Şehir alanına Seattle veya Portland içeren belgeler, dönem içinde belge KIMLIĞINE göre listelenmiş olacaktır. Herhangi bir [ekleme, güncelleştirme veya silme](/rest/api/searchservice/addupdate-or-delete-documents) işleminde, hüküm ve belge kimliği listesi buna göre güncelleştirilir.

> [!NOTE]
> Katı SLA gereksinimleriniz varsa, geliştirme ve dizin oluşturma ile bir üretim dizininden tam yalıtımıyla oluşan yeni bir hizmet sağlamayı düşünebilirsiniz. Ayrı bir hizmet kendi donanımında çalışır ve kaynak çakışması olasılığını ortadan kaldırır. Geliştirme tamamlandığında, yeni dizini yerinde bırakabilir, sorguları yeni uç noktaya ve dizine yönlendirebilir ya da özgün Azure Bilişsel Arama hizmetinizde düzeltilmiş bir dizin yayımlamak için tamamlanmış kodu çalıştırırsınız. Kullanıma kullanım dışı bir dizini başka bir hizmete taşımak için şu anda hiçbir mekanizma yoktur.

## <a name="check-for-updates"></a>Güncelleştirmeleri denetle

İlk belge yüklendikten hemen sonra bir dizini sorgulamaya başlayabilirsiniz. Bir belgenin KIMLIĞINI biliyorsanız, [Arama belgesi REST API](/rest/api/searchservice/lookup-document) belirli belgeyi döndürür. Daha geniş bir test için, Dizin tam olarak yüklenene kadar beklemeniz gerekir ve ardından görmek istediğiniz bağlamı doğrulamak için sorguları kullanabilirsiniz.

Güncelleştirilmiş içeriği denetlemek için, [Arama Gezgini](search-explorer.md) 'Ni veya [postman](search-get-started-rest.md) veya [Visual Studio Code](search-get-started-vs-code.md) gibi bir Web Testi aracını kullanabilirsiniz.

Bir alanı eklediyseniz veya yeniden adlandırdıysanız, bu alanı döndürmek için [$Select](search-query-odata-select.md) kullanın: `search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Ayrıca bkz.

+ [Dizin Oluşturucu’ya genel bakış](search-indexer-overview.md)
+ [Büyük veri kümelerini ölçeğe göre dizine at](search-howto-large-index.md)
+ [Portalda dizin oluşturma](search-import-data-portal.md)
+ [Azure SQL veritabanı Dizin Oluşturucu](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB dizinleyici](search-howto-index-cosmosdb.md)
+ [Azure Blob depolama Dizin Oluşturucu](search-howto-indexing-azure-blob-storage.md)
+ [Azure Tablo depolama Dizin Oluşturucu](search-howto-indexing-azure-tables.md)
+ [Azure Bilişsel Arama güvenliği](search-security-overview.md)