---
title: API v5 'ten v7-Bing Web Araması API'si sürümüne yükseltme
titleSuffix: Azure Cognitive Services
description: Bing Web Araması v7 API 'Lerini kullanmak için uygulamanızın hangi bölümlerinin güncelleştirme gerektirdiğini belirleme.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d930543671a5328d76a38aa7e1b421c111e89e39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349529"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Bing Web Araması API'si v5 'ten v7 'ye yükseltme

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bu yükseltme Kılavuzu, sürüm 5 ve Bing Web Araması API'si sürüm 7 arasındaki değişiklikleri tanımlar. Uygulamanızın 7 sürümünü kullanmak için güncelleştirmeniz gereken parçalarını belirlemenize yardımcı olması için bu kılavuzu kullanın.

## <a name="breaking-changes"></a>Yeni değişiklikler

### <a name="endpoints"></a>Uç Noktalar

- Uç noktanın sürüm numarası, V5 'ten v7 'e değişti. Örneğin, https: \/ \/ api.Cognitive.Microsoft.com/Bing/**v 7.0**/Search.

### <a name="error-response-objects-and-error-codes"></a>Hata yanıtı nesneleri ve hata kodları

- Tüm başarısız istekler `ErrorResponse` Yanıt gövdesine bir nesne içermelidir.

- Nesnesine aşağıdaki alanlar eklendi `Error` .  
  - `subCode`&mdash;Mümkünse hata kodunu farklı demetlere göre bölümler
  - `moreDetails`&mdash;Alanda açıklanan hata hakkında ek bilgiler `message`


- V5 hata kodları aşağıdaki olası `code` ve `subCode` değerlerle değiştirilmiştir.

|Kod|Alt|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Alt kod koşullarından herhangi biri gerçekleştiğinde Bing ServerError hatası döndürüyor. HTTP durum kodu 500 ise yanıt bu hataları içerir.
|Invalidrequest|ParameterMissing<br/>Parameterınvalidvalue<br/>HttpNotAllowed<br/>Engellendi|İsteğin herhangi bir bölümü geçerli değilse Bing, ınvalidrequest döndürüyor. Örneğin, gerekli bir parametre eksik veya bir parametre değeri geçerli değil.<br/><br/>Hata ParameterMissing veya Parameterınvalidvalue ise, HTTP durum kodu 400 ' dir.<br/><br/>Hataya HttpNotAllowed varsa, HTTP durum kodu 410 ' dir.
|Ratelimitexcebaşında||Bing, sorgu/saniye (QPS) veya aylık sorgu (QPM) kotası her aşışınızda Ratelimitexceden başına döndürür.<br/><br/>QPM 'yi aştıysanız Bing, QPS ve 403 değerini aştıysanız, 429 HTTP durum kodunu döndürür.
|Invalidauthorleştirme|AuthorizationMissing<br/>Authorizationartıklık|Bing çağıranın kimliğini doğrulayamayan Bing, ınvalidauthortıcıyla geri döndürür. Örneğin, `Ocp-Apim-Subscription-Key` üst bilgi eksik veya abonelik anahtarı geçerli değil.<br/><br/>Birden fazla kimlik doğrulama yöntemi belirtirseniz artıklık oluşur.<br/><br/>Hata eksik ise, HTTP durum kodu 401 ' dir.
|InsufficientAuthorization|AuthorizationDisabled<br/>Authorization, zaman aşımına uğradı|Çağıranın kaynağa erişim izni olmadığında Bing, InsufficientAuthorization döndürür. Bu hata, abonelik anahtarı devre dışı bırakılmışsa veya süresi dolmuşsa oluşabilir. <br/><br/>Hata InsufficientAuthorization ise, HTTP durum kodu 403 ' dir.

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


## <a name="non-breaking-changes"></a>Kırılamayan değişiklikler  

### <a name="headers"></a>Üst Bilgiler

- İsteğe bağlı [pragma](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) istek üst bilgisi eklendi. Varsayılan olarak, Bing önbelleğe alınmış içeriği (varsa) döndürür. Bing'in önbelleğe alınmış içeriği döndürmesini önlemek için, Pragma üst bilgisini no-cache olarak ayarlayın (örneğin, Pragma: no-cache).

### <a name="query-parameters"></a>Sorgu parametreleri

- [AnswerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) sorgu parametresi eklendi. Yanıtın içermesini istediğiniz yanıt sayısını belirtmek için bu parametreyi kullanın. Yanıtlar, sıralamaya göre seçilir. Örneğin, bu parametreyi üç (3) olarak ayarlarsanız, yanıt, en çok üç dereceli yanıtı içerir.  

- [Yükseltme](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) sorgu parametresi eklendi. Bu parametreyi `answerCount` , derecelendirmesinden bağımsız olarak bir veya daha fazla yanıt türünü açıkça içerecek şekilde kullanın. Örneğin, Videoları ve görüntüleri yanıta yükseltmek için, *videoları videolar, görüntüler* olarak ayarlamanız gerekir. Yükseltmek istediğiniz yanıtların listesi sınıra göre sayılmaz `answerCount` . Örneğin, `answerCount` 2 ise ve `promote` *videolar, görüntüler* olarak ayarlanırsa, yanıt Web sayfaları, Haberler, videolar ve görüntüler içerebilir.

### <a name="object-changes"></a>Nesne değişiklikleri

- `someResultsRemoved`Alanı [Webanswer](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) nesnesine eklediniz. Alan, yanıtın Web yanıtındaki bazı sonuçları dışarıda bırakıldığını belirten bir Boole değeri içerir.