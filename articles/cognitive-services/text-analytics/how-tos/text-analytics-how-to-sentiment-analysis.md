---
title: Metin Analizi REST API ile yaklaşım analizini gerçekleştirme
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure bilişsel hizmetler Metin Analizi REST API metinle ilgili yaklaşım hakkında nasıl algılanacağı gösterilir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: f7a62d1d2b695726421ba990d0f118eb3ae199da
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031455"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Örnek: Metin Analizi ile yaklaşımı Algıla

[Azure yaklaşım Analizi API 'si](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) metin girişi değerlendirir ve her belge için bir yaklaşım puanı döndürür. 0 (negatif) ile 1 (pozitif) arasında puan alır.

Bu yetenek; sosyal medya, müşteri incelemeleri ve tartışma forumlarında pozitif ve negatif yaklaşımları algılamak için kullanışlıdır. İçerik sizin tarafınızdan sağlanır. Modeller ve eğitim verileri hizmet tarafından sağlanır.

Şu anda Yaklaşım Analizi API, Ingilizce, Almanca, Ispanyolca ve Fransızca 'yı destekler. Diğer diller önizleme aşamasındadır. Daha fazla bilgi için bkz. [Desteklenen diller](../text-analytics-supported-languages.md).

> [!TIP]
> Azure Metin Analizi API'si, yaklaşım analizi için Linux tabanlı bir Docker kapsayıcı görüntüsü sağlar, böylece Metin Analizi kapsayıcısını verilerinize yakın şekilde [yükleyebilir ve çalıştırabilirsiniz](text-analytics-how-to-install-containers.md) .

## <a name="concepts"></a>Kavramlar

Metin Analizi, 0 ile 1 arasında bir yaklaşım puanı oluşturmak için makine öğrenmesi sınıflandırma algoritması kullanır. 1’e yakın puanlar pozitif yaklaşımı, 0’a yakın puanlar ise negatif yaklaşımı gösterir. Model, yaklaşım ilişkilendirmeleri ile kapsamlı bir metin gövdesi kullanılarak önceden eğitilir. Şu anda kendi eğitim verilerinizi sağlamak mümkün değildir. Model, metin analizi sırasında tekniklerin birleşimini kullanır. Teknik işleme, konuşma bölümü analizi, sözcük yerleşimi ve Word ilişkilendirmelerini içeren teknikler. Algoritma hakkında daha fazla bilgi için bkz. [Metin Analizi Tanıtımı](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Metindeki belirli bir varlık için yaklaşımı ayıklamanın tersine yaklaşım analizi, belgenin tamamında gerçekleştirilir. Uygulamada, büyük bir metin bloğu yerine bir ya da iki cümle içerdiğinde, gelişmenin doğruluğu için kullanım eğilimi vardır. Nesnellik değerlendirmesi aşamasında model, belgenin tamamının nesnel mi olduğunu yoksa yaklaşım mı içerdiğini belirler. Genellikle hedefi olan bir belge, daha fazla işlem olmadan 0,50 puanı ile sonuçlanan yaklaşım algılama aşamasına ileretmez. Ardışık düzende devam eden belgeler için, sonraki aşama 0,50 veya üzeri bir puan üretir. Puan, belgede algılanan yaklaşım derecesine bağlıdır.

## <a name="sentiment-analysis-v3-public-preview"></a>Yaklaşım Analizi v3 genel önizleme

[Yaklaşım Analizi sonraki sürümü](https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) artık genel önizlemeye sunuldu. API 'nin metin kategorisi ve Puanlama açısından doğruluk ve ayrıntı açısından önemli geliştirmeler sağlar.

> [!NOTE]
> * Yaklaşım Analizi v3 istek biçimi ve [veri sınırları](../overview.md#data-limits) , önceki sürümle aynıdır.
> * Şu anda v3 Yaklaşım Analizi:
>    * Şu anda Ingilizce (`en`), Japonca (`ja`), Çince Basitleştirilmiş (`zh-Hans`), geleneksel Çince (`zh-Hant`), Fransızca (`fr`), Italyanca (`it`), Ispanyolca (`es`), Felemenkçe (`nl`), Portekizce (`pt`) ve Almanca (`de`) dilleri.
>    * Şu bölgelerde kullanılabilir: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`ve `West US 2`.

|Özellik |Açıklama  |
|---------|---------|
|İyileştirilmiş doğruluk     | Metin belgelerinde olumlu, nötr, olumsuz ve karışık yaklaşımların algılanması konusunda önceki sürümlere göre ciddi bir iyileştirme sağlandı.           |
|Belge ve tümce düzeyinde yaklaşım puanı     | Hem belgenin hem belgedeki tek tek cümlelerin yaklaşımını algılayabilirsiniz. Belgede birden fazla cümle varsa her cümleye de ayrı bir yaklaşım puanı verilir.         |
|Yaklaşım etiketleme ve Puanlama     | API artık, bir yaklaşım puanına ek olarak metin için yaklaşım kategorileri döndürüyor. Kategoriler `positive`, `negative`, `neutral`ve `mixed`.       |
| Geliştirilmiş çıkış | Yaklaşım Analizi artık metin belgesinin tamamı ve tek tek cümleleri için bilgi döndürüyor. |
| Model-sürümü parametresi | Verilerinizde Metin Analizi modelinin hangi sürümünün kullanıldığını seçmek için isteğe bağlı bir parametre. |

### <a name="sentiment-labeling"></a>Yaklaşım etiketleme

Yaklaşım Analizi v3, bir tümce ve belge düzeyinde puanlar ve Etiketler döndürebilir. Puanlar ve Etiketler `positive`, `negative`ve `neutral`. Belge düzeyinde `mixed` yaklaşım etiketi (puan değil) de döndürülebilir. Belgenin yaklaşımı, Tümcelerin puanlarını toplayarak belirlenir.

| Tümce yaklaşımı                                                        | Döndürülen belge etiketi |
|---------------------------------------------------------------------------|----------------|
| En az bir pozitif tümce ve Tümcelerin geri kalanı tarafsız. | `positive`     |
| En az bir negatif tümce ve Tümcelerin geri kalanı tarafsız.  | `negative`     |
| En az bir negatif tümce ve en az bir pozitif tümce.         | `mixed`        |
| Tüm tümceler tarafsız.                                                 | `neutral`      |

### <a name="model-versioning"></a>Model sürümü oluşturma

> [!NOTE]
> Yaklaşım analizi için model sürümü oluşturma, sürüm `v3.0-preview.1`başlayarak kullanılabilir.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="sentiment-analysis-v3-example-request"></a>Yaklaşım Analizi v3 örnek isteği

Aşağıdaki JSON Yaklaşım Analizi yeni sürümüne yapılan bir isteğin örneğidir. İstek biçimlendirmesi, önceki sürümle aynıdır:

```json
    {
        "documents": [
        {
            "language": "en",
            "id": "1",
            "text": "Hello world. This is some input text that I love."
        },
        {
            "language": "en",
            "id": "2",
            "text": "It's incredibly sunny outside! I'm so happy."
        }
        ],
    }
```

### <a name="sentiment-analysis-v3-example-response"></a>Yaklaşım Analizi v3 örnek yanıtı

İstek biçimi önceki sürümle aynı olsa da, yanıt biçimi değişmiştir. Aşağıdaki JSON, API 'nin yeni sürümünden bir örnek yanıt örneğidir:

```json
    {
        "documents": [
            {
                "id": "1",
                "sentiment": "positive",
                "documentScores": {
                    "positive": 0.98570585250854492,
                    "neutral": 0.0001625834556762,
                    "negative": 0.0141316400840878
                },
                "sentences": [
                    {
                        "sentiment": "neutral",
                        "sentenceScores": {
                            "positive": 0.0785155147314072,
                            "neutral": 0.89702343940734863,
                            "negative": 0.0244610067456961
                        },
                        "offset": 0,
                        "length": 12
                    },
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.98570585250854492,
                            "neutral": 0.0001625834556762,
                            "negative": 0.0141316400840878
                        },
                        "offset": 13,
                        "length": 36
                    }
                ]
            },
            {
                "id": "2",
                "sentiment": "positive",
                "documentScores": {
                    "positive": 0.89198976755142212,
                    "neutral": 0.103382371366024,
                    "negative": 0.0046278294175863
                },
                "sentences": [
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.78401315212249756,
                            "neutral": 0.2067587077617645,
                            "negative": 0.0092281140387058
                        },
                        "offset": 0,
                        "length": 30
                    },
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.99996638298034668,
                            "neutral": 0.0000060341349126,
                            "negative": 0.0000275444017461
                        },
                        "offset": 31,
                        "length": 13
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="example-c-code"></a>Örnek C# kod

[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs)üzerinde yaklaşım analizi bu sürümünü C# çağıran örnek bir uygulama bulabilirsiniz.

## <a name="preparation"></a>Hazırlık

Yaklaşım analizi, daha küçük metin öbeklerini üzerinde çalışmaya verdiğiniz zaman daha yüksek kaliteli bir sonuç üretir. Bu, büyük metin öbekleri üzerinde daha iyi performans gösteren anahtar ifade ayıklamasının tersidir. Her iki işlemden de en iyi sonuçları elde etmek için girişleri uygun şekilde yeniden yapılandırın.

Bu biçimde JSON belgelerinize sahip olmanız gerekir: KIMLIK, metin ve dil.

Belge boyutunun belge başına 5.120 karakter altında olması gerekir. Koleksiyon başına en fazla 1.000 öğe (kimlik) kullanabilirsiniz. Koleksiyon, istek gövdesinde gönderilir. Aşağıdaki örnek, yaklaşım analizi için gönderebilecek içeriklere bir örnektir:

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

## <a name="step-1-structure-the-request"></a>1\. Adım: İsteği yapılandırma

İstek tanımı hakkında daha fazla bilgi için bkz. [Metin Analizi API'si çağırma](text-analytics-how-to-call-api.md). Kolaylık olması için aşağıdaki noktalar yeniden belirtilmektedir:

+ POST isteği oluşturun. Bu isteğin API belgelerini gözden geçirmek için [YAKLAŞıM ANALIZI API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)'sine bakın.

+ Azure 'da bir Metin Analizi kaynağı veya bir örneklenmiş [metin analizi kapsayıcısı](text-analytics-how-to-install-containers.md)kullanarak yaklaşım ANALIZI için HTTP uç noktasını ayarlayın. URL 'ye `/text/analytics/v2.1/sentiment` dahil etmeniz gerekir. Örneğin: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`.

+ Metin Analizi işlemler için [erişim anahtarı](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) eklemek üzere bir istek üst bilgisi ayarlayın.

+ İstek gövdesinde, bu analiz için hazırladığınız JSON belgeleri koleksiyonunu sağlayın.

> [!Tip]
> İsteği yapılandırmak ve hizmete göndermek için [Postman](text-analytics-how-to-call-api.md) 'ı kullanın veya [belgelerde](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) **API test konsolunu** açın.

## <a name="step-2-post-the-request"></a>2\. Adım: İsteği gönderme

İstek alındığında analiz gerçekleştirilir. Dakika ve saniye başına gönderebilmeniz için istek boyutu ve sayısı hakkında bilgi için genel bakış konusunun [veri sınırları](../overview.md#data-limits) bölümüne bakın.

Hizmetin durum bilgisi olmadığını unutmayın. Hesabınızda bir veri depolanmaz. Sonuçlar hemen yanıtta döndürülür.


## <a name="step-3-view-the-results"></a>3\. Adım: sonuçları görüntüleme

Yaklaşım Çözümleyicisi, metni ağırlıklı pozitif veya negatif olarak sınıflandırır. 0 ile 1 arasında bir puan atar. 0,5’e yakın değerler nötr veya belirsizdir. 0,5 puanı, nötr olma durumunu belirtir. Bir dize yaklaşım için çözümlenememesi veya yaklaşım yoksa, puan her zaman 0,5 ' dir. Örneğin, İngilizce dil koduyla İspanyolca bir dize geçirirseniz puan 0,5 olur.

Hemen çıktı döndürülür. Sonuçları JSON kabul eden bir uygulamaya veya çıktıyı yerel sistemdeki bir dosyaya kaydedebilirsiniz. Sonra çıktıyı, verileri sıralamak, aramak ve işlemek için kullanabileceğiniz bir uygulamaya içeri aktarın.

Aşağıdaki örnekte, bu makaledeki belge koleksiyonu yanıtı gösterilmektedir:

```json
    {
        "documents": [
            {
                "score": 0.9999237060546875,
                "id": "1"
            },
            {
                "score": 0.0000540316104888916,
                "id": "2"
            },
            {
                "score": 0.99990355968475342,
                "id": "3"
            },
            {
                "score": 0.980544924736023,
                "id": "4"
            },
            {
                "score": 0.99996328353881836,
                "id": "5"
            }
        ],
        "errors": []
    }
```

## <a name="summary"></a>Özet

Bu makalede, Azure bilişsel hizmetler 'de Metin Analizi kullanarak yaklaşım analizine yönelik kavramların ve iş akışının öğrenmiş olursunuz. Özet:

+ [Yaklaşım Analizi API 'si](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) seçili diller için kullanılabilir.
+ İstek gövdesindeki JSON belgeleri bir KIMLIK, metin ve dil kodu içerir.
+ POST isteği, kişiselleştirilmiş bir [erişim anahtarı ve](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) aboneliğiniz için geçerli olan bir uç nokta kullanılarak `/sentiment` uç noktasıdır.
+ Her belge KIMLIĞI için bir yaklaşım puanından oluşan yanıt çıkışı, JSON kabul eden herhangi bir uygulamaya akışla eklenebilir. Örnek uygulamalar, birkaç kez ad vermek için Excel ve Power BI içerir.

## <a name="see-also"></a>Ayrıca bkz.

 [Metin analizi genel bakış](../overview.md) hakkında [sık sorulan sorular (SSS)](../text-analytics-resource-faq.md)</br>
 [Metin Analizi ürün sayfası](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Anahtar ifadeleri ayıklama](text-analytics-how-to-keyword-extraction.md)
