---
title: Translator Metin Çevirisi API'si dilleri yöntemi
titleSuffix: Azure Cognitive Services
description: Diller yöntemi, Translator Metin Çevirisi API'si diğer işlemleri tarafından şu anda desteklenen dil kümesini alır.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 37f70399e8125db559098869cdfffdf4533498d7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835826"
---
# <a name="translator-text-api-30-languages"></a>Translator Metin Çevirisi API'si 3,0: Diller

Translator Metin Çevirisi API'si diğer işlemleri tarafından şu anda desteklenen dil kümesini alır. 

## <a name="request-url"></a>İstek URL'si

`GET` isteği gönder:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>İstek parametreleri

Sorgu dizesine geçirilen istek parametreleri şunlardır:

<table width="100%">
  <th width="20%">Sorgu parametresi</th>
  <th>Açıklama</th>
  <tr>
    <td>api sürümü</td>
    <td><em>Gerekli parametre</em>.<br/>İstemci tarafından istenen API 'nin sürümü. Değer `3.0`olmalıdır.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Isteğe bağlı parametre*.<br/>Döndürülecek dil grubunu tanımlayan adların virgülle ayrılmış bir listesi. İzin verilen grup adları şunlardır: `translation`, `transliteration` ve `dictionary`. Hiçbir kapsam verilmezse, tüm gruplar döndürülür ve bu, `scope=translation,transliteration,dictionary`geçirme ile eşdeğerdir. Senaryolarınız için hangi desteklenen diller kümesinin uygun olduğuna karar vermek için, [Yanıt nesnesinin](#response-body)açıklamasına bakın.</td>
  </tr>
</table> 

İstek üst bilgileri:

<table width="100%">
  <th width="20%">Üst bilgiler</th>
  <th>Açıklama</th>
  <tr>
    <td>Kabul etme-dil</td>
    <td>*Isteğe bağlı istek üst bilgisi*.<br/>Kullanıcı arabirimi dizelerinde kullanılacak dil. Yanıttaki alanlardan bazıları dilin veya bölge adlarının adlarıdır. Bu adların döndürüleceği dili tanımlamak için bu parametreyi kullanın. Dil, iyi biçimlendirilmiş bir BCP 47 Language etiketi sağlayarak belirtilir. Örneğin, Fransızca 'da ad istemek için `fr` değerini kullanın veya Çince Geleneksel olarak ad istemek için `zh-Hant` değerini kullanın.<br/>Adlar, hedef dil belirtilmediğinde veya yerelleştirme kullanılabilir olmadığında Ingilizce dilinde sağlanır.
    </td>
  </tr>
  <tr>
    <td>X-Clienttraceıd</td>
    <td>*Isteğe bağlı istek üst bilgisi*.<br/>İsteği benzersiz şekilde tanımlamak için istemci tarafından oluşturulan bir GUID.</td>
  </tr>
</table> 

Dil kaynaklarını almak için kimlik doğrulaması gerekli değildir.

## <a name="response-body"></a>Yanıt gövdesi

İstemci, ilgilendiğiniz dil gruplarını tanımlamak için `scope` sorgu parametresini kullanır.

* `scope=translation`, metni bir dilden başka bir dile çevirmek için desteklenen dilleri sağlar;

* `scope=transliteration`, bir dildeki metni bir betiğe başka bir betiğe dönüştürmek için yetenekler sağlar;

* `scope=dictionary`, `Dictionary` işlemleri için veri döndürdüğü dil çiftleri sağlar.

Bir istemci, virgülle ayrılmış adların bir listesini belirterek birkaç grubu eşzamanlı alabilir. Örneğin, `scope=translation,transliteration,dictionary` tüm gruplar için desteklenen diller döndürebilir.

Başarılı bir yanıt, istenen her grup için bir özelliği olan bir JSON nesnesidir:

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

Her bir özelliğin değeri aşağıdaki gibidir.

* `translation` özelliği

  `translation` özelliğinin değeri (anahtar, değer) çiftlerinin bir sözlüğüdür. Her anahtar bir BCP 47 dili etikettir. Bir anahtar, metnin çevrilebilmesi veya çevrilebilmesi için bir dil tanımlar. Anahtarla ilişkili değer, dili tanımlayan özellikleri içeren bir JSON nesnesidir:

  * `name`: `Accept-Language` üst bilgisi aracılığıyla istenen yerel ayarda dilin görünen adı.

  * `nativeName`: bu dilin yerel ayarında bulunan dilin görünen adı.

  * `dir`: sağdan sola diller veya soldan sağa dillerin `ltr` için `rtl` yönlülük.

  Örnek:
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration` özelliği

  `transliteration` özelliğinin değeri (anahtar, değer) çiftlerinin bir sözlüğüdür. Her anahtar bir BCP 47 dili etikettir. Bir anahtar, metnin bir betikten başka bir betiğe dönüştürülebileceği dili tanımlar. Anahtarla ilişkili değer, dili ve desteklenen betikleri tanımlayan özellikleri içeren bir JSON nesnesidir:

  * `name`: `Accept-Language` üst bilgisi aracılığıyla istenen yerel ayarda dilin görünen adı.

  * `nativeName`: bu dilin yerel ayarında bulunan dilin görünen adı.

  * `scripts`: dönüştürülecek betiklerin listesi. `scripts` listesindeki her öğe için özellikler vardır:

    * `code`: betiği tanımlayan kod.

    * `name`: `Accept-Language` üst bilgisi aracılığıyla istenen yerel ayarda betiğinin görünen adı.

    * `nativeName`: dilin yerel ayarında dilin görünen adı.

    * `dir`: sağdan sola diller veya soldan sağa dillerin `ltr` için `rtl` yönlülük.

    * `toScripts`: metni dönüştürmek için kullanılabilen betikler listesi. `toScripts` listenin her öğesi, daha önce açıklandığı gibi özellikler `code`, `name`, `nativeName`ve `dir` sahiptir.

  Örnek:

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary` özelliği

  `dictionary` özelliğinin değeri (anahtar, değer) çiftlerinin bir sözlüğüdür. Her anahtar bir BCP 47 dili etikettir. Anahtar, alternatif çevirilerin ve geri çevirilerin kullanılabildiği dili tanımlar. Değer, kullanılabilir çevirileri içeren kaynak dilini ve hedef dilleri açıklayan bir JSON nesnesidir:

  * `name`: `Accept-Language` üst bilgisi aracılığıyla istenen yerel ayarda bulunan kaynak dilin görünen adı.

  * `nativeName`: bu dilin yerel ayarında bulunan dilin görünen adı.

  * `dir`: sağdan sola diller veya soldan sağa dillerin `ltr` için `rtl` yönlülük.

  * `translations`: daha farklı çeviriler ve kaynak dilde ifade edilen sorgu örnekleri içeren dillerin listesi. `translations` listesindeki her öğe için özellikler vardır:

    * `name`: `Accept-Language` üst bilgisi aracılığıyla istenen yerel ayarda bulunan hedef dilin görünen adı.

    * `nativeName`: hedef dilin yerel ayarda bulunan hedef dilin görünen adı.

    * `dir`: sağdan sola diller veya soldan sağa dillerin `ltr` için `rtl` yönlülük.
    
    * `code`: hedef dili tanımlayan dil kodu.

  Örnek:

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

Yanıt nesnesinin yapısı, API sürümünde değişiklik yapılmadan değişmeyecektir. Aynı API sürümü için, Microsoft Translator, Hizmetleri tarafından desteklenen dillerin listesini sürekli olarak genişlettiğinden, kullanılabilir dillerin listesi zaman içinde değişebilir.

Desteklenen dillerin listesi sıklıkla değişmeyecektir. Ağ bant genişliğini kaydetmek ve yanıt hızını artırmak için bir istemci uygulaması, dil kaynaklarını ve ilgili varlık etiketini (`ETag`) önbelleğe almayı düşünmelidir. Daha sonra, istemci uygulaması düzenli olarak (örneğin, 24 saatte bir kez), desteklenen dillerin en son kümesini getirmek üzere hizmeti sorgular. Geçerli `ETag` değerini bir `If-None-Match` üst bilgi alanına geçirmek hizmetin yanıtı iyileştirmelerine izin verir. Kaynak değiştirilmediyse, hizmet 304 durum kodunu ve boş bir yanıt gövdesini döndürür.

## <a name="response-headers"></a>Yanıt üst bilgileri

<table width="100%">
  <th width="20%">Üst bilgiler</th>
  <th>Açıklama</th>
  <tr>
    <td>Özelliği</td>
    <td>İstenen desteklenen diller grupları için varlık etiketinin geçerli değeri. Sonraki istekleri daha verimli hale getirmek için, istemci `ETag` değerini bir `If-None-Match` üst bilgi alanında gönderebilir.
    </td>
  </tr>
  <tr>
    <td>X-RequestId</td>
    <td>İsteği tanımlamak için hizmet tarafından oluşturulan değer. Sorun giderme amacıyla kullanılır.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Yanıt durum kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir. 

<table width="100%">
  <th width="20%">Durum kodu</th>
  <th>Açıklama</th>
  <tr>
    <td>200</td>
    <td>Başarılı.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>İstek üst bilgileri tarafından belirtilen sürümden sonra kaynak değiştirilmedi `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Sorgu parametrelerinden biri eksik veya geçersiz. Yeniden denemeden önce istek parametrelerini düzeltin.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>İstemci istek sınırlarını aştığından, sunucu isteği reddetti.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Beklenmeyen bir hata oluştu. Hata devam ederse, bununla raporla: hatanın tarih ve saati, yanıt üst bilgisi `X-RequestId`istek tanımlayıcısı ve istek üst bilgisi `X-ClientTraceId`istemci tanımlayıcısı.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Sunucu geçici olarak kullanılamıyor. İsteği yeniden deneyin. Hata devam ederse, bununla raporla: hatanın tarih ve saati, yanıt üst bilgisi `X-RequestId`istek tanımlayıcısı ve istek üst bilgisi `X-ClientTraceId`istemci tanımlayıcısı.</td>
  </tr>
</table> 

Bir hata oluşursa, istek bir JSON hata yanıtı da döndürür. Hata kodu 3 basamaklı HTTP durum kodunu birleştiren 6 basamaklı bir sayıdır ve ardından hatayı daha fazla kategorilere ayırarak 3 basamaklı bir sayıdır. Ortak hata kodları, [v3 Translator metin çevirisi API'si başvuru sayfasında](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)bulunabilir. 

## <a name="examples"></a>Örnekler

Aşağıdaki örnek, metin çevirisi için desteklenen dillerin nasıl alınacağını gösterir.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
