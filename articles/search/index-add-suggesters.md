---
title: Dizine typeahead sorguları ekleme
titleSuffix: Azure Cognitive Search
description: Otomatik tamamlama veya otomatik önerilmiş sorgu koşulları çağıran istekleri öneri araçları ve formül oluşturarak Azure Bilişsel Arama 'de yazma öncesi sorgu eylemlerini etkinleştirin.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a312068d5c8c574e7b069263cf37e3b855810e4b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790111"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>Azure Bilişsel Arama typeahead için bir dizine öneri araçları ekleme

Azure Bilişsel Arama 'de, "yazarken arama" veya typeahead işlevselliği, bir [arama dizinine](search-what-is-an-index.md)eklediğiniz bir **öneri aracı** yapısını temel alır. Bu, typeahead 'in etkinleştirilmesini istediğiniz bir veya daha fazla alanın listesidir.

Bir öneri aracı, yazmakta olduğunuz terimi veya tümceciği tamamlayan ve eşleşen belgelerin kısa bir listesini döndüren *önerilerden* oluşan iki typeahead çeşidini destekler: *AutoComplete*.  

Aşağıdaki ekran görüntüsünde, örnek [olarak ilk uygulamanızı oluşturma bölümünde C# ](tutorial-csharp-type-ahead-and-suggestions.md) typeahead gösterilmektedir. Kullanıcının arama kutusuna girebileceği anticipates otomatik tamamlama. Gerçek giriş "tw" dir, bu da otomatik tamamlama "ın" ile biter ve olası arama terimi olarak "ikizi" olarak çözümleniyor. Öneriler, açılan listede görselleştirilir. Öneriler için, sonucu en iyi açıklayan bir belgenin herhangi bir bölümünü yüzeyinize ekleyebilirsiniz. Bu örnekte, öneriler otel adlarıdır. 

![Otomatik tamamlama ve önerilen sorguların görsel karşılaştırması](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Otomatik tamamlama ve önerilen sorguların görsel karşılaştırması")

Bu davranışları Azure Bilişsel Arama uygulamak için bir dizin ve sorgu bileşeni vardır. 

+ Dizinde bir öneri aracı ekleyin. Portal, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)veya [.NET SDK 'sını](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)kullanabilirsiniz. Bu makalenin geri kalanında bir öneri aracı oluşturmaya odaklanılmıştır. 

+ Sorgu isteğinde, [aşağıda listelenen API](#how-to-use-a-suggester)'lerden birini çağırın.

Arama ölçütü türü, alan temelinde desteklenir. Ekran görüntüsünde gösterilenle benzer bir deneyim istiyorsanız, aynı arama çözümü içinde her iki typeahead davranışını da uygulayabilirsiniz. Her iki istek de, bir Kullanıcı en az üç karakter girişi dizesi sağladıktan sonra, belirli dizin ve yanıtların *belge* koleksiyonunu hedefler.

## <a name="create-a-suggester"></a>Öneri oluşturucu oluşturma

Bir öneri aracı birçok özelliğe sahip olsa da, birincil olarak bir typeahead deneyimini etkinleştirilebilmeniz için bir alanlar koleksiyonudur. Örneğin, bir seyahat uygulaması, hedefler, şehirler ve atlamalar üzerinde typeahead aramasını etkinleştirmek isteyebilir. Bu nedenle, üç alan de alanlar koleksiyonunda gider.

Bir öneri aracı oluşturmak için bir dizin şemasına bir tane ekleyin. Bir dizinde (özellikle, öneri araçları koleksiyonunda bir öneri aracı) bir öneri aracı olabilir. 

### <a name="when-to-create-a-suggester"></a>Ne zaman bir öneri aracı oluşturulur?

Bir öneri aracı oluşturmak için en iyi zaman, alan tanımının kendisini de oluşturmaktır.

Önceden var olan alanları kullanarak bir öneri aracı oluşturmaya çalışırsanız, API buna izin vermez. İki veya daha fazla karakter kombinasyondaki kısmi terimler, tüm koşullara göre simgeleştirilmiştir, dizin oluşturma sırasında typeahead metni oluşturulur. Mevcut alanlar zaten simgeleştirilmiş olduğunda, bir öneri aracı eklemek istiyorsanız dizini yeniden oluşturmanız gerekir. Yeniden dizin oluşturma hakkında daha fazla bilgi için bkz. [Azure bilişsel arama dizin oluşturma](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>REST API kullanarak oluşturma

REST API [Dizin oluşturma](https://docs.microsoft.com/rest/api/searchservice/create-index) veya [güncelleştirme dizini](https://docs.microsoft.com/rest/api/searchservice/update-index)aracılığıyla öneri araçları ekleyin. 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```


### <a name="create-using-the-net-sdk"></a>.NET SDK kullanarak oluşturma

İçinde C#, bir [öneri aracı nesnesi](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)tanımlayın. `Suggesters` bir koleksiyon ancak yalnızca bir öğe alabilir. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="property-reference"></a>Özellik başvurusu

|Özellik      |Açıklama      |
|--------------|-----------------|
|`name`        |Öneri aracı adı.|
|`searchMode`  |Aday tümcecikleri aramak için kullanılan strateji. Şu anda desteklenen tek mod, Tümcelerin başındaki veya ortasındaki tümceciklerden esnek eşleme gerçekleştiren `analyzingInfixMatching`.|
|`sourceFields`|Önerilerin içerik kaynağı olan bir veya daha fazla alanın listesi. Alanların `Edm.String` ve `Collection(Edm.String)`türünde olması gerekir. Alanda bir çözümleyici belirtilmişse, [Bu listeden](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) bir adlandırılmış çözümleyici olmalıdır (özel çözümleyici değil).<p/>En iyi uygulama olarak, bir arama çubuğunda veya açılan listede bir tamamlanmış dize olup olmadığı için, yalnızca beklenen ve uygun bir yanıta ödünç veren alanları belirtin.<p/>Bir otel adı duyarlık içerdiğinden iyi bir adaydır. Açıklamalar ve açıklamalar gibi ayrıntılı alanlar çok yoğun. Benzer şekilde, Kategoriler ve Etiketler gibi yinelenen alanlar daha az etkilidir. Örneklerde, birden fazla alanı dahil etbileceğinizi göstermek için, "Category" de yer alır. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>Bir öneri aracı içindeki sourceFields için çözümleyici kısıtlamaları

Azure Bilişsel Arama, alan içeriğini çözümleyerek tek tek şartlar üzerinde sorgu yapmayı etkinleştirir. Öneri araçları, kaynak alanları üzerinde ek analizler gerektiren tüm koşullara ek olarak ön eklerin dizinlenmesini gerektirir. Özel çözümleyici yapılandırması, çeşitli belirteçlerin ve filtrelerin herhangi birini, genellikle öneriler için gerekli olan ön ekleri üreten yöntemlerle birleştirebilir. Bu nedenle, Azure Bilişsel Arama özel Çözümleyicileri olan alanların bir öneri aracı dahil edilmesini engeller.

> [!NOTE] 
>  Yukarıdaki sınırlamaya geçici çözüm yapmanız gerekiyorsa, aynı içerik için iki ayrı alan kullanın. Bu, diğer alanlardan birinin bir öneri aracı sahip olmasını sağlar, ancak diğeri özel bir çözümleyici yapılandırmasıyla ayarlanacaktır.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Sorguda öneri aracı kullanma

Bir öneri aracı oluşturulduktan sonra, özelliği çağırmak için sorgu mantığınızdaki uygun API 'yi çağırın. 

+ [Öneriler REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Otomatik tamamlama REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync yöntemi](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync yöntemi](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

API kullanımı, AutoComplete REST API aşağıdaki çağrısında gösterilmiştir. Bu örnekte iki örnek vardır. İlk olarak, tüm sorgularda olduğu gibi, işlem bir dizinin belgeler koleksiyonuna göre yapılır. İkinci olarak, sorgu parametreleri ekleyebilirsiniz. Birçok sorgu parametresi her iki API için de yaygın olsa da, liste her biri için farklıdır.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Dizinde tanımlı bir öneri aracı yoksa otomatik tamamlama veya önerilere çağrı başarısız olur.

## <a name="sample-code"></a>Örnek kod

+ Örnek [olarak C# ilk uygulamanızı oluşturun](tutorial-csharp-type-ahead-and-suggestions.md) , bir öneri aracı yapımı, önerilen sorgular, otomatik tamamlama ve çok yönlü gezinme gösterir. Bu kod örneği, korumalı alan Azure Bilişsel Arama hizmetinde çalışır ve tüm yapmanız gerekir. böylece, uygulamayı çalıştırmak için F5 'e basın. Abonelik ya da oturum açma gerekli değildir.

+ [Dotnethowtoautocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) , hem hem de C# Java kodunu içeren eski bir örnektir. Ayrıca, bir öneri aracı yapımı, önerilen sorgular, otomatik tamamlama ve çok yönlü gezinme gösterir. Bu kod örneği barındırılan [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) örnek verilerini kullanır. 


## <a name="next-steps"></a>Sonraki adımlar

İsteklerin nasıl formül altında olduğunu görmek için aşağıdaki örneği öneririz.

> [!div class="nextstepaction"]
> [Öneriler ve AutoComplete örnekleri](search-autocomplete-tutorial.md) 
