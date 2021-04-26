---
title: Çevirmen dilleri yöntemi
titleSuffix: Azure Cognitive Services
description: Diller yöntemi, çevirmen 'in diğer işlemleri tarafından şu anda desteklenen dil kümesini alır.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 8471983d24cfed39e6521e19366f6023e519adaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895484"
---
# <a name="translator-30-languages"></a>Translator 3,0: Diller

Çeviricisinin diğer işlemleri tarafından şu anda desteklenen dil kümesini alır. 

## <a name="request-url"></a>İstek URL’si

Şu kişiye bir `GET` istek gönder:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>İstek parametreleri

Sorgu dizesine geçirilen istek parametreleri şunlardır:

<table width="100%">
  <th width="20%">Sorgu parametresi</th>
  <th>Açıklama</th>
  <tr>
    <td>api-sürümü</td>
    <td><em>Gerekli parametre</em>.<br/>İstemci tarafından istenen API 'nin sürümü. Değer olmalıdır `3.0` .</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Isteğe bağlı parametre*.<br/>Döndürülecek dil grubunu tanımlayan adların virgülle ayrılmış bir listesi. İzin verilen grup adları: `translation` , `transliteration` ve `dictionary` . Hiçbir kapsam verilmezse, bu, geçirme ile eşdeğer olan tüm gruplar döndürülür `scope=translation,transliteration,dictionary` . Senaryolarınız için hangi desteklenen diller kümesinin uygun olduğuna karar vermek için, [Yanıt nesnesinin](#response-body)açıklamasına bakın.</td>
  </tr>
</table> 

İstek üst bilgileri:

<table width="100%">
  <th width="20%">Üst Bilgiler</th>
  <th>Description</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Isteğe bağlı istek üst bilgisi*.<br/>Kullanıcı arabirimi dizelerinde kullanılacak dil. Yanıttaki alanlardan bazıları dilin veya bölge adlarının adlarıdır. Bu adların döndürüleceği dili tanımlamak için bu parametreyi kullanın. Dil, iyi biçimlendirilmiş bir BCP 47 Language etiketi sağlayarak belirtilir. Örneğin, `fr` Fransızca 'da ad istemek için değerini kullanın veya `zh-Hant` geleneksel Çince 'de ad istemek için değeri kullanın.<br/>Adlar, hedef dil belirtilmediğinde veya yerelleştirme kullanılabilir olmadığında Ingilizce dilinde sağlanır.
    </td>
  </tr>
  <tr>
    <td>X-Clienttraceıd</td>
    <td>*Isteğe bağlı istek üst bilgisi*.<br/>İsteği benzersiz şekilde tanımlamak için istemci tarafından oluşturulan bir GUID.</td>
  </tr>
</table> 

Dil kaynaklarını almak için kimlik doğrulaması gerekli değildir.

## <a name="response-body"></a>Yanıt gövdesi

İstemci, `scope` hangi dil gruplarını ilgilendiğinizi tanımlamak için sorgu parametresini kullanır.

* `scope=translation` metni bir dilden başka bir dile çevirmek için desteklenen dilleri sağlar;

* `scope=transliteration` bir dildeki metni bir betiğe başka bir betiğe dönüştürmek için yetenekler sağlar;

* `scope=dictionary``Dictionary`işlem verileri döndüren dil çiftlerini sağlar.

Bir istemci, virgülle ayrılmış adların bir listesini belirterek birkaç grubu eşzamanlı alabilir. Örneğin, `scope=translation,transliteration,dictionary` tüm gruplar için desteklenen diller döndürür.

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

* `translation` özelliði

  `translation`Özelliğin değeri (anahtar, değer) çiftlerinin bir sözlüğüdür. Her anahtar bir BCP 47 dili etikettir. Bir anahtar, metnin çevrilebilmesi veya çevrilebilmesi için bir dil tanımlar. Anahtarla ilişkili değer, dili tanımlayan özellikleri içeren bir JSON nesnesidir:

  * `name`: Başlık aracılığıyla istenen yerel ayarda dilin görünen adı `Accept-Language` .

  * `nativeName`: Bu dilin yerel ayarında bulunan dilin görünen adı.

  * `dir`: `rtl` Sağdan sola diller veya soldan sağa diller için olan yönlülük `ltr` .

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

* `transliteration` özelliði

  `transliteration`Özelliğin değeri (anahtar, değer) çiftlerinin bir sözlüğüdür. Her anahtar bir BCP 47 dili etikettir. Bir anahtar, metnin bir betikten başka bir betiğe dönüştürülebileceği dili tanımlar. Anahtarla ilişkili değer, dili ve desteklenen betikleri tanımlayan özellikleri içeren bir JSON nesnesidir:

  * `name`: Başlık aracılığıyla istenen yerel ayarda dilin görünen adı `Accept-Language` .

  * `nativeName`: Bu dilin yerel ayarında bulunan dilin görünen adı.

  * `scripts`: Dönüştürülecek betikler listesi. Listedeki her öğe için `scripts` Özellikler vardır:

    * `code`: Betiği tanımlayan kod.

    * `name`: Üst bilgide istenen yerel ayarda bulunan betiğin görünen adı `Accept-Language` .

    * `nativeName`: Dil için yerel ayarda dilin görünen adı.

    * `dir`: `rtl` Sağdan sola diller veya soldan sağa diller için olan yönlülük `ltr` .

    * `toScripts`: Metni dönüştürmek için kullanılabilen betikler listesi. Listenin her öğesi, `toScripts` `code` `name` `nativeName` `dir` daha önce açıklandığı gibi özellikler,, ve ' dir.

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

* `dictionary` özelliði

  `dictionary`Özelliğin değeri (anahtar, değer) çiftlerinin bir sözlüğüdür. Her anahtar bir BCP 47 dili etikettir. Anahtar, alternatif çevirilerin ve geri çevirilerin kullanılabildiği dili tanımlar. Değer, kullanılabilir çevirileri içeren kaynak dilini ve hedef dilleri açıklayan bir JSON nesnesidir:

  * `name`: Başlık aracılığıyla istenen yerel ayarda kaynak dilin görünen adı `Accept-Language` .

  * `nativeName`: Bu dilin yerel ayarında bulunan dilin görünen adı.

  * `dir`: `rtl` Sağdan sola diller veya soldan sağa diller için olan yönlülük `ltr` .

  * `translations`: Değişiklikleri ve kaynak dilde ifade edilen sorgu örnekleri içeren dillerin listesi. Listedeki her öğe için `translations` Özellikler vardır:

    * `name`: Başlık aracılığıyla istenen yerel ayarda hedef dilin görünen adı `Accept-Language` .

    * `nativeName`: Hedef dilin yerel ayarda bulunan hedef dilin görünen adı.

    * `dir`: `rtl` Sağdan sola diller veya soldan sağa diller için olan yönlülük `ltr` .
    
    * `code`: Hedef dili tanımlayan dil kodu.

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

Desteklenen dillerin listesi sıklıkla değişmeyecektir. Ağ bant genişliğini kaydetmek ve yanıt hızını artırmak için, bir istemci uygulaması dil kaynaklarını ve ilgili varlık etiketini () önbelleğe almayı göz önünde bulundurmalıdır `ETag` . Daha sonra, istemci uygulaması düzenli olarak (örneğin, 24 saatte bir kez), desteklenen dillerin en son kümesini getirmek üzere hizmeti sorgular. `ETag`Üstbilgi alanına geçerli değeri geçirmek `If-None-Match` hizmetin yanıtı iyileştirmelerine izin verir. Kaynak değiştirilmediyse, hizmet 304 durum kodunu ve boş bir yanıt gövdesini döndürür.

## <a name="response-headers"></a>Yanıt üst bilgileri

<table width="100%">
  <th width="20%">Üst Bilgiler</th>
  <th>Description</th>
  <tr>
    <td>Özelliği</td>
    <td>İstenen desteklenen diller grupları için varlık etiketinin geçerli değeri. Sonraki istekleri daha verimli hale getirmek için, istemci `ETag` değeri bir `If-None-Match` başlık alanında gönderebilir.
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
  <th width="20%">Durum Kodu</th>
  <th>Description</th>
  <tr>
    <td>200</td>
    <td>Başarılı.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>Kaynak, istek üstbilgileri tarafından belirtilen sürümden bu yana değiştirilmedi `If-None-Match` .</td>
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
    <td>Beklenmeyen bir hata oluştu. Hata devam ederse, bununla raporla: hatanın tarih ve saati, yanıt başlığından istek tanımlayıcısı `X-RequestId` ve istek üst bilgisinden istemci tanımlayıcısı `X-ClientTraceId` .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Sunucu geçici olarak kullanılamıyor. İsteği yeniden deneyin. Hata devam ederse, bununla raporla: hatanın tarih ve saati, yanıt başlığından istek tanımlayıcısı `X-RequestId` ve istek üst bilgisinden istemci tanımlayıcısı `X-ClientTraceId` .</td>
  </tr>
</table> 

Bir hata oluşursa, istek bir JSON hata yanıtı da döndürür. Hata kodu 3 basamaklı HTTP durum kodunu birleştiren 6 basamaklı bir sayıdır ve ardından hatayı daha fazla kategorilere ayırarak 3 basamaklı bir sayıdır. Ortak hata kodları, [v3 Translator başvurusu sayfasında](./v3-0-reference.md#errors)bulunabilir. 

## <a name="examples"></a>Örnekler

Aşağıdaki örnek, metin çevirisi için desteklenen dillerin nasıl alınacağını gösterir.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```