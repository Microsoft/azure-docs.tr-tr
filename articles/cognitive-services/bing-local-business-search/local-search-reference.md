---
title: Bing yerel Iş Arama API 'SI v7 başvurusu
titleSuffix: Azure Cognitive Services
description: Bu makalede, yanıt nesneleri ve arama sonuçlarını etkileyen sorgu parametreleri ve üstbilgileri hakkında teknik ayrıntılar verilmektedir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: db764a73aa1bb18ef2fc0f8f6e5ffe8fd60d388c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075689"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Bing yerel Iş Arama API 'SI v7 başvurusu

Yerel Iş Arama API 'SI, Restoran, otel veya diğer yerel işletmelerin bulunduğu sonuçları almak için Bing 'e bir arama sorgusu gönderir. Bu şekilde, sorgu yerel iş veya bir kategorinin adını belirtebilir (örneğin, restoranlar gibi). Varlık sonuçları kişileri, yerleri veya nesneleri kapsar. Bu bağlamdaki bir yerde iş varlıkları, durumlar, ülkeler/bölgeler vb.  

Bu bölümde, yanıt nesneleriyle ilgili teknik ayrıntılar ve arama sonuçlarını etkileyen sorgu parametreleri ve üst bilgiler yer almaktadır. İsteklerin nasıl yapılacağını gösteren örnekler için bkz. [Yerel Iş araması C# hızlı başlangıç](quickstarts/local-quickstart.md) veya [yerel iş arama Java hızlı başlangıç](quickstarts/local-search-java-quickstart.md). 
  
İsteklerin içermesi gereken üstbilgiler hakkında daha fazla bilgi için bkz. [üstbilgiler](#headers).  
  
İsteklerin içermesi gereken sorgu parametreleri hakkında daha fazla bilgi için bkz. [Query Parameters](#query-parameters).  
  
Yanıtın içerdiği JSON nesneleri hakkında daha fazla bilgi için bkz. [Response Objects](#response-objects).

İzin verilen kullanım ve sonuçların görüntülenmesi hakkında daha fazla bilgi için bkz. [kullanımı ve görüntüleme gereksinimleri](use-display-requirements.md).


  
## <a name="endpoint"></a>Uç Nokta  
Yerel iş sonuçları istemek için şunu öğesine bir GET isteği gönderin: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
İstek, HTTPS protokolünü kullanmalıdır.  
  
> [!NOTE]
> URL uzunluğu üst sınırı 2.048 karakterdir. URL uzunluğunun sınırı aşmadığından emin olmak için, sorgu parametrelerinizin en fazla uzunluğu 1.500 karakterden az olmalıdır. URL 2.048 karakteri aşarsa, sunucu 404 ' i döndürür.  
  
  
## <a name="headers"></a>Üst bilgiler  
Bir istek ve yanıtın içerebilme üstbilgileri aşağıda verilmiştir.  
  
|Üst bilgi|Açıklama|  
|------------|-----------------|  
|ettiğinizde|İsteğe bağlı istek üst bilgisi.<br /><br /> Varsayılan medya türü uygulama/JSON ' dır. Yanıtın [JSON-ld](https://json-ld.org/)kullanmasını belirtmek için Accept üst bilgisini Application/ld + JSON olarak ayarlayın.|  
|<a name="acceptlanguage" />Accept-Language|İsteğe bağlı istek üst bilgisi.<br /><br /> Kullanıcı arabirimi dizelerinde kullanılacak virgülle sınırlanmış bir dil listesi. Liste, tercih edilme durumuna göre azalan düzende sıralanır. Beklenen biçim de içinde olmak üzere daha fazla bilgi için bkz. [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Bu üst bilgi ve [setLang](#setlang) sorgu parametresi karşılıklı olarak birbirini dışlar. İkisini birlikte belirtmeyin.<br /><br /> Bu üstbilgiyi ayarlarsanız, CC sorgu parametresini de belirtmeniz gerekir. Hangi pazardan sonuç döndürüleceğini belirlemek için, Bing listeden bulduğu ilk desteklenen dili kullanır ve bunu `cc` parametresinin değeriyle birleştirir. Liste desteklenen bir dil içermiyorsa, Bing isteği destekleyen en yakın dili ve pazarı bulur ya da sonuçlar için toplu veya varsayılan bir pazar kullanır. Bing'in kullandığı pazarı saptamak için BingAPIs-Market üst bilgisine bakın.<br /><br /> Ancak birden çok dil belirtirseniz bu üst bilgiyi ve `cc` sorgu parametresini kullanın. Aksi takdirde, [mkt](#mkt) ile [setLang](#setlang) sorgu parametrelerini kullanın.<br /><br /> Kullanıcı arabirimi dizesi, kullanıcı arabiriminde etiket olarak kullanılan dizedir. JSON yanıt nesnelerinde çok az kullanıcı arabirimi dizesi vardır. Yanıt nesnelerinde Bing.com özelliklerine yönelik bağlantılar da belirtilen dildedir.|  
|<a name="market" />BingAPIs-Market|Yanıt üst bilgisi.<br /><br /> İstek tarafından kullanılan pazar. Biçimi şöyledir: \<languageCode\>-\<countryCode\>. Örneğin, tr-TR.|  
|<a name="traceid" />BingAPIs-TraceId|Yanıt üst bilgisi.<br /><br /> İsteğin ayrıntılarını içeren günlük girdisinin kimliği. Hata oluştuğunda, bu kimliği yakalayın. Sorunu belirleyemez ve çözemezseniz, Destek ekibine diğer bilgilerle birlikte bu kimliği de sağlayın.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Gerekli istek üst bilgisi.<br /><br /> [Bilişsel Hizmetler](https://www.microsoft.com/cognitive-services/)'de bu hizmete kaydolduğunuzda aldığınız abonelik anahtarı.|  
|<a name="pragma" />Pragma|İsteğe bağlı istek üst bilgisi<br /><br /> Varsayılan olarak, Bing önbelleğe alınmış içeriği (varsa) döndürür. Bing'in önbelleğe alınmış içeriği döndürmesini önlemek için, Pragma üst bilgisini no-cache olarak ayarlayın (örneğin, Pragma: no-cache).
|<a name="useragent" />User-Agent|İsteğe bağlı istek üst bilgisi.<br /><br /> İsteği başlatan kullanıcı aracısı. Bing, mobil kullanıcılara iyileştirilmiş bir deneyim sağlamak için kullanıcı aracısını kullanır. İsteğe bağlı olsa da, bu üst bilgiyi her zaman belirtmeniz önerilir.<br /><br /> User-agent, yaygın olarak kullanılan tarayıcılardan gönderilen dizeyle aynı olmalıdır. Kullanıcı aracıları hakkında bilgi için bkz. [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Aşağıda örnek user-agent dizelerini bulabilirsiniz.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|İsteğe bağlı istek ve yanıt üst bilgisi.<br /><br /> Bing, kullanıcılara tüm Bing API çağrılarında tutarlı bir davranış sağlamak için bu üst bilgiyi kullanır. Bing sık sık yeni özellikler ve geliştirmeler dağıtır ve farklı dağıtımlarda trafik ataması yapmak için anahtar olarak istemci kimliğini kullanır. Bir kullanıcı için birden çok istekte aynı istemci kimliğini kullanmazsanız, Bing kullanıcıyı birden çok çakışan dağıtıma atayabilir. Birden çok çakışan dağıtıma eklenmek, tutarsız bir kullanıcı deneyimine yol açabilir. Örneğin, ikinci isteğin dağıtım ataması ilkinden farklıysa, beklenmeyen bir deneyim yaşanabilir. Ayrıca, Bing istemci kimliğini kullanarak web sonuçlarını istemci kimliğinin arama geçmişine uyarlayabilir ve bu sayede kullanıcıya daha zengin bir deneyim sağlayabilir.<br /><br /> Bing, istemci kimliği tarafından oluşturulan etkinliği analiz ederek sonuç derecelendirmelerini geliştirmeye yardımcı olması için de bu üst bilgiyi kullanabilir. İlgi geliştirmeleri Bing API'lerinin daha kaliteli sonuçlar vermesine yardımcı olur ve böylelikle API tüketicisi için daha yüksek tıklama oranları getirir.<br /><br /> **ÖNEMLİ:** İsteğe bağlı olsa da, bu üst bilgiyi gerekli olarak kabul edebilirsiniz. Aynı son kullanıcı ile cihaz bileşimi için birden çok istekte aynı istemci kimliğini kullanıldığında, 1) API tüketicisi tutarlı bir kullanıcı deneyimi elde eder ve 2) Bing API'lerinden daha kaliteli sonuçlar alındığından tıklama oranları daha yüksek olur.<br /><br /> Bu üst bilgi için geçerli olan temel kullanım kuralları şunlardır:<br /><ul><li>Cihazda uygulamanızı kullanan her kullanıcının Bing tarafından oluşturulan benzersiz bir istemci kimliği olmalıdır.<br /><br/>İsteğe bu üst bilgiyi eklemezseniz, Bing bir kimlik oluşturur ve bu kimliği X-MSEdge-ClientID yanıt üst bilgisinde döndürür. İsteğe bu üst bilgiyi EKLEMEMENİZ gereken tek durum, söz konusu cihazda kullanıcının uygulamanızı ilk kez kullanmasıdır.<br /><br/></li><li>Cihazda uygulamanızın bu kullanıcı için yaptığı her Bing API'si isteğinde istemci kimliğini kullanın.<br /><br/></li><li>**Dikkat:** Bu Istemci KIMLIĞININ, herhangi bir kimlik doğrulayan Kullanıcı hesabı bilgisine bağlanabilir olmadığından emin olmanız gerekir.</li><br/><li>İstemci kimliğinin kalıcı olmasını sağlayın. Tarayıcı uygulamasında kimliği kalıcı hale getirmek için, tüm oturumlarda kimliğin kullanmasını sağlayacak bir kalıcı HTTP tanımlama bilgisi kullanın. Oturum tanımlama bilgisi kullanmayın. Mobil uygulamalar gibi diğer uygulamalarda, kimliği kalıcı hale getirmek için cihazın kalıcı depolamasını kullanın.<br /><br/>Kullanıcı o cihazda uygulamanızı yeniden kullandığında, kalıcı hale getirdiğiniz istemci kimliğini alın.</li></ul><br /> **NOT:** Bing yanıtları bu üst bilgiyi içerebilir veya içermeyebilir. Yanıt bu üst bilgiyi içeriyorsa, istemci kimliğini yakalayın ve o cihazda kullanıcı için bunu izleyen tüm Bing isteklerinde onu kullanın.<br /><br /> **NOT:** X-MSEdge-ClientID üst bilgisini eklerseniz, isteğe tanımlama bilgileri eklememelisiniz.|  
|<a name="clientip" />X-MSEdge-ClientIP|İsteğe bağlı istek üst bilgisi.<br /><br /> İstemci cihazının IPv4 veya IPv6 adresi. IP adresi, kullanıcının konumunu bulmak için kullanılır. Bing konum bilgisini kullanarak güvenli arama davranışını saptar.<br /><br /> **NOT:** İsteğe bağlı olsa da, bu üst bilgiyi ve X-Search-Location üst bilgisini her zaman belirtmeniz önerilir.<br /><br /> Adresi karartmayın (örneğin, son sekiz karakteri 0'la değiştirerek). Adresin karartılması, cihazın gerçek konumuna yakın olmayan bir konum sonucu verir ve bu da Bing'in hatalı sonuçlar sağlamasına yol açabilir.|  
|<a name="location" />X-Search-Location|İsteğe bağlı istek üst bilgisi.<br /><br /> İstemcinin coğrafi konumunu açıklayan noktalı virgülle sınırlanmış anahtar/değer çifti listesi. Bing konum bilgisini kullanarak güvenli arama davranışını saptar ve ilgili yerel içeriği döndürür. Anahtar/değer çiftini \<anahtar\>:\<değer\> olarak belirtin. Aşağıda, kullanıcının konumunu belirtmek için kullandığınız anahtarlar gösterilir.<br /><br /><ul><li>Enlem, istemci konumunun derece cinsinden Enlem&mdash;. Enlem -90,0 değerinden büyük veya bu değere eşit ve +90,0 değerinden küçük veya bu değere eşit olmalıdır. Negatif değerler güney enlemlerini ve pozitif değerler de kuzey enlemlerini gösterir.<br /><br /></li><li>Long&mdash;istemci konumunun Boylam derece cinsinden. Boylam -180,0 değerinden büyük veya bu değere eşit ve +180,0 değerinden küçük veya bu değere eşit olmalıdır. Negatif değerler batı boylamlarını ve pozitif değerler de doğu boylamlarını gösterir.<br /><br /></li><li>koordinatların yatay doğruluğunu belirten, ölçü cinsinden yarıçapı&mdash;. Cihazın konum hizmeti tarafından döndürülen değeri geçirin. Normalde değerler GPS/Wi-Fi için 22 m, baz istasyonu triangülasyonu için 380 m ve ters IP araması için 18.000 m'dir.<br /><br /></li><li>TS, istemcinin konumda olduğu zamana ait UTC UNIX zaman damgasını&mdash;. (UNIX zaman damgası 1 Ocak 1970'den başlayarak saniye sayısıdır.)<br /><br /></li><li>head&mdash;İsteğe bağlı. İstemcinin göreli seyahat yönü. Gerçek kuzeye göre saat yönünün tersine 0 ile 360 derece arasında bir seyahat yönü belirtin. Bu anahtarı ancak `sp` anahtarı sıfırdan farklı bir değer olduğunda belirtin.<br /><br /></li><li>SP, istemci cihazının yolculukta olduğu, saniye başına ölçü cinsinden yatay hız (hız)&mdash;.<br /><br /></li><li>alt&mdash; istemci cihazının ölçü cinsinden yüksekliği.<br /><br /></li><li>are&mdash;İsteğe bağlı. Koordinatların dikey doğruluğunu belirten metre cinsinden yarıçap. Yarıçap varsayılan olarak 50 Kiloters 'e sahiptir. Bu anahtarı ancak `alt` anahtarı belirttiğiniz durumda belirtin.<br /><br /></li></ul> **Note:** Bu anahtarlar isteğe bağlı olsa da, sağladığınız daha fazla bilgi, konum sonuçları daha doğru olur.<br /><br /> **Note:** Kullanıcının coğrafi konumunu her zaman belirtmeniz önerilir. İstemcinin IP adresi kullanıcının fiziksel konumunu doğru yansıtmıyorsa (örneğin istemci VPN kullanıyorsa), konumun belirtilmesi özellikle önemlidir. En iyi sonuçları elde etmek için, bu üst bilgiyi ve X-MSEdge-ClientIP üst bilgisini eklemelisiniz; ama en azından bu üst bilgiyi eklemeniz gerekir.|

> [!NOTE] 
> Kullanım Koşulları'nın, bu üst bilgilerin kullanımıyla ilgili olanlar da dahil olmak üzere tüm ilgili yasalara uymayı gerektirdiğini unutmayın. Örneğin, Avrupa gibi bazı yasama bölgelerinde kullanıcı cihazlarına izleme cihazları takmadan önce kullanıcının iznini almak gerekir.
  

## <a name="query-parameters"></a>Sorgu parametreleri  
İstek aşağıdaki sorgu parametrelerini içerebilir. Gerekli parametreler için gereken sütuna bakın. Sorgu parametrelerini URL kodlamanız gerekir.  
  
  
|Ad|Değer|Tür|Gerekli|  
|----------|-----------|----------|--------------|
|<a name="count" />sayısı|`offset` parametresi tarafından belirtilen dizinden başlayarak döndürülecek sonuç sayısı.|Dize|Hayır|   
|<a name="localCategories" />localCategories|İş kategorisine göre arama tanımlayan seçeneklerin listesi.  Bkz. [Yerel Iş kategorileri arama](local-categories.md)|Dize|Hayır|  
|<a name="mkt" />mkt|Sonuçların geldiği pazar. <br /><br />Olası Pazar değerlerinin listesi için bkz. Pazar kodları.<br /><br /> **Note:** Yerel Iş Arama API 'SI Şu anda yalnızca en-ABD pazar ve dilini desteklemektedir.<br /><br />|Dize|Yes|
|<a name="offset"/>boşluğu|`count` parametresi tarafından belirtilen sonuçları başlatacak dizin.|Tamsayı|Hayır|  
|<a name="query" />q|Kullanıcının arama terimi.|Dize|Hayır|  
|<a name="responseformat" />responseFormat|Yanıt için kullanılacak medya türü. Aşağıdakiler, büyük/küçük harf duyarsız değerlerdir.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Varsayılan değer JSON ' dır. Yanıtın içerdiği JSON nesneleri hakkında daha fazla bilgi için bkz. [Response Objects](#response-objects).<br /><br />  JsonLd belirtirseniz, yanıt gövdesi, arama sonuçlarını içeren JSON-LD nesnelerini içerir. JSON-LD hakkında daha fazla bilgi için bkz. [JSON-ld](https://json-ld.org/).|Dize|Hayır|  
|<a name="safesearch" />safeSearch|Yetişkinlere yönelik içeriği filtrelemek için kullanılan bir filtre. Aşağıdakiler, büyük/küçük harfe duyarlı olmayan olası filtre değerleridir.<br /><ul><li>Kapalı&mdash;yetişkinlere yönelik metin, resim veya videoların bulunduğu Web sayfalarını geri döndürün.<br /><br/></li><li>Orta&mdash;yetişkinlere yönelik metinler içeren Web sayfalarını, yetişkinlere yönelik görüntüleri veya videoları geri döndürür.<br /><br/></li><li>Katı&mdash;yetişkinlere yönelik metin, resim veya video içeren Web sayfaları döndürmez.</li></ul><br /> Varsayılan ayar Moderate değeridir.<br /><br /> **Note:** İstek bir marketten geliyorsa, Bing yetişkin ilkesinin `safeSearch` katı olarak ayarlanmasını gerektiriyorsa, Bing `safeSearch` değerini yoksayar ve katı kullanır.<br/><br/>**NOT:** `site:` sorgu işlecini kullanmanız durumunda, `safeSearch` parametresinin ayarına bakılmaksızın yanıtta yetişkinlere yönelik içerik bulunabilir. `site:` işlecini yalnızca sitenin içeriği hakkında bilgi sahibiyseniz ve senaryonuz, yetişkinlere yönelik içeriğin mevcut olma ihtimalini destekliyorsa kullanın. |Dize|Hayır|  
|<a name="setlang" />setLang|Kullanıcı arabirimi dizelerinde kullanılacak dil. Dili belirtirken ISO 639-1 2 harfi dil kodunu kullanın. Örneğin, Türkçe için dil kodu TR'dir. Varsayılan değer EN (İngilizce) ayarıdır.<br /><br /> İsteğe bağlı olsa da, her zaman dil belirtmelisiniz. Kullanıcı tarafından kullanıcı arabirimi dizelerinin farklı dilde görüntülenmesi istenmediği sürece, normalde `setLang` parametresini `mkt` parametresiyle aynı dile ayarlarsınız.<br /><br /> Bu parametre ve [Accept-Language](#acceptlanguage) üst bilgisi karşılıklı olarak birbirini dışlar. İkisini birlikte belirtmeyin.<br /><br /> Kullanıcı arabirimi dizesi, kullanıcı arabiriminde etiket olarak kullanılan dizedir. JSON yanıt nesnelerinde çok az kullanıcı arabirimi dizesi vardır. Ayrıca, yanıt nesnelerinde Bing.com özelliklerine yönelik bağlantılar da belirtilen dildedir.|Dize|Hayır| 


## <a name="response-objects"></a>Yanıt nesneleri  
Aşağıda, yanıtın dahil olabileceği JSON yanıt nesneleri verilmiştir. İstek başarılı olursa, yanıttaki en üst düzey nesne [SearchResponse](#searchresponse) nesnesidir. İstek başarısız olursa, en üst düzey nesne [errorResponse](#errorresponse) nesnesidir.


|Nesne|Açıklama|  
|------------|-----------------|  
|[Koy](#place)|Restoran veya otel gibi yerel bir işletme hakkındaki bilgileri tanımlar.|  

  
### <a name="error"></a>Hata  
Oluşan hatayı tanımlar.  
  
|Öğe|Açıklama|Tür|  
|-------------|-----------------|----------|  
|<a name="error-code" />kodu|Hata kategorisini tanımlayan hata kodu. Olası kodların listesi için bkz. [hata kodları](#error-codes).|Dize|  
|<a name="error-message" />iletisi|Hatanın açıklaması.|Dize|  
|<a name="error-moredetails" />ayrıntılı Ayrıntılar|Hata hakkında ek bilgi sağlayan bir açıklama.|Dize|  
|<a name="error-parameter" />parametresi|İstekte hataya neden olan sorgu parametresi.|Dize|  
|<a name="error-subcode" />alt kod|Hatayı tanımlayan hata kodu. Örneğin, `code` ınvalidrequest ise, `subCode` Parametergeçersiz veya Parameterınvalidvalue olabilir. |Dize|  
|<a name="error-value" />değeri|Sorgu parametresinin değeri geçerli değil.|Dize|  
  

### <a name="errorresponse"></a>ErrorResponse  
İstek başarısız olduğunda yanıtın içerdiği en üst düzey nesne.  
  
|Ad|Değer|Tür|  
|----------|-----------|----------|  
|_type|İpucu yazın.|Dize|  
|<a name="errors" />hataları|İsteğin başarısız olma nedenlerini betimleyen hataların listesi.|[Hata](#error)[]|  

  
  
### <a name="license"></a>Lisans  
Metin veya fotoğrafın kullanılabileceği lisansı tanımlar.  
  
|Ad|Değer|Tür|  
|----------|-----------|----------|  
|ad|Lisansın adı.|Dize|  
|url|Kullanıcının lisans hakkında daha fazla bilgi alabileceğiniz bir Web sitesinin URL 'SI.<br /><br /> Köprü oluşturmak için adı ve URL 'YI kullanın.|Dize|  


### <a name="link"></a>Bağlantı  
Bir köprünün bileşenlerini tanımlar.  
  
|Ad|Değer|Tür|  
|----------|-----------|----------|  
|_type|İpucu yazın.|Dize|  
|metin|Görüntü metni.|Dize|  
|url|BIR URL. Köprü oluşturmak için URL 'YI ve görüntü metnini kullanın.|Dize|  
  


  
### <a name="organization"></a>Kuruluş  
Bir yayımcıyı tanımlar.  
  
Bir yayımcının adlarını veya Web sitelerini veya her ikisini de sağlayabileceğini unutmayın.  
  
|Ad|Değer|Tür|  
|----------|-----------|----------|  
|ad|Yayımcının adı.|Dize|  
|url|Yayımcının Web sitesinin URL 'SI.<br /><br /> Yayımcının bir Web sitesi sağlayamadığını unutmayın.|Dize|  
  
  

### <a name="place"></a>Yerleştir  
Restoran veya otel gibi yerel bir işletme hakkındaki bilgileri tanımlar.  
  
|Ad|Değer|Tür|  
|----------|-----------|----------|  
|_type|Aşağıdakilerden birine ayarlanmış olabilecek tür İpucu:<br /><br /><ul><li>Otel</li><li>Yerel Iş<br /></li><li>Restoran</ul><li>|Dize|  
|address|Varlığın bulunduğu posta adresi.|PostalAddress|  
|Entitypresentationınfo|Varlığın türünü belirlemede kullanabileceğiniz ipuçları gibi varlıkla ilgili ek bilgiler. Örneğin, bir restoran veya otel olup olmadığı. `entityScenario` alanı ListItem olarak ayarlanır.|Entitypresentationınfo|  
|ad|Varlığın adı.|Dize|  
|unuzdaki|Varlığın telefon numarası.|Dize|  
|url|Varlığın Web sitesinin URL 'SI.<br /><br /> Tıklamadan kullanıcıyı varlığın Web sitesine götüren bir köprü oluşturmak için bu URL 'YI varlığın adıyla birlikte kullanın.|Dize|  
|webSearchUrl|Bu yer için Bing 'in arama sonucunun URL 'SI.|Dize| 
  
  
### <a name="querycontext"></a>queryContext  
İstek için Bing tarafından kullanılan sorgu bağlamını tanımlar.  
  
|Öğe|Açıklama|Tür|  
|-------------|-----------------|----------|  
|Adultınkatlanmış|Belirtilen sorgunun yetişkinlere yönelik amaç olup olmadığını gösteren bir Boole değeri. Sorguda yetişkinlere yönelik amaç varsa değer **true** 'dur; Aksi takdirde, **false**.|Boole|  
|alterationOverrideQuery|Bing 'in özgün dizeyi kullanmasına zorlamak için kullanılacak sorgu dizesi. Örneğin, sorgu dizesi *downwind salalıyorsa*, geçersiz kılma sorgu dizesi *+ sallama*olur. *% 2Bsaling + downrüzgar*ile sonuçlanan sorgu dizesini kodlamayı unutmayın.<br /><br /> Bu alan, yalnızca özgün sorgu dizesinde yazım hatası varsa dahil edilir.|Dize|  
|alteredQuery|Sorguyu gerçekleştirmek için Bing tarafından kullanılan sorgu dizesi. Özgün sorgu dizesinde yazım hataları yer alıyorsa Bing değiştirilen sorgu dizesini kullanır. Örneğin, sorgu dizesi `saling downwind`, değiştirilen sorgu dizesi `sailing downwind`olur.<br /><br /> Bu alan, yalnızca özgün sorgu dizesinde yazım hatası varsa dahil edilir.|Dize|  
|askUserForLocation|Bing 'in, kullanıcının konumunun doğru sonuçlar sağlamasını gerektirip gerektirmediğini belirten bir Boole değeri. Kullanıcının konumunu [X-MSEdge-ClientIP](#clientip) ve [x-Search-location](#location) üst bilgilerini kullanarak belirttiyseniz, bu alanı yoksayabilirsiniz.<br /><br /> "Bugün hava durumu" veya "Yakınımdaki Restoran" gibi konum duyarlı sorgularda, bu alan doğru sonuçlar sağlamak için kullanıcının konumuna ihtiyacı olan bu alan **true**olarak ayarlanır.<br /><br /> Konumu içeren konum duyarlı sorgular için (örneğin, "Seattle Hava durumu"), bu alan **false**olarak ayarlanır. Bu alan ayrıca "en iyi satıcılar" gibi konum duyarlı olmayan sorgular için **false** olarak ayarlanır.|Boole|  
|originalQuery|İstekte belirtilen şekilde sorgu dizesi.|Dize|  

### <a name="identifiable"></a>Maya

|Ad|Değer|Tür|  
|-------------|-----------------|----------|
|id|Kaynak tanımlayıcısı|Dize|
 
### <a name="rankinggroup"></a>RankingGroup
Mainline gibi bir arama sonuçları grubunu tanımlar.

|Ad|Değer|Tür|  
|-------------|-----------------|----------|
|items|Grupta görüntülenecek arama sonuçlarının listesi.|Rankingıtem|

### <a name="rankingitem"></a>Rankingıtem
Görüntülenecek bir arama sonuç öğesi tanımlar.

|Ad|Değer|Tür|  
|-------------|-----------------|----------|
|Resultındex|Görüntülenecek cevap içindeki öğenin sıfır tabanlı dizini. Öğe bu alanı içermiyorsa, yanıtdaki tüm öğeleri görüntüleyin. Örneğin, haber yanıtında tüm haber makalelerini görüntüleyin.|Tamsayı|
|answerType|Görüntülenecek öğeyi içeren yanıt. Örneğin, Haberler.<br /><br />SearchResponse nesnesinde yanıtı bulmak için türü kullanın. Tür, SearchResponse alanının adıdır.<br /><br /> Ancak, yanıt türünü yalnızca bu nesne değer alanını içeriyorsa kullanın; Aksi takdirde, yoksayabilirsiniz.|Dize|
|Textualındex|Görüntülenecek textualAnswers içindeki yanıtın dizini.| İşaretsiz tamsayı|
|değer|Görüntülenecek yanıtı veya görüntülenecek yanıtın bir öğesini tanımlayan KIMLIK. KIMLIK bir yanıt tanımlarsa, yanıtın tüm öğelerini görüntüleyin.|Maya|

### <a name="rankingresponse"></a>RankingResponse  
Arama sonuçları sayfasının içeriğini ve ne sırada yerleştirileceğini tanımlar.  
  
|Ad|Değer|  
|----------|-----------|  
|<a name="ranking-mainline" />ana hat|Ana satırda görüntülenecek arama sonuçları.|  
|<a name="ranking-pole" />direği|En sık görülen işleme (örneğin, ana hat ve kenar çubuğu üzerinde görüntülenir) uygun olması gereken arama sonuçları.|  
|<a name="ranking-sidebar" />kenar çubuğu|Kenar çubuğunda görüntülenecek arama sonuçları.| 

### <a name="searchresponse"></a>SearchResponse  
İstek başarılı olduğunda yanıtın içerdiği en üst düzey nesneyi tanımlar.  
  
Hizmet bir hizmet reddi saldırısından şüpheleniyorsa, isteğin başarılı olacağını unutmayın (HTTP durum kodu 200 Tamam); Ancak, yanıt gövdesi boş olur.  
  
|Ad|Değer|Tür|  
|----------|-----------|----------|  
|_type|SearchResponse olarak ayarlanan tip ipucu.|Dize|  
|yer|Arama sorgusuyla ilgili varlıkların listesi.|JSON nesnesi|  
|queryContext|İstek için Bing tarafından kullanılan sorgu dizesini içeren bir nesne.<br /><br /> Bu nesne, Kullanıcı tarafından girildiği şekilde sorgu dizesini içerir. Sorgu dizesi yazım hatası içeriyorsa sorgu için kullanılan değiştirilmiş bir sorgu dizesi de içerebilir.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Hata kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir.  
  
|Durum kodu|Açıklama|  
|-----------------|-----------------|  
|200|Başarılı.|  
|400|Sorgu parametrelerinden biri eksik veya geçersiz.|  
|401|Abonelik anahtarı eksik veya geçersiz.|  
|403|Kullanıcının kimliği doğrulanır (örneğin, geçerli bir abonelik anahtarı kullanmışsa), ancak istenen kaynak için izinleri yoktur.<br /><br /> Çağıran, her ay için sorguları aşarsa, bu durum da bu durumu döndürebilir.|  
|410|İstek HTTPS protokolü yerine HTTP kullandı. Yalnızca HTTPS desteklenen protokoldür.|  
|429|Çağıran, saniye başına sorgu sorgularını aştı.|  
|500|Beklenmeyen sunucu hatası.|

İstek başarısız olursa, yanıt hatanın nedenini açıklayan [hata](#error) nesnelerinin bir listesini Içeren bir [errorResponse](#errorresponse) nesnesi içerir. Hata bir parametreyle ilişkiliyse, `parameter` alanı sorun olan parametreyi tanımlar. Hata bir parametre değeriyle ilişkiliyse, `value` alanı geçerli olmayan değeri tanımlar.

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
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP durum kodu 500 ' dir.
|Invalidrequest|ParameterMissing<br/>Parameterınvalidvalue<br/>HttpNotAllowed<br/>Engellendi|İsteğin herhangi bir bölümü geçerli değilse Bing, ınvalidrequest döndürüyor. Örneğin, gerekli bir parametre eksik veya bir parametre değeri geçerli değil.<br/><br/>Hata ParameterMissing veya Parameterınvalidvalue ise, HTTP durum kodu 400 ' dir.<br/><br/>HTTPS yerine HTTP protokolünü kullanırsanız, Bing HttpNotAllowed öğesini döndürür ve HTTP durum kodu 410 ' dir.
|Ratelimitexcebaşında|Alt kod yok|Bing, sorgu/saniye (QPS) veya aylık sorgu (QPM) kotası her aşışınızda Ratelimitexceden başına döndürür.<br/><br/>QPS 'yi aşarsanız Bing, 429 HTTP durum kodunu döndürür ve QPM 'yi aşarsanız Bing, 403 döndürür.
|Invalidauthorleştirme|AuthorizationMissing<br/>Authorizationartıklık|Bing çağıranın kimliğini doğrulayamayan Bing, ınvalidauthortıcıyla geri döndürür. Örneğin, `Ocp-Apim-Subscription-Key` üstbilgisi eksik veya abonelik anahtarı geçerli değil.<br/><br/>Birden fazla kimlik doğrulama yöntemi belirtirseniz artıklık oluşur.<br/><br/>Hata eksik ise, HTTP durum kodu 401 ' dir.
|InsufficientAuthorization|AuthorizationDisabled<br/>Authorization, zaman aşımına uğradı|Çağıranın kaynağa erişim izni olmadığında Bing, InsufficientAuthorization döndürür. Abonelik anahtarı devre dışı bırakılmışsa veya süresi dolmuşsa bu durum oluşabilir. <br/><br/>Hata InsufficientAuthorization ise, HTTP durum kodu 403 ' dir.

## <a name="next-steps"></a>Sonraki adımlar
- [Yerel Iş araması hızlı başlangıç](quickstarts/local-quickstart.md)
- [Yerel Iş arama Java hızlı başlangıç](quickstarts/local-search-java-quickstart.md)
- [Yerel Iş arama düğümü hızlı başlangıç](quickstarts/local-search-node-quickstart.md)
- [Yerel Iş arama Python hızlı başlangıç](quickstarts/local-search-python-quickstart.md)
