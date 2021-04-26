---
title: Translator V 3.0 başvurusu
titleSuffix: Azure Cognitive Services
description: Translator V 3.0 için başvuru belgeleri. Çeviricisinin 3. sürümü, modern bir JSON tabanlı Web API 'SI sağlar.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 8/11/2020
ms.author: lajanuar
ms.openlocfilehash: 2b391c5a435c2dd2f19a3f170bf7c84edd7143f2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063041"
---
# <a name="translator-v30"></a>Translator v 3.0

## <a name="whats-new"></a>Yenilikler

Çeviricisinin 3. sürümü, modern bir JSON tabanlı Web API 'SI sağlar. Mevcut özellikleri daha az işlem halinde birleştirerek kullanılabilirliği ve performansı artırır ve yeni özellikler sağlar.

 * Tek bir dildeki metni bir betikten başka bir betiğe dönüştürmek için alfabe alfabmına dönüştürme.
 * Tek bir istekte birden çok dile çeviri.
 * Tek bir istekte dil algılama, çeviri ve alfabede iyileştirme.
 * Bir terime ait alternatif çevirileri aramak için sözlük, bağlamda kullanılan terimleri gösteren geri çevirileri ve örnekleri bulun.
 * Daha bilgilendirici dil algılama sonuçları.

## <a name="base-urls"></a>Temel URL 'Ler

Microsoft Translator, birden çok veri merkezi konumundan kullanıma sunulur. Şu anda, 10 [Azure coğrafi grafları](https://azure.microsoft.com/global-infrastructure/regions)'nda bulunuyor:

* **Kuzey:** Doğu ABD, Orta Güney ABD, Orta Batı ABD ve Batı ABD 2 
* **Asya Pasifik:** Kore Güney, Japonya Doğu, Güneydoğu Asya ve Avustralya Doğu
* **Avrupa:** Kuzey Avrupa, Batı Avrupa

Microsoft Translator 'a yönelik istekler, isteğin kaynaklandığı yere en yakın veri merkezi tarafından işlenen çoğu durumda bulunur. Bir veri merkezi arızası durumunda, istek Azure Coğrafya dışında yönlendirilebilir.

İsteğin belirli bir Azure Coğrafya tarafından işlenmesine zorlamak için API isteğindeki genel uç noktasını istenen coğrafi uç noktaya değiştirin:

|Description|Azure Coğrafya|Temel URL (coğrafi uç noktası)|
|:--|:--|:--|
|Azure|Genel (bölgesel olmayan)|   api.cognitive.microsofttranslator.com|
|Azure|Birleşik Devletler|   api-nam.cognitive.microsofttranslator.com|
|Azure|Avrupa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asya Pasifik|    api-apc.cognitive.microsofttranslator.com|

İsviçre Kuzey veya İsviçre Batı bulunan bir kaynağa sahip <sup>1</sup> müşteri, metin API isteklerinin İsviçre içinde sunulmasını sağlayabilir. İsteklerin Isviçre 'de işlenmesini sağlamak için, ' ' İsviçre Kuzey ' veya ' İsviçre Batı ' kaynak bölgesinde çevirmen kaynağını oluşturun, sonra API isteklerinizin kaynak özel uç noktasını kullanın. Örneğin: ' İsviçre Kuzey ' olarak ' kaynak Bölgesi ' ile Azure portal bir Translator kaynağı oluşturursanız ve kaynak adınız ' My-ch-n ' ise, Özel uç noktanız " https://my-ch-n.cognitiveservices.azure.com " olur. Ve çevrilecek örnek bir istek şunlardır:
```curl
// Pass secret key and region using headers to a custom endpoint
curl -X POST " my-ch-n.cognitiveservices.azure.com/translator/text/v3.0/translate?to=fr" \
-H "Ocp-Apim-Subscription-Key: xxx" \
-H "Ocp-Apim-Subscription-Region: switzerlandnorth" \
-H "Content-Type: application/json" \
-d "[{'Text':'Hello'}]" -v
```
<sup>2</sup> özel çevirmen, İsviçre 'de Şu anda kullanılamıyor.

## <a name="authentication"></a>Kimlik Doğrulaması

Azure bilişsel hizmetler 'de Translator veya bilişsel [Hizmetler çoklu hizmet hizmetine](https://azure.microsoft.com/pricing/details/cognitive-services/) abone olun ve kimlik doğrulaması için abonelik anahtarınızı (Azure Portal ' de kullanılabilir) kullanın. 

Aboneliğinizin kimliğini doğrulamak için kullanabileceğiniz üç üst bilgi vardır. Bu tablo, her birinin nasıl kullanıldığını açıklar:

|Üst Bilgiler|Description|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Gizli anahtarınızı geçirmektir bilişsel hizmetler aboneliğiyle kullanın*.<br/>Değer, Translator aboneliğiniz için Azure gizli anahtarı ' dır.|
|Yetkilendirme|*Kimlik doğrulama belirteci geçirçalışıyorsanız bilişsel hizmetler aboneliğiyle kullanın.*<br/>Değer, taşıyıcı belirtecidir: `Bearer <token>` .|
|OCP-apim-Subscription-Region|*Bilişsel hizmetler çoklu hizmet ve bölgesel çevirmen kaynağı ile kullanın.*<br/>Değer, çok hizmet veya bölgesel çevirmen kaynağının bölgesidir. Bu değer, genel bir çevirmen kaynağı kullanılırken isteğe bağlıdır.|

###  <a name="secret-key"></a>Gizli anahtar
İlk seçenek üstbilgiyi kullanarak kimlik doğrulaması yapmanız gerekir `Ocp-Apim-Subscription-Key` . `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>`Üstbilgiyi isteğinize ekleyin.

#### <a name="authenticating-with-a-global-resource"></a>Genel kaynakla kimlik doğrulama

[Küresel bir çevirmen kaynağı](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)kullandığınızda, çeviriciyi çağırmak için bir üst bilgi eklemeniz gerekir.

|Üst Bilgiler|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Değer, Translator aboneliğiniz için Azure gizli anahtarı ' dır.|

Aşağıda, genel çevirmen kaynağını kullanarak çeviriciyi çağırma isteği verilmiştir

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Bölgesel kaynakla kimlik doğrulama

[Bölgesel bir çevirmen kaynağı](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)kullandığınızda.
Çeviriciyi çağırmanız gereken 2 üst bilgi vardır.

|Üst Bilgiler|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Değer, Translator aboneliğiniz için Azure gizli anahtarı ' dır.|
|OCP-apim-Subscription-Region| Değer, çevirmen kaynağının bölgesidir. |

Aşağıda bölgesel çevirmen kaynağını kullanarak çeviriciyi çağırma isteği verilmiştir

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Çoklu hizmet kaynağıyla kimlik doğrulama

Bilişsel hizmetin çoklu hizmet kaynağını kullandığınızda. Bu, birden çok hizmete yönelik isteklerin kimliğini doğrulamak için tek bir gizli anahtar kullanmanıza olanak sağlar. 

Birden çok hizmet gizli anahtarı kullandığınızda, isteğinize iki kimlik doğrulama üst bilgisi eklemeniz gerekir. Çeviriciyi çağırmanız gereken 2 üst bilgi vardır.

|Üst Bilgiler|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Değer, çok hizmet kaynağınız için Azure gizli anahtarı ' dır.|
|OCP-apim-Subscription-Region| Değer, çoklu hizmet kaynağının bölgesidir. |

Çoklu hizmet metin API 'SI aboneliği için bölge gereklidir. Seçtiğiniz bölge, çok sunuculu abonelik anahtarını kullanırken metin çevirisi için kullanabileceğiniz tek bölgedir ve Azure portal aracılığıyla çok hizmet aboneliğiniz için kaydolduğunuzda seçtiğiniz bölge olmalıdır.

Kullanılabilir bölgeler,,,,,,,,, `australiaeast` `brazilsouth` `canadacentral` `centralindia` `centralus` `centraluseuap` `eastasia` `eastus` `eastus2` `francecentral` , `japaneast` ,, `japanwest` , `koreacentral` `northcentralus` , `northeurope` , `southcentralus` ,, `southeastasia` `uksouth` , `westcentralus` `westeurope` `westus` `westus2` `southafricanorth` ,,,,,,,, ve.

Sorgu dizesindeki gizli anahtarı parametresiyle geçirirseniz `Subscription-Key` , bu bölgeyi sorgu parametresiyle birlikte belirtmeniz gerekir `Subscription-Region` .

### <a name="authenticating-with-an-access-token"></a>Erişim belirteci ile kimlik doğrulama
Alternatif olarak, bir erişim belirteci için gizli anahtarınızı değiş tokuş edebilirsiniz. Bu belirteç her bir isteğe üstbilgi olarak dahildir `Authorization` . Yetkilendirme belirteci almak için `POST` AŞAĞıDAKI URL 'ye bir istek yapın:

| Kaynak türü     | Kimlik doğrulama hizmeti URL 'SI                                |
|-----------------|-----------------------------------------------------------|
| Genel          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Bölgesel veya çok hizmet | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Aşağıda gizli anahtar verilen bir belirteç elde etmek için örnek istekler verilmiştir:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Başarılı bir istek, kodlanmış erişim belirtecini yanıt gövdesinde düz metin olarak döndürür. Geçerli belirteç, çevirici hizmetine yetkilendirmede bir taşıyıcı belirteci olarak geçirilir.

```http
Authorization: Bearer <Base64-access_token>
```

Bir kimlik doğrulama belirteci 10 dakika için geçerlidir. Çevirmene birden çok çağrı yapıldığında belirtecin yeniden kullanılması gerekir. Ancak, programınız çevirmende uzun bir süre boyunca istek yapıyorsa, programınızın düzenli aralıklarla yeni bir erişim belirteci istemesi gerekir (örneğin, her 8 dakikada bir).

## <a name="virtual-network-support"></a>Sanal Ağ desteği

Translator hizmeti artık Azure genel bulutunun tüm bölgelerinde sanal ağ (VNET) özellikleri ile kullanılabilir. Sanal ağı etkinleştirmek için lütfen bkz. Azure bilişsel [Hizmetler sanal ağlarını yapılandırma](../../cognitive-services-virtual-networks.md?tabs=portal). 

Bu özelliği etkinleştirdikten sonra, çeviriciyi çağırmak için özel uç noktasını kullanmanız gerekir. Küresel çevirmen uç noktasını ("api.cognitive.microsofttranslator.com") kullanamazsınız ve bir erişim belirteciyle kimlik doğrulaması yapılamaz.

Bir [çevirici kaynağı](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) oluşturup seçili ağlardan ve özel uç noktalardan erişime izin verdikten sonra özel uç noktayı bulabilirsiniz.

|Üst Bilgiler|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Değer, Translator aboneliğiniz için Azure gizli anahtarı ' dır.|
|OCP-apim-Subscription-Region| Değer, çevirmen kaynağının bölgesidir. Kaynak ise bu değer isteğe bağlıdır `global`|

Özel uç nokta kullanarak çeviriciyi çağırmak için örnek bir istek aşağıda verilmiştir

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Hatalar

Standart hata yanıtı, ad/değer çifti adlı bir JSON nesnesidir `error` . Değer aynı zamanda şu özelliklere sahip bir JSON nesnesidir:

  * `code`: Sunucu tanımlı bir hata kodu.
  * `message`: Hata için okunabilir bir gösterimi sağlayan bir dize.

Örneğin, ücretsiz deneme aboneliği olan bir müşteri, ücretsiz kota tükendiğinde aşağıdaki hatayı alır:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Hata kodu 3 basamaklı HTTP durum kodunu birleştiren 6 basamaklı bir sayıdır ve ardından hatayı daha fazla kategorilere ayırarak 3 basamaklı bir sayıdır. Ortak hata kodları şunlardır:

| Kod | Description |
|:----|:-----|
| 400000| İstek girişlerinden biri geçerli değil.|
| 400001| "Scope" parametresi geçersiz.|
| 400002| "Category" parametresi geçersiz.|
| 400003| Bir dil tanımlayıcısı eksik veya geçersiz.|
| 400004| Hedef betik Belirleyicisi ("betiğe") eksik veya geçersiz.|
| 400005| Giriş metni eksik veya geçersiz.|
| 400006| Dil ve betiğin birleşimi geçerli değil.|
| 400018| Kaynak betik Belirleyicisi ("betikten") eksik veya geçersiz.|
| 400019| Belirtilen dillerden biri desteklenmez.|
| 400020| Giriş metni dizisindeki öğelerden biri geçerli değil.|
| 400021| API sürümü parametresi eksik veya geçersiz.|
| 400023| Belirtilen dil çiftinin biri geçerli değil.|
| 400035| Kaynak dili ("Kimden" alanı) geçerli değil.|
| 400036| Hedef dil ("to" alanı) eksik veya geçersiz.|
| 400042| Belirtilen seçeneklerden biri ("Seçenekler" alanı) geçerli değil.|
| 400043| İstemci izleme KIMLIĞI (Clienttraceıd alanı veya X-Clienttranceıd üstbilgisi) eksik veya geçersiz.|
| 400050| Giriş metni çok uzun. [İstek sınırlarını](../request-limits.md)görüntüleyin.|
| 400064| "Translation" parametresi eksik veya geçersiz.|
| 400070| Hedef betiklerin (ToScript parametresi) sayısı, hedef dillerin sayısıyla (parametreye) eşleşmiyor.|
| 400071| Değer TextType için geçerli değil.|
| 400072| Giriş metninin dizisinde çok fazla öğe vardır.|
| 400073| Betik parametresi geçerli değil.|
| 400074| İsteğin gövdesi geçerli bir JSON değil.|
| 400075| Dil çifti ve kategori birleşimi geçerli değil.|
| 400077| İstek boyutu üst sınırı aşıldı. [İstek sınırlarını](../request-limits.md)görüntüleyin.|
| 400079| Ve dilinden dile arasında çeviri için istenen özel sistem yok.|
| 400080| Söz konusu dil veya betik için alfabede desteklenmez.|
| 401000| Kimlik bilgileri eksik veya geçersiz olduğundan istek yetkilendirilmemiş.|
| 401015| "Belirtilen kimlik bilgileri konuşma API 'SI içindir. Bu istek, metin API 'SI için kimlik bilgileri gerektirir. Translator için bir abonelik kullanın. "|
| 403000| İşleme izin verilmiyor.|
| 403001| Abonelik, boş kotasını aştığından işleme izin verilmiyor.|
| 405000| İstek yöntemi istenen kaynak için desteklenmiyor.|
| 408001| İstenen çeviri sistemi hazırlanıyor. Lütfen birkaç dakika sonra yeniden deneyin.|
| 408002| Gelen akış beklenirken istek zaman aşımına uğradı. İstemci, sunucunun beklemeye hazırlandığı süre içinde bir istek üretmedi. İstemci daha sonra değişiklik yapmadan isteği yineleyebilir.|
| 415000| Content-Type üst bilgisi eksik veya geçersiz.|
| 429000, 429001, 429002| İstemci istek sınırlarını aştığından, sunucu isteği reddetti.|
| 500000| Beklenmeyen bir hata oluştu. Hata devam ederse, hatayı tarih/saat ile, X-RequestId yanıt başlığından istek tanımlayıcısını ve X-Clienttraceıd istek başlığından istemci tanımlayıcısını bildirin.|
| 503000| Hizmet geçici olarak kullanılamıyor. Lütfen yeniden deneyin. Hata devam ederse, hatayı tarih/saat ile, X-RequestId yanıt başlığından istek tanımlayıcısını ve X-Clienttraceıd istek başlığından istemci tanımlayıcısını bildirin.|

## <a name="metrics"></a>Ölçümler 
Ölçümler, aşağıdaki ekran görüntüsünde gösterildiği gibi ölçümler bölümünde Azure portal, çevirmen kullanım ve kullanılabilirlik bilgilerini görüntülemenize olanak sağlar. Daha fazla bilgi için bkz. [veri ve platform ölçümleri](../../../azure-monitor/essentials/data-platform-metrics.md).

![Çevirmen ölçümleri](../media/translatormetrics.png)

Bu tablo, kullanılabilir ölçümleri, çeviri API çağrılarını izlemek için nasıl kullanıldıklarından ilgili açıklama ile listeler.

| Ölçümler | Description |
|:----|:-----|
| Toplam çağrılar| Toplam API çağrısı sayısı.|
| TotalTokenCalls| Kimlik doğrulama belirteci kullanılarak belirteç hizmeti aracılığıyla yapılan toplam API çağrısı sayısı.|
| Başarılı çağrılar| Başarılı çağrı sayısı.|
| Toplam hata sayısı| Hata yanıtı olan çağrı sayısı.|
| Blockedçağrılarında| Oran veya kota sınırını aşan çağrı sayısı.|
| ServerErrors| Sunucu iç hatası olan çağrı sayısı (5XX).|
| ClientErrors| İstemci tarafı hatası olan çağrı sayısı (4XX).|
| Gecikme süresi| İsteğin tamamlanma süresi (milisaniye).|
| Karakter Yabanslamuş| Gelen metin isteğindeki toplam karakter sayısı.|
