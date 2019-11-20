---
title: Görüntü İşleme API’sini çağırma
titleSuffix: Azure Cognitive Services
description: Azure bilişsel hizmetler 'de REST API kullanarak Görüntü İşleme API'si nasıl çağrılacağını öğrenin.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 298228eedb73298f00654f4f72c201d9ed671090
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177056"
---
# <a name="call-the-computer-vision-api"></a>Görüntü İşleme API’sini çağırma

Bu makalede, REST API kullanılarak Görüntü İşleme API'si nasıl çağrılacağını gösterir. Örnekler, Görüntü İşleme API'si istemci kitaplığı ve C# http post veya Get çağrıları kullanılarak üzerinde yazılır. Makale şu şekilde odaklanır:

- Etiketler, bir açıklama ve Kategoriler alma
- Etki alanına özgü bilgileri veya "ünlüleri" alma

## <a name="prerequisites"></a>Önkoşullar

- Yerel olarak depolanmış görüntünün bir resim URL 'SI veya yolu
- Desteklenen giriş yöntemleri: bir uygulama/sekizli akış veya bir görüntü URL 'SI biçiminde Ham görüntü ikilisi
- Desteklenen görüntü dosyası biçimleri: JPEG, PNG, GIF ve BMP
- Görüntü dosyası boyutu: 4 MB veya daha az
- Resim boyutları: 50 &times; 50 piksel veya daha büyük
  
Bu makaledeki örneklerde aşağıdaki özellikler gösterilmektedir:

* Bir resim dizisini ve açıklamayı döndürmek için bir görüntüyü analiz etme
* Bir görüntüyü alana özgü bir modelle analiz etme (özellikle, "Ünlüler" modeli), JSON 'da karşılık gelen sonucu döndürecek şekilde

Özellikler aşağıdaki seçenekleri sunar:

- **Seçenek 1**: kapsamlı analiz-yalnızca belirtilen modeli çözümle
- **Seçenek 2**: gelişmiş analiz- [86-Categories taksonomi](../Category-Taxonomy.md) kullanarak ek ayrıntılar sağlamak için çözümleyin
  
## <a name="authorize-the-api-call"></a>API çağrısını yetkilendir

Görüntü İşleme API’sine yapılan her çağrı için bir abonelik anahtarı gerekir. Bu anahtar, bir sorgu dizesi parametresi aracılığıyla geçirilmelidir veya istek üstbilgisinde belirtilmelidir.

Ücretsiz deneme anahtarı almak için aşağıdakilerden birini yapın:
* Bilişsel [Hizmetler 'ı deneyin](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) sayfasına gidin. 
* Görüntü İşleme abone olmak için bilişsel [Hizmetler hesabı oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) sayfasına gidin.

Aşağıdakilerden birini yaparak abonelik anahtarını geçirebilirsiniz:

* Bu Görüntü İşleme API'si örnekte olduğu gibi bir sorgu dizesi aracılığıyla geçirin:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Bunu HTTP istek üstbilgisinde belirtin:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* İstemci kitaplığını kullandığınızda, anahtarı ComputerVisionClient Oluşturucusu aracılığıyla geçirin ve bölgeyi istemcinin bir özelliğinde belirtin:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Görüntü İşleme API'si hizmetine bir görüntü yükleme

Görüntü İşleme API'si çağrısını gerçekleştirmenin temel yolu, etiketleri, açıklamayı ve ünlüleri döndürmek için doğrudan bir görüntü karşıya yüklüyor. Bunu, HTTP gövdesinde ikili görüntüyle bir "POST" isteği göndererek, görüntüden okunan verilerle birlikte oluşturursunuz. Yükleme yöntemi tüm Görüntü İşleme API'si çağrılarında aynıdır. Tek fark, belirttiğiniz sorgu parametreleridir. 

Belirtilen bir görüntü için aşağıdaki seçeneklerden birini kullanarak Etiketler ve bir açıklama alın:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Seçenek 1: etiketlerin ve açıklamanın listesini alın

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Seçenek 2: yalnızca etiketlerin veya yalnızca bir açıklamanın listesini alın

Yalnızca Etiketler için şunu çalıştırın:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Yalnızca bir açıklama için şunu çalıştırın:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Etki alanına özgü analiz (ünlüler) Al

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Seçenek 1: kapsamlı analiz-yalnızca belirtilen modeli çözümle
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Bu seçenek için diğer tüm sorgu parametreleri {visualFeatures, details} geçerli değildir. Desteklenen tüm modelleri görmek istiyorsanız şu seçeneği kullanın:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Seçenek 2: gelişmiş analiz-86-Categories taksonomi kullanarak ek ayrıntılar sağlamak için çözümleyin

Bir veya daha fazla etki alanına özgü modelden ayrıntıların yanı sıra genel görüntü analizini almak istediğiniz uygulamalar için modeller sorgu parametresini kullanarak v1 API 'sini genişletin.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Bu yöntemi çağırdığınızda, önce [86 kategori](../Category-Taxonomy.md) sınıflandırıcısını çağırın. Kategorilerden herhangi biri bilinen veya eşleşen bir modelle eşleşiyorsa, bir sınıflandırıcı çağırmaları ikinci geçişi oluşur. Örneğin, "details = All" veya "details", "Ünlüler" i içeriyorsa, 86 kategori sınıflandırıcısını çağırdıktan sonra ünlüler modelini çağırabilirsiniz. Sonuç, kategori kişiyi içerir. Seçenek 1 ' in aksine bu yöntem, ünlülerle ilgilenen kullanıcılar için gecikme süresini artırır.

Bu durumda, tüm v1 sorgu parametreleri aynı şekilde davranır. VisualFeatures = Kategoriler belirtmezseniz, örtülü olarak etkinleştirilir.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Analiz için JSON çıkışını alma ve anlama

Bir örneği aşağıda verilmiştir:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Alan | Tür | İçerik
------|------|------|
Etiketler  | `object` | Bir etiket dizisi için en üst düzey nesne.
tags[].Name | `string`  | Etiketler sınıflandırıcıdan anahtar sözcüğü.
tags[].Score    | `number`  | Güvenirlik puanı, 0 ile 1 arasındadır.
açıklama  | `object` | Bir açıklama için en üst düzey nesne.
description.tags[] |    `string`    | Etiketlerin listesi.  Bir resim yazısı üretebilme özelliği için yeterli güven yoksa, Etiketler çağıranın kullanabildiği tek bilgiler olabilir.
description.captions[].text | `string`  | Görüntüyü açıklayan bir ifadedir.
description.captions[].confidence   | `number`  | İfadenin Güvenirlik puanı.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Etki alanına özgü modellerin JSON çıkışını alma ve anlama

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Seçenek 1: kapsamlı analiz-yalnızca belirtilen modeli çözümle

Çıktı, aşağıdaki örnekte gösterildiği gibi bir etiket dizisidir:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Seçenek 2: gelişmiş analiz-"86-Categories" taksonomi kullanarak ek ayrıntılar sağlamak için çözümleyin

Seçenek 2 (gelişmiş analiz) kullanan alana özgü modeller için, aşağıdaki örnekte gösterildiği gibi kategori dönüş türü genişletilir:

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

Kategoriler alanı, orijinal taksonominin bir veya daha fazla [86 kategorisinin](../Category-Taxonomy.md) listesidir. Alt çizgi ile biten Kategoriler, bu kategori ve alt öğeleri ile eşleşir (örneğin, "people_" veya "people_group", ünlüler modeli için).

Alan   | Tür  | İçerik
------|------|------|
kategoriler | `object`   | Üst düzey nesne.
categories[].name    | `string` | 86-kategori taksonomi listesindeki addır.
categories[].score  | `number`  | Güvenirlik puanı, 0 ile 1 arasındadır.
categories[].detail  | `object?`      | Seçim Ayrıntı nesnesi.

Birden çok kategori eşleşiyorsa (örneğin, 86 kategori Sınıflandırıcısı, model = ünlüler olduğunda "people_" ve "people_young" için bir puan döndürürse, ayrıntılar en genel düzey eşleştirmeye (Bu örnekteki "people_") eklenir.

## <a name="error-responses"></a>Hata yanıtları

Bu hatalar, hem 1 hem de seçenek 2 senaryolarında döndürülen ek NotSupportedModel hatası (HTTP 400) ile birlikte İncelenme ile aynıdır. Seçenek 2 (gelişmiş analiz) için, ayrıntılarda belirtilen modellerden herhangi biri tanınmazsa, API bir veya daha fazla geçerli olsa bile NotSupportedModel döndürür. Hangi modellerin desteklendiğini öğrenmek için Listmodellerini çağırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

REST API’yi kullanmak için [Görüntü İşleme API’si Başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44) bölümüne gidin.
