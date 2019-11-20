---
title: Proje URL 'SI önizleme başvurusu
titlesuffix: Azure Cognitive Services
description: Proje URL 'SI önizleme uç noktası için başvuru.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: f92c0faaaa3aa0cd2af16a031f3bed4c6b41fc22
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706841"
---
# <a name="project-url-preview-v7-reference"></a>Proje URL 'SI önizleme v7 başvurusu

URL önizlemesi, blog gönderileri, Forum tartışmaları, önizleme sayfaları vb. için web kaynaklarının kısa açıklamalarını destekler. URL herhangi bir Internet kaynağı türü olabilir: Web sayfası, Haberler, resim veya video. Sorgu, http veya https düzenine sahip mutlak bir URL olmalıdır; göreli URL 'Ler veya ftp://gibi diğer şemalar desteklenmez.

URL önizlemesi kullanan uygulamalar, bir sorgu parametresinde önizlemesi yapılacak bir URL ile uç noktaya Web istekleri gönderir. İstek, *OCP-apim-Subscription-Key* üst bilgisini içermelidir.

JSON yanıtı, Önizleme bilgileri için ayrıştırılabilir: ad, kaynak, *isFamilyFriendly*ve temsili bir görüntüye ve tüm kaynağa çevrimiçi erişim sağlayan bağlantıların açıklaması.

Sosyal medya, sohbet bot veya benzer tekliflerde Son Kullanıcı tarafından başlatılan URL paylaşımında, kaynak sitelerine köprü uygulanmış önizleme kod parçacıklarını ve küçük resim görüntülerini görüntülemek için yalnızca URL önizlemesindeki verileri kullanmanız gerekir. Proje URL 'SI önizlemeden aldığınız herhangi bir veriyi kopyalamamanız, depolamanız veya önbelleğe almamanız gerekir. Web sitesi veya içerik sahiplerinden alabileceğiniz önizlemeleri devre dışı bırakmak için herhangi bir isteği dikkate almalısınız.

## <a name="endpoint"></a>Uç Nokta
URL önizleme sonuçları istemek için aşağıdaki uç noktaya bir istek gönderin. Daha fazla belirtim tanımlamak için üstbilgiler ve URL parametreleri kullanın.

Uç nokta al:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

İsteğin HTTPS protokolünü kullanması ve aşağıdaki sorgu parametresini içermesi gerekir:

q-önizlemesi yapılacak URL 'YI tanımlayan sorgu

Aşağıdaki bölümlerde, yanıt nesneleri, sorgu parametreleri ve arama sonuçlarını etkileyen üstbilgiler hakkında teknik ayrıntılar sağlanmaktadır.

İsteklerin içermesi gereken üstbilgiler hakkında daha fazla bilgi için bkz. [üstbilgiler](#headers).

İsteklerin içermesi gereken sorgu parametreleri hakkında daha fazla bilgi için bkz. [Query Parameters](#query-parameters).

Yanıtın içerdiği JSON nesneleri hakkında daha fazla bilgi için bkz. [Response Objects](#response-objects).

Sorgu URL 'SI uzunluğu üst sınırı 2.048 karakterdir. URL uzunluğunun sınırı aşmadığından emin olmak için, sorgu parametrelerinizin en fazla uzunluğu 1.500 karakterden az olmalıdır. URL 2.048 karakteri aşarsa, sunucu 404 ' i döndürür.

İzin verilen kullanım ve sonuçların görüntülenmesi hakkında daha fazla bilgi için bkz. [kullanımı ve görüntüleme gereksinimleri](use-display-requirements.md).

> [!NOTE]
> Diğer arama API 'Leri için anlamlı olan bazı istek üstbilgileri URL önizlemeyi etkilemez
> - Pragma – çağıran, URL önizlemesinin önbellek kullanıp kullanmadığını denetler
> - Kullanıcı Aracısı – şimdilik, URL önizleme API 'Si bılgısayar, dizüstü bilgisayar veya mobil bilgisayarlardan gelen çağrılar için farklı yanıtlar sağlamaz.
> 
> Ayrıca, bazı parametreler URL önizleme API 'si için şu anda anlamlı değildir, ancak gelecekte iyileştirilmiş Genelleştirme için kullanılabilir.

## <a name="headers"></a>Üst bilgiler
Bir istek ve yanıtın içerebilme üstbilgileri aşağıda verilmiştir.

|Üstbilgi|Açıklama|
|------------|-----------------|
|<a name="market" />BingAPIs-Market|Yanıt üst bilgisi.<br /><br /> İstek tarafından kullanılan pazar. Biçimi şöyledir: \<languageCode\>-\<countryCode\>. Örneğin, tr-TR.|
|<a name="traceid" />BingAPIs-TraceId|Yanıt üst bilgisi.<br /><br /> İsteğin ayrıntılarını içeren günlük girdisinin kimliği. Hata oluştuğunda, bu kimliği yakalayın. Sorunu belirleyemez ve çözemezseniz, Destek ekibine diğer bilgilerle birlikte bu kimliği de sağlayın.|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Gerekli istek üst bilgisi.<br /><br /> [Bilişsel Hizmetler](https://www.microsoft.com/cognitive-services/)'de bu hizmete kaydolduğunuzda aldığınız abonelik anahtarı.|
|<a name="clientid" />X-MSEdge-ClientID|İsteğe bağlı istek ve yanıt üst bilgisi.<br /><br /> Bing, kullanıcılara tüm Bing API çağrılarında tutarlı bir davranış sağlamak için bu üst bilgiyi kullanır. Bing sık sık yeni özellikler ve geliştirmeler dağıtır ve farklı dağıtımlarda trafik ataması yapmak için anahtar olarak istemci kimliğini kullanır. Bir kullanıcı için birden çok istekte aynı istemci kimliğini kullanmazsanız, Bing kullanıcıyı birden çok çakışan dağıtıma atayabilir. Birden çok çakışan dağıtıma eklenmek, tutarsız bir kullanıcı deneyimine yol açabilir. Örneğin, ikinci isteğin dağıtım ataması ilkinden farklıysa, beklenmeyen bir deneyim yaşanabilir. Ayrıca, Bing istemci kimliğini kullanarak web sonuçlarını istemci kimliğinin arama geçmişine uyarlayabilir ve bu sayede kullanıcıya daha zengin bir deneyim sağlayabilir.<br /><br /> Bing, istemci kimliği tarafından oluşturulan etkinliği analiz ederek sonuç derecelendirmelerini geliştirmeye yardımcı olması için de bu üst bilgiyi kullanabilir. İlgi geliştirmeleri Bing API'lerinin daha kaliteli sonuçlar vermesine yardımcı olur ve böylelikle API tüketicisi için daha yüksek tıklama oranları getirir.<br /><br />Bu üst bilgi için geçerli olan temel kullanım kuralları şunlardır:<br /><ul><li>Cihazda uygulamanızı kullanan her kullanıcının Bing tarafından oluşturulan benzersiz bir istemci kimliği olmalıdır.<br /><br/>İsteğe bu üst bilgiyi eklemezseniz, Bing bir kimlik oluşturur ve bu kimliği X-MSEdge-ClientID yanıt üst bilgisinde döndürür. İsteğe bu üst bilgiyi EKLEMEMENİZ gereken tek durum, söz konusu cihazda kullanıcının uygulamanızı ilk kez kullanmasıdır.<br /><br/></li><li>Cihazda uygulamanızın bu kullanıcı için yaptığı her Bing API'si isteğinde istemci kimliğini kullanın.<br /><br/></li><li>**ÖZELLIKLE** Bu Istemci KIMLIĞININ, herhangi bir kimlik doğrulayan Kullanıcı hesabı bilgisine bağlanabilir olmadığından emin olmanız gerekir.</li><br/><li>İstemci kimliğinin kalıcı olmasını sağlayın. Tarayıcı uygulamasında kimliği kalıcı hale getirmek için, tüm oturumlarda kimliğin kullanmasını sağlayacak bir kalıcı HTTP tanımlama bilgisi kullanın. Oturum tanımlama bilgisi kullanmayın. Mobil uygulamalar gibi diğer uygulamalarda, kimliği kalıcı hale getirmek için cihazın kalıcı depolamasını kullanın.<br /><br/>Kullanıcı o cihazda uygulamanızı yeniden kullandığında, kalıcı hale getirdiğiniz istemci kimliğini alın.</li></ul><br /> **NOT:** Bing yanıtları bu üstbilgiyi içermeyebilir veya içermeyebilir. Yanıt bu üst bilgiyi içeriyorsa, istemci kimliğini yakalayın ve o cihazda kullanıcı için bunu izleyen tüm Bing isteklerinde onu kullanın.<br /><br /> **NOT:** X-MSEdge-ClientID ' yi eklerseniz, isteğe tanımlama bilgilerini dahil etmeniz gerekir.|
|<a name="clientip" />X-MSEdge-ClientIP|İsteğe bağlı istek üst bilgisi.<br /><br /> İstemci cihazının IPv4 veya IPv6 adresi. IP adresi, kullanıcının konumunu bulmak için kullanılır. Bing konum bilgisini kullanarak güvenli arama davranışını saptar.<br /><br /> Adresi karartmayın (örneğin, son sekiz karakteri 0'la değiştirerek). Adresin karartılması, cihazın gerçek konumuna yakın olmayan bir konum sonucu verir ve bu da Bing'in hatalı sonuçlar sağlamasına yol açabilir.|

## <a name="query-parameters"></a>Sorgu parametreleri
İstek aşağıdaki sorgu parametrelerini içerebilir. Gerekli parametreler için gereken sütuna bakın. Sorgu parametrelerini URL kodlamanız gerekir. Sorgu, http veya https düzenine sahip mutlak bir URL olmalıdır; göreli URL 'Leri veya ftp://gibi diğer düzenleri desteklemiyoruz

|Ad|Değer|Type|Gerekli|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|Sonuçların geldiği pazar. <br /><br />Olası Pazar değerlerinin listesi için bkz. Pazar kodları.<br /><br /> **NOT:** URL önizleme API 'SI Şu anda yalnızca bızı Coğrafya ve Ingilizce dilini destekliyor.<br /><br />|Dize|Evet|
|<a name="query" />ç|Önizlenecek URL|Dize|Evet|
|<a name="responseformat" />responseFormat|Yanıt için kullanılacak medya türü. Aşağıdakiler, büyük/küçük harf duyarsız değerlerdir.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Varsayılan değer JSON ' dır. Yanıtın içerdiği JSON nesneleri hakkında daha fazla bilgi için bkz. [Response Objects](#response-objects).<br /><br />JsonLd belirtirseniz, yanıt gövdesi, arama sonuçlarını içeren JSON-LD nesnelerini içerir. JSON-LD hakkında daha fazla bilgi için bkz. [JSON-ld](https://json-ld.org/).|Dize|Hayır|
|<a name="safesearch"/>safeSearch|Hatalı yetişkin içeriği veya korsan içerik 400 hata koduyla engelleniyor ve *isFamilyFriendly* bayrağı döndürülmüyor. <p>Yasal yetişkin içeriği için aşağıdaki davranış vardır. Durum kodu 200 döndürür ve *isFamilyFriendly* bayrağı false olarak ayarlanır.<ul><li>Güvenli Arama = katı: Başlık, açıklama, URL ve görüntü döndürülmeyecektir.</li><li>Güvenli Arama = orta; Açıklayıcı görüntüyü değil başlık, URL ve açıklama alın.</li><li>Güvenli Arama = kapalı; Tüm Yanıt nesneleri/öğeleri – başlık, URL, açıklama ve görüntü al.</li></ul> |Dize|Gerekli değildir. </br> Varsayılan olarak safeSearch = Strict olur.|

## <a name="response-objects"></a>Yanıt nesneleri
Yanıt şeması, Web Araması API 'sinde olduğu gibi bir [Web sayfası] ya da ErrorResponse 'dır. İstek başarısız olursa, en üst düzey nesne [errorResponse](#errorresponse) nesnesidir.

|Object|Açıklama|
|------------|-----------------|
|[Web](#webpage)|Önizlemenin özniteliklerini içeren üst düzey JSON nesnesi.|

### <a name="error"></a>Hata
Oluşan hatayı tanımlar.

|Öğe|Açıklama|Type|
|-------------|-----------------|----------|
|<a name="error-code" />kodudur|Hata kategorisini tanımlayan hata kodu. Olası kodların listesi için bkz. [hata kodları](#error-codes).|Dize|
|<a name="error-message" />İleti|Hatanın açıklaması.|Dize|
|<a name="error-moredetails" />Ayrıntılı Ayrıntılar|Hata hakkında ek bilgi sağlayan bir açıklama.|Dize|
|<a name="error-parameter" />parametresinin|İstekte hataya neden olan sorgu parametresi.|Dize|
|<a name="error-subcode" />Alt|Hatayı tanımlayan hata kodu. Örneğin, `code` `subCode` ınvalidrequest ise parametergeçersiz veya parameterınvalidvalue olabilir. |Dize|
|<a name="error-value" />deeri|Sorgu parametresinin değeri geçerli değil.|Dize|

### <a name="errorresponse"></a>ErrorResponse
İstek başarısız olduğunda yanıtın içerdiği en üst düzey nesne.

|Ad|Value|Type|
|----------|-----------|----------|
|_tür|İpucu yazın.|Dize|
|<a name="errors" />hatası|İsteğin başarısız olma nedenlerini betimleyen hataların listesi.|[Hata](#error) []|

### <a name="webpage"></a>Web
Önizleme aşamasında bir Web sayfası hakkındaki bilgileri tanımlar.

|Ad|Value|Type|
|----------|-----------|----------|
|name|HTML başlığı değil, sayfa başlığı|Dize|
|url|Gerçekten gezinmekte olan URL (istek yeniden yönlendirmeleri takip edebilir)|Dize|
|description|Sayfanın ve içeriğin kısa açıklaması|Dize|
|isFamilyFriendly|Web dizinindeki öğeler için en doğru; gerçek zamanlı getirme, bu algılamayı yalnızca URL 'ye göre değil, sayfa içeriğini değil|boolean|
|Primaryımageofpage/contentUrl|Önizlemeye dahil edilecek temsili bir görüntünün URL 'SI|Dize|

### <a name="identifiable"></a>Maya
|Ad|Value|Type|
|-------------|-----------------|----------|
|id|Kaynak tanımlayıcısı|Dize|

## <a name="error-codes"></a>Hata kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir.

|Durum Kodu|Açıklama|
|-----------------|-----------------|
|200|Başarılı.|
|400|Sorgu parametrelerinden biri eksik veya geçersiz.|
|400|Sunucuhatası, alt kod ResourceError: İstenen URL 'ye ulaşılamıyor|
|400|Sunucuhatası, alt kod ResourceError: İstenen URL, bir başarı kodu döndürmedi (HTTP 404 döndürüldüğünden dahil)|
|400|Invalidrequest, alt kod engellendi: İstenen URL yetişkinlere yönelik içerik içerebilir ve engellendi|
|401|Abonelik anahtarı eksik veya geçersiz.|
|403|Kullanıcının kimliği doğrulanır (örneğin, geçerli bir abonelik anahtarı kullanmışsa), ancak istenen kaynak için izinleri yoktur.<br /><br /> Çağıran, her ay için sorguları aşarsa, bu durum da bu durumu döndürebilir.|
|410|İstek HTTPS protokolü yerine HTTP kullandı. Yalnızca HTTPS desteklenen protokoldür.|
|429|Çağıran, saniye başına sorgu sorgularını aştı.|
|500|Beklenmeyen sunucu hatası.|

İstek başarısız olursa, yanıt hatanın nedenini açıklayan [hata](#error) nesnelerinin bir listesini Içeren bir [errorResponse](#errorresponse) nesnesi içerir. Hata bir parametreyle ilişkiliyse, `parameter` alan, sorun olan parametreyi tanımlar. Hata bir parametre değeriyle ilişkiliyse, `value` alan geçerli olmayan değeri tanımlar.

```json
{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidRequest",
      "subCode": "ParameterMissing",
      "message": "Required parameter is missing.",
      "parameter": "q"
    }
  ]
}

{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidAuthorization",
      "subCode": "AuthorizationMissing",
      "message": "Authorization is required.",
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Olası hata kodu ve alt hata kodu değerleri aşağıda verilmiştir.

|Kod|Alt|Açıklama
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Uygulanmadı|HTTP durum kodu 500 ' dir.
|Invalidrequest|ParameterMissing<br/>Parameterınvalidvalue<br/>HttpNotAllowed<br/>Engellendi|İsteğin herhangi bir bölümü geçerli değilse Bing, ınvalidrequest döndürüyor. Örneğin, gerekli bir parametre eksik veya bir parametre değeri geçerli değil.<br/><br/>Hata ParameterMissing veya Parameterınvalidvalue ise, HTTP durum kodu 400 ' dir.<br/><br/>HTTPS yerine HTTP protokolünü kullanırsanız, Bing HttpNotAllowed öğesini döndürür ve HTTP durum kodu 410 ' dir.
|Ratelimitexcebaşında|Alt kod yok|Bing, sorgu/saniye (QPS) veya aylık sorgu (QPM) kotası her aşışınızda Ratelimitexceden başına döndürür.<br/><br/>QPS 'yi aşarsanız Bing, 429 HTTP durum kodunu döndürür ve QPM 'yi aşarsanız Bing, 403 döndürür.
|Invalidauthorleştirme|AuthorizationMissing<br/>Authorizationartıklık|Bing çağıranın kimliğini doğrulayamayan Bing, ınvalidauthortıcıyla geri döndürür. Örneğin, `Ocp-Apim-Subscription-Key` üst bilgi eksik veya abonelik anahtarı geçerli değil.<br/><br/>Birden fazla kimlik doğrulama yöntemi belirtirseniz artıklık oluşur.<br/><br/>Hata eksik ise, HTTP durum kodu 401 ' dir.
|InsufficientAuthorization|AuthorizationDisabled<br/>Authorization, zaman aşımına uğradı|Çağıranın kaynağa erişim izni olmadığında Bing, InsufficientAuthorization döndürür. Abonelik anahtarı devre dışı bırakılmışsa veya süresi dolmuşsa bu durum oluşabilir. <br/><br/>Hata InsufficientAuthorization ise, HTTP durum kodu 403 ' dir.

## <a name="next-steps"></a>Sonraki adımlar
- [C# hızlı başlangıcı](csharp.md)
- [Java hızlı başlangıcı](java-quickstart.md)
- [JavaScript hızlı başlangıcı](javascript.md)
- [Node hızlı başlangıcı](node-quickstart.md)
- [Python hızlı başlangıcı](python-quickstart.md)
