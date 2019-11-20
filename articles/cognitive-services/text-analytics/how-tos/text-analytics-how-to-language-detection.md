---
title: Metin Analizi REST API dili algılayın
titleSuffix: Azure Cognitive Services
description: Azure bilişsel hizmetler 'deki Metin Analizi REST API kullanarak dili algılayın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: d34f3a03e1bcd35c270d13c4dda57d0394a36e4b
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387800"
---
# <a name="example-detect-language-with-text-analytics"></a>Örnek: Metin Analizi Dili algıla

Azure Metin Analizi REST API [dil algılama](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) özelliği, her belge için metin girişini değerlendirir ve analizin gücünü gösteren bir puanla birlikte Dil tanımlayıcıları döndürür.

Bu özellik, dilin bilinmediği rastgele metni toplayan içerik depoları için kullanışlıdır. Giriş belgesinde hangi dilin kullanıldığını belirlemek için bu analizin sonuçlarını ayrıştırabilirsiniz. Yanıt Ayrıca modelin güvenini yansıtan bir puan döndürür. Puan değeri 0 ile 1 arasındadır.

Dil Algılama özelliği, çeşitli diller, çeşitler, diapacts ve bazı bölgesel veya kültürel dillerini algılayabilir. Bu özellik için dillerin tam listesi yayımlanmamıştır.

Daha az sıklıkta kullanılan bir dilde ifade ettiğiniz bir içeriğiniz varsa, bir kodu döndürüp döndürdüğünü görmek için Dil Algılama özelliğini deneyebilirsiniz. Algılanamayan dillerin yanıtı `unknown`.

> [!TIP]
> Metin Analizi Ayrıca, dil algılama için Linux tabanlı bir Docker kapsayıcı görüntüsü sağlar, bu sayede Metin Analizi kapsayıcısını verilerinize yakın şekilde [yükleyip çalıştırabilirsiniz](text-analytics-how-to-install-containers.md) .

## <a name="preparation"></a>Hazırlık

Bu biçimde JSON belgelerinize sahip olmanız gerekir: KIMLIK ve metin.

Belge boyutunun belge başına 5.120 karakter altında olması gerekir. Koleksiyon başına en fazla 1.000 öğe (kimlik) kullanabilirsiniz. Koleksiyon, istek gövdesinde gönderilir. Aşağıdaki örnek, dil algılama için gönderebilmeniz gerekebilecek içeriğe bir örnektir:

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>1\. adım: İsteği yapısı

İstek tanımı hakkında daha fazla bilgi için bkz. [Metin Analizi API'si çağırma](text-analytics-how-to-call-api.md). Kolaylık olması için aşağıdaki noktalar yeniden belirtilmektedir:

+ POST isteği oluşturun. Bu isteğin API belgelerini gözden geçirmek için [DIL ALGıLAMA API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)'sine bakın.

+ Dil algılama için HTTP uç noktasını ayarlayın. Azure 'da bir Metin Analizi kaynağı veya örneği oluşturulmuş bir [metin analizi kapsayıcısı](text-analytics-how-to-install-containers.md)kullanın. URL 'ye dahil `/text/analytics/v2.1/languages` etmeniz gerekir. Örneğin: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`

+ Metin Analizi işlemler için [erişim anahtarı](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) eklemek üzere bir istek üst bilgisi ayarlayın.

+ İstek gövdesinde, bu analiz için hazırladığınız JSON belgeleri koleksiyonunu sağlayın.

> [!Tip]
> İsteği yapılandırmak ve hizmete GÖNDERMEK için [Postman](text-analytics-how-to-call-api.md) kullanın veya [belgelerdeki](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) **API testi konsolu**’nu açın.

## <a name="step-2-post-the-request"></a>2\. adım: İsteği gönder

İstek alındığında analiz gerçekleştirilir. Dakika ve saniye başına gönderebilmeniz için istek boyutu ve sayısı hakkında bilgi için genel bakış konusunun [veri sınırları](../overview.md#data-limits) bölümüne bakın.

Hizmetin durum bilgisi olmadığını unutmayın. Hesabınızda bir veri depolanmaz. Sonuçlar hemen yanıtta döndürülür.


## <a name="step-3-view-the-results"></a>3\. adım: Sonuçları görüntüleme

Tüm POST istekleri, kimliklerle ve algılanan özelliklerle JSON biçimli bir yanıt döndürür.

Hemen çıktı döndürülür. Sonuçları JSON kabul eden bir uygulamaya veya çıktıyı yerel sistemdeki bir dosyaya kaydedebilirsiniz. Sonra çıktıyı, verileri sıralamak, aramak ve işlemek için kullanabileceğiniz bir uygulamaya içeri aktarın.

Örnek istek için sonuçlar, aşağıdaki JSON gibi görünmelidir. Birden çok öğe içeren bir belge olduğuna dikkat edin. Çıktı İngilizce dilindedir. Dil tanımlayıcıları bir kolay ad ve [ISO 639-1](https://www.iso.org/standard/22109.html) biçiminde dil kodu içerir.

1,0 pozitif puanı, analizin mümkün olan en yüksek güvenilirlik düzeyini ifade eder.

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "Spanish",
                        "iso6391Name": "es",
                        "score": 1
                    }
                ]
            },
            {
                "id": "3",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            },
            {
                "id": "4",
                "detectedLanguages": [
                    {
                        "name": "Chinese_Simplified",
                        "iso6391Name": "zh_chs",
                        "score": 1
                    }
                ]
            },
            {
                "id": "5",
                "detectedLanguages": [
                    {
                        "name": "Russian",
                        "iso6391Name": "ru",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="ambiguous-content"></a>Belirsiz içerik

Bazı durumlarda, girişi temel alarak dilleri ayırt etmek zor olabilir. `countryHint` Parametresini 2 harfli bir ülke kodu belirtmek için kullanabilirsiniz. Varsayılan olarak, API varsayılan Countryipucu olarak "US" kullanıyor, bu davranışı kaldırmak için bu değeri boş dize `countryHint = ""` olarak ayarlayarak bu parametreyi sıfırlayabilirsiniz.

Örneğin, "Imkansız" hem Ingilizce hem de Fransızca için ortaktır ve sınırlı bağlamla verildiyse, yanıt "ABD" ülke ipucunu temel alır. Metnin başlangıcının, ipucu olarak verilen Fransa 'dan geldiği bilinirse.

**Girdi**

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

Hizmetin artık daha iyi bir değerlendirme yapmak için ek bağlamı vardır: 

**Çıktı**

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Çözümleyici girişi ayrıştıramaz, döndürür `(Unknown)`. Yalnızca Arapça rakamları içeren bir metin bloğu gönderirseniz örnek bir örnektir.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "score": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Karışık dil içeriği

Aynı belge içindeki karışık dil içeriği, içerikte en büyük gösterimle ve daha düşük pozitif bir derecelendirmeden dili döndürür. Derecelendirme, değerlendirmenin marjinal kuvvetini yansıtır. Aşağıdaki örnekte giriş, İngilizce, İspanyolca ve Fransızca dillerinin birleşimidir. Çözümleyici, hakim dili belirlemek için her bir kesimdeki karakterleri sayar.

**Girdi**

```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
      ]
    }
```

**Çıktı**

Elde edilen çıktı, daha zayıf bir güvenilirlik düzeyini belirten 1,0 ' den az bir puan içeren, önceden baskın dilden oluşur.

```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 0.9375
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>Özet

Bu makalede, Azure bilişsel hizmetler 'de Metin Analizi kullanarak dil algılama kavramlarını ve iş akışını öğrendiniz. Aşağıdaki noktaları açıklanmaktadır ve gösterilmiştir:

+ [Dil algılama](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) , çok çeşitli diller, çeşitler, diapacts ve bazı bölgesel veya kültürel dilleri için kullanılabilir.
+ İstek gövdesindeki JSON belgeleri bir KIMLIK ve metin içerir.
+ POST isteği, kişiselleştirilmiş bir `/languages` [erişim anahtarı ve](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) aboneliğiniz için geçerli olan bir uç nokta kullanarak bir uç noktaya gönderilir.
+ Yanıt çıktısı her belge KIMLIĞI için dil tanımlayıcılarından oluşur. Çıktı, JSON kabul eden herhangi bir uygulamaya akışla eklenebilir. Örnek uygulamalar, birkaç kez ad vermek için Excel ve Power BI içerir.

## <a name="see-also"></a>Ayrıca bkz.

 [Metin analizi genel bakış](../overview.md) [Sık sorulan sorular (SSS)](../text-analytics-resource-faq.md)</br>
 [Metin Analizi ürün sayfası](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yaklaşımı analiz etme](text-analytics-how-to-sentiment-analysis.md)
