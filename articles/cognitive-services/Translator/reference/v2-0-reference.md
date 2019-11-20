---
title: Translator Metin Çevirisi API'si v 2.0
titleSuffix: Azure Cognitive Services
description: Translator Metin Çevirisi API'si v 2.0 için başvuru belgeleri.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242494"
---
# <a name="translator-text-api-v20"></a>Translator Metin Çevirisi API'si v 2.0

> [!IMPORTANT]
> Translator Metin Çevirisi API'si bu sürümü kullanımdan kaldırılmıştır. [Translator metin çevirisi API'si sürüm 3 ' ün belgelerini görüntüleyin](v3-0-reference.md).

Translator Metin Çevirisi API'si sürüm 2, uygulamalar, Web siteleri, araçlarınızla veya çok dilli kullanıcı deneyimleri sağlamak için diğer çözümlerle sorunsuzca tümleştirilebilir. Endüstri standartlarına göre, dil çevirisini ve metin okuma ve metin okuma gibi diğer dille ilgili görevleri gerçekleştirmek için herhangi bir donanım platformunda ve herhangi bir işletim sistemiyle kullanabilirsiniz. Daha fazla bilgi için bkz. [Translator metin çevirisi API'si](../translator-info-overview.md).

## <a name="getting-started"></a>Başlarken
Translator Metin Çevirisi API'si erişmek için [Microsoft Azure kaydolmanız](../translator-text-how-to-signup.md)gerekir.

## <a name="authentication"></a>Kimlik doğrulaması 
Translator Metin Çevirisi API'si yapılan tüm çağrılar, kimlik doğrulaması için bir abonelik anahtarı gerektirir. API üç kimlik doğrulama yöntemini destekler:

- Erişim belirteci. Kimlik doğrulama hizmetine bir POST isteği yaparak bir erişim belirteci oluşturmak için abonelik anahtarını kullanın. Ayrıntılar için belirteç hizmeti belgelerine bakın. @No__t-0 üst bilgisini veya `access_token` sorgu parametresini kullanarak erişim belirtecini Translator hizmetine geçirin. Erişim belirteci 10 dakika için geçerlidir. 10 dakikada bir yeni bir erişim belirteci alın ve 10 dakika boyunca yinelenen istekler için aynı erişim belirtecini kullanmaya devam edin.
- Doğrudan kullanılan bir abonelik anahtarı. Abonelik anahtarınızı, Translator Metin Çevirisi API'si isteğinize dahil `Ocp-Apim-Subscription-Key` üst bilgisinde bir değer olarak geçirin. Abonelik anahtarını doğrudan kullandığınızda, bir erişim belirteci oluşturmak için belirteç kimlik doğrulama hizmetini çağırmanız gerekmez.
- Azure bilişsel [Hizmetler çoklu hizmet aboneliği](https://azure.microsoft.com/pricing/details/cognitive-services/). Bu yöntem, birden fazla hizmete yönelik isteklerin kimliğini doğrulamak için tek bir gizli anahtar kullanmanıza olanak sağlar.
Çoklu hizmet gizli anahtarı kullandığınızda, isteğinize iki kimlik doğrulama üst bilgisi eklemeniz gerekir. İlk üstbilgi gizli anahtarı geçirir. İkinci üstbilgi, aboneliğinizle ilişkili bölgeyi belirtir:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

Çoklu hizmet metin API 'SI aboneliği için bölge gereklidir. Seçtiğiniz bölge, çok servis abonelik anahtarını kullandığınızda metin çevirisi için kullanabileceğiniz tek bölgedir. Azure portal, çoklu hizmet aboneliğiniz için kaydolduğunuzda seçtiğiniz bölge ile aynı olması gerekir.

Kullanılabilir bölgeler `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, 0, 1, 2, 3, 4, 5 ve 6 ' dır.

Abonelik anahtarınız ve erişim belirteciniz, görünümden gizlenmesi gereken gizli dizlardır.

## <a name="profanity-handling"></a>Küfür işleme
Normalde, çevirmen hizmeti kaynakta bulunan küfür 'ı korur. Küfür derecesi ve küfürlü sözcüklerini yapan bağlam, kültüre göre farklılık gösterir. Bu nedenle, hedef dildeki küfür derecesi artırılabilir veya azaltılabilir.

Kaynak metinde olsa bile küfür 'ın çeviride oluşmasını engellemek isterseniz, onu destekleyen yöntemler için küfür filtreleme seçeneğini kullanabilirsiniz. Seçeneği, uygunsuz küfür 'ın silinip silinmeyeceğini veya uygun etiketlere göre işaretlenip işaretlenmediğini veya hedefte küfür izin verip vermeyeceğinizi seçmenizi sağlar. @No__t-0 ' ın kabul edilen değerleri `NoAction` (varsayılan), `Marked` ve `Deleted` ' tir.


|ProfanityAction    |Eylem |Örnek kaynak (Japonca)  |Örnek çeviri (Ingilizce)  |
|:--|:--|:--|:--|
|NoAction   |Varsayılanını. Seçeneği ayarlamaya benzer. Küfür kaynaktan hedefe geçirilecek.        |彼はジャッカスです.     |Bu bir Jackass.   |
|İm     |Küfürlü kelimeleri \<küfür > ve \</küfür > XML etiketleriyle çevrelecektir.       |彼はジャッカスです. |Bir \<küfür, Jackass @ no__t-1/küfür > >.  |
|Silindi    |Küfürlü sözcükler, değişiklik yapılmadan çıkışta kaldırılacak.     |彼はジャッカスです. |Bir.   |

    
## <a name="excluding-content-from-translation"></a>İçeriği çeviriye dışlama
HTML (`contentType=text/html`) gibi etiketlerle içerik çevirdığınızda, bazı durumlarda belirli içerikleri çeviride dışlamak yararlı olur. Özgün dilinde kalması gereken içeriği belirtmek için `class=notranslate` özniteliğini kullanabilirsiniz. Aşağıdaki örnekte, ilk `div` öğesindeki İçerik çevrilmeyecek, ancak ikinci `div` öğesindeki İçerik çevrilecek.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>/Translate al

### <a name="implementation-notes"></a>Uygulama notları
Bir metin dizesini bir dilden diğerine çevirir.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/Translate` ' dır.

**Dönüş değeri:** Çevrilmiş metni temsil eden bir dize.

Daha önce `AddTranslation` veya `AddTranslationArray` ' i daha önce aynı kaynak cümlesi için 5 veya daha yüksek bir dereceye sahip bir çeviri girmek üzere kullandıysanız, `Translate` yalnızca sisteminiz için kullanılabilir olan en üst seçimi döndürür. "Aynı kaynak cümlesi", bir tümcenin sonundaki büyük harfler, boşluk, etiket değerleri ve noktalama işaretleri dışında tam olarak aynı (% 100 eşleşme) anlamına gelir. Bir derecelendirme 5 veya üzeri bir derecelendirmeden depolanmıyorsa, döndürülen sonuç Microsoft Translator tarafından otomatik çeviri olacaktır.

### <a name="response-class-status-200"></a>Response sınıfı (durum 200)

dize

Yanıt içerik türü: Application/XML

### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama    |Parametre türü|veri türü|
|:--|:--|:--|:--|:--|
|AppID  |olmamalıdır    |Gerekli. @No__t-0 veya `Ocp-Apim-Subscription-Key` üst bilgisi kullanılırsa, `appid` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.|sorgu|dize|
|metin|olmamalıdır   |Gerekli. Çevrilecek metni temsil eden bir dize. Metin 10.000 karakterden fazlasını içeremez.|sorgu|dize|
|Kaynak|olmamalıdır   |İsteğe bağlı. Çevrilen metnin dil kodunu temsil eden bir dize. Örneğin, Ingilizce için en.|sorgu|dize|
|to|olmamalıdır |Gerekli. Metni çevirecek dilin kodunu temsil eden bir dize.|sorgu|dize|
|contentType|olmamalıdır    |İsteğe bağlı. Çevrilen metnin biçimi. Desteklenen biçimler `text/plain` (varsayılan) ve `text/html` ' dir. Herhangi bir HTML öğesinin düzgün biçimlendirilmiş, tam öğeler olması gerekir.|sorgu|dize|
|category|olmamalıdır   |İsteğe bağlı. Çevirinin kategorisini (etki alanı) içeren bir dize. Varsayılan, `general` değeridir.|sorgu|dize|
|Yetkilendirme|olmamalıdır  |@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir. Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.|üstbilgi|dize|
|OCP-apim-Subscription-Key|olmamalıdır  |@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|


### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu|Neden|
|:--|:--|
|400    |Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin.|
|401    |Geçersiz kimlik bilgileri.|
|500    |Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503    |Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|

## <a name="post-translatearray"></a>POST/TranslateArray

### <a name="implementation-notes"></a>Uygulama notları
Birden çok kaynak metin için çevirileri alır.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray` ' dır.

İstek gövdesinin biçimi şöyledir:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Bu öğeler `TranslateArrayRequest` ' dır:


* `AppId`: gerekli. @No__t-0 veya `Ocp-Apim-Subscription-Key` üst bilgisi kullanılırsa, `AppId` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.
* `From`: Isteğe bağlı. Çevrilen metnin dil kodunu temsil eden bir dize. Bu alan boş bırakılırsa, yanıt otomatik dil algılamanın sonucunu dahil eder.
* `Options`: Isteğe bağlı. Aşağıdaki değerleri içeren `Options` nesnesi. Bunların tümü isteğe bağlıdır ve varsayılan olarak en sık kullanılan ayarlara sahiptir. Belirtilen öğelerin alfabetik sırada listelenmesi gerekir.
    - `Category`: çevirinin kategorisini (etki alanı) içeren bir dize. Varsayılan, `general` değeridir.
    - `ContentType`: çevrilmekte olan metnin biçimi. Desteklenen biçimler `text/plain` (varsayılan), `text/xml` ve `text/html` ' dir. Herhangi bir HTML öğesinin düzgün biçimlendirilmiş, tam öğeler olması gerekir.
    - `ProfanityAction`: daha önce açıklandığı gibi profanities nasıl işleneceğini belirtir. Kabul edilen değerler `NoAction` (varsayılan), `Marked` ve `Deleted` ' dir.
    - `State`: istek ve yanıtı ilişkilendirmenize yardımcı olmak için Kullanıcı durumu. Yanıtta aynı içerik döndürülecek.
    - `Uri`: sonuçları bu URI 'ye göre filtreleyin. Varsayılan: `all`.
    - `User`: sonuçları bu kullanıcıya göre filtreleyin. Varsayılan: `all`.
* `Texts`: gerekli. Çeviri metnini içeren bir dizi. Tüm dizeler aynı dilde olmalıdır. Çevrilecek tüm metinlerin toplamı 10.000 karakteri aşamaz. Dizi öğesi sayısı üst sınırı 2.000 ' dir.
* `To`: gerekli. Metni çevirecek dilin kodunu temsil eden bir dize.

İsteğe bağlı öğeleri atlayabilirsiniz. @No__t-0 ' ın doğrudan alt öğesi olan öğelerin alfabetik sırada listelenmesi gerekir.

@No__t-0 yöntemi `Content-Type` için `application/xml` veya `text/xml` kabul eder.

**Dönüş değeri:** @No__t-1 dizisi. Her `TranslateArrayResponse` şu öğelere sahiptir:

* `Error`: bir hata oluşursa hatayı gösterir. Aksi takdirde null olarak ayarlayın.
* `OriginalSentenceLengths`: kaynak metindeki her tümcenin uzunluğunu gösteren bir tamsayılar dizisi. Dizinin uzunluğu, Tümcelerin sayısını gösterir.
* `TranslatedText`: çevrilmiş metin.
* `TranslatedSentenceLengths`: çevrilmiş metindeki her tümcenin uzunluğunu gösteren tamsayılar dizisi. Dizinin uzunluğu, Tümcelerin sayısını gösterir.
* `State`: istek ve yanıtı ilişkilendirmenize yardımcı olmak için Kullanıcı durumu. İstekle aynı içeriği döndürür.

İşte yanıt gövdesinin biçimi:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Response sınıfı (durum 200)
Başarılı bir yanıt, daha önce açıklanan biçimde `TranslateArrayResponse` dizileri dizisi içerir.

dize

Yanıt içerik türü: Application/XML

### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama|Parametre türü|Veri türü|
|:--|:--|:--|:--|:--|
|Yetkilendirme|olmamalıdır  |@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir. Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.|üstbilgi|dize|
|OCP-apim-Subscription-Key|olmamalıdır|@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|

### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu   |Neden|
|:--|:--|
|400    |Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin. Yaygın hatalar şunlardır: <ul><li>Dizi öğesi boş olamaz.</li><li>Geçersiz kategori.</li><li>Dil, geçersiz.</li><li>Dil geçersiz.</li><li>İstek çok fazla öğe içeriyor.</li><li>Kimden dili desteklenmiyor.</li><li>To dili desteklenmiyor.</li><li>Çeviri Isteğinde çok fazla veri yok.</li><li>HTML doğru biçimde değil.</li><li>Çeviri Isteğine çok fazla sayıda dize geçirildi.</li></ul>|
|401    |Geçersiz kimlik bilgileri.|
|500    |Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503    |Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|

## <a name="post-getlanguagenames"></a>POST/GetLanguageNames

### <a name="implementation-notes"></a>Uygulama notları
Geçirilen `locale` diline yerelleştirilmiş `languageCodes` parametresi olarak geçirilen dillerin kolay adlarını alır.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames` ' dır.

İstek gövdesi, kolay adların alınacağı ISO 639-1 dil kodlarını temsil eden bir dize dizisi içerir. Örnek buradadır:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Dönüş değeri:** Çeviri hizmeti tarafından desteklenen dil adlarını içeren, istenen dile yerelleştirilmiş bir dize dizisi.

### <a name="response-class-status-200"></a>Response sınıfı (durum 200)
İstenen dile yerelleştirilmiş, çevirmen hizmeti tarafından desteklenen dil adlarını içeren bir dize dizisi.

dize

Yanıt içerik türü: Application/XML
 
### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama|Parametre türü|Veri türü|
|:--|:--|:--|:--|:--|
|AppID|olmamalıdır|Gerekli. @No__t-0 veya `Ocp-Apim-Subscription-Key` üst bilgisi kullanılırsa, `appid` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.|sorgu|dize|
|locale|olmamalıdır |Gerekli. Dil adlarını yerelleştirmek için kullanılan aşağıdakilerden birini temsil eden bir dize: <ul><li>ISO 639 2-bir dille ilişkili küçük harfli bir kültür kodu ve ISO 3166 2-Letter büyük harf alt kültür kodu birleşimi. <li>Bir ISO 639 küçük harfli kültür kodu.|sorgu|dize|
|Yetkilendirme|olmamalıdır  |@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir. Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.|üstbilgi|dize|
|OCP-apim-Subscription-Key|olmamalıdır  |@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|

### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu|Neden|
|:--|:--|
|400    |Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin.|
|401    |Geçersiz kimlik bilgileri.|
|500    |Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503    |Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|

## <a name="get-getlanguagesfortranslate"></a>/GetLanguagesForTranslate al

### <a name="implementation-notes"></a>Uygulama notları
Çeviri hizmeti tarafından desteklenen dilleri temsil eden dil kodlarının bir listesini alır.  `Translate` ve `TranslateArray` bu dillerin ikisi arasında çeviri yapabilir.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate` ' dır.

**Dönüş değeri:** Translator hizmeti tarafından desteklenen dil kodlarını içeren bir dize dizisi.

### <a name="response-class-status-200"></a>Response sınıfı (durum 200)
Translator hizmeti tarafından desteklenen dil kodlarını içeren bir dize dizisi.

dize

Yanıt içerik türü: Application/XML
 
### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama|Parametre türü|Veri türü|
|:--|:--|:--|:--|:--|
|AppID|olmamalıdır|Gerekli. @No__t-0 veya `Ocp-Apim-Subscription-Key` üst bilgisi kullanılırsa, `appid` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.|sorgu|dize|
|Yetkilendirme|olmamalıdır  |@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir. Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.|üstbilgi|dize|
|OCP-apim-Subscription-Key|olmamalıdır|@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|

### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu|Neden|
|:--|:--|
|400    |Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin.|
|401    |Geçersiz kimlik bilgileri.|
|500    |Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503|Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|

## <a name="get-getlanguagesforspeak"></a>/GetLanguagesForSpeak al

### <a name="implementation-notes"></a>Uygulama notları
Konuşma birleştirme için kullanılabilen dilleri alır.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak` ' dır.

**Dönüş değeri:** Konuşma sen, çeviri hizmeti tarafından desteklenen dil kodlarını içeren bir dize dizisi.

### <a name="response-class-status-200"></a>Response sınıfı (durum 200)
Konuşma sen, çeviri hizmeti tarafından desteklenen dil kodlarını içeren bir dize dizisi.

dize

Yanıt içerik türü: Application/XML

### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama|Parametre türü|Veri türü|
|:--|:--|:--|:--|:--|
|AppID|olmamalıdır|Gerekli. @No__t-0 veya `Ocp-Apim-Subscription-Key` üst bilgisi kullanılırsa, `appid` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.|sorgu|dize|
|Yetkilendirme|olmamalıdır|@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir. Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.|üstbilgi|dize|
|OCP-apim-Subscription-Key|olmamalıdır|@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|
 
### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu|Neden|
|:--|:--|
|400|Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin.|
|401|Geçersiz kimlik bilgileri.|
|500    |Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503    |Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|

## <a name="get-speak"></a>/Konuş al

### <a name="implementation-notes"></a>Uygulama notları
İstenen dilde konuşulan, geçirilen metnin bir WAV veya MP3 akışını döndürür.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/Speak` ' dır.

**Dönüş değeri:** İstenen dilde konuşulan, geçirilen metnin bir WAV veya MP3 akışı.

### <a name="response-class-status-200"></a>Response sınıfı (durum 200)

ikili

Yanıt içerik türü: Application/XML

### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama|Parametre türü|Veri türü|
|:--|:--|:--|:--|:--|
|AppID|olmamalıdır|Gerekli. @No__t-0 veya `Ocp-Apim-Subscription-Key` üst bilgisi kullanılırsa, `appid` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.|sorgu|dize|
|metin|olmamalıdır   |Gerekli. Belirtilen dilde akış için söylenen bir veya daha fazla cümle içeren bir dize. Metnin 2.000 karakteri aşmaması gerekir.|sorgu|dize|
|dil|olmamalıdır   |Gerekli. Metnin konuşmasını istediğiniz dilin desteklenen dil kodunu temsil eden bir dize. Kod, yöntem tarafından döndürülen kodlardan biri olmalıdır `GetLanguagesForSpeak`.|sorgu|dize|
|biçim|olmamalıdır|İsteğe bağlı. Content-Type KIMLIĞINI belirten bir dize. Şu anda `audio/wav` ve `audio/mp3` kullanılabilir. Varsayılan değer `audio/wav` şeklindedir.|sorgu|dize|
|seçenekler|olmamalıdır    |İsteğe bağlı. Sentezlenmiş konuşmanın özelliklerini belirten bir dize:<ul><li>`MaxQuality` ve `MinSize` ses sinyalinin kalitesini belirtin. `MaxQuality` en yüksek kaliteyi sağlar. `MinSize` en küçük dosya boyutunu sağlar. Varsayılan değer `MinSize` ' dır.</li><li>`female` ve `male`, istenen sesin cinsiyetini belirtir. Varsayılan, `female` değeridir. Birden çok seçenek dahil etmek için dikey çubuğu (<code>\|</code>) kullanın. Örneğin, `MaxQuality|Male`.</li></li></ul>  |sorgu|dize|
|Yetkilendirme|olmamalıdır|@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir. Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.|üstbilgi|dize|
|OCP-apim-Subscription-Key|olmamalıdır  |@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|

### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu|Neden|
|:--|:--|
|400    |Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin.|
|401    |Geçersiz kimlik bilgileri.|
|500    |Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503    |Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|

## <a name="get-detect"></a>/Detect al

### <a name="implementation-notes"></a>Uygulama notları
Metnin bir bölümünün dilini tanımlar.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/Detect` ' dır.

**Dönüş değeri:** Metin için iki karakterli bir dil kodu içeren bir dize.

### <a name="response-class-status-200"></a>Response sınıfı (durum 200)

dize

Yanıt içerik türü: Application/XML

### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama|Parametre türü|Veri türü|
|:--|:--|:--|:--|:--|
|AppID|olmamalıdır  |Gerekli. @No__t-0 veya `Ocp-Apim-Subscription-Key` üst bilgisi kullanılırsa, `appid` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.|sorgu|dize|
|metin|olmamalıdır|Gerekli. Dili tanımlanabilecek metni içeren bir dize. Metnin 10.000 karakteri aşmaması gerekir.|sorgu|  dize|
|Yetkilendirme|olmamalıdır|@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir. Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.|üstbilgi|dize|
|OCP-apim-Subscription-Key  |olmamalıdır    |@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|

### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu|Neden|
|:--|:--|
|400|Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin.|
|401    |Geçersiz kimlik bilgileri.|
|500    |Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503    |Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|


## <a name="post-detectarray"></a>POST/DetectArray

### <a name="implementation-notes"></a>Uygulama notları

Dizeler dizisindeki dilleri tanımlar. Bağımsız olarak her bir dizi öğesinin dilini algılar ve dizinin her satırı için bir sonuç döndürür.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/DetectArray` ' dır.

İstek gövdesinin biçimi şöyledir:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Metin 10.000 karakterden uzun olamaz.

**Dönüş değeri:** Giriş dizisindeki her satır için iki karakterli bir dil kodu içeren bir dize dizisi.

İşte yanıt gövdesinin biçimi:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Response sınıfı (durum 200)
`DetectArray` başarılı oldu. Giriş dizisinin her satırı için iki karakterli bir dil kodu içeren bir dize dizisi döndürür.

dize

Yanıt içerik türü: Application/XML
 
### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama|Parametre türü|Veri türü|
|:--|:--|:--|:--|:--|
|AppID|olmamalıdır|Gerekli. @No__t-0 veya `Ocp-Apim-Subscription-Key` üst bilgisi kullanılırsa, `appid` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.|sorgu|dize|
|Yetkilendirme|olmamalıdır|@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir.  Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.|üstbilgi|dize|
|OCP-apim-Subscription-Key|olmamalıdır|@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|

### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu|Neden|
|:--|:--|
|400    |Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin.|
|401    |Geçersiz kimlik bilgileri.|
|500    |Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503    |Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|

## <a name="get-addtranslation"></a>/AddTranslation 'i al

### <a name="implementation-notes"></a>Uygulama notları

> [!IMPORTANT]
> **Kullanımdan kaldırma notumu:** Bu yöntem 31 Ocak 2018 ' den sonra yeni tümce gönderilerini kabul etmez. Bir hata iletisi alırsınız. Lütfen Işbirliğine dayalı çeviri çerçevesindeki (CTF) değişiklikler hakkında duyurusuna bakın.

Çeviri belleğine bir çeviri ekler.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation` ' dır.

### <a name="response-class-status-200"></a>Response sınıfı (durum 200)

dize

Yanıt içerik türü: uygulama: XML
 
### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama|Parametre türü|Veri türü   |
|:--|:--|:--|:--|:--|
|AppID|olmamalıdır|Gerekli. @No__t-0 veya `Ocp-Apim-Subscription-Key` üst bilgisi kullanılırsa, `appid` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.|sorgu|dize|
|originalText|olmamalıdır|Gerekli. Çevrilecek metni içeren bir dize. Dizenin uzunluk üst sınırı 1.000 karakterdir.|sorgu|dize|
|translatedText|olmamalıdır |Gerekli. Hedef dile çevrilmiş metin içeren bir dize. Dizenin uzunluk üst sınırı 2.000 karakterdir.|sorgu|dize|
|Kaynak|olmamalıdır   |Gerekli. Metnin özgün dilinin dil kodunu temsil eden bir dize. Örneğin, en Ingilizce ve Almanca için de.|sorgu|dize|
|to|olmamalıdır|Gerekli. Metni çevirecek dilin dil kodunu temsil eden bir dize.|sorgu|dize|
|Derecesiyle|olmamalıdır |İsteğe bağlı. Dize için kalite derecesini temsil eden bir tamsayı. Değer-10 ile 10 arasındadır. Varsayılan değer 1'dir.|sorgu|tamsayı|
|contentType|olmamalıdır    |İsteğe bağlı. Çevrilen metnin biçimi. Desteklenen biçimler `text/plain` ve `text/html` ' dir. Herhangi bir HTML öğesinin düzgün biçimlendirilmiş, tam öğeler olması gerekir.    |sorgu|dize|
|category|olmamalıdır|İsteğe bağlı. Çevirinin kategorisini (etki alanı) içeren bir dize. Varsayılan, `general` değeridir.|sorgu|dize|
|kullanıcı|olmamalıdır|Gerekli. Gönderimi kaynağını izlemek için kullanılan bir dize.|sorgu|dize|
|URI|olmamalıdır|İsteğe bağlı. Çevirinin içerik konumunu içeren bir dize.|sorgu|dize|
|Yetkilendirme|olmamalıdır|@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir.  Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.  |üstbilgi|dize|
|OCP-apim-Subscription-Key|olmamalıdır|@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|

### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu|Neden|
|:--|:--|
|400    |Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin.|
|401    |Geçersiz kimlik bilgileri.|
|410|`AddTranslation` artık desteklenmiyor.|
|500    |Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503    |Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|

## <a name="post-addtranslationarray"></a>/AddTranslationArray SONRASı

### <a name="implementation-notes"></a>Uygulama notları

> [!IMPORTANT]
> **Kullanımdan kaldırma notumu:** Bu yöntem 31 Ocak 2018 ' den sonra yeni tümce gönderilerini kabul etmez. Bir hata iletisi alırsınız. Lütfen Işbirliğine dayalı çeviri çerçevesindeki (CTF) değişiklikler hakkında duyurusuna bakın.

Çeviri belleğine bir çeviri dizisi ekler. Bu yöntem `AddTranslation` ' ın bir dizi sürümüdür.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray` ' dır.

İstek gövdesinin biçimi şöyledir:

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Bu öğeler `AddtranslationsRequest` ' dır:

* `AppId`: gerekli. @No__t-0 veya `Ocp-Apim-Subscription-Key` üst bilgisi kullanılırsa, `AppId` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.
* `From`: gerekli. Kaynak dilin dil kodunu içeren bir dize. @No__t-0 yöntemiyle döndürülen dillerden biri olmalıdır.
* `To`: gerekli. Hedef dilin dil kodunu içeren bir dize. @No__t-0 yöntemiyle döndürülen dillerden biri olmalıdır.
* `Translations`: gerekli. Çeviri belleğine eklenecek bir çeviri dizisi. Her çeviri `OriginalText`, `TranslatedText` ve `Rating` içermelidir. @No__t-0 ve `TranslatedText` en büyük boyutu 1.000 karakterdir. Tüm `OriginalText` ve `TranslatedText` öğelerinin toplamı 10.000 karakterden uzun olamaz. Dizi öğesi sayısı üst sınırı 100 ' dir.
* `Options`: gerekli. @No__t-0, `ContentType`, `Uri` ve `User` gibi seçenekler kümesi. `User` gereklidir. `Category`, `ContentType` ve `Uri` isteğe bağlıdır. Belirtilen öğelerin alfabetik sırada listelenmesi gerekir.

### <a name="response-class-status-200"></a>Response sınıfı (durum 200)
`AddTranslationArray` yöntemi başarılı oldu. 

31 Ocak 2018 ' den sonra cümle gönderimleri kabul edilmez. Hizmet 410 hata koduyla yanıt verir.

dize

Yanıt içerik türü: Application/XML
 
### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama|Parametre türü|Veri türü|
|:--|:--|:--|:--|:--|
|Yetkilendirme|olmamalıdır|@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir.  Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.|üstbilgi|dize|
|OCP-apim-Subscription-Key|olmamalıdır|@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|

### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu|Neden|
|:--|:--|
|400    |Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin.|
|401    |Geçersiz kimlik bilgileri.|
|410    |`AddTranslation` artık desteklenmiyor.|
|500    |Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503|Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|

## <a name="get-breaksentences"></a>/Breakcümleler al

### <a name="implementation-notes"></a>Uygulama notları
Metnin bir bölümünü cümlelere böler ve her tümcenin uzunluklarını içeren bir dizi döndürür.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences` ' dır.

**Dönüş değeri:** Tümcelerin uzunluklarının temsil eden tamsayılar dizisi. Dizinin uzunluğu, Tümcelerin sayısını temsil eder. Değerler her tümcenin uzunluğunu temsil eder.

### <a name="response-class-status-200"></a>Response sınıfı (durum 200)
Tümcelerin uzunluklarının temsil eden tamsayılar dizisi. Dizinin uzunluğu, Tümcelerin sayısını temsil eder. Değerler her tümcenin uzunluğunu temsil eder.

tamsayı

Yanıt içerik türü: Application/XML

### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama|Parametre türü|Veri türü|
|:--|:--|:--|:--|:--|
|AppID|olmamalıdır  |Gerekli. @No__t-0 veya `Ocp-Apim-Subscription-Key` üst bilgisi kullanılırsa, `appid` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.|sorgu| dize|
|metin|olmamalıdır   |Gerekli. Cümlelere bölünecek metni temsil eden bir dize. Metnin en büyük boyutu 10.000 karakterdir.|sorgu|dize|
|dil   |olmamalıdır    |Gerekli. Giriş metninin dil kodunu temsil eden bir dize.|sorgu|dize|
|Yetkilendirme|olmamalıdır|@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir. Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.   |üstbilgi|dize|
|OCP-apim-Subscription-Key|olmamalıdır|@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|

### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu|Neden|
|:--|:--|
|400|Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin.|
|401|Geçersiz kimlik bilgileri.|
|500|Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503|Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|

## <a name="post-gettranslations"></a>POST/GetTranslations

### <a name="implementation-notes"></a>Uygulama notları
Mağazadan ve MT altyapısından belirli bir dil çiftinin çeviri dizisini alır. `GetTranslations`, tüm kullanılabilir çevirileri döndüren `Translate` ' den farklıdır.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations` ' dır.

İsteğin gövdesi, bu biçimi içeren isteğe bağlı `TranslationOptions` nesnesini içerir:

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

@No__t-0 nesnesi aşağıdaki listedeki değerleri içerir. Bunların tümü isteğe bağlıdır ve varsayılan olarak en sık kullanılan ayarlara sahiptir. Belirtilen öğelerin alfabetik sırada listelenmesi gerekir.

* `Category`: çevirinin kategorisini (etki alanı) içeren bir dize. Varsayılan, `general` değeridir.
* `ContentType`: desteklenen tek seçenek ve varsayılan, `text/plain` ' dir.
* `IncludeMultipleMTAlternatives`: MT altyapısından birden fazla alternatif döndürülüp döndürülmeyeceğini belirten bir Boole bayrağı. Geçerli değerler `true` ve `false` (büyük/küçük harfe duyarlı). Varsayılan değer yalnızca bir alternatif döndüren `false` ' dır. Bayrağını `true` olarak ayarlamak, birlikte çalışan Çeviri Çerçevesi (CTF) ile tamamen tümleşik yapay alternatifler oluşturulmasına olanak sağlar. Özelliği, kod çözücünün *n*en iyi listesinden yapay alternatifler ekleyerek CTF 'de çevirisi olmayan cümleler için alternatifleri döndürmeyi mümkün.
    - Lendir. Derecelendirmeler aşağıdaki gibi uygulanır: 
         - En iyi otomatik çeviri, 5 derecesine sahiptir.
       - CTF 'nin alternatifleri gözden geçirenin yetkilisini yansıtır. -10 ile + 10 arasında değişir.
       - Otomatik olarak oluşturulan (*n*-en iyi) çeviri alternatifleri, 0 derecelendirmesine ve 100 eşleşme derecesine sahiptir.
    - Alternatifin sayısı. Döndürülen alternatiflere ilişkin sayı `maxTranslations` ' da belirtilen değer kadar yüksek olabilir, ancak daha az olabilir.
    - Dil çiftleri. Bu işlev, Basitleştirilmiş Çince ve geleneksel Çince arasında her iki yönde de çeviri için kullanılamaz. Microsoft Translator tarafından desteklenen tüm diğer dil çiftleri için kullanılabilir.
* `State`: istek ve yanıtı ilişkilendirmenize yardımcı olmak için Kullanıcı durumu. Yanıtta aynı içerik döndürülecek.
* `Uri`: sonuçları bu URI 'ye göre filtreleyin. Değer ayarlanmamışsa, varsayılan değer `all` ' dır.
* `User`: sonuçları bu kullanıcıya göre filtreleyin. Değer ayarlanmamışsa, varsayılan değer `all` ' dır.

İstek `Content-Type` `text/xml` olmalıdır.

**Dönüş değeri:** Yanıtın biçimi şöyledir:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Bu yanıt, aşağıdaki değerleri içeren bir `GetTranslationsResponse` öğesi içerir:

* `Translations`: `TranslationMatch` nesnelerinde depolanan eşleşmelerin bir dizisi (aşağıdaki bölümde açıklanmıştır). Çeviriler orijinal metnin hafif türevlerini (belirsiz eşleşme) içerebilir. Çeviriler sıralanacaktır: ilk olarak% 100 eşleşme, belirsiz eşleşme ileri.
* `From`: Yöntem bir `From` dili belirtmezse, bu değer otomatik dil algılamasında gelir. Aksi halde, belirtilen `From` dili olur.
* `State`: istek ve yanıtı ilişkilendirmenize yardımcı olmak için Kullanıcı durumu. @No__t-0 parametresinde sağlanan değeri içerir.

@No__t-0 nesnesi şu değerlerden oluşur:

* `Error`: belirli bir giriş dizesi için bir hata oluşursa hata kodu. Aksi takdirde, bu alan boştur.
* `MatchDegree`: giriş metninin depoda bulunan özgün metinle ne kadar yakın olduğunu gösterir. Sistem, tam eşleşmeler dahil olmak üzere, depoya karşı giriş cümlelerini eşleştirir. Değer, 0 ile 100 arasında bir değer döndürür; burada 0 benzerlik yoktur ve 100 tam, büyük/küçük harfe duyarlı bir eşleşmedir.
* `MatchedOriginalText`: bu sonuçla eşleşen özgün metin. Bu değer yalnızca eşleşen özgün metnin giriş metninde farklı olması durumunda döndürülür. Benzer bir eşleşmenin kaynak metnini döndürmek için kullanılır. Bu değer, Microsoft Translator sonuçları için döndürülmez.
* `Rating`: kalite kararı veren kişinin yetkilisini belirtir. Makine çevirisi sonuçlarında 5 derecelendirmesi vardır. Anonim olarak belirtilen Çeviriler genellikle 1 ile 4 arasında bir derecelendirmesine sahiptir. Yetkili olarak sağlanmış Çeviriler genellikle 6 ile 10 arasında bir derecelendirme olur.
* `Count`: Bu derecelendirmeden bu çevirinin kaç kez seçildiği. Değer, otomatik olarak çevrilmiş yanıt için 0 ' dır.
* `TranslatedText`: çevrilmiş metin.

### <a name="response-class-status-200"></a>Response sınıfı (durum 200)
Daha önce açıklanan biçimdeki `GetTranslationsResponse` nesnesi.

dize

Yanıt içerik türü: Application/XML
 
### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama|Parametre türü|Veri türü|
|:--|:--|:--|:--|:--|
|AppID|olmamalıdır|Gerekli. @No__t-0 veya `Ocp-Apim-Subscription-Key` üst bilgisi kullanılırsa, `appid` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.|sorgu|dize|
|metin|olmamalıdır|Gerekli. Çevrilecek metni temsil eden bir dize. Metnin en büyük boyutu 10.000 karakterdir.|sorgu|dize|
|Kaynak|olmamalıdır|Gerekli. Çevrilen metnin dil kodunu temsil eden bir dize.|sorgu|dize|
|to |olmamalıdır    |Gerekli. Metni çevirecek dilin dil kodunu temsil eden bir dize.|sorgu|dize|
|Maxçeviriler|olmamalıdır|Gerekli. Döndürülecek en fazla çeviri sayısını temsil eden bir tamsayı.|sorgu|tamsayı|
|Yetkilendirme| olmamalıdır|@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir. Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.|dize|  üstbilgi|
|OCP-apim-Subscription-Key|olmamalıdır  |@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|

### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu|Neden|
|:--|:--|
|400    |Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin.|
|401    |Geçersiz kimlik bilgileri.|
|500    |Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503|Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|

## <a name="post-gettranslationsarray"></a>POST/GetTranslationsArray

### <a name="implementation-notes"></a>Uygulama notları
Birden çok kaynak metin için birden çok çeviri adayları alır.

İstek URI 'SI `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray` ' dır.

İstek gövdesinin biçimi şöyledir:

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` şu öğeleri içerir:

* `AppId`: gerekli. @No__t-0 üstbilgisi kullanılıyorsa, `AppId` alanını boş bırakın. Aksi takdirde, `"Bearer" + " " + "access_token"` içeren bir dize ekleyin.
* `From`: gerekli. Çevrilen metnin dil kodunu temsil eden bir dize.
* `MaxTranslations`: gerekli. Döndürülecek en fazla çeviri sayısını temsil eden bir tamsayı.
* `Options`: Isteğe bağlı. Aşağıdaki değerleri içeren `Options` nesnesi. Bunların tümü isteğe bağlıdır ve varsayılan olarak en sık kullanılan ayarlara sahiptir. Belirtilen öğelerin alfabetik sırada listelenmesi gerekir.
    - `Category`: çevirinin kategorisini (etki alanı) içeren bir dize. Varsayılan, `general` değeridir.
    - `ContentType`: desteklenen tek seçenek ve varsayılan, `text/plain` ' dir.
    - `IncludeMultipleMTAlternatives`: MT altyapısından birden fazla alternatif döndürülüp döndürülmeyeceğini belirten bir Boole bayrağı. Geçerli değerler `true` ve `false` (büyük/küçük harfe duyarlı). Varsayılan değer yalnızca bir alternatif döndüren `false` ' dır. Bayrak `true` olarak ayarlandığında, birlikte çalışan Çeviri Çerçevesi (CTF) ile tam olarak tümleştirilmiş, çeviride yapay alternatifler oluşturulmasına olanak sağlar. Özelliği, kod çözücüsünün *n*en iyi listesinden yapay alternatifler ekleyerek CTF 'de alternatif olmayan cümleler için alternatifler döndürmeyi mümkün.
        - Derecelendirmeler aşağıdaki gibi uygulanır:
          - En iyi otomatik çeviri, 5 derecesine sahiptir.
          - CTF 'nin alternatifleri gözden geçirenin yetkilisini yansıtır. -10 ile + 10 arasında değişir.
          - Otomatik olarak oluşturulan (*n*-en iyi) çeviri alternatifleri, 0 derecelendirmesine ve 100 eşleşme derecesine sahiptir.
        - Alternatifin sayısı. Döndürülen alternatiflere ilişkin sayı `maxTranslations` ' da belirtilen değer kadar yüksek olabilir, ancak daha az olabilir.
        - Dil çiftleri. Bu işlev, Basitleştirilmiş Çince ve geleneksel Çince arasında her iki yönde de çeviri için kullanılamaz. Microsoft Translator tarafından desteklenen tüm diğer dil çiftleri için kullanılabilir.
* `State`: istek ve yanıtı ilişkilendirmenize yardımcı olmak için Kullanıcı durumu. Yanıtta aynı içerik döndürülecek.
* `Uri`: sonuçları bu URI 'ye göre filtreleyin. Değer ayarlanmamışsa, varsayılan değer `all` ' dır.
* `User`: sonuçları bu kullanıcıya göre filtreleyin. Değer ayarlanmamışsa, varsayılan değer `all` ' dır.
* `Texts`: gerekli. Çeviri metnini içeren bir dizi. Tüm dizeler aynı dilde olmalıdır. Çevrilecek tüm metinlerin toplamı 10.000 karakteri aşamaz. Dizi öğelerinin maksimum sayısı 10 ' dur.
* `To`: gerekli. Metni çevirecek dilin dil kodunu temsil eden bir dize.

İsteğe bağlı öğeleri atlayabilirsiniz. @No__t-0 ' ın doğrudan alt öğesi olan öğelerin alfabetik sırada listelenmesi gerekir.

İstek `Content-Type` `text/xml` olmalıdır.

**Dönüş değeri:** Yanıtın biçimi şöyledir:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Her `GetTranslationsResponse` öğesi şu değerleri içerir:

* `Translations`: `TranslationMatch` nesnelerinde depolanan eşleşmelerin bir dizisi (aşağıdaki bölümde açıklanmıştır). Çeviriler orijinal metnin hafif türevlerini (belirsiz eşleşme) içerebilir. Çeviriler sıralanacaktır: ilk olarak% 100 eşleşme, belirsiz eşleşme ileri.
* `From`: Yöntem bir `From` dili belirtmezse, bu değer otomatik dil algılamasında gelir. Aksi halde, belirtilen `From` dili olur.
* `State`: istek ve yanıtı ilişkilendirmenize yardımcı olmak için Kullanıcı durumu. @No__t-0 parametresinde sağlanan değeri içerir.

@No__t-0 nesnesi aşağıdaki değerleri içerir:
* `Error`: belirli bir giriş dizesi için bir hata oluşursa hata kodu. Aksi takdirde, bu alan boştur.
* `MatchDegree`: giriş metninin depoda bulunan özgün metinle ne kadar yakın olduğunu gösterir. Sistem, tam eşleşmeler dahil olmak üzere, depoya karşı giriş cümlelerini eşleştirir. Değer, 0 ile 100 arasında bir değer döndürür; burada 0 benzerlik yoktur ve 100 tam, büyük/küçük harfe duyarlı bir eşleşmedir.
* `MatchedOriginalText`: bu sonuçla eşleşen özgün metin. Bu değer yalnızca eşleşen özgün metnin giriş metninde farklı olması durumunda döndürülür. Benzer bir eşleşmenin kaynak metnini döndürmek için kullanılır. Bu değer, Microsoft Translator sonuçları için döndürülmez.
* `Rating`: kalite kararı veren kişinin yetkilisini belirtir. Makine çevirisi sonuçlarında 5 derecelendirmesi vardır. Anonim olarak belirtilen Çeviriler genellikle 1 ile 4 arasında bir derecelendirmesine sahiptir. Yetkili olarak sağlanmış Çeviriler genellikle 6 ile 10 arasında bir derecelendirmesine sahiptir.
* `Count`: Bu derecelendirmeden bu çevirinin kaç kez seçildiği. Değer, otomatik olarak çevrilmiş yanıt için 0 ' dır.
* `TranslatedText`: çevrilmiş metin.


### <a name="response-class-status-200"></a>Response sınıfı (durum 200)

dize

Yanıt içerik türü: Application/XML
 
### <a name="parameters"></a>Parametreler

|Parametre|Değer|Açıklama|Parametre türü|Veri türü|
|:--|:--|:--|:--|:--|
|Yetkilendirme  |olmamalıdır    |@No__t-0 alanı ve `Ocp-Apim-Subscription-Key` üst bilgisi boş bırakılırsa gereklidir.  Yetkilendirme belirteci: `"Bearer" + " " + "access_token"`.|üstbilgi|dize|
|OCP-apim-Subscription-Key|olmamalıdır  |@No__t-0 alanı ve `Authorization` üst bilgisi boş bırakılırsa gereklidir.|üstbilgi|dize|

### <a name="response-messages"></a>Yanıt iletileri

|HTTP durum kodu|Neden|
|:--|:--|
|400    |Hatalı istek. Giriş parametrelerini ve ayrıntılı hata yanıtını denetleyin.|
|401    |Geçersiz kimlik bilgileri.|
|500    |Sunucu hatası. Hata devam ederse, bize bilgi verin. Lütfen isteğin yaklaşık Tarih & ve `X-MS-Trans-Info` yanıt üstbilgisine dahil edilen istek KIMLIĞIYLE bize bildirin.|
|503    |Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin ve hatanın devam edip etmediğini bize bildirin.|

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Translator Metin Çevirisi API'si v3 'ye geçir](../migrate-to-v3.md)


