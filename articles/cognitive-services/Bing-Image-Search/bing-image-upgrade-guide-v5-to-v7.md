---
title: Bing Resim Arama API'si v5 'ten v7 'ye yükseltme
titleSuffix: Azure Cognitive Services
description: Bu yükseltme kılavuzunda Bing Resim Arama API'si sürüm 5 ve sürüm 7 arasındaki değişiklikler açıklanmaktadır. Uygulamanızın 7 sürümünü kullanmak için güncelleştirmeniz gereken parçalarını belirlemenize yardımcı olması için bu kılavuzu kullanın.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d10db37934bb0d6571eb0191d5f1be47dae000ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96342184"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Bing Resim Arama API'si v7 Yükseltme Kılavuzu

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bu yükseltme Kılavuzu, sürüm 5 ve Bing Resim Arama API'si sürüm 7 arasındaki değişiklikleri tanımlar. Uygulamanızın 7 sürümünü kullanmak için güncelleştirmeniz gereken parçalarını belirlemenize yardımcı olması için bu kılavuzu kullanın.

## <a name="breaking-changes"></a>Yeni değişiklikler

### <a name="endpoints"></a>Uç Noktalar

- Uç noktanın sürüm numarası, V5 'ten v7 'e değişti. Örneğin, https: \/ /api.Cognitive.Microsoft.com/Bing/ \* \* v 7.0 * */images/Search.

### <a name="error-response-objects-and-error-codes"></a>Hata yanıtı nesneleri ve hata kodları

- Tüm başarısız istekler `ErrorResponse` Yanıt gövdesine bir nesne içermelidir.

- Nesnesine aşağıdaki alanlar eklendi `Error` .  
  - `subCode`&mdash;Mümkünse hata kodunu farklı demetlere göre bölümler
  - `moreDetails`&mdash;Alanda açıklanan hata hakkında ek bilgiler `message`


- V5 hata kodları aşağıdaki olası `code` ve `subCode` değerlerle değiştirilmiştir.

|Kod|Alt|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing, alt kod koşullarından herhangi biri gerçekleştiğinde ServerError döndürür. HTTP durum kodu 500 ise yanıt bu hataları içerir.
|Invalidrequest|ParameterMissing<br/>Parameterınvalidvalue<br/>HttpNotAllowed<br/>Engellendi|İsteğin herhangi bir bölümü geçerli değilse Bing, ınvalidrequest döndürüyor. Örneğin, gerekli bir parametre eksik veya bir parametre değeri geçerli değil.<br/><br/>Hata ParameterMissing veya Parameterınvalidvalue ise, HTTP durum kodu 400 ' dir.<br/><br/>Hataya HttpNotAllowed varsa, HTTP durum kodu 410 ' dir.
|Ratelimitexcebaşında||Bing, sorgu/saniye (QPS) veya aylık sorgu (QPM) kotası her aşışınızda Ratelimitexceden başına döndürür.<br/><br/>QPM 'yi aştıysanız Bing, QPS ve 403 değerini aştıysanız, 429 HTTP durum kodunu döndürür.
|Invalidauthorleştirme|AuthorizationMissing<br/>Authorizationartıklık|Bing çağıranın kimliğini doğrulayamayan Bing, ınvalidauthortıcıyla geri döndürür. Örneğin, `Ocp-Apim-Subscription-Key` üst bilgi eksik veya abonelik anahtarı geçerli değil.<br/><br/>Birden fazla kimlik doğrulama yöntemi belirtirseniz artıklık oluşur.<br/><br/>Hata eksik ise, HTTP durum kodu 401 ' dir.
|InsufficientAuthorization|AuthorizationDisabled<br/>Authorization, zaman aşımına uğradı|Çağıranın kaynağa erişim izni olmadığında Bing, InsufficientAuthorization döndürür. Abonelik anahtarı devre dışı bırakılmışsa veya süresi dolmuşsa bu durum oluşabilir. <br/><br/>Hata InsufficientAuthorization ise, HTTP durum kodu 403 ' dir.

- Aşağıda önceki hata kodları yeni kodlara eşlenir. V5 hata kodlarıyla bir bağımlılık yaptıysanız, kodunuzu uygun şekilde güncelleştirin.

|Sürüm 5 kodu|Sürüm 7 Code. alt kod
|-|-
|RequestParameterMissing yok|Invalidrequest. ParameterMissing yok
Requestparameterınvalidvalue|Invalidrequest. Parameterınvalidvalue
Resourceaccessreddedildi|InsufficientAuthorization
ExceededVolume|Ratelimitexcebaşında
ExceededQpsLimit|Ratelimitexcebaşında
Devre dışı|InsufficientAuthorization. AuthorizationDisabled
UnexpectedError|ServerError. UnexpectedError
DataSourceErrors|Sunucuhatası. ResourceError
AuthorizationMissing|Invalidauthorleştirme. AuthorizationMissing
HttpNotAllowed|Invalidrequest. HttpNotAllowed
UserAgentMissing|Invalidrequest. ParameterMissing yok
NotImplemented|ServerError. NotImplemented
Invalidauthorleştirme|Invalidauthorleştirme
Invalidauthorizationmethod|Invalidauthorleştirme
MultipleAuthorizationMethod|Invalidauthorasyon. Authorizationartıklık
ExpiredAuthorizationToken|InsufficientAuthorization. Authorization, zaman aşımına uğradı
InsufficientScope|InsufficientAuthorization
Engellendi|Invalidrequest. engellendi



### <a name="query-parameters"></a>Sorgu parametreleri

- `modulesRequested`Sorgu parametresi [modüller](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)olarak yeniden adlandırıldı.  

- Ek açıklamaları Etiketler olarak yeniden adlandırıldı. Bkz. Etiketler için [modüller](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) sorgu parametresi.  

- ShoppingSources filtre değerinin desteklenen pazarların listesi yalnızca en-US olarak değiştirildi. Bkz. [ImageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Görüntü öngörüleri değişiklikleri

- `annotations`Imaiçgörüler [](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) alanı olarak yeniden adlandırıldı `imageTags` .  

- `AnnotationModule`Nesnesi [ımagetagsmodule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule)olarak yeniden adlandırıldı.  

- `Annotation`Nesneyi [etiketlemek](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag)için yeniden adlandırıldı ve `confidence` alan kaldırıldı.  

- `insightsSourcesSummary` [Görüntü](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) nesnesinin alanı olarak yeniden adlandırıldı `insightsMetadata` .  

- Nesnesi, `InsightsSourcesSummary` [ınsightsmetadata](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata)olarak yeniden adlandırıldı.  

- `https://api.cognitive.microsoft.com/bing/v7.0/images/details`Uç nokta eklendi. Bu uç noktayı,/images/Search uç noktası yerine görüntü öngörüleri istemek için kullanın. Bkz. [görüntü öngörüleri](./image-insights.md).

- Aşağıdaki sorgu parametreleri artık yalnızca `/images/details` uç noktayla geçerlidir.  

    -   [ınsi, Stoken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [Modüler](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [kabini](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [yükseltil](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [kedi](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [unu](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- `ImageInsightsResponse`Nesne [ımageınsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights)olarak yeniden adlandırıldı.  

- [Imageınsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) nesnesindeki aşağıdaki alanların veri türleri değiştirildi.  

    -   Alanın türü, `relatedCollections` öğesinden `ImageGallery[]` [Relatedcollectionsmodule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule)olarak değiştirildi.  

    -   Alanın türü, `pagesIncluding` öğesinden `Image[]` [ımabir modül](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)olarak değiştirildi.  

    -   Alanın türü, `relatedSearches` öğesinden `Query[]` [Relatedsearchesmodule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule)olarak değiştirildi.  

    -   `recipes`Alan türü `Recipe[]` [RecipesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule)olarak değiştirildi.  

    -   Alanın türü, `visuallySimilarImages` öğesinden `Image[]` [ımabir modül](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)olarak değiştirildi.  

    -   Alanın türü, `visuallySimilarProducts` öğesinden `ProductSummaryImage[]` [ımabir modül](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)olarak değiştirildi.  

    -   Nesne kaldırıldı `ProductSummaryImage` ve ürünle ilgili alanları [Image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) nesnesine taşındı. `Image`Nesne, yalnızca görüntü Insight yanıtında görsel açıdan benzer ürünlerin bir parçası olarak dahil edildiğinde ürünle ilgili alanları içerir.  

    -   Alanın türü, yerine `recognizedEntityGroups` `RecognizedEntityGroup[]` [Recognizedentitıesmodule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule)olarak değiştirildi.  

-   `categoryClassification` [Imageınsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) alanını olarak yeniden adlandırdı `annotations` ve türünü olarak değiştirdi `AnnotationsModule` .  

### <a name="images-answer"></a>Görüntü yanıtı

-   [Görüntülerden](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images)displayShoppingSourcesBadges ve displayRecipeSourcesBadges alanları kaldırıldı.  

-   `nextOffsetAddCount` [Görüntülerin](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) alanı olarak yeniden adlandırıldı `nextOffset` . Sapmayı kullanma yönteminiz de değişmiştir. Daha önce, [fark](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) sorgu parametresini `nextOffsetAddCount` değere ve önceki fark değerine ve sonuç içindeki görüntü sayısına göre ayarlarsınız. Şimdi `offset` `nextOffset` değere ayarlanır.  


## <a name="non-breaking-changes"></a>Kırılamayan değişiklikler

### <a name="query-parameters"></a>Sorgu parametreleri

- Olası bir [ImageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) filtre değeri olarak saydam eklendi. Saydam filtre yalnızca saydam bir arka plana sahip görüntüler döndürüyor.

- Olası bir [Lisans](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) filtresi değeri olarak eklendi. Herhangi bir filtre yalnızca lisans altında olan görüntüleri döndürür.

- [MaxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) ve [minfilesize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) sorgu parametreleri eklendi. Bu filtreleri kullanarak bir dosya boyutu aralığı içindeki görüntüleri döndürün.  

- [MaxHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [MinHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [MaxWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [MinWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) sorgu parametreleri eklendi. Bu filtreleri, bir yükseklik ve genişlik aralığı içindeki görüntüleri döndürmek için kullanın.  

### <a name="object-changes"></a>Nesne değişiklikleri

- `description` `lastUpdated` [Teklif](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) nesnesine ve alanları eklendi.  

- `name`Alanı [ımagegallery](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery) nesnesine eklediniz.  

- `similarTerms` [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) nesnesine eklendi. Bu alan, kullanıcının sorgu dizesinde anlamı olan koşulların bir listesini içerir.