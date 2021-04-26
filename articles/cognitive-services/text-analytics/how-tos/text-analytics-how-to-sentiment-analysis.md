---
title: Metin Analizi REST API ile yaklaşım analizini ve fikrinizi araştırma yapın
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure bilişsel hizmetler Metin Analizi API'si ile metinde nasıl yaklaşım ve e-posta ile ilgili nasıl algılanacağı gösterilir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 03/29/2021
ms.author: aahi
ms.openlocfilehash: 7cd2b0a6b943ceb32420ef119a7fc5eddefa2e19
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277003"
---
# <a name="how-to-sentiment-analysis-and-opinion-mining"></a>Nasıl yapılır: yaklaşım Analizi ve Görüşleme madenciliği

Metin Analizi API'si Yaklaşım Analizi özelliği pozitif ve olumsuz yaklaşımı tespit etmek için iki yol sağlar. Bir Yaklaşım Analizi isteği gönderirseniz, API, tümce ve belge düzeyindeki yaklaşım etiketlerini ("negatif", "nötr" ve "pozitif") ve güvenirlik puanlarını döndürür. Ayrıca, kelimeyle ilgili (ürünlerin veya hizmetlerin öznitelikleri gibi) sözcüklerle ilgili ayrıntılı bilgiler sağlayan Yaklaşım Analizi uç noktası kullanarak da bilgi araştırma istekleri gönderebilirsiniz.

API tarafından kullanılan AI modelleri hizmet tarafından sağlanır, yalnızca analiz için içerik göndermeniz gerekir.

## <a name="sentiment-analysis-versions-and-features"></a>Sürümler ve Özellikler Yaklaşım Analizi

| Özellik                                   | Yaklaşım Analizi v3 | Yaklaşım Analizi v 3.1 (Önizleme) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Tek ve toplu istekler için Yöntemler    | X                     | X                                 |
| Yaklaşım Analizi puanları ve etiketleme             | X                     | X                                 |
| Linux tabanlı [Docker kapsayıcısı](text-analytics-how-to-install-containers.md) | X  |  |
| Görüşün madenciliği                            |                       | X                                 |

## <a name="sentiment-analysis"></a>Yaklaşım Analizi

Sürüm 3. x ' teki Yaklaşım Analizi, bir tümce ve belge düzeyinde döndürülen ve her biri için güven puanı bulunan yaklaşım etiketlerini metne uygular. 

Etiketler *pozitif*, *negatif* ve *nötr*. Belge düzeyinde, *karışık* yaklaşım etiketi de döndürülebilir. Belgenin yaklaşımı aşağıda belirlenir:

| Tümce yaklaşımı                                                                            | Döndürülen belge etiketi |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Belgede en az bir `positive` cümle vardır. Tümcelerin geri kalanı şunlardır `neutral` . | `positive`              |
| Belgede en az bir `negative` cümle vardır. Tümcelerin geri kalanı şunlardır `neutral` . | `negative`              |
| Belgede en az bir `negative` cümle ve en az bir `positive` cümle vardır.    | `mixed`                 |
| Belgedeki tüm tümceler `neutral` .                                                  | `neutral`               |

Güvenirlik puanları 1 ile 0 arasındadır. 1 ' e yakın puan, etiketin sınıflandırmasında daha yüksek bir güven gösterir, ancak daha düşük puanlar daha düşük güvenilirliğe işaret ediyor. Her belge veya her tümce için, etiketlerle ilişkilendirilen tahmin edilen puanlar (pozitif, negatif ve nötr) 1 ' e kadar bir değer ekler. Daha fazla bilgi için [metin analizi saydamlık notuna](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context)bakın. 

## <a name="opinion-mining"></a>Görüşün madenciliği

Fikrinizi araştırma, 3,1 sürümünün önizlemeden başlayarak Yaklaşım Analizi özelliğidir. Doğal dil Işlemede (NLP) en boy tabanlı Yaklaşım Analizi olarak da bilinen bu özellik, metin içindeki ürünlerin veya hizmetlerin öznitelikleriyle ilgili daha ayrıntılı bilgiler sağlar. API yüzeyleri, bir hedef (ad veya fiil) ve bir değerlendirme (sıfatı) olarak opklarca.

Örneğin, bir müşteri bir otel hakkında "Oda harika, ancak personelin kullanımı çok iyiydi" gibi bir otel hakkında geri bildirim bırakırsa, bu görüşmelerin, metindeki hedefleri (yönleri) ve bunlarla ilişkili değerlendirmeleri (oplarons) ve duygularını bulmaları gerekir. Yaklaşım Analizi yalnızca negatif bir yaklaşım rapor edebilir.

:::image type="content" source="../media/how-tos/opinion-mining.png" alt-text="Fikrinizi araştırma örneği diyagramı" lightbox="../media/how-tos/opinion-mining.png":::

Sonuçlarınızda dikkatli araştırma elde etmek için, `opinionMining=true` bayrağı bir yaklaşım Analizi isteğine dahil etmeniz gerekir. Fikrinizi araştırma sonuçları, yaklaşım Analizi yanıtına dahil edilir. Fikrinizi araştırma bir Yaklaşım Analizi uzantısıdır ve geçerli [fiyatlandırma katmanınıza](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)dahil edilmiştir.


## <a name="sending-a-rest-api-request"></a>REST API isteği gönderiliyor 

### <a name="preparation"></a>Hazırlık

Yaklaşım analizi, üzerinde çalışmak üzere daha az miktarda metin verdiğiniz zaman daha yüksek kalitede bir sonuç üretir. Bu, büyük metin öbekleri üzerinde daha iyi performans gösteren anahtar ifade ayıklamasının tersidir. Her iki işlemden de en iyi sonuçları almak için, girişleri buna göre yeniden yapılandırmayı göz önünde bulundurun.

Bu biçimde JSON belgelerinize sahip olmanız gerekir: KIMLIK, metin ve dil. Yaklaşım Analizi, önizleme aşamasında çok çeşitli dilleri destekler. Daha fazla bilgi için bkz. [Desteklenen diller](../language-support.md).

Belge boyutunun belge başına 5.120 karakter altında olması gerekir. Bir koleksiyonda izin verilen en fazla belge sayısı için kavramlar bölümündeki [veri limitleri](../concepts/data-limits.md?tabs=version-3) makalesine bakın. Koleksiyon, istek gövdesinde gönderilir.

## <a name="structure-the-request"></a>İsteği yapısı

Bir POST isteği oluşturun. Hızlı bir şekilde yapısına ve gönderebilmeniz için aşağıdaki başvuru bağlantılarında [Postman](text-analytics-how-to-call-api.md) veya **API test konsolunu** kullanabilirsiniz. 

#### <a name="version-31-preview"></a>[Sürüm 3,1-Önizleme](#tab/version-3-1)

[Yaklaşım Analizi v 3.1 başvurusu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

[Yaklaşım Analizi v3 başvurusu](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>İstek uç noktaları

Azure 'da bir Metin Analizi kaynağı veya bir örneklenmiş [metin analizi kapsayıcısı](text-analytics-how-to-install-containers.md)kullanarak yaklaşım ANALIZI için HTTPS uç noktasını ayarlayın. Kullanmak istediğiniz sürüm için doğru URL 'YI dahil etmeniz gerekir. Örnek:

> [!NOTE]
> Metin Analizi kaynağınız için anahtarınızı ve uç noktanızı Azure portal bulabilirsiniz. Kaynak **yönetimi** altında kaynağın **hızlı başlangıç** sayfasında yer alır. 

#### <a name="version-31-preview"></a>[Sürüm 3,1-Önizleme](#tab/version-3-1)

**Yaklaşım Analizi**

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/sentiment`

**Görüşün madenciliği**

Fikrinizi araştırma sonuçları almak için parametresini eklemeniz gerekir `opinionMining=true` . Örnek:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/sentiment?opinionMining=true`

Bu parametre `false` Varsayılan olarak olarak ayarlanır. 

#### <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

**Yaklaşım Analizi**

V 3.0 'da, kullanılabilir tek uç nokta Yaklaşım Analizi içindir.
 
`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

Metin Analizi API'si anahtarınızı dahil etmek için bir istek üst bilgisi ayarlayın. İstek gövdesinde, bu analiz için hazırladığınız JSON belgeleri koleksiyonunu sağlayın.

### <a name="example-request-for-sentiment-analysis-and-opinion-mining"></a>Yaklaşım Analizi ve görüşme madenciliği için örnek istek  

Aşağıda, yaklaşım analizi için gönderebileceğiniz bir içerik örneği verilmiştir. İstek biçimi hem hem de için aynıdır `v3.0` `v3.1-preview` .
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>İsteği gönder

İstek alındığında analiz gerçekleştirilir. Dakika ve saniye başına gönderebilmeniz için istek boyutu ve sayısı hakkında bilgi için genel bakış konusunun [veri sınırları](../overview.md#data-limits) bölümüne bakın.

Metin Analizi API'si durum bilgisiz. Hesabınızda hiç veri depolanmaz ve sonuçlar yanıt içinde hemen döndürülür.


### <a name="view-the-results"></a>Sonuçları görüntüleme

Hemen çıktı döndürülür. Sonuçları JSON kabul eden bir uygulamaya veya çıktıyı yerel sistemdeki bir dosyaya kaydedebilirsiniz. Sonra çıktıyı, verileri sıralamak, aramak ve işlemek için kullanabileceğiniz bir uygulamaya içeri aktarın. Çok dilli ve Emoji desteği nedeniyle, yanıt metin uzaklıkları içerebilir. Daha fazla bilgi için bkz. [uzaklıkları işleme](../concepts/text-offsets.md) .

#### <a name="version-31-preview"></a>[Sürüm 3,1-Önizleme](#tab/version-3-1)

### <a name="sentiment-analysis-and-opinion-mining-example-response"></a>Yaklaşım Analizi ve görüşün Inceleme örneği yanıtı

> [!IMPORTANT]
> Aşağıda, API 'nin v 3.1 ' de sunulan, Yaklaşım Analizi ile birlikte, bir,,, ile ilgili olarak, bir ve daha fazla İstek araştırma isteğinde yoksa, API yanıtı **sürüm 3,0** sekmesi ile aynı olacaktır.  

Yaklaşım Analizi v 3.1, hem Yaklaşım Analizi hem de görüşün madenciliği için yanıt nesneleri döndürebilir.
  
Yaklaşım analizi, tüm belge ve içindeki her cümle için bir yaklaşım etiketi ve Güvenirlik puanı döndürür. 1 ' e yakın puan, etiketin sınıflandırmasında daha yüksek bir güven gösterir, ancak daha düşük puanlar daha düşük güvenilirliğe işaret ediyor. Bir belgede birden fazla tümce olabilir ve her belge veya tümce içindeki güven puanları 1 ' e kadar eklenebilir.

Fikrinizi Inceleme, metinde hedefleri (isimler veya fiiller) ve bunlarla ilişkili değerlendirmeyi (sıfatıcı) bulur. Aşağıdaki yanıtta, *restorana tümce harika yiyecek içeriyordu ve garson kullanımı kolay* iki hedefe sahiptir: *yiyecek* ve *garson*. Her bir hedefin `relations` özelliği, `ref` ilişkili `documents` , ve nesnelerine URI başvurusu olan bir değer içerir `sentences` `assessments` .

API, bir hedef (ad veya fiil) ve bir değerlendirme (sıfatıcı) olarak opın 'leri döndürür.

```json
{
  "documents": [
    {
      "id": "1",
      "sentiment": "positive",
      "confidenceScores": {
        "positive": 1,
        "neutral": 0,
        "negative": 0
      },
      "sentences": [
        {
          "sentiment": "positive",
          "confidenceScores": {
            "positive": 1,
            "neutral": 0,
            "negative": 0
          },
          "offset": 0,
          "length": 58,
          "text": "The restaurant had great food and our waiter was friendly.",
          "targets": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 25,
              "length": 4,
              "text": "food",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/0"
                }
              ]
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 38,
              "length": 6,
              "text": "waiter",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/1"
                }
              ]
            }
          ],
          "assessments": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 19,
              "length": 5,
              "text": "great",
              "isNegated": false
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 49,
              "length": 8,
              "text": "friendly",
              "isNegated": false
            }
          ]
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

### <a name="sentiment-analysis-example-response"></a>Yaklaşım Analizi örnek yanıt

Yaklaşım analizi, tüm belge ve içindeki her cümle için bir yaklaşım etiketi ve Güvenirlik puanı döndürür. 1 ' e yakın puan, etiketin sınıflandırmasında daha yüksek bir güven gösterir, ancak daha düşük puanlar daha düşük güvenilirliğe işaret ediyor. Bir belgede birden fazla tümce olabilir ve her belge veya tümce içindeki güven puanları 1 ' e kadar eklenebilir.

Yaklaşım Analizi v3 'ten gelen yanıtlar, çözümlenen her tümce ve belge için yaklaşım etiketleri ve puanları içerir.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>Özet

Bu makalede, Metin Analizi API'si kullanarak yaklaşım analizi için kavramları ve iş akışını öğrendiniz. Özet:

+ Yaklaşım Analizi ve görüşme madenciliği seçme dilleri için kullanılabilir.
+ İstek gövdesindeki JSON belgeleri bir KIMLIK, metin ve dil kodu içerir.
+ POST isteği, `/sentiment` kişiselleştirilmiş bir [erişim anahtarı ve](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) aboneliğiniz için geçerli olan bir uç nokta kullanarak bir uç noktaya gönderilir.
+ `opinionMining=true`Sentient analiz isteklerinde, dikkatli araştırma sonuçları elde edin.
+ Her belge KIMLIĞI için bir yaklaşım puanından oluşan yanıt çıkışı, JSON kabul eden herhangi bir uygulamaya akışla eklenebilir. Örneğin, Excel ve Power BI.

## <a name="see-also"></a>Ayrıca bkz.

* [Metin Analizine genel bakış](../overview.md)
* [Metin Analizi istemci kitaplığını kullanma](../quickstarts/client-libraries-rest-api.md)
* [Yenilikler](../whats-new.md)
