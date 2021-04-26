---
title: Metin Analizi API’sini çağırma
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure bilişsel hizmetler REST API ve Postman Metin Analizi nasıl çağrlayabileceğiniz açıklanır.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 5790c7c62b9d97df9683773170301b6e09a47667
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728491"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Metin Analizi nasıl çağrılacağını REST API

Bu makalede, temel kavramları göstermek için Metin Analizi REST API ve [Postman](https://www.postman.com/downloads/) kullanırız. API, hizmetin özelliklerini kullanmak için birkaç zaman uyumlu ve zaman uyumsuz uç nokta sağlar. 

## <a name="create-a-text-analytics-resource"></a>Metin Analizi kaynağı oluşturma

> [!NOTE]
> * Veya uç noktalarını kullanmak istiyorsanız, standart (S) [fiyatlandırma katmanını](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) kullanarak bir metin analizi kaynağına ihtiyacınız olacaktır `/analyze` `/health` . `/analyze`Uç nokta [fiyatlandırma katmanınıza](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)dahildir.

Metin Analizi API'si kullanmadan önce, uygulamalarınız için anahtar ve uç nokta içeren bir Azure kaynağı oluşturmanız gerekecektir. 

1.  İlk olarak, [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) gidin ve henüz yoksa yeni bir metin analizi kaynağı oluşturun. [Fiyatlandırma katmanı](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)seçin.

2.  Uç noktanız için kullanmak istediğiniz bölgeyi seçin.  Lütfen `/analyze` ve `/health` uç noktaların yalnızca şu bölgelerde kullanılabilir olduğunu unutmayın: Batı ABD 2, Doğu ABD 2, Orta ABD, Kuzey Avrupa ve Batı Avrupa.

3.  Metin Analizi kaynağını oluşturun ve sayfanın solundaki "anahtarlar ve uç nokta dikey penceresine" gidin. Daha sonra API 'Leri çağırdığınızda kullanılacak anahtarı kopyalayın. Bunu daha sonra üst bilgi için bir değer olarak eklersiniz `Ocp-Apim-Subscription-Key` .

4. Metin Analizi kaynağınız kullanılarak gönderilen metin kayıtlarının sayısını denetlemek için:

    1. Azure portal Metin Analizi kaynağına gidin. 
    2. Sol gezinti menüsünde **izleme** altında bulunan **ölçümler**' e tıklayın. 
    3. **Ölçüm** için açılan kutuda *işlenen metin kayıtları* ' nı seçin.
    
Bir metin kaydı 1000 karakterdir.

## <a name="change-your-pricing-tier"></a>Fiyatlandırma katmanınızı değiştirin 

S0 ile S4 fiyatlandırma katmanını kullanarak mevcut bir Metin Analizi kaynağınız varsa, standart [fiyatlandırma katmanını](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)kullanmak için onu güncelleştirmeniz gerekir. S0 aracılığıyla S4 fiyatlandırma katmanları kullanımdan kaldırılacak. Kaynağınızın fiyatlandırmasını güncelleştirmek için:

1. [Azure portal](https://portal.azure.com/)metin analizi kaynağına gidin.
2. Sol gezinti menüsünde **fiyatlandırma katmanı** ' nı seçin. **Kaynak yönetiminin** altında olacaktır. 
3. Standart fiyatlandırma katmanını seçin. Ardından **Seç**'e tıklayın.

Ayrıca standart (ler) fiyatlandırma katmanıyla yeni bir Metin Analizi kaynağı oluşturabilir ve uygulamalarınızı yeni kaynak için kimlik bilgilerini kullanacak şekilde geçirebilirsiniz. 

## <a name="using-the-api-synchronously"></a>API 'YI eşzamanlı olarak kullanma

Metin Analizi zaman uyumlu olarak çağırabilirsiniz (düşük gecikmeli senaryolar için). Zaman uyumlu API kullanırken her API 'YI (özellik) ayrı ayrı çağırmanız gerekir. Birden çok özelliği çağırmanız gerekiyorsa, Metin Analizi zaman uyumsuz olarak nasıl çağrılacağını öğrenmek için aşağıdaki bölüme göz atın. 

## <a name="using-the-api-asynchronously"></a>API 'YI zaman uyumsuz kullanma

V 3.1-Preview. 3 ' den başlayarak Metin Analizi API'si iki zaman uyumsuz uç nokta sağlar: 

* `/analyze`Metin analizi uç noktası, tek BIR API çağrısında birden çok metin analizi özellikleriyle aynı metin belgelerinin aynı kümesini analiz etmenizi sağlar. Daha önce, birden çok özelliği kullanmak için her işlem için ayrı API çağrıları yapmanız gerekir. Birden fazla Metin Analizi özelliği olan büyük belge kümelerini analiz etmeniz gerektiğinde bu yeteneği göz önünde bulundurun.

* `/health`Uygunluk belgelerindeki ilgili tıbbi bilgileri ayıklayabilen ve etiketleyen sistem durumu için metin analizi uç noktası.  

/Analyze ve/Health uç noktalarının yalnızca şu bölgelerde kullanılabilir olduğunu lütfen unutmayın: Batı ABD 2, Doğu ABD 2, Orta ABD, Kuzey Avrupa ve Batı Avrupa.

Hangi özelliklerin zaman uyumsuz olarak kullanılabileceğini görmek için aşağıdaki tabloya bakın. Uç noktadan yalnızca birkaç özelliğin çağrılabilecek olduğunu unutmayın `/analyze` . 

| Özellik | Zaman Uyumlu | Zaman uyumsuz |
|--|--|--|
| Dil algılama | ✔ |  |
| Yaklaşım analizi | ✔ |  |
| Görüşün madenciliği | ✔ |  |
| Anahtar ifade ayıklama | ✔ | ✔* |
| Adlandırılmış varlık tanıma (PII ve FI dahil) | ✔ | ✔* |
| Varlık bağlama | ✔ | ✔* |
| Sistem durumu için Metin Analizi (kapsayıcı) | ✔ |  |
| Sistem durumu için Metin Analizi (API) |  | ✔  |

`*` -Uç nokta aracılığıyla zaman uyumsuz olarak çağırılır `/analyze` .


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>API istek biçimleri

Metin Analizi API'si hem zaman uyumlu hem de zaman uyumsuz çağrılar gönderebilirsiniz.

#### <a name="synchronous"></a>[Zaman Uyumlu](#tab/synchronous)

### <a name="synchronous-requests"></a>Zaman uyumlu istekler

API isteklerinin biçimi tüm zaman uyumlu işlemler için aynıdır. Belgeler, bir JSON nesnesinde ham yapılandırılmamış metin olarak gönderilir. XML desteklenmiyor. JSON şeması aşağıda açıklanan öğelerden oluşur.

| Öğe | Geçerli değerler | Gerekli mi? | Kullanım |
|---------|--------------|-----------|-------|
|`id` |Veri türü dizedir, ancak uygulama belge kimlikleri ' nde tam sayı olarak eğilimlidir. | Gerekli | Sistem çıktıyı yapılandırmak için sağladığınız kimlikleri kullanır. İstekteki her bir KIMLIK için dil kodları, anahtar tümceleri ve yaklaşım puanları oluşturulur.|
|`text` | Yapılandırılmamış ham metin, en fazla 5.120 karakter. | Gerekli | Dil algılama için metin herhangi bir dilde ifade edilebilir. Yaklaşım analizi, anahtar ifade ayıklama ve varlık tanımlama için, metin [desteklenen bir dilde](../language-support.md)olmalıdır. |
|`language` | 2 karakterlik [ıso 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) [desteklenen bir dil](../language-support.md) için kod | Değişir | Yaklaşım analizi, anahtar ifade ayıklama ve varlık bağlama için gereklidir; dil algılama için isteğe bağlı. Bunu dışladığınızda bir hata yoktur, ancak analiz bu olmadan zayıflatılmalıdır. Dil kodu, sağladığınız öğesine karşılık gelmelidir `text` . |

Aşağıda, zaman uyumlu Metin Analizi uç noktaları için bir API isteği örneği verilmiştir. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="asynchronous"></a>[Zaman uyumsuz](#tab/asynchronous)

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>Uç noktaya yönelik zaman uyumsuz istekler `/analyze`

> [!NOTE]
> Metin Analizi istemci kitaplığının en son sürümü, istemci nesnesini kullanarak zaman uyumsuz analiz işlemlerini çağırmanızı sağlar. GitHub 'da örnekleri bulabilirsiniz:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

`/analyze`Uç nokta, tek BIR API çağrısında kullanmak istediğiniz desteklenen metin analizi özelliklerinden hangisini seçmenizi sağlar. Bu uç nokta şu anda şunları destekler:

* Anahtar İfade Ayıklama 
* Adlandırılmış varlık tanıma (PII ve FI dahil)
* Varlık Bağlama

| Öğe | Geçerli değerler | Gerekli mi? | Kullanım |
|---------|--------------|-----------|-------|
|`displayName` | Dize | İsteğe Bağlı | İşe benzersiz tanımlayıcı için görünen ad olarak kullanılır.|
|`analysisInput` | `documents`Aşağıdaki alanı içerir | Gerekli | Göndermek istediğiniz belgelerin bilgilerini içerir. |
|`documents` | `id` `text` Aşağıdaki ve alanlarını içerir | Gerekli | Gönderilen her belge için bilgileri ve belgenin ham metnini içerir. |
|`id` | Dize | Gerekli | Sağladığınız kimlikler çıktıyı yapılandırmak için kullanılır. |
|`text` | Yapılandırılmamış ham metin, en fazla 125.000 karakter. | Gerekli | Şu anda desteklenen tek dil olan Ingilizce dilde olmalıdır. |
|`tasks` | Aşağıdaki metin analizi özelliklerini içerir: `entityRecognitionTasks` , `entityLinkingTasks` , `keyPhraseExtractionTasks` veya `entityRecognitionPiiTasks` . | Gerekli | Kullanmak istediğiniz bir veya daha fazla Metin Analizi özelliği. `entityRecognitionPiiTasks` `domain` `pii` `phi` ' `pii-categories` Nin, seçilen varlık türlerinin algılanması için veya olarak ayarlanabilmesi için isteğe bağlı bir parametresinin olduğunu unutmayın. `domain`Parametresi belirtilmemişse, sistem varsayılan olarak öğesine ayarlanır `pii` . |
|`parameters` | `model-version` `stringIndexType` Aşağıdaki ve alanlarını içerir | Gerekli | Bu alan, seçtiğiniz yukarıdaki özellik görevlerine dahil edilmiştir. Bunlar, kullanmak istediğiniz model sürümü ve Dizin türü hakkında bilgiler içerir. |
|`model-version` | Dize | Gerekli | Hangi modelin kullanmak istediğinizi çağırmakta olduğunu belirtin.  |
|`stringIndexType` | Dize | Gerekli | Programlama ortamınızla eşleşen metin kod çözücüsünü belirtin.  Desteklenen türler `textElement_v8` (varsayılan), `unicodeCodePoint` , `utf16CodeUnit` . Daha fazla bilgi için lütfen [metin uzaklık makalesine](../concepts/text-offsets.md#offsets-in-api-version-31-preview) bakın.  |
|`domain` | Dize | İsteğe Bağlı | Yalnızca göreve parametre olarak uygulanır `entityRecognitionPiiTasks` ve veya olarak ayarlanabilir `pii` `phi` . Belirtilmemişse, varsayılan olarak olur `pii` .  |

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
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityLinkingTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest",
                "stringIndexType": "TextElements_v8",
                "domain": "phi",
                "pii-categories":"default"
            }
        }]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Uç noktaya yönelik zaman uyumsuz istekler `/health`

Durum barındırılan API için Metin Analizi API isteklerinin biçimi, kapsayıcısı için olan ile aynıdır. Belgeler, bir JSON nesnesinde ham yapılandırılmamış metin olarak gönderilir. XML desteklenmiyor. JSON şeması aşağıda açıklanan öğelerden oluşur.  Lütfen sistem durumu genel önizlemesi için Metin Analizi erişim istemek üzere bilişsel [Hizmetler istek formunu](https://aka.ms/csgate) doldurun ve iletin. Sistem durumu kullanımı için Metin Analizi faturalandırılmaz. 

| Öğe | Geçerli değerler | Gerekli mi? | Kullanım |
|---------|--------------|-----------|-------|
|`id` |Veri türü dizedir, ancak uygulama belge kimlikleri ' nde tam sayı olarak eğilimlidir. | Gerekli | Sistem çıktıyı yapılandırmak için sağladığınız kimlikleri kullanır. |
|`text` | Yapılandırılmamış ham metin, en fazla 5.120 karakter. | Gerekli | Şu anda yalnızca Ingilizce metnin desteklendiğini unutmayın. |
|`language` | 2 karakterlik [ıso 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) [desteklenen bir dil](../language-support.md) için kod | Gerekli | Yalnızca `en` Şu anda destekleniyor. |

Aşağıda, sistem durumu uç noktaları için Metin Analizi yönelik bir API isteği örneği verilmiştir. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Metin Analizi API'si veri göndermeye yönelik ücretler ve boyut limitleri hakkında bilgi için [veri ve oran limitleri](../concepts/data-limits.md) makalesine bakın.


## <a name="set-up-a-request"></a>İstek ayarlama 

Postman 'da (veya başka bir Web API test aracında) kullanmak istediğiniz özelliğin uç noktasını ekleyin. Uygun uç nokta biçimini bulmak için aşağıdaki tabloyu kullanın ve kaynak uç noktanızla değiştirin `<your-text-analytics-resource>` . Örnek:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Zaman Uyumlu](#tab/synchronous)

### <a name="endpoints-for-sending-synchronous-requests"></a>Zaman uyumlu istek göndermek için uç noktalar

| Özellik | İstek türü | Kaynak uç noktaları |
|--|--|--|
| Dil algılama | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Yaklaşım analizi | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Görüşün madenciliği | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Anahtar ifade ayıklama | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Adlandırılmış varlık tanıma-genel | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Adlandırılmış varlık tanıma-PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Adlandırılmış varlık tanıma-FI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Zaman uyumsuz](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Uç noktaya zaman uyumsuz isteklerin gönderilmesi için uç noktalar `/analyze`

| Özellik | İstek türü | Kaynak uç noktaları |
|--|--|--|
| Analiz işini gönder | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze` |
| Çözümleme durumunu ve sonuçlarını al | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Uç noktaya zaman uyumsuz isteklerin gönderilmesi için uç noktalar `/health`

| Özellik | İstek türü | Kaynak uç noktaları |
|--|--|--|
| Sistem durumu işi için Metin Analizi gönder  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs` |
| İş durumunu ve sonuçları al | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |
| İşi iptal et | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |

--- 

Uç noktanız, Postman 'da (veya başka bir Web API test aracında) oluşturulduktan sonra:

1. Kullanmak istediğiniz özelliğin istek türünü seçin.
2. Yukarıdaki tabloda istediğiniz uygun işlemin uç noktasını yapıştırın.
3. Üç istek üst bilgilerini ayarlayın:

   + `Ocp-Apim-Subscription-Key`: Azure portal adresinden elde edilen erişim anahtarınız
   + `Content-Type`: uygulama/JSON
   + `Accept`: uygulama/JSON

    Postman kullanıyorsanız, isteğiniz aşağıdaki ekran görüntüsüne benzer bir `/keyPhrases` uç nokta kabul etmelidir.
    
    ![Uç nokta ve üst bilgilerle ekran görüntüsü iste](../media/postman-request-keyphrase-1.png)
    
4. **Gövde** biçimi için **Ham** seçin
    
    ![Gövde ayarları ile ekran görüntüsü iste](../media/postman-request-body-raw.png)

5. Bazı JSON belgelerini geçerli bir biçimde yapıştırın. Yukarıdaki **API istek biçimi** bölümündeki örnekleri kullanın ve daha fazla bilgi ve örnek için aşağıdaki konulara bakın:

      + [Dil algılama](text-analytics-how-to-language-detection.md)
      + [Anahtar tümceciği ayıklama](text-analytics-how-to-keyword-extraction.md)
      + [Yaklaşım analizi](text-analytics-how-to-sentiment-analysis.md)
      + [Varlık tanıma](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>İsteği gönder

API isteğini gönder. Zaman uyumlu bir uç noktaya çağrı yaptıysanız, yanıt, istekte belirtilen her belge KIMLIĞI için bir öğe olan tek bir JSON belgesi olarak hemen görüntülenir.

Zaman uyumsuz `/analyze` veya `/health` uç noktalara çağrı yaptıysanız, bir 202 yanıt kodu aldığınızı kontrol edin. sonuçları görüntülemek için yanıtı almanız gerekir:

1. API yanıtında, `Operation-Location` API 'ye gönderdiğiniz işi tanımlayan üst bilgiden öğesini bulun. 
2. Kullandığınız uç nokta için bir GET isteği oluşturun. uç nokta biçimi için [yukarıdaki tabloya](#set-up-a-request) başvurun ve [API başvuru belgelerini](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus)gözden geçirin. Örnek:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>`

3. Öğesini `Operation-Location` isteğe ekleyin.

4. Yanıt, istekte belirtilen her belge KIMLIĞI için bir öğe içeren tek bir JSON belgesi olacaktır.

Lütfen hem zaman uyumsuz hem de `/analyze` `/health` işlemler için yukarıdaki adım 2 ' deki GET isteğinin sonuçları, işin oluşturulduğu zamandan itibaren 24 saat için kullanılabilir olduğunu unutmayın.  Bu süre, `expirationDateTime` Get yanıtında değeri ile belirtilir.  Bu süre dolduktan sonra sonuçlar temizlenir ve artık alma için kullanılamaz.    

## <a name="example-api-responses"></a>Örnek API yanıtları
 
# <a name="synchronous"></a>[Zaman Uyumlu](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Zaman uyumlu işlem için örnek yanıtlar

Zaman uyumlu uç nokta yanıtları kullandığınız uç noktaya göre değişir. Örnek yanıtlar için aşağıdaki makalelere bakın.

+ [Dil algılama](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Anahtar tümceciği ayıklama](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Yaklaşım analizi](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Varlık tanıma](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Zaman uyumsuz](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Zaman uyumsuz işlemler için örnek yanıtlar

Başarılı olursa, uç noktaya yönelik GET isteği `/analyze` atanan görevleri içeren bir nesne döndürür. Örneğin, `keyPhraseExtractionTasks`. Bu görevler, uygun Metin Analizi özelliğinden gelen yanıt nesnesini içerir. Daha fazla bilgi için aşağıdaki makalelere bakın.

+ [Anahtar tümceciği ayıklama](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Varlık tanıma](text-analytics-how-to-entity-linking.md#view-results)
+ [Sistem durumu için Metin Analizi](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>Ayrıca bkz.

* [Metin Analizine genel bakış](../overview.md)
* [Sık sorulan sorular (SSS)](../text-analytics-resource-faq.md)</br>
* [Metin Analizi ürün sayfası](//go.microsoft.com/fwlink/?LinkID=759712)
* [Metin Analizi istemci kitaplığını kullanma](../quickstarts/client-libraries-rest-api.md)
* [Yenilikler](../whats-new.md)
