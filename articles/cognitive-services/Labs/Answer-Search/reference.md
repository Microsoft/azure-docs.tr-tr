---
title: Proje Yanıtı Arama başvurusu
titlesuffix: Azure Cognitive Services
description: Proje yanıtı arama uç noktası için başvuru.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: reference
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 28449435479aef0d6a1d8aee3e53de1a78f401b3
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705323"
---
# <a name="project-answer-search-v7-reference"></a>Proje yanıtı arama v7 başvurusu

`searchResponse` Bing yanıtı searchapı bir sorgu parametresi alır ve: `facts` veya `entities`ile `answerType`döndürür. 

Yanıt araması API 'sini kullanan uygulamalar bir sorgu parametresinde önizlemesi yapılacak bir URL ile uç noktaya istek gönderir.  İstek, ve `q=searchTerm` *OCP-apim-Subscription-Key* üst bilgisini içermelidir.   

JSON yanıtı, arama nesnesiyle ilgili ayrıntıları içeren olgular ve varlıklar için ayrıştırılabilir.

## <a name="endpoint"></a>Uç Nokta
Yanıt arama sonuçları istemek için aşağıdaki uç noktaya bir istek gönderin. Daha fazla belirtim tanımlamak için üstbilgiler ve URL parametreleri kullanın.

Uç nokta al: 
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

```

İsteğin HTTPS protokolünü kullanması ve aşağıdaki sorgu parametresini içermesi gerekir:
-  `q=<URL>`-Arama nesnesini tanımlayan sorgu

İsteklerin nasıl yapılacağını gösteren örnekler için bkz [ C# . hızlı başlangıç](c-sharp-quickstart.md) veya [Java hızlı başlangıç](java-quickstart.md). 

Aşağıdaki bölümlerde, yanıt nesneleri, sorgu parametreleri ve arama sonuçlarını etkileyen üstbilgiler hakkında teknik ayrıntılar sağlanmaktadır. 
  
İsteklerin içermesi gereken üstbilgiler hakkında daha fazla bilgi için bkz. [üstbilgiler](#headers).  
  
İsteklerin içermesi gereken sorgu parametreleri hakkında daha fazla bilgi için bkz. [Query Parameters](#query-parameters).  
  
Yanıtın içerdiği JSON nesneleri hakkında daha fazla bilgi için bkz. [Response Objects](#response-objects).

Sorgu URL 'SI uzunluğu üst sınırı 2.048 karakterdir. URL uzunluğunun sınırı aşmadığından emin olmak için, sorgu parametrelerinizin en fazla uzunluğu 1.500 karakterden az olmalıdır. URL 2.048 karakteri aşarsa, sunucu 404 ' i döndürür.  

İzin verilen kullanım ve sonuçların görüntülenmesi hakkında daha fazla bilgi için bkz. [kullanımı ve görüntüleme gereksinimleri](use-display-requirements.md). 

> [!NOTE]
> Diğer arama API 'Leri için anlamlı olan bazı istek üstbilgileri URL önizlemeyi etkilemez
> - Pragma – çağıran, URL önizlemesinin önbellek kullanıp kullanmadığını denetler
> - Cache-Control – çağıran, URL önizlemesinin önbellek kullanıp kullanmadığını denetler
> - User-Agent
> 
> Ayrıca, bazı parametreler URL önizleme API 'si için şu anda anlamlı değildir, ancak gelecekte iyileştirilmiş Genelleştirme için kullanılabilir. 
 
## <a name="headers"></a>Üst bilgiler  
Bir istek ve yanıtın içerebilme üstbilgileri aşağıda verilmiştir.  
  
|Üstbilgi|Açıklama|  
|------------|-----------------|  
|Kabul|İsteğe bağlı istek üst bilgisi.<br /><br /> Varsayılan medya türü uygulama/JSON ' dır. Yanıtın [JSON-ld](https://json-ld.org/)kullanmasını belirtmek için Accept üst bilgisini Application/ld + JSON olarak ayarlayın.|  
|<a name="acceptlanguage" />Accept-Language|İsteğe bağlı istek üst bilgisi.<br /><br /> Kullanıcı arabirimi dizelerinde kullanılacak virgülle sınırlanmış bir dil listesi. Liste, tercih edilme durumuna göre azalan düzende sıralanır. Beklenen biçim de içinde olmak üzere daha fazla bilgi için bkz. [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Bu üst bilgi ve [setLang](#setlang) sorgu parametresi karşılıklı olarak birbirini dışlar. İkisini birlikte belirtmeyin.<br /><br /> Bu üstbilgiyi ayarlarsanız, CC sorgu parametresini de belirtmeniz gerekir. Hangi pazardan sonuç döndürüleceğini belirlemek için, Bing listeden bulduğu ilk desteklenen dili kullanır ve bunu `cc` parametresinin değeriyle birleştirir. Liste desteklenen bir dil içermiyorsa, Bing isteği destekleyen en yakın dili ve pazarı bulur ya da sonuçlar için toplu veya varsayılan bir pazar kullanır. Bing'in kullandığı pazarı saptamak için BingAPIs-Market üst bilgisine bakın.<br /><br /> Ancak birden çok dil belirtirseniz bu üst bilgiyi ve `cc` sorgu parametresini kullanın. Aksi takdirde, [mkt](#mkt) ile [setLang](#setlang) sorgu parametrelerini kullanın.<br /><br /> Kullanıcı arabirimi dizesi, kullanıcı arabiriminde etiket olarak kullanılan dizedir. JSON yanıt nesnelerinde çok az kullanıcı arabirimi dizesi vardır. Yanıt nesnelerinde Bing.com özelliklerine yönelik bağlantılar da belirtilen dildedir.|  
|<a name="market" />BingAPIs-Market|Yanıt üst bilgisi.<br /><br /> İstek tarafından kullanılan pazar. Biçimi şöyledir: \<languageCode\>-\<countryCode\>. Örneğin, tr-TR.|  
|<a name="traceid" />BingAPIs-TraceId|Yanıt üst bilgisi.<br /><br /> İsteğin ayrıntılarını içeren günlük girdisinin kimliği. Hata oluştuğunda, bu kimliği yakalayın. Sorunu belirleyemez ve çözemezseniz, Destek ekibine diğer bilgilerle birlikte bu kimliği de sağlayın.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Gerekli istek üst bilgisi.<br /><br /> [Bilişsel Hizmetler](https://www.microsoft.com/cognitive-services/)'de bu hizmete kaydolduğunuzda aldığınız abonelik anahtarı.|  
|<a name="pragma" />Pragma|İsteğe bağlı istek üst bilgisi<br /><br /> Varsayılan olarak, Bing önbelleğe alınmış içeriği (varsa) döndürür. Bing'in önbelleğe alınmış içeriği döndürmesini önlemek için, Pragma üst bilgisini no-cache olarak ayarlayın (örneğin, Pragma: no-cache).
|<a name="useragent" />User-Agent|İsteğe bağlı istek üst bilgisi.<br /><br /> İsteği başlatan kullanıcı aracısı. Bing, mobil kullanıcılara iyileştirilmiş bir deneyim sağlamak için kullanıcı aracısını kullanır. İsteğe bağlı olsa da, bu üst bilgiyi her zaman belirtmeniz önerilir.<br /><br /> User-agent, yaygın olarak kullanılan tarayıcılardan gönderilen dizeyle aynı olmalıdır. Kullanıcı aracıları hakkında bilgi için bkz. [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Aşağıda örnek user-agent dizelerini bulabilirsiniz.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|İsteğe bağlı istek ve yanıt üst bilgisi.<br /><br /> Bing, kullanıcılara tüm Bing API çağrılarında tutarlı bir davranış sağlamak için bu üst bilgiyi kullanır. Bing sık sık yeni özellikler ve geliştirmeler dağıtır ve farklı dağıtımlarda trafik ataması yapmak için anahtar olarak istemci kimliğini kullanır. Bir kullanıcı için birden çok istekte aynı istemci kimliğini kullanmazsanız, Bing kullanıcıyı birden çok çakışan dağıtıma atayabilir. Birden çok çakışan dağıtıma eklenmek, tutarsız bir kullanıcı deneyimine yol açabilir. Örneğin, ikinci isteğin dağıtım ataması ilkinden farklıysa, beklenmeyen bir deneyim yaşanabilir. Ayrıca, Bing istemci kimliğini kullanarak web sonuçlarını istemci kimliğinin arama geçmişine uyarlayabilir ve bu sayede kullanıcıya daha zengin bir deneyim sağlayabilir.<br /><br /> Bing, istemci kimliği tarafından oluşturulan etkinliği analiz ederek sonuç derecelendirmelerini geliştirmeye yardımcı olması için de bu üst bilgiyi kullanabilir. İlgi geliştirmeleri Bing API'lerinin daha kaliteli sonuçlar vermesine yardımcı olur ve böylelikle API tüketicisi için daha yüksek tıklama oranları getirir.<br /><br /> **ÖNEMLI** İsteğe bağlı olsa da, bu üst bilgiyi gerekli kabul etmelisiniz. Aynı son kullanıcı ile cihaz bileşimi için birden çok istekte aynı istemci kimliğini kullanıldığında, 1) API tüketicisi tutarlı bir kullanıcı deneyimi elde eder ve 2) Bing API'lerinden daha kaliteli sonuçlar alındığından tıklama oranları daha yüksek olur.<br /><br /> Bu üst bilgi için geçerli olan temel kullanım kuralları şunlardır:<br /><ul><li>Cihazda uygulamanızı kullanan her kullanıcının Bing tarafından oluşturulan benzersiz bir istemci kimliği olmalıdır.<br /><br/>İsteğe bu üst bilgiyi eklemezseniz, Bing bir kimlik oluşturur ve bu kimliği X-MSEdge-ClientID yanıt üst bilgisinde döndürür. İsteğe bu üst bilgiyi EKLEMEMENİZ gereken tek durum, söz konusu cihazda kullanıcının uygulamanızı ilk kez kullanmasıdır.<br /><br/></li><li>Cihazda uygulamanızın bu kullanıcı için yaptığı her Bing API'si isteğinde istemci kimliğini kullanın.<br /><br/></li><li>**ÖZELLIKLE** Bu Istemci KIMLIĞININ, herhangi bir kimlik doğrulayan Kullanıcı hesabı bilgisine bağlanabilir olmadığından emin olmanız gerekir.</li><br/><li>İstemci kimliğinin kalıcı olmasını sağlayın. Tarayıcı uygulamasında kimliği kalıcı hale getirmek için, tüm oturumlarda kimliğin kullanmasını sağlayacak bir kalıcı HTTP tanımlama bilgisi kullanın. Oturum tanımlama bilgisi kullanmayın. Mobil uygulamalar gibi diğer uygulamalarda, kimliği kalıcı hale getirmek için cihazın kalıcı depolamasını kullanın.<br /><br/>Kullanıcı o cihazda uygulamanızı yeniden kullandığında, kalıcı hale getirdiğiniz istemci kimliğini alın.</li></ul><br /> **NOT:** Bing yanıtları bu üstbilgiyi içermeyebilir veya içermeyebilir. Yanıt bu üst bilgiyi içeriyorsa, istemci kimliğini yakalayın ve o cihazda kullanıcı için bunu izleyen tüm Bing isteklerinde onu kullanın.<br /><br /> **NOT:** X-MSEdge-ClientID ' yi eklerseniz, isteğe tanımlama bilgilerini dahil etmeniz gerekir.|  
|<a name="clientip" />X-MSEdge-ClientIP|İsteğe bağlı istek üst bilgisi.<br /><br /> İstemci cihazının IPv4 veya IPv6 adresi. IP adresi, kullanıcının konumunu bulmak için kullanılır. Bing konum bilgisini kullanarak güvenli arama davranışını saptar.<br /><br /> **NOT:** İsteğe bağlı olsa da, bu üstbilgiyi ve X-Search-location üst bilgisini her zaman belirtmeniz önerilir.<br /><br /> Adresi karartmayın (örneğin, son sekiz karakteri 0'la değiştirerek). Adresin karartılması, cihazın gerçek konumuna yakın olmayan bir konum sonucu verir ve bu da Bing'in hatalı sonuçlar sağlamasına yol açabilir.|  
|<a name="location" />X-Search-Location|İsteğe bağlı istek üst bilgisi.<br /><br /> İstemcinin coğrafi konumunu açıklayan noktalı virgülle sınırlanmış anahtar/değer çifti listesi. Bing konum bilgisini kullanarak güvenli arama davranışını saptar ve ilgili yerel içeriği döndürür. Anahtar/değer çiftini \<anahtar\>:\<değer\> olarak belirtin. Aşağıda, kullanıcının konumunu belirtmek için kullandığınız anahtarlar gösterilir.<br /><br /><ul><li>&mdash;istemci konumunu, derece cinsinden enlem. Enlem -90,0 değerinden büyük veya bu değere eşit ve +90,0 değerinden küçük veya bu değere eşit olmalıdır. Negatif değerler güney enlemlerini ve pozitif değerler de kuzey enlemlerini gösterir.<br /><br /></li><li>istemci&mdash;konumunun boyda derece cinsinden uzunluğu. Boylam -180,0 değerinden büyük veya bu değere eşit ve +180,0 değerinden küçük veya bu değere eşit olmalıdır. Negatif değerler batı boylamlarını ve pozitif değerler de doğu boylamlarını gösterir.<br /><br /></li><li>koordinatların yatay doğruluğunu belirten, ölçü cinsinden yarıçapı yeniden&mdash; yapın. Cihazın konum hizmeti tarafından döndürülen değeri geçirin. Normalde değerler GPS/Wi-Fi için 22 m, baz istasyonu triangülasyonu için 380 m ve ters IP araması için 18.000 m'dir.<br /><br /></li><li>&mdash; istemci konumda olduğu zaman UTC UNIX zaman damgası. (UNIX zaman damgası 1 Ocak 1970'den başlayarak saniye sayısıdır.)<br /><br /></li><li>head&mdash;İsteğe bağlı. İstemcinin göreli seyahat yönü. Gerçek kuzeye göre saat yönünün tersine 0 ile 360 derece arasında bir seyahat yönü belirtin. Bu anahtarı ancak `sp` anahtarı sıfırdan farklı bir değer olduğunda belirtin.<br /><br /></li><li>&mdash; istemci cihazının yolculukta olduğu, saniye başına düşen yatay hız (hız).<br /><br /></li><li>alt&mdash; , istemci cihazının ölçü cinsinden yüksekliği.<br /><br /></li><li>are&mdash;İsteğe bağlı. Koordinatların dikey doğruluğunu belirten metre cinsinden yarıçap. Yarıçap varsayılan olarak 50 Kiloters 'e sahiptir. Bu anahtarı ancak `alt` anahtarı belirttiğiniz durumda belirtin.<br /><br /></li></ul> **NOT:** Bu anahtarlar isteğe bağlı olsa da, sağladığınız daha fazla bilgi, konum sonuçları daha doğru olur.<br /><br /> **NOT:** Kullanıcının coğrafi konumunu her zaman belirtmeniz önerilir. İstemcinin IP adresi kullanıcının fiziksel konumunu doğru yansıtmıyorsa (örneğin istemci VPN kullanıyorsa), konumun belirtilmesi özellikle önemlidir. En iyi sonuçları elde etmek için, bu üst bilgiyi ve X-MSEdge-ClientIP üst bilgisini eklemelisiniz; ama en azından bu üst bilgiyi eklemeniz gerekir.|

> [!NOTE] 
> Kullanım Koşulları'nın, bu üst bilgilerin kullanımıyla ilgili olanlar da dahil olmak üzere tüm ilgili yasalara uymayı gerektirdiğini unutmayın. Örneğin, Avrupa gibi bazı yasama bölgelerinde kullanıcı cihazlarına izleme cihazları takmadan önce kullanıcının iznini almak gerekir.
  

## <a name="query-parameters"></a>Sorgu parametreleri  
İstek aşağıdaki sorgu parametrelerini içerebilir. Gerekli parametreler için gereken sütuna bakın. Sorgu parametrelerini URL kodlamanız gerekir.  
  
  
|Ad|Value|Type|Gerekli|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Sonuçların geldiği pazar. <br /><br />Olası Pazar değerlerinin listesi için bkz. Pazar kodları.<br /><br /> **NOT:** URL önizleme API 'SI Şu anda yalnızca en-US Pazar ve dilini desteklemektedir.<br /><br />|Dize|Evet|  
|<a name="query" />ç|Önizlenecek URL|Dize|Evet|  
|<a name="responseformat" />responseFormat|Yanıt için kullanılacak medya türü. Aşağıdakiler, büyük/küçük harf duyarsız değerlerdir.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Varsayılan değer JSON ' dır. Yanıtın içerdiği JSON nesneleri hakkında daha fazla bilgi için bkz. [Response Objects](#response-objects).<br /><br />  JsonLd belirtirseniz, yanıt gövdesi, arama sonuçlarını içeren JSON-LD nesnelerini içerir. JSON-LD hakkında daha fazla bilgi için bkz. [JSON-ld](https://json-ld.org/).|Dize|Hayır|  
|<a name="safesearch" />safeSearch|Yetişkinlere yönelik içeriği filtrelemek için kullanılan bir filtre. Aşağıdakiler, büyük/küçük harfe duyarlı olmayan olası filtre değerleridir.<br /><ul><li>Yetişkinlere yönelik metin, resim veya video içeren Web sayfalarınıgeridöndürün.&mdash;<br /><br/></li><li>Orta&mdash;, yetişkinlere yönelik metinler içeren Web sayfalarını, yetişkinlere yönelik görüntüleri veya videoları geri döndürür.<br /><br/></li><li>Sıkı&mdash;metin, resim veya video içeren Web sayfaları döndürün.</li></ul><br /> Varsayılan ayar Moderate değeridir.<br /><br /> **NOT:** İstek, Bing yetişkin ilkesinin katı olarak ayarlanmasını gerektiren `safeSearch` bir marketten geliyorsa, Bing `safeSearch` değeri yoksayar ve katı kullanır.<br/><br/>**NOT:** `site:` Sorgu işlecini kullanırsanız, `safeSearch` sorgu parametresinin ne şekilde ayarlandığına bakılmaksızın yanıtın yetişkinlere yönelik içerik içerebileceği bir şansınız vardır. `site:` işlecini yalnızca sitenin içeriği hakkında bilgi sahibiyseniz ve senaryonuz, yetişkinlere yönelik içeriğin mevcut olma ihtimalini destekliyorsa kullanın. |Dize|Hayır|  
|<a name="setlang" />setLang|Kullanıcı arabirimi dizelerinde kullanılacak dil. Dili belirtirken ISO 639-1 2 harfi dil kodunu kullanın. Örneğin, Türkçe için dil kodu TR'dir. Varsayılan değer EN (İngilizce) ayarıdır.<br /><br /> İsteğe bağlı olsa da, her zaman dil belirtmelisiniz. Kullanıcı tarafından kullanıcı arabirimi dizelerinin farklı dilde görüntülenmesi istenmediği sürece, normalde `setLang` parametresini `mkt` parametresiyle aynı dile ayarlarsınız.<br /><br /> Bu parametre ve [Accept-Language](#acceptlanguage) üst bilgisi karşılıklı olarak birbirini dışlar. İkisini birlikte belirtmeyin.<br /><br /> Kullanıcı arabirimi dizesi, kullanıcı arabiriminde etiket olarak kullanılan dizedir. JSON yanıt nesnelerinde çok az kullanıcı arabirimi dizesi vardır. Ayrıca, yanıt nesnelerinde Bing.com özelliklerine yönelik bağlantılar da belirtilen dildedir.|Dize|Hayır| 


## <a name="response-objects"></a>Yanıt nesneleri  
Yanıt şeması, Web Araması API 'sinde olduğu gibi bir [Web sayfası] ya da ErrorResponse 'dır. İstek başarısız olursa, en üst düzey nesne [errorResponse](#errorresponse) nesnesidir.


|Object|Açıklama|  
|------------|-----------------|  
|Web|Önizlemenin özniteliklerini içeren üst düzey JSON nesnesi.|  
|Halde|Olgu içeren en üst düzey JSON nesnesi.| 
|[Varlıklar|Varlık ayrıntılarını içeren en üst düzey JSON nesnesi.| 

  
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
  
|Ad|Değer|Type|  
|----------|-----------|----------|  
|_tür|İpucu yazın.|Dize|  
|<a name="errors" />hatası|İsteğin başarısız olma nedenlerini betimleyen hataların listesi.|[Hata:](#error)|  

  
  
### <a name="license"></a>Lisans  
Metin veya fotoğrafın kullanılabileceği lisansı tanımlar.  
  
|Ad|Value|Type|  
|----------|-----------|----------|  
|name|Lisansın adı.|Dize|  
|url|Kullanıcının lisans hakkında daha fazla bilgi alabileceğiniz bir Web sitesinin URL 'SI.<br /><br /> Köprü oluşturmak için adı ve URL 'YI kullanın.|Dize|  
  

### <a name="licenseattribution"></a>Licenseattributıon  
Lisans attributıon için bir sözleşme kuralı tanımlar.  
  
|Ad|Value|Type|  
|----------|-----------|----------|  
|_tür|LicenseAttribution olarak ayarlanan bir tür ipucu.|Dize|  
|lisans|İçeriğin kullanılabileceği lisans.|[Lisan](#license)|  
|Licensenolet|Hedeflenen alanın yanında görüntülenecek lisans. Örneğin, "CC-BY-SA lisansı altında metin".<br /><br /> Lisansın ayrıntılarını açıklayan Web sitesine bir köprü oluşturmak için `license` , bu hesabın adını ve URL 'sini kullanın. Sonra, `licenseNotice` dizedeki lisans adını (örneğin, bilgi-sa) yeni oluşturduğunuz köprü ile değiştirin.|Dize|  
|mustBeCloseToContent|Kuralın içeriğini kuralın uygulandığı alana yakın bir yerde konumlandırıp yerleştirilmeyeceğini belirleyen bir Boole değeri. **True**ise içeriğin yakın yakınlığa yerleştirilmesi gerekir. **False**ise veya bu alan yoksa, içerik çağıranın kararına yerleştirilebilecek.|Boole değeri|  
|targetPropertyName|Kuralın geçerli olduğu alanın adı.|Dize|  
  

### <a name="link"></a>Bağlantı  
Bir köprünün bileşenlerini tanımlar.  
  
|Ad|Value|Type|  
|----------|-----------|----------|  
|_tür|İpucu yazın.|Dize|  
|text|Görüntü metni.|Dize|  
|url|BIR URL. Köprü oluşturmak için URL 'YI ve görüntü metnini kullanın.|Dize|  
  

### <a name="linkattribution"></a>LinkAttribution  
Bağlantı attributıon için bir sözleşme kuralı tanımlar.  
  
|Ad|Değer|Type|  
|----------|-----------|----------|  
|_tür|LinkAttribution olarak ayarlanan bir tür ipucu.|Dize|  
|mustBeCloseToContent|Kuralın içeriğini kuralın uygulandığı alana yakın bir yerde konumlandırıp yerleştirilmeyeceğini belirleyen bir Boole değeri. **True**ise içeriğin yakın yakınlığa yerleştirilmesi gerekir. **False**ise veya bu alan yoksa, içerik çağıranın kararına yerleştirilebilecek.|Boole değeri|  
|targetPropertyName|Kuralın geçerli olduğu alanın adı.<br /><br /> Bir hedef belirtilmemişse, atım varlığa bir bütün olarak uygulanır ve varlık sunumundaki hemen sonra da görüntülenmelidir. Bir hedef belirtmeyen birden çok metin ve bağlantı birleştirme kuralı varsa, bunları birleştirmeniz ve bir "veri kaynağından:" etiketini kullanarak görüntüetmeniz gerekir. Örneğin, "< sağlayıcısından veriler name1\> &#124; < sağlayıcı AD2\>".|Dize|  
|text|Atısyon metni.|Dize|  
|url|Sağlayıcının web sitesinin URL 'SI. Köprü `text` oluşturmak için ve URL kullanın.|Dize|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Medya atısyonu için bir sözleşme kuralı tanımlar.  
  
|Ad|Değer|Type|  
|----------|-----------|----------|  
|_tür|MediaAttribution olarak ayarlanan bir tür ipucu.|Dize|  
|mustBeCloseToContent|Kuralın içeriğini kuralın uygulandığı alana yakın bir yerde konumlandırıp yerleştirilmeyeceğini belirleyen bir Boole değeri. **True**ise içeriğin yakın yakınlığa yerleştirilmesi gerekir. **False**ise veya bu alan yoksa, içerik çağıranın kararına yerleştirilebilecek.|Boole değeri|  
|targetPropertyName|Kuralın geçerli olduğu alanın adı.|Dize|  
|url|Medya içeriğinin köprü oluşturmak için kullandığınız URL. Örneğin, hedef bir görüntü ise, görüntüyü tıklatılabilir hale getirmek için URL 'YI kullanırsınız.|Dize|  
  
  
  
### <a name="organization"></a>Kuruluş  
Bir yayımcıyı tanımlar.  
  
Bir yayımcının adlarını veya Web sitelerini veya her ikisini de sağlayabileceğini unutmayın.  
  
|Ad|Value|Type|  
|----------|-----------|----------|  
|name|Yayımcının adı.|Dize|  
|url|Yayımcının Web sitesinin URL 'SI.<br /><br /> Yayımcının bir Web sitesi sağlayamadığını unutmayın.|Dize|  
  
  

### <a name="webpage"></a>Web  
Önizleme aşamasında bir Web sayfası hakkındaki bilgileri tanımlar.  
  
|Ad|Value|Type|  
|----------|-----------|----------|
|name|HTML başlığı değil, sayfa başlığı|Dize|
|url|Gerçekten gezinmekte olan URL (istek yeniden yönlendirmeleri takip edebilir)|Dize|  
|description|Sayfanın ve içeriğin kısa açıklaması|Dize|  
|isFamilyFriendly|Web dizinindeki öğeler için en doğru; gerçek zamanlı getirme, bu algılamayı yalnızca URL 'ye göre değil, sayfa içeriğini değil|boolean|
|Primaryımageofpage/contentUrl|Önizlemeye dahil edilecek temsili bir görüntünün URL 'SI|Dize| 
  
  
### <a name="querycontext"></a>QueryContext  
İstek için Bing tarafından kullanılan sorgu bağlamını tanımlar.  
  
|Öğe|Açıklama|Type|  
|-------------|-----------------|----------|  
|Adultınkatlanmış|Belirtilen sorgunun yetişkinlere yönelik amaç olup olmadığını gösteren bir Boole değeri. Sorguda yetişkinlere yönelik amaç varsa değer **true** 'dur; Aksi takdirde, **false**.|Boole değeri|  
|alterationOverrideQuery|Bing 'in özgün dizeyi kullanmasına zorlamak için kullanılacak sorgu dizesi. Örneğin, sorgu dizesi *downwind salalıyorsa*, geçersiz kılma sorgu dizesi *+ sallama*olur. *% 2Bsaling + downrüzgar*ile sonuçlanan sorgu dizesini kodlamayı unutmayın.<br /><br /> Bu alan, yalnızca özgün sorgu dizesinde yazım hatası varsa dahil edilir.|Dize|  
|alteredQuery|Sorguyu gerçekleştirmek için Bing tarafından kullanılan sorgu dizesi. Özgün sorgu dizesinde yazım hataları yer alıyorsa Bing değiştirilen sorgu dizesini kullanır. Örneğin, sorgu dizesi ise `saling downwind`, değiştirilen sorgu dizesi `sailing downwind`olacaktır.<br /><br /> Bu alan, yalnızca özgün sorgu dizesinde yazım hatası varsa dahil edilir.|Dize|  
|askUserForLocation|Bing 'in, kullanıcının konumunun doğru sonuçlar sağlamasını gerektirip gerektirmediğini belirten bir Boole değeri. Kullanıcının konumunu [X-MSEdge-ClientIP](#clientip) ve [x-Search-location](#location) üst bilgilerini kullanarak belirttiyseniz, bu alanı yoksayabilirsiniz.<br /><br /> "Bugün hava durumu" veya "Yakınımdaki Restoran" gibi konum duyarlı sorgularda, bu alan doğru sonuçlar sağlamak için kullanıcının konumuna ihtiyacı olan bu alan **true**olarak ayarlanır.<br /><br /> Konumu içeren konum duyarlı sorgular için (örneğin, "Seattle Hava durumu"), bu alan **false**olarak ayarlanır. Bu alan ayrıca "en iyi satıcılar" gibi konum duyarlı olmayan sorgular için **false** olarak ayarlanır.|Boole değeri|  
|originalQuery|İstekte belirtilen şekilde sorgu dizesi.|Dize|  

### <a name="identifiable"></a>Maya

|Ad|Value|Type|  
|-------------|-----------------|----------|
|id|Kaynak tanımlayıcısı|Dize|
 
### <a name="rankinggroup"></a>RankingGroup
Mainline gibi bir arama sonuçları grubunu tanımlar.

|Ad|Value|Type|  
|-------------|-----------------|----------|
|items|Grupta görüntülenecek arama sonuçlarının listesi.|Rankingıtem|

### <a name="rankingitem"></a>Rankingıtem
Görüntülenecek bir arama sonuç öğesi tanımlar.

|Ad|Değer|Type|  
|-------------|-----------------|----------|
|Resultındex|Görüntülenecek cevap içindeki öğenin sıfır tabanlı dizini. Öğe bu alanı içermiyorsa, yanıtdaki tüm öğeleri görüntüleyin. Örneğin, haber yanıtında tüm haber makalelerini görüntüleyin.|Tamsayı|
|answerType|Görüntülenecek öğeyi içeren yanıt. Örneğin, Haberler.<br /><br />SearchResponse nesnesinde yanıtı bulmak için türü kullanın. Tür, SearchResponse alanının adıdır.<br /><br /> Ancak, yanıt türünü yalnızca bu nesne değer alanını içeriyorsa kullanın; Aksi takdirde, yoksayabilirsiniz.|Dize|
|Textualındex|Görüntülenecek textualAnswers içindeki yanıtın dizini.| İşaretsiz tamsayı|
|value|Görüntülenecek yanıtı veya görüntülenecek yanıtın bir öğesini tanımlayan KIMLIK. KIMLIK bir yanıt tanımlarsa, yanıtın tüm öğelerini görüntüleyin.|Maya|

### <a name="rankingresponse"></a>RankingResponse  
Arama sonuçları sayfasının içeriğini ve ne sırada yerleştirileceğini tanımlar.  
  
|Ad|Value|  
|----------|-----------|  
|<a name="ranking-mainline" />Ana hat|Ana satırda görüntülenecek arama sonuçları.|  
|<a name="ranking-pole" />kutup|En sık görülen işleme (örneğin, ana hat ve kenar çubuğu üzerinde görüntülenir) uygun olması gereken arama sonuçları.|  
|<a name="ranking-sidebar" />kutusuna|Kenar çubuğunda görüntülenecek arama sonuçları.| 


### <a name="searchresponse"></a>SearchResponse  
İstek başarılı olduğunda yanıtın içerdiği en üst düzey nesneyi tanımlar.  
  
Hizmet bir hizmet reddi saldırısından şüpheleniyorsa, isteğin başarılı olacağını unutmayın (HTTP durum kodu 200 Tamam); Ancak, yanıt gövdesi boş olur.  
  
|Ad|Değer|Type|  
|----------|-----------|----------|  
|_tür|SearchResponse olarak ayarlanan tip ipucu.|Dize|  
|Web|Önizlemeyi tanımlayan bir JSON nesnesi|dize|  
  
  
### <a name="textattribution"></a>TextAttribution  
Düz metin atısyonu için bir sözleşme kuralı tanımlar.  
  
|Ad|Value|Type|  
|----------|-----------|----------|  
|_tür|TextAttribution olarak ayarlanan bir tür ipucu.|Dize|  
|text|Atısyon metni.<br /><br /> Metin atısyonu, varlığa bir bütün olarak uygulanır ve varlık sunumunun hemen ardından gelmelidir. Hedef belirtmeyen birden çok metin veya bağlantı birleştirme kuralı varsa, bunları birleştirmeniz ve bir "veri kaynağından:" etiketini kullanarak görüntüetmeniz gerekir.|Dize| 


## <a name="error-codes"></a>Hata kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir.  
  
|Durum Kodu|Açıklama|  
|-----------------|-----------------|  
|200|Başarılı.|  
|400|Sorgu parametrelerinden biri eksik veya geçersiz.|  
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
- [C# hızlı başlangıcı](c-sharp-quickstart.md)
- [Java hızlı başlangıcı](java-quickstart.md)
- [Node hızlı başlangıcı](node-quickstart.md)
- [Python hızlı başlangıcı](python-quickstart.md)

