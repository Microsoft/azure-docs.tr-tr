---
title: Arama sonuçlarıyla çalışma
titleSuffix: Azure Cognitive Search
description: Arama sonuçlarını yapı ve sıralama, bir belge sayısı edinme ve Azure Bilişsel Arama arama sonuçlarına içerik gezintisi ekleme.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 31af550d4f499b4b4440a27037dc210bfdf0cb6f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793462"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Azure Bilişsel Arama arama sonuçlarıyla çalışma
Bu makalede, toplam sayımlar, belge alımı, sıralama düzenleri ve gezinti gibi bir arama sonuçları sayfasının standart öğelerinin nasıl uygulanacağı hakkında rehberlik sunulmaktadır. Arama sonuçlarınıza veri veya bilgi katkıda bulunan sayfayla ilgili seçenekler, Azure Bilişsel Arama hizmetinize gönderilen [Arama belgesi](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) istekleri aracılığıyla belirtilir. 

REST API istekler, hizmete ne istenmekte olduğunu ve yanıtı nasıl ifade edeceğinizi bildiren bir GET komutu, yolu ve sorgu parametreleri içerir. .NET SDK 'sında, eşdeğer API [Documentsearchresult sınıfıdır](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Birçok kod örneği, burada bulabileceğiniz bir Web ön uç arabirimi içerir: [New York City işleri tanıtım uygulaması](https://azjobsdemo.azurewebsites.net/) ve [Bilivesearchön uç](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Geçerli bir istek, hizmet URL 'SI ve yol, HTTP fiili, `api-version`vb. gibi çeşitli öğeleri içerir. Kısaltma için, yalnızca Sayfalandırmaya uygun olan sözdizimini vurgulamak üzere örnekleri kırptık. İstek sözdizimi hakkında daha fazla bilgi için bkz. [Azure BILIŞSEL arama REST API 'leri](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Toplam isabet sayısı ve sayfa sayısı

Bir sorgudan döndürülen toplam sonuç sayısını gösterir ve daha sonra bu sonuçları daha küçük parçalara döndürmek, neredeyse tüm arama sayfalarında temel değildir.

![][1]

Azure Bilişsel Arama 'de, bu değerleri döndürmek için `$count`, `$top`ve `$skip` parametrelerini kullanırsınız. Aşağıdaki örnek, `@odata.count`olarak döndürülen "Online-Catalog" adlı bir dizindeki toplam isabet sayısı için örnek bir istek gösterir:

    GET /indexes/online-catalog/docs?$count=true

15 ' in gruplarındaki belgeleri alın ve ilk sayfadan başlayarak, toplam isabetlerinin de gösterilmesini sağlayabilirsiniz:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

`$top` ve `$skip`gerekir; burada `$top`, bir toplu işte kaç öğe dönebileceğiniz ve `$skip` atlanacak öğe sayısını belirtir. Aşağıdaki örnekte, her sayfada sonraki 15 öğe gösterilmektedir ve bu, `$skip` parametresindeki artımlı atlamalar tarafından gösterilir.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Düzen

Arama sonuçları sayfasında, bir küçük resim, bir alan alt kümesi ve tam ürün sayfasının bağlantısını göstermek isteyebilirsiniz.

 ![][2]

Azure Bilişsel Arama, bu deneyimi uygulamak için `$select` ve bir [Arama API 'si isteği](https://docs.microsoft.com/rest/api/searchservice/search-documents) kullanırsınız.

Döşeli bir düzen için alanların bir alt kümesini döndürmek için:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Görüntüler ve medya dosyaları doğrudan aranabilir değildir ve maliyetleri azaltmak için Azure Blob depolama gibi başka bir depolama platformunda depolanmalıdır. Dizin ve belgelerde, dış içeriğin URL adresini depolayan bir alan tanımlayın. Daha sonra alanı bir görüntü başvurusu olarak kullanabilirsiniz. Görüntünün URL 'SI belgede olmalıdır.

Bir **OnClick** olayının ürün açıklaması sayfasını almak için, alınacak belgenin anahtarını geçirmek üzere [Arama belgesi](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) ' ni kullanın. Anahtarın veri türü `Edm.String`. Bu örnekte, *246810*' dir.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>İlgiye, derecelendirmeye veya fiyata göre sıralayın

Düzenleri genellikle ilgiye göre sıralayın, ancak müşterilerin var olan sonuçları farklı bir derecelendirme sırasıyla hızla reshuffle için alternatif sıralama emirlerinin hazır hale getirmek yaygın olarak sunulmaktadır.

 ![][3]

Azure Bilişsel Arama 'de sıralama, `$orderby` ifadesi temel alınarak, `"Sortable": true.` bir `$orderby` yan tümcesi olarak dizine alınmış tüm alanlar için bir OData deyimidir. Sözdizimi hakkında daha fazla bilgi için bkz. [filtreler ve order by yan tümceleri Için OData ifade sözdizimi](query-odata-filter-orderby-syntax.md).

Uygunluk, Puanlama profilleriyle ilişkili olarak kesin. Bir arama terimi üzerinde daha fazla veya daha fazla eşleşmelerle daha yüksek puanlar elde edilecek şekilde, tüm sonuçları derecelendirmek için metin analizini ve istatistikleri temel alan varsayılan Puanlama ' yi kullanabilirsiniz.

Alternatif sıralama düzenleri genellikle sıralama düzenini oluşturan bir yönteme geri çağıran **OnClick** olayları ile ilişkilendirilir. Örneğin, Bu sayfa öğesi verildiğinde:

 ![][4]

Giriş olarak seçilen sıralama seçeneğini kabul eden bir yöntem oluşturur ve bu seçenekle ilişkili ölçütler için sıralı bir liste döndürür.

 ![][5]

> [!NOTE]
> Varsayılan Puanlama birçok senaryo için yeterli olsa da, bunun yerine özel bir Puanlama profiliyle ilgili olarak bir ilgiyi dayandırın. Özel bir Puanlama profili, işletmenize daha faydalı olan öğeleri artırmak için bir yol sağlar. Daha fazla bilgi için bkz. [Puanlama profilleri ekleme](index-add-scoring-profiles.md) .
>

## <a name="faceted-navigation"></a>Çok yönlü navigasyon

Arama gezinmesi, genellikle bir sayfanın yanında veya üstünde bulunan bir sonuçlar sayfasında ortaktır. Azure Bilişsel Arama 'de, çok yönlü gezinme, önceden tanımlanmış filtreleri temel alan otomatik olarak yönlendirilmiş arama sağlar. Ayrıntılar için bkz. [Azure 'da çok yönlü gezinme bilişsel arama](search-faceted-navigation.md) .

## <a name="filters-at-the-page-level"></a>Sayfa düzeyinde filtreler

Çözüm tasarımınız belirli içerik türleri için adanmış arama sayfaları içeriyorsa (örneğin, sayfanın üst kısmında listelenen departmanlara sahip bir çevrimiçi perakende uygulaması), bir **OnClick** olayının yanına bir [filtre ifadesi](search-filters.md) ekleyebilirsiniz önceden filtrelenmiş durumda bir sayfa açın.

Arama ifadesi içeren veya içermeyen bir filtre gönderebilirsiniz. Örneğin, aşağıdaki istek marka adını filtreleyecek ve yalnızca onunla eşleşen belgeleri döndürüyor.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

`$filter` ifadeler hakkında daha fazla bilgi için bkz. [arama belgeleri (Azure BILIŞSEL arama API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) .

## <a name="see-also"></a>Ayrıca Bkz.

- [Azure Bilişsel Arama REST API](https://docs.microsoft.com/rest/api/searchservice)
- [Dizin Işlemleri](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Belge Işlemleri](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Azure Bilişsel Arama çok yönlü gezinme](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
