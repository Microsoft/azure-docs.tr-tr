---
title: Görüntü analizi API 'sini çağırma
titleSuffix: Azure Cognitive Services
description: Görüntü analizi API 'sini çağırmayı ve davranışını yapılandırmayı öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3f9a6afe3202df40e26332c3a8c91b8c3eca8a32
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012277"
---
# <a name="call-the-image-analysis-api"></a>Görüntü analizi API 'sini çağırma

Bu makalede görüntünün görsel özellikleri hakkında bilgi döndürmek için görüntü analizi API 'sinin nasıl çağrılacağını gösterir.

Bu kılavuzda, bir <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> görüntü işleme kaynağı oluşturma </a> ve bir abonelik anahtarı ve uç nokta URL 'si edindiğinizi bir görüntü işleme kaynağı oluşturmuş olduğunuz varsayılmaktadır. Yapmadıysanız, başlamak için bir [hızlı başlangıç](../quickstarts-sdk/image-analysis-client-library.md) izleyin.
  
## <a name="submit-data-to-the-service"></a>Verileri hizmete gönder

Çözümle API 'sine yerel bir görüntü veya uzak görüntü gönderebilirsiniz. Yerel için, ikili görüntü verilerini HTTP istek gövdesine yerleştirebilirsiniz. Uzak için, istek gövdesini aşağıdaki gibi biçimlendirerek görüntünün URL 'sini belirtirsiniz: `{"url":"http://example.com/images/test.jpg"}` .

## <a name="determine-how-to-process-the-data"></a>Verilerin nasıl işleyeceğini belirleme

###  <a name="select-visual-features"></a>Görsel özellikleri seçin

[Çözümle API 'si](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) , tüm hizmetin görüntü analizi özelliklerine erişmenizi sağlar. URL sorgu parametrelerini ayarlayarak hangi özellikleri kullanmak istediğinizi belirtmeniz gerekir. Bir parametre, virgüllerle ayırarak birden fazla değere sahip olabilir. Belirttiğiniz her bir özellik için ek hesaplama süresi gerekir, bu nedenle yalnızca ihtiyacınız olanları belirtin.

|URL parametresi | Değer | Açıklama|
|---|---|--|
|`visualFeatures`|`Adult` | resmin pornografik (çıplaklık veya bir sex Yasası gösterir) ya da Gori (Extreme şiddet veya kan) olup olmadığını algılar. Cinsel, kışkırtıcı içerik (diğer adıyla) de algılanır.|
||`Brands` | , yaklaşık konum da dahil olmak üzere bir görüntü içindeki çeşitli markalarını algılar. Markalar bağımsız değişkeni yalnızca Ingilizce olarak kullanılabilir.|
||`Categories` | , belgelerde tanımlanan bir sınıflandırmaya göre görüntü içeriğini kategorilere ayırır. Bu varsayılan değerdir `visualFeatures` .|
||`Color` | vurgu rengini, baskın rengi ve görüntünün siyah&beyaz olduğunu belirler.|
||`Description` | desteklenen dillerde tüm cümlelere sahip görüntü içeriğini açıklar.|
||`Faces` | yüzlerin mevcut olup olmadığını algılar. Varsa, koordinatlar, cinsiyet ve yaş üretin.|
||`ImageType` | resmin küçük resim veya çizgi çizimi olduğunu algılar.|
||`Objects` | , yaklaşık konum da dahil olmak üzere bir görüntüdeki çeşitli nesneleri algılar. Nesneler bağımsız değişkeni yalnızca Ingilizce olarak kullanılabilir.|
||`Tags` | görüntüyü resim içeriğiyle ilgili ayrıntılı bir sözcük listesiyle Etiketler.|
|`details`| `Celebrities` | görüntüde algılanırsa ünlüleri tanımlar.|
||`Landmarks` |görüntüde algılanırsa yer işaretlerini tanımlar.|

Doldurulmuş bir URL aşağıdaki gibi görünebilir:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>Dilleri belirtin

Döndürülen verilerin dilini de belirtebilirsiniz. Aşağıdaki URL sorgu parametresi dili belirtir. `en` varsayılan değerdir.

|URL parametresi | Değer | Açıklama|
|---|---|--|
|`language`|`en` | İngilizce|
||`es` | İspanyolca|
||`ja` | Japonca|
||`pt` | Portekizce|
||`zh` | Basitleştirilmiş Çince|

Doldurulmuş bir URL aşağıdaki gibi görünebilir:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **Kapsamlı API çağrıları**
>
> Görüntü analizine ait özelliklerden bazıları doğrudan veya çözümleme API çağrısı aracılığıyla çağrılabilir. Örneğin, bir istek yaparak yalnızca görüntü etiketlerinin kapsamlı bir analizini yapabilirsiniz `https://{endpoint}/vision/v3.2-preview.3/tag` . Ayrı olarak çağrılabilen diğer özellikler için [başvuru belgelerine](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) bakın.

## <a name="get-results-from-the-service"></a>Hizmetten sonuçları al

Hizmet bir `200` http yanıtı döndürür ve gövde döndürülen VERILERI JSON dizesi biçiminde içerir. JSON yanıtının bir örneği aşağıda verilmiştir.

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

Bu örnekteki alanların açıklamaları için aşağıdaki tabloya bakın:

Alan | Tür | Content
------|------|------|
Etiketler  | `object` | Bir etiket dizisi için en üst düzey nesne.
tags[].Name | `string`    | Etiketler sınıflandırıcıdan anahtar sözcüğü.
tags[].Score    | `number`    | Güvenirlik puanı, 0 ile 1 arasındadır.
açıklama     | `object`    | Bir görüntü açıklaması için en üst düzey nesne.
description.tags[] |    `string`    | Etiketlerin listesi. Bir resim yazısı üretebilme özelliği için yeterli güven yoksa, Etiketler çağıranın kullanabildiği tek bilgiler olabilir.
description.captions[].text    | `string`    | Görüntüyü açıklayan bir ifadedir.
description.captions[].confidence    | `number`    | İfadenin Güvenirlik puanı.

### <a name="error-codes"></a>Hata kodları

Aşağıdaki olası hatalar ve nedenleri listesine bakın:

* 400
    * InvalidImageUrl-Image URL 'SI hatalı biçimlendirildi veya erişilebilir durumda değil.
    * InvalidImageFormat-Input verileri geçerli bir görüntü değil.
    * InvalidImageSize-Input Image çok büyük.
    * NotSupportedVisualFeature-belirtilen özellik türü geçerli değil.
    * NotSupportedImage-desteklenmeyen görüntü, örneğin alt pornografi.
    * Invaliddetails-desteklenmeyen `detail` parametre değeri.
    * NotSupportedLanguage-istenen işlem belirtilen dilde desteklenmiyor.
    * BadArgument-hata iletisinde ek ayrıntılar sağlanmaktadır.
* 415-desteklenmeyen medya türü hatası. Içerik türü izin verilen türlerde değil:
    * Bir görüntü URL 'SI için: Content-Type uygulama/JSON olmalıdır
    * İkili görüntü verileri için: Content-Type uygulama/sekizli-Stream veya multipart/form-Data olmalıdır
* 500
    * FailedToProcess
    * Zaman aşımı-görüntü işleme zaman aşımına uğradı.
    * InternalServerError

## <a name="next-steps"></a>Sonraki adımlar

REST API denemek için [görüntü ANALIZI API başvurusuna](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b)gidin.
