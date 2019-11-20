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
ms.openlocfilehash: c4c6b95996206cfb38ea3f77b89c3ebe3c2c0026
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883499"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Bing Resim Arama API'si v7 Yükseltme Kılavuzu

Bu yükseltme Kılavuzu, sürüm 5 ve Bing Resim Arama API'si sürüm 7 arasındaki değişiklikleri tanımlar. Uygulamanızın 7 sürümünü kullanmak için güncelleştirmeniz gereken parçalarını belirlemenize yardımcı olması için bu kılavuzu kullanın.

## <a name="breaking-changes"></a>Yeni değişiklikler

### <a name="endpoints"></a>Uç Noktalar

- Uç noktanın sürüm numarası, V5 'ten v7 'e değişti. Örneğin, https:\//api.Cognitive.Microsoft.com/Bing/\*\*v 7.0 * */images/Search.

### <a name="error-response-objects-and-error-codes"></a>Hata yanıtı nesneleri ve hata kodları

- Tüm başarısız istekler yanıt gövdesine bir `ErrorResponse` nesne içermelidir.

- `Error` Nesnesine aşağıdaki alanlar eklendi.  
  - `subCode`&mdash;Mümkünse hata kodunu farklı demetlere göre bölümler
  - `moreDetails`&mdash;`message` Alanda açıklanan hata hakkında ek bilgiler


- V5 hata kodları aşağıdaki olası `code` ve `subCode` değerlerle değiştirilmiştir.

|Kod|Alt|Açıklama
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Uygulanmadı|Bing, alt kod koşullarından herhangi biri gerçekleştiğinde ServerError döndürür. HTTP durum kodu 500 ise yanıt bu hataları içerir.
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
Uygulanmadı|ServerError. NotImplemented
Invalidauthorleştirme|Invalidauthorleştirme
Invalidauthorizationmethod|Invalidauthorleştirme
MultipleAuthorizationMethod|Invalidauthorasyon. Authorizationartıklık
ExpiredAuthorizationToken|InsufficientAuthorization. Authorization, zaman aşımına uğradı
InsufficientScope|InsufficientAuthorization
Engellendi|Invalidrequest. engellendi



### <a name="query-parameters"></a>Sorgu parametreleri

- Sorgu parametresi modüller olarak yeniden adlandırıldı. [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) `modulesRequested`  

- Ek açıklamaları Etiketler olarak yeniden adlandırıldı. Bkz. Etiketler için [modüller](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) sorgu parametresi.  

- ShoppingSources filtre değerinin desteklenen pazarların listesi yalnızca en-US olarak değiştirildi. Bkz. [ImageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Görüntü öngörüleri değişiklikleri

- Imaiçgörüler [alanı olarak](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) `imageTags`yeniden adlandırıldı. `annotations`  

- Nesnesi ımagetagsmodule olarak yeniden adlandırıldı. [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule) `AnnotationModule`  

- Nesneyi etiketlemek için yeniden [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag)adlandırıldı ve `confidence` alan kaldırıldı. `Annotation`  

- `insightsMetadata` [Görüntü](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) nesnesinin alanı olarak yeniden adlandırıldı. `insightsSourcesSummary`  

- Nesnesi, `InsightsSourcesSummary` [ınsightsmetadata](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata)olarak yeniden adlandırıldı.  

- `https://api.cognitive.microsoft.com/bing/v7.0/images/details` Uç nokta eklendi. Bu uç noktayı,/images/Search uç noktası yerine görüntü öngörüleri istemek için kullanın. Bkz. [görüntü öngörüleri](./image-insights.md).

- Aşağıdaki sorgu parametreleri artık yalnızca `/images/details` uç noktayla geçerlidir.  

    -   [ınsi, Stoken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [Modüler](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [kabini](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [yükseltil](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [Araç](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [kedi](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [unu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Nesne ımageınsights olarak yeniden adlandırıldı. [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) `ImageInsightsResponse`  

- [Imageınsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) nesnesindeki aşağıdaki alanların veri türleri değiştirildi.  

    -   `relatedCollections` Alanın türü, öğesinden `ImageGallery[]` [relatedcollectionsmodule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule)olarak değiştirildi.  

    -   `pagesIncluding` Alanın türü, öğesinden `Image[]` [ımabir modül](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)olarak değiştirildi.  

    -   `relatedSearches` Alanın türü, öğesinden `Query[]` [relatedsearchesmodule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule)olarak değiştirildi.  

    -   Alantürü`recipes` [RecipesModule olarak değiştirildi.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule) `Recipe[]`  

    -   `visuallySimilarImages` Alanın türü, öğesinden `Image[]` [ımabir modül](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)olarak değiştirildi.  

    -   `visuallySimilarProducts` Alanın türü, öğesinden `ProductSummaryImage[]` [ımabir modül](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)olarak değiştirildi.  

    -   Nesne kaldırıldı ve ürünle ilgili alanları Image nesnesine taşındı. [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) `ProductSummaryImage` `Image` Nesne, yalnızca görüntü Insight yanıtında görsel açıdan benzer ürünlerin bir parçası olarak dahil edildiğinde ürünle ilgili alanları içerir.  

    -   Alanın türü, yerine [recognizedentitıesmodule olarak değiştirildi](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule) `RecognizedEntityGroup[]`. `recognizedEntityGroups`  

-   [Imageınsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) `annotations`alanını olarak yeniden adlandırdı ve türünü olarak `AnnotationsModule`değiştirdi. `categoryClassification`  

### <a name="images-answer"></a>Görüntü yanıtı

-   [Görüntülerden](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images)displayShoppingSourcesBadges ve displayRecipeSourcesBadges alanları kaldırıldı.  

-   [Görüntülerin](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) alanı olarak`nextOffset`yenidenadlandırıldı. `nextOffsetAddCount` Sapmayı kullanma yönteminiz de değişmiştir. Daha önce, [fark](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) sorgu parametresini `nextOffsetAddCount` değere ve önceki fark değerine ve sonuç içindeki görüntü sayısına göre ayarlarsınız. Şimdi `offset` `nextOffset` değere ayarlanır.  


## <a name="non-breaking-changes"></a>Kırılamayan değişiklikler

### <a name="query-parameters"></a>Sorgu parametreleri

- Olası bir [ImageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) filtre değeri olarak saydam eklendi. Saydam filtre yalnızca saydam bir arka plana sahip görüntüler döndürüyor.

- Olası bir [Lisans](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) filtresi değeri olarak eklendi. Herhangi bir filtre yalnızca lisans altında olan görüntüleri döndürür.

- [MaxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) ve [minfilesize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) sorgu parametreleri eklendi. Bu filtreleri kullanarak bir dosya boyutu aralığı içindeki görüntüleri döndürün.  

- [MaxHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [MinHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [MaxWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [MinWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) sorgu parametreleri eklendi. Bu filtreleri, bir yükseklik ve genişlik aralığı içindeki görüntüleri döndürmek için kullanın.  

### <a name="object-changes"></a>Nesne değişiklikleri

- `lastUpdated` [Teklif](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) nesnesine ve alanları eklendi `description` .  

- Alanı ımagegallery nesnesine eklediniz. [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery) `name`  

- Images `similarTerms` nesnesine eklendi [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) . Bu alan, kullanıcının sorgu dizesinde anlamı olan koşulların bir listesini içerir.  
