---
title: Sorgu oluşturma
titleSuffix: Azure Cognitive Search
description: Bilişsel Arama, test ve kod için kullanılacak araçlar ve API 'Leri ve sorgu kararlarının Dizin tasarımıyla nasıl başlatılacağını öğrenin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 4f5cc0d5eefd5969566040e4148ca7358d348736
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951513"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Azure Bilişsel Arama sorgu oluşturma

İlk kez bir sorgu oluşturuyorsanız, bu makalede sorguları ayarlamaya yönelik yaklaşımlar ve yöntemler açıklanmaktadır. Ayrıca, bir sorgu isteği de sunar ve alan özniteliklerinin ve dil Çözümleyicileri 'nin sorgu sonuçlarını nasıl etkileyebileceğini açıklar.

## <a name="whats-a-query-request"></a>Sorgu isteği nedir?

Sorgu, tek bir arama dizininin docs koleksiyonuna yönelik salt okunurdur. Bir ' Search ' parametresi, terimleri, tırnak içine alınmış tümcecikleri ve işleçleri içeren sorgu ifadesini içerir.

Ek parametreler sorgu ve yanıta daha fazla tanım sağlar. Örneğin, ' searchFields ' kapsamları belirli alanlara sorgu yürütmeyi sorgular, ' Select ' hangi alanların sonuçlarda döndürüleceğini belirtir ve ' Count ', dizinde bulunan eşleşmelerin sayısını döndürür.

Aşağıdaki örnek, kullanılabilir parametrelerin bir alt kümesini göstererek bir sorgu isteğine ilişkin genel bir fikir verir. Sorgu kompozisyonu hakkında daha fazla bilgi için bkz. [sorgu türleri ve kompozisyon](search-query-overview.md) ve [arama belgeleri (REST)](/rest/api/searchservice/search-documents).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "NY +view",
    "queryType": "simple",
    "searchMode": "all",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

## <a name="choose-a-client"></a>İstemci seçin

Azure portal veya Postman gibi bir araca ya da API 'Leri kullanarak bir sorgu istemcisini örnekleyen koda ihtiyacınız vardır. Erken geliştirme ve kavram kanıtı testi için Azure portal veya REST API 'Leri öneririz.

### <a name="permissions"></a>İzinler

Sorgu istekleri dahil olmak üzere herhangi bir işlem, bir [yönetıcı API anahtarı](search-security-api-keys.md)altında çalışır, ancak sorgu istekleri isteğe bağlı olarak BIR [sorgu API anahtarı](search-security-api-keys.md#create-query-keys)kullanabilir. Sorgu API 'SI anahtarları kesinlikle önerilir. Hizmet başına en fazla 50 oluşturabilir ve farklı uygulamalara farklı anahtarlar atayabilirsiniz.

Azure portal, Araçlar, sihirbazlar ve nesneler erişimi, hizmette katkıda bulunan rolünde veya üzerinde üyelik gerektirir. 

### <a name="use-azure-portal-to-query-an-index"></a>Bir dizini sorgulamak için Azure portal kullanma

[Arama Gezgini (portal)](search-explorer.md) , temel arama hizmetindeki dizinlerde sorgular çalıştıran Azure Portal bir sorgu arabirimidir. Dahili olarak, portal [arama belgelerinin](/rest/api/searchservice/search-documents) isteklerini yapar, ancak otomatik tamamlama, öneriler veya belge aramasını çağıramıyor. 

Önizleme dahil olmak üzere herhangi bir dizini ve REST API sürümünü seçebilirsiniz. Sorgu dizesi, tüm sorgu parametreleri (filtre, seçme, searchFields vb.) desteğiyle basit veya tam sözdizimini kullanabilir. Portalda, bir dizini açtığınızda, alan özniteliklerine kolay erişim için yan yana sekmelerde JSON tanımının yanı sıra arama Gezgini ile çalışabilirsiniz. Sorguları test ederken hangi alanların aranabilir, sıralanabilir, filtrelenebilir ve çok yönlü tablo olduğunu denetleyin.

### <a name="use-a-rest-client"></a>REST istemcisi kullanma

Hem Postman hem de Visual Studio Code (Azure Bilişsel Arama Uzantısı ile) sorgu istemcisi olarak çalışabilir. İki aracı kullanarak, arama hizmetinize bağlanabilir ve [arama belgeleri (REST)](/rest/api/searchservice/search-documents) isteklerini gönderebilirsiniz. Çok sayıda öğretici ve örnek, dizin oluşturmayı sorgulamak için REST istemcilerini gösterir. 

Her istemci hakkında bilgi edinmek için Bu makalelerden birini başlatın (her ikisi de sorgular için yönergeler içerir):

+ [REST ve Postman kullanarak arama dizini oluşturma](search-get-started-rest.md)
+ [Visual Studio Code ve Azure Bilişsel Arama kullanmaya başlama](search-get-started-vs-code.md)

Her istek tek başına yapılır, bu nedenle her istekte uç nokta, dizin adı ve API sürümü sağlamanız gerekir. Diğer özellikler, Içerik türü ve API anahtarı istek üstbilgisine geçirilir. Daha fazla bilgi için bkz. sorgu isteklerini [Formülleyen yardım Için belgeleri arama (REST)](/rest/api/searchservice/search-documents) .

### <a name="use-an-sdk"></a>SDK kullanma

Bilişsel Arama için Azure SDK 'Ları, genel olarak kullanılabilen özellikleri uygular. Bu nedenle, bir dizini sorgulamak için SDK 'Lardan herhangi birini kullanabilirsiniz. Bunların hepsi, sorgu isteklerini formüllemek için bir dizin ile, arama belgeleriyle bir dizin yüklemeden, bir dizinle etkileşim kurma yöntemlerine sahip bir **Searchclient** sağlar.

| Azure SDK | İstemci | Örnekler |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Searchfordynamicdocumentsexörnek. Java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | Yayınlan. |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query. Kopyala ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>Bir sorgu türü seçin: basit | tümünü

Sorgunuz tam metin arama ise, arama terimleri ve tümceleri olarak geçirilen metinleri işlemek için bir sorgu ayrıştırıcısı kullanılacaktır. Azure Bilişsel Arama iki sorgu ayrıştırıcıları sunmaktadır. 

+ Basit ayrıştırıcı [basit sorgu söz dizimini](query-simple-syntax.md)anlamıştır. Bu ayrıştırıcı, ücretsiz form metin sorgularındaki hız ve verimlilik için varsayılan olarak seçilmiştir. Söz dizimi, terim ve tümcecik aramaları için ortak arama işleçlerini (ve veya, DEĞIL) ve ön ek ( `*` ) araması (Seattle ve mevsimin için "Sea *" içinde olduğu gibi) destekler. Genel bir öneri öncelikle basit ayrıştırıcı yapmayı denemenize ve uygulama gereksinimleri daha güçlü sorgular için çağrı yaparsanız tam ayrıştırıcıya geçiş yapmak.

+ İsteğe eklediğinizde etkinleştirilen [tam Lucene sorgu söz dizimi](query-Lucene-syntax.md#bkmk_syntax), `queryType=full` [Apache Lucene ayrıştırıcısına](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)dayalıdır.

Tam sözdizimi ve basit sözdizimi, her ikisi de aynı öneki ve Boole işlemlerini destekledikleri, ancak tam sözdizimi daha fazla işleç sağladığı ölçüde çakışıyor. Tam olarak, Boolean ifadelerine yönelik daha fazla işleç ve benzer arama, joker karakter arama, yakınlık araması ve normal ifadeler gibi gelişmiş sorgular için daha fazla işleç vardır.

## <a name="choose-query-methods"></a>Sorgu yöntemlerini seçin

Arama, hüküm veya deyimlerin bir arama kutusundan toplandığı ya da bir sayfadaki tıklama olaylarından oluşan Kullanıcı odaklı bir alıştırmadır. Aşağıdaki tabloda, Kullanıcı girişini, beklenen arama deneyimiyle birlikte toplayabilmeniz için gereken mekanizmalar özetlenmektedir.

| Giriş | Deneyim |
|-------|---------|
| [Arama yöntemi](/rest/api/searchservice/search-documents) | Kullanıcı, işleç içeren veya olmayan bir arama kutusuna hüküm veya ifadeler ve isteği göndermek için ara ' yı tıklatır. Arama, aynı istekteki filtrelerle birlikte kullanılabilir, ancak otomatik tamamlama veya önerilere sahip değildir. |
| [AutoComplete yöntemi](/rest/api/searchservice/autocomplete) | Bir Kullanıcı birkaç karakter ve her yeni karakter yazıldıktan sonra sorgular başlatılır. Yanıt, dizinden tamamlanan bir dizedir. Belirtilen dize geçerliyse, Kullanıcı bu sorguyu hizmete göndermek için ara ' yı tıklatır. |
| [Öneriler yöntemi](/rest/api/searchservice/suggestions) | Otomatik tamamlama işleminde olduğu gibi, Kullanıcı birkaç karakter ve artımlı sorgular oluşturulur. Yanıt, genellikle birkaç benzersiz veya açıklayıcı alana göre temsil edilen eşleşen belgelerin açılan bir listesidir. Seçimlerden herhangi biri geçerliyse Kullanıcı bir tane tıklamasına ve eşleşen belge döndürülür. |
| [Çok yönlü gezinme](/rest/api/searchservice/search-documents#query-parameters) | Sayfada, aramanın kapsamını daraltıp tıklanabilir gezinti bağlantıları veya içerik haritaları gösterilir. Çok yönlü bir gezinti yapısı, ilk sorguya göre dinamik olarak oluşturulur. Örneğin, `search=*` her olası kategoriden oluşan, çok yönlü bir gezinti ağacını doldurmak için. Bir sorgu yanıtından çok yönlü bir gezinti yapısı oluşturulur, ancak bir sonraki sorguyu ifade etmek için de bir mekanizmadır. n REST API başvuru, `facets` bir arama belgeleri işleminin sorgu parametresi olarak belgelenmiştir, ancak parametresi olmadan kullanılabilir `search` .|
| [Filter yöntemi](/rest/api/searchservice/search-documents#query-parameters) | Filtreler, sonuçları daraltmak için modellerle birlikte kullanılır. Ayrıca sayfanın arkasında bir filtre uygulayabilirsiniz, örneğin, dile özgü alanlarla sayfayı başlatmak için. REST API Başvurusu ' nda, `$filter` bir arama belgeleri işleminin sorgu parametresi olarak belgelenmiştir, ancak parametresi olmadan kullanılabilir `search` .|

## <a name="know-your-field-attributes"></a>Alan öznitelerinizi öğrenin

Daha önce [sorgu türlerini ve oluşturmayı](search-query-overview.md)gözden geçirdiyseniz, sorgu isteğindeki parametrelerin, alanların bir dizinde nasıl ilişkilendirilebilmesine bağlı olduğunu unutmayın. Örneğin, bir sorgu, filtre veya sıralama düzeninde kullanılmak üzere, bir alan *aranabilir*, *filtrelenebilir* ve *sıralanabilir* olmalıdır. Benzer şekilde, sonuçlarda yalnızca *alınabilir* olarak işaretlenen alanlar görünebilir. `search` `filter` Talebinizdeki, ve parametrelerini belirtmeye başladığınızda `orderby` , beklenmedik sonuçlara engel olmak için öznitelikleri giderek kontrol ettiğinizden emin olun.

[Oteller örnek dizininin](search-get-started-portal.md)altındaki Portal ekran görüntüsünde, yalnızca son Iki "LastRenovationDate" ve "derecelendirme" alanları `"$orderby"` tek bir yan tümcesinde kullanılabilir.

![Otel örneği için Dizin tanımı](./media/search-query-overview/hotel-sample-index-definition.png "Otel örneği için Dizin tanımı")

Alan özniteliklerinin açıklaması için bkz. [Dizin oluşturma (REST API)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Belirteçlerinizi öğrenin

Dizin oluşturma sırasında, arama motoru dizelerde metin analizi gerçekleştirmek için bir çözümleyici kullanır ve sorgu sırasında eşleşme potansiyelini en üst düzeye çıkarır. En azından, dizeler daha düşük düzeylerdir, ancak aynı zamanda bir yandan da daha fazla hareket edebilir ve Word kaldırma işlemini durdurabilir. Daha büyük dizeler veya bileşik sözcükler genellikle boşluk, kısa çizgi veya tire ile ayrılır ve ayrı belirteçler olarak dizinlenir. 

Burada ele almanız gereken nokta, dizininizin neleri içerdiğini düşündüğünüzden farklı olabilir. Sorgular beklenen sonuçları döndürmezse, çözümleyici tarafından oluşturulan belirteçleri [Çözümle metni (REST API)](/rest/api/searchservice/test-analyzer)aracılığıyla inceleyebilirsiniz. Simgeleştirme ve sorgulara etkisi hakkında daha fazla bilgi için, bkz. [kısmi terim arama ve özel karakterlerle desenler](search-query-partial-matching.md).

## <a name="next-steps"></a>Sonraki adımlar

Sorgu isteklerinin nasıl çalıştığını daha iyi kavradığınıza göre, uygulamalı deneyim için aşağıdaki hızlı başlangıçlara ihtiyacınız vardır.

+ [Arama Gezgini](search-explorer.md)
+ [REST 'te sorgulama](search-get-started-rest.md)
+ [.NET 'te sorgulama](search-get-started-dotnet.md)
+ [Python 'da sorgulama](search-get-started-python.md)
+ [JavaScript 'te sorgulama](search-get-started-javascript.md)