---
title: Uygulamalarda arama gezinmesi için model filtreleri
titleSuffix: Azure Cognitive Search
description: Microsoft Azure üzerinde barındırılan bir bulut arama hizmeti olan Azure Bilişsel Arama 'deki sorgularda arama sonuçlarını azaltmak için ölçütleri Kullanıcı güvenliği kimliği, coğrafi konum veya sayısal değerlere göre filtreleyin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792888"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Azure Bilişsel Arama bir model filtresi oluşturma 

Çok yönlü gezinme, bir arama uygulamasındaki sorgu sonuçları için, uygulamanızın belge gruplarına (örneğin, Kategoriler veya markalar) arama kapsamını belirlemek için Kullanıcı arabirimi denetimleri sağladığı ve Azure Bilişsel Arama veri yapısını sağladığı bir arama uygulamasında kendi kendine yönlendirilmiş filtreleme için kullanılır. deneyimini geri yüklemek için. Bu makalede, sağlamak istediğiniz arama deneyimini yedekleyen çok yönlü bir gezinti yapısı oluşturmaya yönelik temel adımları hızlıca gözden geçirin. 

> [!div class="checklist"]
> * Filtreleme ve Faks oluşturma için alanları seçin
> * Alanda öznitelikleri ayarla
> * Dizin oluşturma ve verileri yükleme
> * Bir sorguya model filtreleri ekleme
> * Sonuçları işleme

Modeller dinamiktir ve sorgu üzerinde döndürülür. Arama yanıtları, sonuçlarda gezinmek için kullanılan model kategorileriyle birlikte getirir. Modellerle ilgili bilginiz yoksa, aşağıdaki örnek bir model Gezinti yapısının bir temsilidir.

  ![](./media/search-filters-facets/facet-nav.png)

Çok yönlü gezinmede yeni ve daha fazla ayrıntı istiyor musunuz? Bkz. [Azure bilişsel arama 'da çok yönlü gezintiyi uygulama](search-faceted-navigation.md).

## <a name="choose-fields"></a>Alanları seçin

Modeller, tek değer alanları ve koleksiyonlar üzerinden hesaplanabilir. Çok yönlü gezinmede en iyi şekilde çalışan alanlar düşük kardinalite sahiptir: arama corpminizdeki belgeleri (örneğin, bir renk, ülke/bölge veya marka adı) tekrarlayan çok az sayıda benzersiz değer. 

`facetable` özniteliğini `true`olarak ayarlayarak, dizin oluşturduğunuzda, bir alan temelinde, her zaman bir alan temelinde etkinleştirilir. Ayrıca, arama uygulamanızın son kullanıcının seçtiği modelleri temel alarak bu alanlara filtre yapabilmesi için `filterable` özniteliğini bu alanlar için `true` olarak ayarlamanız gerekir. 

REST API kullanarak bir dizin oluştururken, çok yönlü gezintide kullanılabilecek bir [alan türü](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) , varsayılan olarak `facetable` olarak işaretlenir:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Sayısal alan türleri: `Edm.Int32`, `Edm.Int64`, `Edm.Double`
+ Yukarıdaki türlerin koleksiyonları (örneğin, `Collection(Edm.String)` veya `Collection(Edm.Double)`)

Çok yönlü gezintide `Edm.GeographyPoint` veya `Collection(Edm.GeographyPoint)` alanlarını kullanamazsınız. Modeller düşük önem taşıyan alanlarda en iyi şekilde çalışır. Coğrafi koordinatların çözümlenme nedeniyle, her iki ortak ordinonun kümesi, belirli bir veri kümesinde eşit olacaktır. Bu nedenle, coğrafi koordinatlara yönelik modeller desteklenmez. Konuma göre model için bir şehir veya bölge alanı gerekir.

## <a name="set-attributes"></a>Öznitelikleri ayarla

Bir alanın nasıl kullanıldığını denetleyen dizin öznitelikleri, dizindeki ayrı alan tanımlarına eklenir. Aşağıdaki örnekte, düşük önem taşıyan alanlar, her zaman, `category` (otel, Motel, Hostel), `tags`ve `rating`' den oluşur. Bu alanlarda, tanım amaçları için aşağıdaki örnekte açıkça ayarlanmış `filterable` ve `facetable` öznitelikleri vardır. 

> [!Tip]
> Performans ve depolama iyileştirmesi için en iyi uygulama olarak, bir model olarak asla kullanılmamalıdır. Özellikle, bir KIMLIK veya ürün adı gibi benzersiz değerler için dize alanları, çok yönlü olan gezinmede yanlışlıkla (ve verimsiz) kullanılmasını engellemek için `"facetable": false` olarak ayarlanmalıdır.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Bu dizin tanımı [, REST API kullanılarak Azure bilişsel arama dizin oluşturma](https://docs.microsoft.com/azure/search/search-create-index-rest-api)işleminden kopyalanır. Bu, alan tanımlarındaki yararlanmayan farklılıkları dışında aynıdır. `filterable` ve `facetable` öznitelikleri `category`, `tags`, `parkingIncluded`, `smokingAllowed`ve `rating` alanlarında açıkça eklenir. Uygulamada, `filterable` ve `facetable`, REST API kullanılırken bu alanlarda varsayılan olarak etkinleştirilir. .NET SDK kullanırken, bu özniteliklerin açıkça etkinleştirilmesi gerekir.

## <a name="build-and-load-an-index"></a>Dizin oluşturma ve yükleme

Ara (ve belirgin) bir adım sorgu oluşturmadan önce [dizini derleyip doldurmanız](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) gerekir. Bu adımdan daha fazla bahsedin. Dizinin kullanılabilir olup olmadığını belirlemenin bir yolu, [portaldaki](https://portal.azure.com)dizinler listesini denetleyerek.

## <a name="add-facet-filters-to-a-query"></a>Bir sorguya model filtreleri ekleme

Uygulama kodu ' nda, arama ifadeleri, modeller, filtreler, Puanlama profilleri gibi geçerli bir sorgunun tüm parçalarını belirten bir sorgu oluşturun ve bir isteği formülleştirmek için kullanılan her şey vardır. Aşağıdaki örnek, konaklama, derecelendirme ve diğer değişiklik türlerini temel alan model gezintisi oluşturan bir istek oluşturur.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Tıklama olaylarına göre filtrelenmiş sonuçları döndür

Son Kullanıcı bir model değerine tıkladığında, Click olayının işleyicisi kullanıcının hedefini gerçekleştirmek için bir filtre ifadesi kullanmalıdır. `category` modeli verildiğinde, "Motel" kategorisini tıklatmak, bu türün konaklamaları seçen bir `$filter` ifadesi ile uygulanır. Kullanıcı yalnızca Motels gösterilmesi gerektiğini belirtmek için "Motel" öğesini tıkladığında, uygulamanın gönderdiği sonraki sorgu `$filter=category eq 'motel'`içerir.

Aşağıdaki kod parçacığı, bir Kullanıcı kategori modeli 'nden bir değer seçerse filtreye kategori ekler.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Kullanıcı `tags`gibi bir koleksiyon alanı için bir model değerine tıklarsa, örneğin "Pool" değeri, uygulamanız şu filtre sözdizimini kullanmalıdır: `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>İpuçları ve geçici çözümler

### <a name="initialize-a-page-with-facets-in-place"></a>Özellikleri yerinde bir sayfa başlatın

Özellikleri yerinde bir sayfa başlatmak istiyorsanız, sayfayı bir başlangıç modeli yapısına göre temel almak için sayfa başlatmanın bir parçası olarak bir sorgu gönderebilirsiniz.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Filtrelenmiş sonuçlardan zaman uyumsuz bir model gezinti yapısını koruma

Azure Bilişsel Arama 'de model gezintisi ile ilgili güçlüklerden biri yalnızca geçerli sonuçlar için mevcut olan bir modeldir. Uygulamada, bir statik model kümesinin tutulması yaygın bir yöntemdir. böylece Kullanıcı, arama içeriği aracılığıyla alternatif yolları araştırmak için adımları yeniden izleyerek geri gidebilir. 

Bu, yaygın olarak kullanılan bir kullanım durumu olsa da, model Gezinti yapısının Şu anda kullanıma hazır olarak sağladığı bir şey değildir. Statik modeller isteyen geliştiriciler genellikle iki filtrelenmiş sorgu vererek sınırlamanın etrafında çalışır: sonuçlara kapsamlı bir kapsam, diğer bir deyişle, gezinme amacıyla bir statik model listesi oluşturmak için kullanılır.

## <a name="see-also"></a>Ayrıca bkz.

+ [Azure Bilişsel Arama filtreler](search-filters.md)
+ [Dizin REST API oluştur](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Belgelerde ara REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
