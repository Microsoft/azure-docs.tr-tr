---
title: Çok yönlü bir gezinti kategorisi hiyerarşisi ekleyin
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama ile tümleştirilen arama uygulamalarına kendi kendine yönlendirilmiş filtreleme için çok yönlü gezinme ekleyin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 927f57ae3ca90f5701791022b68c4bf0d91d4e7d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112089"
---
# <a name="how-to-implement-faceted-navigation-in-azure-cognitive-search"></a>Azure Bilişsel Arama çok yönlü gezintiyi uygulama

Çok yönlü gezinme, arama uygulamalarında kendine yönelik ayrıntıya gidilme gezinmesi sağlayan bir filtreleme mekanizmasıdır. ' Çok yönlü gezinme ' terimi tanıdık olmayabilir, ancak büyük olasılıkla daha önce kullandınız. Aşağıdaki örnekte gösterildiği gibi, çok yönlü gezinme sonuçları filtrelemek için kullanılan kategorilerden herhangi bir şey değildir.

 ![Azure Bilişsel Arama Iş portalı tanıtımı](media/search-faceted-navigation/azure-search-faceting-example.png "Azure Bilişsel Arama Iş portalı tanıtımı")

Çok yönlü gezinme, arama için alternatif bir giriş noktasıdır. El ile karmaşık arama ifadeleri yazmak için kullanışlı bir alternatif sunar. Modeller, aradığınızı bulmanıza yardımcı olabilir, ancak sıfır sonuçları elde etmeyin. Geliştirici olarak, modeller arama dizininize gitmek için en faydalı arama ölçütlerini sergilemenizi sağlar. Çevrimiçi perakende uygulamalarında, çok yönlü gezinme genellikle markalar, departmanlar (çocukların ayakları), boyut, Fiyat, popülerlik ve derecelendirmelere göre oluşturulmuştur. 

Çok yönlü gezintiyi uygulama, arama teknolojileri arasında farklılık gösterir. Azure Bilişsel Arama 'de, çok yönlü gezinme, daha önce şemanıza sahip olduğunuz alanları kullanarak sorgu zamanında oluşturulur.

-   Uygulamanızın derlemedeki sorgularda, bu belge sonuç kümesi için kullanılabilir model filtresi değerlerini almak üzere bir sorgunun *model sorgu parametrelerini* gönderebilmesi gerekir.

-   Belge sonuç kümesini gerçekten kırpmak için, uygulamanın Ayrıca bir `$filter` ifadesi uygulaması gerekir.

Uygulama geliştirmede, sorgu oluşturan kod yazmak çalışmanın toplu işini oluşturur. Çok yönlü gezinmede bekleeceğiniz uygulama davranışlarından birçoğu, Aralık tanımlamaya ve model sonuçları için sayıları almaya yönelik yerleşik destek de dahil olmak üzere hizmet tarafından sağlanır. Hizmet Ayrıca, farkında olmayan gezinti yapıtlarından kaçınmanıza yardımcı olan, senerişilebilir varsayılanlar içerir. 

## <a name="sample-code-and-demo"></a>Örnek kod ve tanıtım
Bu makale örnek olarak bir iş arama portalı kullanır. Örnek, bir ASP.NET MVC uygulaması olarak uygulanır.

-   [Azure bilişsel arama Iş portalı tanıtımında](https://azjobsdemo.azurewebsites.net/)çalışan tanıtımı çevrimiçi olarak görün ve test edin.

-   [GitHub 'Daki Azure-Samples deposundan](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)kodu indirin.

## <a name="get-started"></a>Başlarken
Arama geliştirmeye yeni başladıysanız, çok yönlü gezinme yapmanın en iyi yolu, kendi kendine yönlendirilmiş arama olasılıklarını gösterir. Bu, arama sonuçlarını nokta ve tıklama eylemleri aracılığıyla hızlıca daraltmak için kullanılan, önceden tanımlanmış filtreleri temel alan detaya gitme arama deneyiminin bir türüdür. 

### <a name="interaction-model"></a>Etkileşim modeli

Çok yönlü gezinme için arama deneyimi yinelemelidir, bu nedenle bunu kullanıcı eylemlerine yanıt olarak katlanan bir sorgu dizisi olarak öğrenerek başlayalım.

Başlangıç noktası, genellikle Periphery üzerine yerleştirilmiş olan, çok yönlü gezinme sağlayan bir uygulama sayfasıdır. Çok yönlü gezinme, genellikle her bir değer için onay kutuları veya tıklatılabilir metinler içeren bir ağaç yapısıdır. 

1. Azure Bilişsel Arama gönderilen bir sorgu, bir veya daha fazla model sorgu parametresi aracılığıyla çok yönlü gezinti yapısını belirtir. Örneğin, sorgu `facet=Rating`içerebilir; belki de sunuyu daha da belirginleştirmek için `:values` veya `:sort` seçeneği olabilir.
2. Sunu katmanı, istekte belirtilen modelleri kullanarak çok yönlü gezinme sağlayan bir arama sayfası oluşturur.
3. Derecelendirme içeren çok yönlü bir gezinti yapısı verildiğinde, yalnızca 4 veya üzeri derecelendirmesine sahip ürünlerin gösterilmesi gerektiğini göstermek için "4" tıklayabiliyorsunuz. 
4. Yanıt olarak, uygulama `$filter=Rating ge 4` içeren bir sorgu gönderir 
5. Sunu katmanı, yalnızca yeni ölçütlere uyan öğeleri (Bu örnekte 4 ve üzeri) karşılayan öğeleri içeren, daha düşük bir sonuç kümesi göstererek sayfayı güncelleştirir.

Bir model bir sorgu parametresidir, ancak sorgu girişi ile karıştırmayın. Bir sorguda seçim ölçütü olarak hiçbir şekilde kullanılmaz. Bunun yerine, model sorgu parametrelerini yanıta geri gelen gezinti yapısına giriş olarak düşünün. Sağladığınız her model sorgu parametresi için, Azure Bilişsel Arama her bir model değerinin kısmi sonuçlarında kaç belgenin olduğunu değerlendirir.

4\. adımdaki `$filter` dikkat edin. Filtre, çok yönlü bir gezintinin önemli bir yönüdür. Modeller ve filtreler API 'ye bağımsız olsa da, istediğiniz deneyimi sağlamak için her ikisi de gereklidir. 

### <a name="app-design-pattern"></a>Uygulama tasarım kalıbı

Uygulama kodunda, model, çok yönlü gezinti yapısını, model sonuçları ve bir $filter ifadesiyle birlikte döndürmek için model sorgu parametrelerini kullanmaktır.  Filtre ifadesi, model değerindeki Click olayını işler. Sunu katmanına döndürülen arama sonuçlarının gerçek bölünmesi arkasındaki kod olarak `$filter` ifadesini düşünün. Renkler modeli verildiğinde, kırmızı renge tıklanması yalnızca kırmızı rengi olan öğeleri seçen `$filter` ifadesiyle uygulanır. 

### <a name="query-basics"></a>Sorgu temelleri

Azure Bilişsel Arama, bir veya daha fazla sorgu parametresi aracılığıyla bir istek belirtilir (her birinin açıklaması için bkz. [arama belgeleri](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) ). Sorgu parametrelerinden hiçbiri gerekli değildir, ancak sorgunun geçerli olabilmesi için en az bir tane olmalıdır.

Önemli olmayan isabetlerin filtreleyebilme özelliği olarak anlaşıldığı duyarlık, bu ifadelerden biri veya her ikisi üzerinden elde edilir:

-   **ara =**  
    Bu parametrenin değeri, arama ifadesini oluşturur. Tek bir metin parçası veya birden çok terim ve işleç içeren bir karmaşık arama ifadesi olabilir. Sunucusunda, tam metin araması için bir arama ifadesi kullanılır, bu arada eşleşen terimler için dizinde aranabilir alanları sorgular ve sonuçları sıra sırasına geri döndürür. `search` null olarak ayarlarsanız, sorgu yürütme tüm dizinin (yani, `search=*`) üzerinde olur. Bu durumda, `$filter` veya Puanlama profili gibi sorgunun diğer öğeleri, `($filter`) ve hangi sırada (`scoringProfile` ya da `$orderby`) hangi belgelerin döndürüleceğini etkileyen birincil faktörlerdir.

-   **$filter =**  
    Filtre, belirli belge özniteliklerinin değerlerine göre arama sonuçlarının boyutunu sınırlamak için güçlü bir mekanizmadır. Önce bir `$filter` değerlendirilir ve her bir değer için kullanılabilir değerleri ve karşılık gelen sayıları üreten bir mantık mantığı gelir

Karmaşık arama ifadeleri sorgunun performansını düşürür. Mümkün olduğunda duyarlık arttırmak ve sorgu performansını artırmak için iyi oluşturulmuş filtre ifadeleri kullanın.

Bir filtrenin daha fazla duyarlık eklemesi hakkında daha iyi anlamak için, bir karmaşık arama ifadesini filtre ifadesi içeren bir ile karşılaştırın:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Her iki sorgu de geçerlidir, ancak Seattle 'da Park ile Motels olmayan bir for arıyorsanız ikincisi üstün bir düzeydir.
-   İlk sorgu ad, açıklama ve aranabilir verileri içeren diğer alanlar gibi dize alanlarında bahsedilmekte olan veya söz konusu belirli kelimeleri kullanır.
-   İkinci sorgu, yapılandırılmış veriler üzerinde kesin eşleşmeler arar ve çok daha doğru olabilir.

Çok yönlü gezinme içeren uygulamalarda, çok yönlü bir gezinti yapısı üzerinde her bir kullanıcı eyleminin, arama sonuçlarının bir daraltma ile birlikte geldiğinden emin olun. Sonuçları daraltmak için bir filtre ifadesi kullanın.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Çok yönlü bir gezinti uygulaması oluşturun
Azure Bilişsel Arama ile, arama isteğini oluşturan uygulama kodunuzda çok yönlü bir gezinti uygulabiliyorsunuz. Çok yönlü gezinme, şemanızda daha önce tanımladığınız öğeleri kullanır.

Arama dizininizdeki ön tanımlı `Facetable [true|false]` Dizin özniteliğidir ve kullanımı çok yönlü bir gezinti yapısında kullanımını etkinleştirmek veya devre dışı bırakmak için seçili alanlar üzerinde ayarlanır. `"Facetable" = true`olmadan, model gezintide bir alan kullanılamaz.

Kodunuzda sunum katmanı Kullanıcı deneyimini sağlar. Etiket, değerler, onay kutuları ve sayı gibi çok yönlü gezintinin bileşen parçalarını listelemelidir. Azure Bilişsel Arama REST API platform belirsiz olduğundan istediğiniz dili ve platformu kullanın. Önemli şey, her ek model seçildiği için, güncelleştirilmiş Kullanıcı arabirimi durumu ile artımlı yenilemeyi destekleyen kullanıcı arabirimi öğelerini dahil etmek için kullanılır. 

Sorgu zamanında, uygulama kodunuz, alanı tarafından modeli sağlayan bir istek parametresi olan `facet=[string]`içeren bir istek oluşturur. Bir sorgu, her biri bir ve işareti (&) karakteriyle ayrılan `&facet=color&facet=category&facet=rating`gibi birden çok model içerebilir.

Uygulama kodu Ayrıca, çok yönlü gezinmede tıklama olaylarını işlemek için bir `$filter` ifadesi de oluşturmanız gerekir. `$filter`, model değerini filtre ölçütü olarak kullanarak arama sonuçlarını azaltır.

Azure Bilişsel Arama, girdiğiniz bir veya daha fazla terim temelinde, çok yönlü gezinti yapısına yönelik güncelleştirmelerle birlikte arama sonuçlarını döndürür. Azure Bilişsel Arama 'de, çok yönlü gezinme, model değerleri olan tek düzeyli bir yapım ve her biri için kaç sonuç bulunduğunu sayar.

Aşağıdaki bölümlerde, her bir parçayı nasıl derlemenize daha yakından bakacağız.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Dizini oluşturma
Bu dizin özniteliği aracılığıyla, dizinde alan temelinde, dizin içinde bir alan temelinde etkin olur: `"Facetable": true`.  
Büyük olasılıkla çok yönlü gezintide kullanılabilecek tüm alan türleri varsayılan olarak `Facetable`. Bu tür alan türleri `Edm.String`, `Edm.DateTimeOffset`ve tüm sayısal alan türlerini içerir (temel olarak, tüm alan türleri, çok yönlü bir gezinmede kullanılamayan `Edm.GeographyPoint`dışında çok yönlü tablolardır). 

Bir dizin oluştururken, çok yönlü gezinme için en iyi uygulama bir model olarak asla kullanılmamalıdır.  Özellikle, bir KIMLIK veya ürün adı gibi tek değerler için dize alanları, yanlışlıkla (ve verimsiz) çok yönlü gezintide kullanımı engellemek için `"Facetable": false` olarak ayarlanmalıdır. İhtiyacınız olmayan yere her ne kadar iyi hale getirmeniz, Dizin boyutunun küçük kalmasını sağlar ve genellikle performansı geliştirir.

Aşağıda, Iş portalı Demo örnek uygulaması şemasının bir parçası verilmiştir ve bu boyut, boyutu azaltmak için bazı özniteliklerin kırpılmakta:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { "name": "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { "name": "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { "name": "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Örnek şemada gördüğünüz gibi, KIMLIK değerleri gibi model olarak kullanılmaması gereken dize alanları için `Facetable` kapalıdır. İhtiyacınız olmayan yere her ne kadar iyi hale getirmeniz, Dizin boyutunun küçük kalmasını sağlar ve genellikle performansı geliştirir.

> [!TIP]
> En iyi uygulama olarak, her alan için dizin özniteliklerinin tam kümesini ekleyin. `Facetable`, neredeyse tüm alanlar için varsayılan olarak açık olsa da, her bir özniteliği özellikle ayarlamak her bir şema kararının etkilerini düşünmenize yardımcı olabilir. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Verileri denetleyin
Verilerinizin kalitesi, çok yönlü gezinti yapısının, onu bekledikleri gibi çalışıp çalışmadığını doğrudan etkiler. Ayrıca, sonuç kümesini azaltmak için filtre oluşturma kolaylığını da etkiler.

Marka veya fiyata göre isterseniz, her belge, geçerli, tutarlı ve bir filtre seçeneği olarak üretken olan *BrandName* ve *ProductPrice* için değerler içermelidir.

Aşağıda, temizleme için bazı anımsatıcılar verilmiştir:

* Kendisine göre istediğiniz her alan için kendi kendine yönlendirilmiş aramada filtre olarak uygun olan değerleri içerip içermediğini kendinize sorun. Rekabet seçenekleri arasında açık bir seçim sunmak için değerler kısa, açıklayıcı ve yeterince ayırt edilmelidir.
* Hatalı yazımları veya neredeyse eşleşen değerler. Renk üzerinde bir değer belirtirseniz ve alan değerleri turuncu ve süslü (yanlış yazım) içeriyorsa, renk alanı temel alan bir model her ikisini de seçer.
* Büyük küçük harf metin Ayrıca, turuncu ve turuncu ile iki farklı değer olarak görünmüş şekilde, çok yönlü gezinmede de wreak düzensizliğe olabilir. 
* Aynı değerin tek ve çoğul sürümleri, her biri için ayrı bir modelin oluşmasına neden olabilir.

Imagine de, verileri hazırlarken, etkin çok yönlü gezintinin önemli bir yönüdür.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Kullanıcı Arabirimini oluşturma
Sunu katmanından geri döndüğünüzde, aksi takdirde kaçırılmış olan gereksinimleri ortaya çıkarabilir ve arama deneyimine yönelik hangi yeteneklerin gerekli olduğunu anlayabilirsiniz.

Çok yönlü gezinme bakımından Web veya uygulama sayfanız, çok yönlü gezinme yapısını görüntüler, sayfada Kullanıcı girişini algılar ve değiştirilen öğeleri ekler. 

Web uygulamaları için, AJAX genellikle sunu katmanında kullanılır, çünkü artımlı değişiklikleri yenileyebilirler. Ayrıca, HTTP üzerinden Azure Bilişsel Arama hizmetine bağlanabilecek ASP.NET MVC veya başka bir görselleştirme platformunu da kullanabilirsiniz. Bu makale boyunca başvurulan örnek uygulama-- **Azure bilişsel arama Iş portalı tanıtımı** – BIR ASP.NET MVC uygulaması olarak gerçekleşir.

Örnekte, çok yönlü gezinme, arama sonuçları sayfasında yerleşiktir. Örnek uygulamanın `index.cshtml` dosyasından alınan aşağıdaki örnek, arama sonuçları sayfasında çok yönlü gezintiyi görüntülemek için statik HTML yapısını gösterir. Model listesi, bir arama terimi gönderdiğinizde veya bir modeli seçtiğinizde veya temizlediğinizde dinamik olarak oluşturulur veya yeniden oluşturulur.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

`index.cshtml` sayfasından aşağıdaki kod parçacığı, ilk modeli, Iş başlığını göstermek için HTML 'i dinamik olarak oluşturur. Benzer işlevler, diğer modeller için HTML 'i dinamik olarak oluşturur. Her modelin bir etiketi ve bir sayı vardır ve bu da bu model sonucu için bulunan öğelerin sayısını görüntüler.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Arama sonuçları sayfasını tasarlarken, kısıtlamaları temizlemek için bir mekanizma eklemeyi unutmayın. Onay kutuları eklerseniz, filtrelerin nasıl temizleyeceğini kolayca görebilirsiniz. Diğer düzenler için bir içerik haritası deseninin veya başka bir yaratıcı yaklaşımın olması gerekebilir. Örneğin, Iş arama portalı örnek uygulamasında, modeli temizlemek için seçili bir modelin ardından `[X]` tıklayabilirsiniz.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Sorguyu oluşturma
Sorgu oluşturmak için yazdığınız kod, arama ifadeleri, modeller, filtreler, Puanlama profilleri gibi geçerli bir sorgunun tüm parçalarını ve bir isteği formülleştirmek için kullanılan her şeyi belirtmelidir. Bu bölümde, modellerin bir sorguya hangi noktada uyduğunu ve daha düşük bir sonuç kümesi sunma ile filtrelerin nasıl kullanıldığını araştırıyoruz.

Bu örnek uygulamada modellerin integral olduğuna dikkat edin. Iş portalı tanıtımında arama deneyimi, çok yönlü gezinme ve filtreler etrafında tasarlanmıştır. Sayfada çok yönlü gezintinin belirgin yerleşimi, önemli olduğunu gösterir. 

Bir örnek genellikle başlamak için iyi bir yerdir. `JobsSearch.cs` dosyasından alınan aşağıdaki örnek, Iş başlığına, konuma, nakil türüne ve en düşük ücret temelinde model gezintisi oluşturan bir istek oluşturur. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Bir model sorgu parametresi bir alana ayarlanır ve veri türüne bağlı olarak, `count:<integer>`, `sort:<>`, `interval:<integer>`ve `values:<list>`içeren virgülle ayrılmış liste ile daha fazla parametreli olabilir. Aralıklar ayarlanırken sayısal veriler için bir değerler listesi desteklenir. Kullanım ayrıntıları için bkz. [arama belgeleri (Azure BILIŞSEL arama API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) .

Modellerle birlikte, uygulamanız tarafından formül oluşturan istek, bir model değer seçimine dayalı olarak aday belgeler kümesini daraltmak için de filtreler derlemelidir. Bir Bisiklet Mağazası için, çok yönlü gezinme *hangi renkler, üreticiler ve bisiklet türlerinin kullanılabildiği*gibi sorulara ipuçları sağlar. Filtreleme, *Bu fiyat aralığında tam Bisikletler kırmızı, Sıradağlar bisikletleri*gibi sorulara yanıt veriyor mu? Yalnızca kırmızı ürünlerin gösterilmesi gerektiğini belirtmek için "kırmızı" tıkladığınızda, uygulamanın gönderdiği sonraki sorgu `$filter=Color eq ‘Red’`içerir.

`JobsSearch.cs` sayfasından aşağıdaki kod parçacığı, Iş başlığı modeli 'nden bir değer seçerseniz, seçili Iş başlığını filtreye ekler.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>İpuçları ve en iyi yöntemler

### <a name="indexing-tips"></a>Dizin oluşturma ipuçları
**Arama kutusu kullanmıyorsanız Dizin verimliliğini iyileştirme**

Uygulamanız özel olarak çok yönlü gezinme kullanıyorsa (yani arama kutusu yoksa), daha küçük bir dizin oluşturmak için `facetable=true` alanı `searchable=false`olarak işaretleyebilirsiniz. Ayrıca, dizin oluşturma işlemi yalnızca tüm model değerlerinde gerçekleşir, çok sözcüklü bir değerin bileşen bölümlerinin hiçbir kelime kesmesi veya dizinlemesi yoktur.

**Hangi alanların model olarak kullanılabileceğini belirtin**

Dizin şemasının, bir model olarak kullanılabilecek alanları belirlediğini unutmayın. Bir alanın çok yönlü olduğunu varsayarsak, sorgu hangi alanların model olarak olduğunu belirtir. Seçtiğiniz alan, etiketin altında görünen değerleri sağlar. 

Her etiket altında görünen değerler dizinden alınır. Örneğin, model alanı *renk*ise, ek filtreleme için kullanılabilen değerler bu alanın kırmızı, siyah ve benzeri değerleri olur.

Yalnızca sayısal ve tarih saat değerleri için model alanında (örneğin, `facet=Rating,values:1|2|3|4|5`) değerleri açık bir şekilde ayarlayabilirsiniz. Bu alan türleri için değer listesine izin verilir. model sonuçlarının bitişik aralıklar halinde ayrılmasını basitleştirir (sayısal değerlere veya zaman dönemlerine göre aralıklar). 

**Varsayılan olarak, çok yönlü bir gezinmede yalnızca bir düzeyinize sahip olabilirsiniz** 

Belirtildiği gibi, bir hiyerarşide iç içe geçme modellerini doğrudan desteklemez. Varsayılan olarak, Azure Bilişsel Arama 'da çok yönlü gezinme yalnızca tek bir filtre düzeyini destekler. Ancak geçici çözümler mevcuttur. Hiyerarşi başına bir giriş noktasıyla `Collection(Edm.String)` hiyerarşik bir model yapısını kodlayabilirsiniz. Bu geçici çözümün uygulanması, bu makalenin kapsamı dışındadır. 

### <a name="querying-tips"></a>İpuçları sorgulanıyor
**Alanları doğrula**

, Güvenilir olmayan kullanıcı girişine göre model listesini dinamik olarak oluşturursanız, çok yönlü alanların adlarının geçerli olduğunu doğrulayın. Ya da, .NET 'teki `Uri.EscapeDataString()` veya tercih ettiğiniz platformunuzun eşdeğerini kullanarak URL 'Ler oluştururken adları kaçış.

### <a name="filtering-tips"></a>Filtreleme ipuçları
**Filtre ile arama hassasiyetini artır**

Filtreleri kullanın. Yalnızca arama ifadelerini tek başına kullanıyorsanız, sözcük kökü ayırmayı kapsayan bir belgenin, alanlarının hiçbirinde kesin model değeri olmayan döndürülmesine neden olabilir.

**Filtrelerle arama performansını artırma**

Arama için aday belgeler kümesini daraltmak ve bunları derecelendirmeden hariç tutmak için filtreler. Büyük bir belge kümesine sahipseniz, seçmeli bir model detaya gitme genellikle daha iyi performans sağlar.
  
**Yalnızca çok yönlü alanları filtrele**

Çok yönlü detaya gitme bölümünde, genellikle, tüm aranabilir alanlarda değil, model değeri yalnızca belirli (çok yönlü) bir alanda olan belgeleri dahil etmek istersiniz. Filtre eklemek, hizmeti yalnızca, eşleşen bir değer için çok yönlü alanda arama yapmak üzere yönlendirerek hedef alanı zorlar.

**Model sonuçlarını daha fazla filtreye göre Kırp**

Model sonuçları, bir model terimiyle eşleşen arama sonuçlarında bulunan belgelerdir. Aşağıdaki örnekte, *bulut bilgi işlem*için arama sonuçlarında, 254 öğe de bir içerik türü olarak *iç belirtime* sahiptir. Öğelerin birbirini karşılıklı olarak dışlamalı olması gerekmez. Bir öğe her iki filtrenin ölçütlerine uyuyorsa, her birinde sayılır. Bu çoğaltma, genellikle belge etiketlemesini uygulamak için kullanılan `Collection(Edm.String)` alanları üzerinde bir değer oluşturduğunuzda mümkündür.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Genel olarak, model sonuçlarının sürekli olarak çok büyük olduğunu fark ederseniz, kullanıcılara aramayı daraltmak için daha fazla seçenek sağlamak üzere daha fazla filtre eklemenizi öneririz.

### <a name="tips-about-result-count"></a>Sonuç sayısı hakkında ipuçları

**Model gezinmede öğelerin sayısını sınırlayın**

Gezinti ağacındaki her bir çok yönlü alan için varsayılan sınır olan 10 değer vardır. Bu varsayılan, değer listesini yönetilebilir bir boyuta tutacağından, gezinme yapıları için anlamlı hale gelir. Sayılacak bir değer atayarak varsayılanı geçersiz kılabilirsiniz.

* `&facet=city,count:5`, en üstteki derecelendirilen sonuçlarda bulunan ilk beş şehrin bir model sonucu olarak döndürülmeyeceğini belirtir. "Havaalanı" ve 32 eşleşme arama terimi ile örnek bir sorgu düşünün. Sorgu `&facet=city,count:5`belirtiyorsa, yalnızca arama sonuçlarında en çok belgeyi içeren ilk beş benzersiz şehir, model sonuçlarına dahil edilir.

Model sonuçları ve arama sonuçları arasındaki ayrımı fark edin. Arama sonuçları sorguyla eşleşen tüm belgelerdir. Model sonuçları her bir model değeri için eşleşmeler olur. Örnekte, arama sonuçları, model sınıflandırma listesinde olmayan şehir adlarını içerir (örneğimizde 5). Çok yönlü gezinmede filtrelenmiş sonuçlar, modelleri temizlediğinizde veya City 'in yanı sıra diğer modelleri seçerken görünür hale gelir. 

> [!NOTE]
> Birden çok tür kafa karıştırıcı olduğunda `count` ele alınabilir. Aşağıdaki tabloda, terimin Azure Bilişsel Arama API 'SI, örnek kod ve belgelerde nasıl kullanıldığı hakkında kısa bir Özet sunulmaktadır. 

* `@colorFacet.count`<br/>
  Sunum kodunda, model sonuç sayısını görüntülemek için kullanılan model üzerinde bir count parametresi görmeniz gerekir. Model sonuçlarında, sayı, model terimi veya aralığında eşleşen belge sayısını gösterir.
* `&facet=City,count:12`<br/>
  Bir model sorgusunda, sayı değerini bir değere ayarlayabilirsiniz.  Varsayılan değer 10 ' dur, ancak daha yüksek veya daha düşük bir değer belirleyebilirsiniz. `count:12` ayarlamak, model sonuçlarında ilk 12 eşleşme belge sayısına göre alır.
* "`@odata.count`"<br/>
  Sorgu yanıtında, bu değer arama sonuçlarında eşleşen öğe sayısını gösterir. Ortalama olarak, arama terimiyle eşleşen öğelerin varlığı nedeniyle tüm model sonuçlarının toplamı, ancak hiçbir model değeri eşleştirilmez.

**Model sonuçlarında sayıları al**

Çok yönlü bir sorguya filtre eklediğinizde, model ekstresini (örneğin, `facet=Rating&$filter=Rating ge 4`) sürdürmek isteyebilirsiniz. Teknik olarak, model = derecelendirme gerekmez, ancak bu derecelendirme 4 ve üzeri için model değerleri sayısını döndürür. Örneğin, "4" e tıklarsanız ve sorgu "4" daha büyük veya buna eşit bir filtre içeriyorsa, 4 ve üzeri her bir derecelendirme için sayımlar döndürülür.  

**Doğru model sayımlarını aldığınızdan emin olun**

Belirli koşullarda, model sayılarının sonuç kümeleriyle eşleşip eşleşmediği fark edebilirsiniz (bkz. [Azure bilişsel arama (Forum gönderisi)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Parçalı mimari nedeniyle model sayısı yanlış olabilir. Her arama dizininin birden çok parçaları vardır ve her parça, ilk N modellerini belge sayısına göre rapor ederek tek bir sonuçla birleştirilir. Bazı parçalar daha fazla eşleşen değere sahip olsa da, diğerleri daha az olduğunda bazı model değerlerinin eksik veya eksik olduğunu fark edebilirsiniz.

Bu davranış herhangi bir zamanda değişebilir, ancak bugün bu davranışla karşılaşırsanız, her bir parçadan tam raporlamayı zorlamak için sayıyı\<> büyük bir sayıya flaşarak geçici bir çözüm bulabilirsiniz. Count değeri, alanındaki benzersiz değer sayısından büyükse veya buna eşitse, doğru sonuçlar garanti edilir. Ancak, belge sayıları yüksek olduğunda bir performans cezası vardır, bu nedenle bu bozacağından seçeneğini kullanın.

### <a name="user-interface-tips"></a>Kullanıcı arabirimi ipuçları
**Model gezintisinde her bir alan için etiketler ekleme**

Etiketler genellikle HTML veya formda tanımlanır (örnek uygulamada`index.cshtml`). Azure Bilişsel Arama model gezinti etiketleri veya diğer meta veriler için bir API yok.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Aralığa göre filtrele
Değer aralıkları üzerinde her zaman, ortak bir arama uygulaması gereksinimidir. Aralıklar sayısal veriler ve tarih saat değerleri için desteklenir. Arama belgelerindeki her yaklaşım hakkında daha fazla bilgi edinebilirsiniz [(Azure BILIŞSEL arama API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Azure Bilişsel Arama, bir Aralık hesaplama için iki yaklaşım sunarak Aralık oluşturmayı basitleştirir. Her iki yaklaşım için de Azure Bilişsel Arama sağladığınız girişler verilen uygun aralıkları oluşturur. Örneğin, 10 | 20 | 30 Aralık değerlerini belirtirseniz, otomatik olarak 0-10, 10-20, 20-30 aralıklarını oluşturur. Uygulamanız, isteğe bağlı olarak boş olan aralıkları kaldırabilir. 

**Yaklaşım 1: Aralık parametresini kullanma**  
Fiyat modellerini $10 artışlarla ayarlamak için şunları belirtmeniz gerekir: `&facet=price,interval:10`

**Yaklaşım 2: bir değerler listesi kullanma**  
Sayısal veriler için bir values listesi kullanabilirsiniz.  Aşağıdaki şekilde işlenen `listPrice` alanı için model aralığını göz önünde bulundurun:

  ![Örnek değerler listesi](media/search-faceted-navigation/Facet-5-Prices.PNG "Örnek değerler listesi")

Önceki ekran görüntüsündeki gibi bir model aralığı belirtmek için bir değerler listesi kullanın:

    facet=listPrice,values:10|25|100|500|1000|2500

Her Aralık, başlangıç noktası olarak 0, listeden bir bitiş noktası olarak bir değer ve daha sonra ayrı aralıklar oluşturmak için önceki aralığın kırpılandır. Azure Bilişsel Arama, bu şeyleri çok yönlü gezintinin bir parçası olarak yapar. Her aralığı yapılandırmak için kod yazmanız gerekmez.

### <a name="build-a-filter-for-a-range"></a>Aralık için filtre oluşturma
Belgelerinizi seçtiğiniz bir aralığa göre filtrelemek için, `"ge"` ve `"lt"` filtre işleçlerini aralığın uç noktalarını tanımlayan iki bölümlü bir ifadede kullanabilirsiniz. Örneğin, bir `listPrice` alanı için 10-25 aralığını seçerseniz, filtre `$filter=listPrice ge 10 and listPrice lt 25`olur. Örnek kodda, filtre ifadesi bitiş noktalarını ayarlamak için **pricefrom** ve **priceto** parametrelerini kullanır. 

  ![Değer aralığı sorgusu](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Değer aralığı sorgusu")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Mesafeden göre filtrele
Geçerli konumunuza yakınlık temelinde bir mağaza, Restoran veya hedef seçmenize yardımcı olan filtreleri görmek yaygındır. Bu tür bir filtre çok yönlü gezinme gibi görünebilir, ancak yalnızca bir filtredir. Özellikle söz konusu tasarım sorunu için uygulama önerisi arayan sizin için burada bahsettik.

Azure Bilişsel Arama, **coğrafi. uzaklık** ve **coğrafi. kesişme**üzerinde iki Jeo-uzamsal işlev vardır.

* **Coğrafi. Distance** işlevi iki noktaya arasındaki mesafeyi kilometre cinsinden döndürür. Bir nokta bir alandır ve diğeri, filtrenin bir parçası olarak geçirilen bir sabittir. 
* **Coğrafi. kesişme** işlevi, belirli bir çokgen belirli bir çokgen içindeyse true değerini döndürür. Nokta bir alandır ve bu, filtrenin bir parçası olarak geçirilen koordinatların sabit listesi olarak belirtilir.

OData ifadesi sözdiziminde filtre örnekleri bulabilirsiniz [(Azure bilişsel arama)](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Tanıtımı deneyin
Azure Bilişsel Arama Iş portalı tanıtımı, bu makalede başvurulan örnekleri içerir.

-   [Azure bilişsel arama Iş portalı tanıtımında](https://azjobsdemo.azurewebsites.net/)çalışan tanıtımı çevrimiçi olarak görün ve test edin.

-   [GitHub 'Daki Azure-Samples deposundan](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)kodu indirin.

Arama sonuçlarıyla çalışırken, sorgu oluşturulmakta olan değişiklikler için URL 'YI izleyin. Bu uygulama, her birini seçerken URI 'ye model eklemek için yapılır.

1. Tanıtım uygulamasının eşleme işlevini kullanmak için [Bing Haritalar geliştirme merkezi](https://www.bingmapsportal.com/)' nden bir Bing Haritalar anahtarı alın. `index.cshtml` sayfasındaki mevcut anahtarın üzerine yapıştırın. `Web.config` dosyasındaki `BingApiKey` ayarı kullanılmıyor. 

2. Uygulamayı çalıştırın. İsteğe bağlı turu alın veya iletişim kutusunu kapatın.
   
3. "Analist" gibi bir arama terimi girin ve arama simgesine tıklayın. Sorgu hızlı bir şekilde yürütülür.
   
   Arama sonuçlarıyla çok yönlü bir gezinti yapısı da döndürülür. Arama sonucu sayfasında, çok yönlü gezinti yapısı her bir model sonucunun sayısını içerir. Hiçbir model seçilmedi, bu nedenle tüm eşleşen sonuçlar döndürülür.
   
   ![Modelleri seçmeden önce arama sonuçları](media/search-faceted-navigation/faceted-search-before-facets.png "Modelleri seçmeden önce arama sonuçları")

4. Bir Iş başlığına, konuma veya en düşük ücret ' e tıklayın. İlk aramada modeller null vardı, ancak değerler üzerinde çalıştıkları için arama sonuçları artık eşleşmeyen öğelerin kırpılmasından oluşur.
   
   ![Modelleri seçtikten sonra sonuçları ara](media/search-faceted-navigation/faceted-search-after-facets.png "Modelleri seçtikten sonra sonuçları ara")

5. Farklı sorgu davranışlarını deneyebilmeniz için, çok yönlü sorguyu temizlemek üzere, modelleri temizlemek için seçili modellerle sonra `[X]` tıklatın.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Daha fazla bilgi edinin
[Azure bilişsel arama derin](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410)ayrıntılarını izleyin. 45:25 ' de, nasıl model uygulanacağını gösteren bir tanıtım vardır.

Çok yönlü gezinme için tasarım ilkeleri hakkında daha fazla bilgi için aşağıdaki bağlantıları öneririz:

* [Tasarım desenleri: çok yönlü gezinme](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Çok yönlü arama uygularken ön uç sorunları-1. Bölüm](https://articles.uie.com/faceted_search2/)

