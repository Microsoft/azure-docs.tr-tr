---
title: 'Hızlı başlangıç: görüntü analizi REST API'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, görüntü analizi REST API kullanmaya başlayın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1d20e484b46dedfc5ecae0d24b4b30205cbe32cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799897"
---
Görüntü analizini REST API için kullanın:

* Etiketler, metin açıklaması, yüzeyler, yetişkinlere yönelik içerik ve daha fazlası için bir görüntüyü çözümleyin.
* Akıllı kırpmadan küçük resim oluşturma

> [!NOTE]
> Bu hızlı başlangıç, REST API çağırmak için kıvrımlı komutları kullanır. Ayrıca, bir programlama dili kullanarak REST API çağırabilirsiniz. [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)ve [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)örnekleri için GitHub örneklerine bakın.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/) 
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Görüntü İşleme kaynağı oluşturun görüntü işleme bir kaynak oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
  * Uygulamanızı Görüntü İşleme hizmetine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
  * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.
* [kıvrımlı](https://curl.haxx.se/) yüklendi

## <a name="analyze-an-image"></a>Resim çözümleme

Çeşitli görsel özellikler için bir görüntüyü çözümlemek üzere aşağıdaki adımları uygulayın:

1. Aşağıdaki komutu bir metin düzenleyicisine kopyalayın.
1. Gerektiğinde komutta aşağıdaki değişiklikleri yapın:
    1. `<subscriptionKey>` değerini abonelik anahtarınızla değiştirin.
    1. İstek URL 'sinin () ilk kısmını `westcentralus` kendi uç nokta URL 'inizdeki metinle değiştirin.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. İsteğe bağlı olarak, istek gövdesindeki görüntü URL’sini (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`), analiz edilecek farklı bir görüntünün URL’si ile değiştirin.
1. Bir komut istemi penceresi açın.
1. Metin düzenleyicisindeki komutu komut istemi penceresine yapıştırın ve komutu çalıştırın.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/analyze?visualFeatures=Categories,Description&details=Landmarks" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

### <a name="examine-the-response"></a>Yanıtı inceleme

Başarılı bir yanıt JSON biçiminde döndürülür. Örnek uygulama, aşağıdaki örneğe benzer şekilde başarılı bir yanıtı ayrıştırıp komut istemi penceresinde görüntüler:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```


## <a name="generate-a-thumbnail"></a>Küçük resim oluşturma

Akıllı kırpma ile bir küçük resim oluşturmak için görüntü analizini kullanabilirsiniz. İstenen yüksekliği ve genişliği belirtirsiniz ve bu, giriş görüntüsünden en boy oranına göre farklılık gösterebilir. Görüntü analizi, ilgilendiğiniz alanı saptamak ve bu bölge etrafında kırpma koordinatları oluşturmak için akıllı kırpma kullanır.
 
Örneği oluşturup çalıştırmak için aşağıdaki adımları uygulayın:

1. Aşağıdaki komutu bir metin düzenleyicisine kopyalayın.
1. Gerektiğinde komutta aşağıdaki değişiklikleri yapın:
    1. `<subscriptionKey>` değerini abonelik anahtarınızla değiştirin.
    1. Değerini, `<thumbnailFile>` döndürülen küçük resim resminin kaydedileceği dosyanın yolu ve adıyla değiştirin.
    1. İstek URL 'sinin () ilk kısmını `westcentralus` kendi uç nokta URL 'inizdeki metinle değiştirin.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. İsteğe bağlı olarak, istek gövdesindeki görüntü URL’sini (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) içinden küçük resim oluşturulacak farklı bir görüntünün URL’si ile değiştirin.
1. Bir komut istemi penceresi açın.
1. Komutu metin düzenleyicisinden komut istemi penceresine yapıştırın.
1. Programı çalıştırmak için ENTER tuşuna basın.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.2/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

### <a name="examine-the-response"></a>Yanıtı inceleme

Başarılı bir yanıt, küçük resim görüntüsünü `<thumbnailFile>` içinde belirtilen dosyaya yazar. İstek başarısız olursa yanıt, bir hata kodu ve nelerin yanlış gittiğini belirlemeye yardımcı olması için bir ileti içerir. İstek başarılı olursa ancak oluşturulan küçük resim geçerli bir görüntü dosyası değilse, abonelik anahtarınız geçersiz olabilir.


## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, REST API kullanarak temel görüntü analiz çağrıları oluşturma hakkında daha fazla öğrendiniz. Sonra, API özelliklerini çözümle hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
>[Çözümle API 'sini çağırma](../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Görüntü analizine genel bakış](../overview-image-analysis.md)
