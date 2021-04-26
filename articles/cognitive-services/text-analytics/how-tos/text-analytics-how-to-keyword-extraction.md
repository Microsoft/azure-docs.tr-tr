---
title: Metin Analizi REST API kullanarak anahtar tümceciği ayıklama
titleSuffix: Azure Cognitive Services
description: Azure bilişsel hizmetler 'deki Metin Analizi REST API kullanarak anahtar tümceleri ayıklama.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/29/2021
ms.author: aahi
ms.openlocfilehash: fdf24fdc56d39d93fed0009e2fadbafd7f97db6c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280505"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Örnek: Metin Analizi kullanarak anahtar tümceleri ayıklama

[Anahtar İfade Ayıklama API’si](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases), yapılandırılmamış metni değerlendirir ve her bir JSON belgesi için bir anahtar ifade listesi döndürür.

Bir belge koleksiyonundaki ana noktaları hızlı şekilde belirlemeniz gerekiyorsa bu özellik kullanışlıdır. Örneğin, "The food was delicious and there were wonderful staff" (Yemek lezizdi ve müthiş personel hizmeti vardı) giriş metni olduğunda hizmet, "food" (yemek) ve "wonderful staff" (müthiş personel) ana konuşma noktalarını döndürür.

Daha fazla bilgi için bkz. [Desteklenen diller](../language-support.md).

> [!TIP]
> * Metin Analizi Ayrıca, anahtar tümceciği ayıklama için Linux tabanlı bir Docker kapsayıcı görüntüsü sağlar, böylece Metin Analizi kapsayıcısını verilerinize yakın şekilde [yükleyip çalıştırabilirsiniz](text-analytics-how-to-install-containers.md) .
> * Bu özelliği uç nokta kullanarak [zaman uyumsuz](text-analytics-how-to-call-api.md) olarak da kullanabilirsiniz `/analyze` .

## <a name="preparation"></a>Hazırlık

Anahtar tümceciği ayıklama, üzerinde çalışmak için büyük miktarda metin verdiğinizde en iyi şekilde çalışır. Bu, daha küçük miktarlarda metin üzerinde daha iyi sonuç veren yaklaşım analizinden daha tersidir. Her iki işlemden de en iyi sonuçları almak için, girişleri buna göre yeniden yapılandırmayı göz önünde bulundurun.

Bu biçimde JSON belgelerinize sahip olmanız gerekir: KIMLIK, metin, dil

Belge boyutu belge başına 5.120 veya daha az karakter olmalıdır ve koleksiyon başına en fazla 1.000 öğe (kimlik) olabilir. Koleksiyon, istek gövdesinde gönderilir. Aşağıdaki örnek, anahtar ifade ayıklaması için gönderebileceğiniz içeriğin bir gösterimidir. 

İstek ve yanıt nesneleri hakkında daha fazla bilgi için bkz. [Metin Analizi API'si çağırma](text-analytics-how-to-call-api.md) .  

### <a name="example-synchronous-request-object"></a>Örnek zaman uyumlu istek nesnesi


```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

### <a name="example-asynchronous-request-object"></a>Örnek zaman uyumsuz istek nesnesi

' Den itibaren `v3.1-preview.3` , uç noktayı kullanarak ner isteklerini zaman uyumsuz olarak gönderebilirsiniz `/analyze` .


```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
    }
}
```

## <a name="step-1-structure-the-request"></a>1. Adım: İsteği yapılandırma

İstek tanımı hakkında bilgi için bkz. [nasıl çağrılacağını Metin Analizi API'si](text-analytics-how-to-call-api.md). Kolaylık olması için aşağıdaki noktalar yeniden belirtilmektedir:

+ Bir **POST** isteği oluşturun. Bu istek için API belgelerini gözden geçirin: [anahtar tümceleri API 'si](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases).

+ Azure 'da bir Metin Analizi kaynağı veya bir örneklenmiş [metin analizi kapsayıcısı](text-analytics-how-to-install-containers.md)kullanarak anahtar tümceciği ayıklama için HTTP uç noktasını ayarlayın. API 'yi eşzamanlı olarak kullanıyorsanız, URL 'ye dahil etmeniz gerekir `/text/analytics/v3.0/keyPhrases` . Örneğin: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`.

+ Metin Analizi işlemler için [erişim anahtarı](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) eklemek üzere bir istek üst bilgisi ayarlayın.

+ İstek gövdesinde, bu analiz için hazırladığınız JSON belgeleri koleksiyonunu sağlayın.

> [!Tip]
> İsteği yapılandırmak ve hizmete GÖNDERMEK için [Postman](text-analytics-how-to-call-api.md) kullanın veya [belgelerdeki](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases)**API testi konsolu**’nu açın.

## <a name="step-2-post-the-request"></a>2. Adım: İsteği gönderme

İstek alındığında analiz gerçekleştirilir. Dakikada veya saniyede gönderebilmeniz için istek boyutu ve sayısı hakkında daha fazla bilgi için, [veri sınırları](../concepts/data-limits.md) makalesine bakın.

Hizmetin durum bilgisi olmadığını unutmayın. Hesabınızda bir veri depolanmaz. Sonuçlar hemen yanıtta döndürülür.

## <a name="step-3-view-results"></a>3. Adım: Sonuçları görüntüleme

Tüm POST istekleri, kimlikler ve algılanan özelliklerle JSON tarafından biçimlendirilmiş bir yanıt döndürür. Döndürülen anahtar tümceciklerin sırası, model tarafından dahili olarak belirlenir.

Hemen çıktı döndürülür. Sonuçları, JSON kabul eden bir uygulamada akışa alabilir veya çıktıyı yerel sistemde bir dosyaya kaydedebilir, sonra da verileri sıralamanıza, aramanıza ve işlemenize olanak sağlayan bir uygulamaya içeri aktarabilirsiniz.

V 3.1-Preview. 2 uç noktasındaki anahtar tümceciği ayıklama çıkışının bir örneği burada gösterilmektedir:

### <a name="synchronous-result"></a>Zaman uyumlu sonuç

```json
    {
       "documents":[
          {
             "id":"1",
             "keyPhrases":[
                "year",
                "trail",
                "trip",
                "views",
                "hike"
             ],
             "warnings":[]
          },
          {
             "id":"2",
             "keyPhrases":[
                "marked trails",
                "Worst hike",
                "goners"
             ],
             "warnings":[]
          },
          {
             "id":"3",
             "keyPhrases":[
                "trail",
                "small children",
                "family"
             ],
             "warnings":[]
          },
          {
             "id":"4",
             "keyPhrases":[
                "spectacular views",
                "trail",
                "Worth",
                "area"
             ],
             "warnings":[]
          },
          {
             "id":"5",
             "keyPhrases":[
                "places",
                "beautiful views",
                "favorite trail",
                "rest"
             ],
             "warnings":[]
          }
       ],
       "errors":[],
       "modelVersion":"2020-07-01"
    }

```
Belirtildiği gibi, çözümleyici, önemli olmayan sözcükleri bulur ve atar ve bir cümlenin konusu veya nesnesi olarak görünen tek terimleri veya tümceleri tutar.

### <a name="asynchronous-result"></a>Zaman uyumsuz sonuç

`/analyze`Uç noktasını zaman uyumsuz işlem için kullanıyorsanız, API 'ye gönderdiğiniz görevleri içeren bir yanıt alırsınız.

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


## <a name="summary"></a>Özet

Bu makalede bilişsel hizmetler 'de Metin Analizi kullanarak anahtar tümceciği ayıklama için kavramları ve iş akışını öğrendiniz. Özet:

+ [Anahtar ifade ayıklama API’si](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases), seçili dillerde kullanılabilir.
+ İstek gövdesindeki JSON belgeleri bir KIMLIK, metin ve dil kodu içerir.
+ POST isteği bir `/keyphrases` veya `/analyze` bitiş noktasına, kişiselleştirilmiş bir [erişim anahtarı ve aboneliğiniz için geçerli bir uç nokta](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) kullanılarak yapılır.
+ Her belge KIMLIĞI için anahtar sözcüklerden ve tümceciklerden oluşan yanıt çıkışı, Microsoft Office Excel ve Power BI dahil olmak üzere, birkaç kez ad vermek üzere JSON kabul eden herhangi bir uygulamaya akışla eklenebilir.

## <a name="see-also"></a>Ayrıca bkz.

 [Metin analizi genel bakış](../overview.md) hakkında [sık sorulan sorular (SSS)](../text-analytics-resource-faq.md)</br>
 [Metin Analizi ürün sayfası](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Sonraki adımlar

* [Metin Analizine genel bakış](../overview.md)
* [Metin Analizi istemci kitaplığını kullanma](../quickstarts/client-libraries-rest-api.md)
* [Yenilikler](../whats-new.md)
