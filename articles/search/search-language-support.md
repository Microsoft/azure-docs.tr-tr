---
title: Ingilizce olmayan arama sorguları için çok dilli dizin oluşturma
titleSuffix: Azure Cognitive Search
description: Çoklu dil içeriğini destekleyen bir dizin oluşturun ve ardından bu içeriğin kapsamına alınmış sorgular oluşturun.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801613"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Azure Bilişsel Arama birden çok dil için dizin oluşturma

Çok dilli arama uygulamasındaki önemli bir gereksinim, kullanıcının kendi dilinde arama ve sonuç alma olanağıdır. Azure Bilişsel Arama, çok dilli bir uygulamanın dil gereksinimlerini karşılamanın bir yolu, belirli bir dilde dizeleri depolamak için ayrılmış alanlar oluşturmaktır ve sonra tam metin aramasını sorgu sırasında yalnızca bu alanlarla kısıtlar.

+ Alan tanımlarında, hedef dilin dilsel kurallarını çağıran bir dil Çözümleyicisi ayarlayın. Desteklenen çözümleyiciler listesinin tam listesini görüntülemek için bkz. [dil Çözümleyicileri ekleme](index-add-language-analyzers.md).

+ Sorgu isteğinde, parametreleri tam metin araması olarak belirli alanlara ayarlayın ve ardından, sunmak istediğiniz arama deneyimiyle uyumlu içerik sağlamayan alanların sonuçlarını kırpın.

Bu teknik, alan içeriklerinin bütünlüğünden başarılı oldu. Azure Bilişsel Arama, sorgu yürütmenin bir parçası olarak dizeleri çevirmez veya dil algılamayı gerçekleştirmez. Alanların, istediğiniz dizeleri içerdiğinden emin olmanız gerekir.

## <a name="define-fields-for-content-in-different-languages"></a>Farklı dillerdeki içerik için alanları tanımlama

Azure Bilişsel Arama 'de sorgular tek bir dizini hedefler. Tek bir arama deneyiminde dile özgü dizeler sağlamak isteyen geliştiriciler genellikle değerleri depolamak için ayrılmış alanlar tanımlar: Ingilizce dizeler için bir alan, Fransızca için bir, vb.

Bir alan tanımındaki "çözümleyici" özelliği, [dil Çözümleyicisi](index-add-language-analyzers.md)'ni ayarlamak için kullanılır. Bu, hem dizin oluşturma hem de sorgu yürütme için kullanılacaktır.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>Dizin oluşturma ve yükleme

Ara (ve belirgin) bir adım sorgu oluşturmadan önce [dizini derleyip doldurmanız](search-get-started-dotnet.md) gerekir. Bu adımdan daha fazla bahsedin. Dizin kullanılabilirliğini belirlemenin bir yolu, [portaldaki](https://portal.azure.com)dizinler listesini denetleyerek.

> [!TIP]
> Dil algılama ve metin çevirisi, [AI zenginleştirme](cognitive-search-concept-intro.md) ve [becerileri](cognitive-search-working-with-skillsets.md)aracılığıyla veri alımı sırasında desteklenir. Karma dil içeriğine sahip bir Azure veri kaynağınız varsa, [veri Içeri aktarma Sihirbazı](cognitive-search-quickstart-blob.md)'nı kullanarak dil algılama ve çeviri özelliklerini deneyebilirsiniz.

## <a name="constrain-the-query-and-trim-results"></a>Sorgu ve kırpma sonuçlarını kısıtlama

Sorgudaki parametreler, aramayı belirli alanlarla sınırlandırmak için kullanılır ve sonra herhangi bir alanın sonuçlarını senaryonuz için faydalı olmayan şekilde kırpabilir. 

| Parametreler | Amaç |
|-----------|--------------|
| **searchFields** | Tam metin aramasını adlandırılmış alanlar listesiyle sınırlandırır. |
| **$select** | Yanıtı yalnızca belirttiğiniz alanları içerecek şekilde kırpar. Varsayılan olarak, tüm alınabilir alanlar döndürülür. **$Select** parametresi, hangi hangilerinin dönebileceği seçmenize olanak sağlar. |

Arama, Fransızca dizeleri içeren alanlara kısıtlama hedefi verildiğinde, sorguyu Bu dildeki dizeleri içeren alanlara hedeflemek için **Searchfields** 'i kullanırsınız.

Bir sorgu isteğinde çözümleyici belirtilmesi gerekli değildir. Alan tanımında bir dil Çözümleyicisi her zaman sorgu işleme sırasında kullanılacaktır. Farklı dil Çözümleyicileri çağıran birden çok alanı belirten sorgular için, hüküm veya tümcecikler her alan için atanan çözümleyiciler tarafından bağımsız olarak işlenir.

Varsayılan olarak, bir arama alınabilir olarak işaretlenen tüm alanları döndürür. Bu nedenle, sağlamak istediğiniz dile özgü arama deneyimiyle uyumlu olmayan alanları dışlamak isteyebilirsiniz. Özellikle, Fransızca dizelerini içeren bir alanla aramayı sınırlandırdıysanız, muhtemelen sonuçlardan Ingilizce dizeler içeren alanları dışlamak isteyebilirsiniz. **$Select** Query parametresinin kullanılması, çağıran uygulamaya hangi alanların döndürüleceğini denetlemenizi sağlar.

#### <a name="example-in-rest"></a>REST içinde örnek

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>C 'de örnek #

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>Dile özgü alanları arttırma

Bazen bir sorguyu veren aracının dili, bu durumda sorgunun tüm alanlara aynı anda verilebileceği anlamına gelir. Belirli bir dilin sonuçları için IA tercihi, [Puanlama profilleri](index-add-scoring-profiles.md)kullanılarak tanımlanabilir. Aşağıdaki örnekte, Ingilizce 'deki açıklamada bulunan eşleşmeler, diğer dillerdeki eşleştirmelere göre daha yüksek puanlanacaktır:

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

Ardından, arama isteğine Puanlama profilini dahil edebilirsiniz:

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>Sonraki adımlar

+ [Dil çözümleyicileri](index-add-language-analyzers.md)
+ [Azure Bilişsel Arama’da tam metin araması nasıl çalışır?](search-lucene-query-architecture.md)
+ [Belgelerde Arama REST API'si](/rest/api/searchservice/search-documents)
+ [AI zenginleştirme genel bakış](cognitive-search-concept-intro.md)
+ [Becerileri genel bakış](cognitive-search-working-with-skillsets.md)