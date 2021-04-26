---
title: Yüz REST API hızlı başlangıç
description: Yüzleri, yüzeyleri algılamak, benzer (görüntüye göre arama), yüzeyleri belirlemek ve yüz verilerinizi geçirmek için kıvrımlı REST API kullanın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: 1a360a19da8006a8ab6d918560f1b1d566fdd66d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104024"
---
Yüz tanıma REST API kullanarak yüz tanıma çalışmaya başlama. Yüz tanıma hizmeti, görüntülerdeki insan yüzlerini algılayıp tanımayı sağlayan gelişmiş algoritmalara erişmenizi sağlar.

Yüz REST API şu şekilde kullanın:

* [Bir görüntüdeki yüzleri algılama](#detect-faces-in-an-image)
* [Benzer yüzeyleri bulun](#find-similar-faces)

> [!NOTE]
> Bu hızlı başlangıç, REST API çağırmak için kıvrımlı komutları kullanır. Ayrıca, bir programlama dili kullanarak REST API çağırabilirsiniz. [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest)ve [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest)örnekleri için GitHub örneklerine bakın.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" bir yüz kaynağı oluşturun "  target="_blank"> </a> Azure Portal anahtar ve uç noktanıza ulaşmak için bir yüz kaynağı oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Yüz Tanıma API'si bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.
* [PowerShell sürüm 6.0 +](/powershell/scripting/install/installing-powershell-core-on-windows)veya benzer bir komut satırı uygulaması.


## <a name="detect-faces-in-an-image"></a>Bir görüntüdeki yüzleri algılama

Yüz Tanıma API'si çağırmak ve bir görüntüden yüz öznitelik verileri almak için aşağıdaki gibi bir komut kullanacaksınız. İlk olarak, kodu bir metin düzenleyicisine kopyalayın, &mdash; çalıştırmadan önce komutun belirli kısımlarında değişiklik yapmanız gerekir.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_2":::

Aşağıdaki değişiklikleri yapın:
1. `Ocp-Apim-Subscription-Key`Geçerli yüz abonelik anahtarınıza atayın.
1. Sorgu URL 'sinin ilk kısmını, abonelik anahtarınıza karşılık gelen uç noktayla eşleşecek şekilde değiştirin.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. İsteğe bağlı olarak, isteğin gövdesindeki URL 'YI farklı bir görüntüye işaret etmek üzere değiştirin.

Değişikliklerinizi yaptıktan sonra, bir komut istemi açın ve yeni komutu girin. 

### <a name="examine-the-results"></a>Sonuçları inceleme

Konsol penceresinde JSON verileri olarak görünen yüz bilgilerini görmeniz gerekir. Örnek:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

### <a name="get-face-attributes"></a>Yüz özniteliklerini Al
 
Yüz özniteliklerini ayıklamak için, algılama API 'sini yeniden çağırın, ancak `detectionModel` olarak ayarlayın `detection_01` . `returnFaceAttributes`Sorgu parametresini de ekleyin. Komut artık aşağıdaki gibi görünmelidir. Daha önce olduğu gibi, yüz abonelik anahtarınızı ve uç noktayı ekleyin.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>Sonuçları inceleme

Döndürülen yüz bilgileri artık yüz öznitelikleri içeriyor. Örnek:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="find-similar-faces"></a>Benzer yüzleri bulma

Bu işlem, algılanan tek bir yüzeyi (kaynak) alır ve diğer yüzlerin (hedef) bir kümesini arar (yüz görüntüye göre arama). Bir eşleşme bulduğunda, eşleşen yüzün KIMLIĞINI konsola yazdırır.

### <a name="detect-faces-for-comparison"></a>Karşılaştırılacak Yüzleri Algıla

İlk olarak, bunları karşılaştırabilmeniz için görüntülerde yüzeyleri tespit etmeniz gerekir. Bu komutu, [yüzeyleri algılama](#detect-faces-in-an-image) bölümünde yaptığınız gibi çalıştırın. Bu algılama yöntemi, karşılaştırma işlemleri için iyileştirilmiştir. Yukarıdaki bölümde olduğu gibi ayrıntılı yüz özniteliklerini ayıklamaz ve farklı bir algılama modeli kullanır.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

`"faceId"`JSON yanıtında değeri bulun ve geçici bir konuma kaydedin. Daha sonra, bu diğer resim URL 'Leri için yukarıdaki komutu yeniden çağırın ve yüz kimliklerini de kaydedin. Bu kimlikleri, benzer bir yüzü bulacağınız yüzlerin hedef grubu olarak kullanacaksınız.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

Son olarak, eşleştirme için kullanacağınız tek kaynak yüzünü tespit edin ve KIMLIĞINI kaydedin. Bu KIMLIĞI diğerlerinden ayrı tutun.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>Eşleşmeleri bul

Aşağıdaki komutu bir metin düzenleyicisine kopyalayın.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

Ardından aşağıdaki değişiklikleri yapın:
1. `Ocp-Apim-Subscription-Key`Geçerli yüz abonelik anahtarınıza atayın.
1. Sorgu URL 'sinin ilk kısmını, abonelik anahtarınıza karşılık gelen uç noktayla eşleşecek şekilde değiştirin.

Değer için aşağıdaki JSON içeriğini kullanın `body` :

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. İçin kaynak yüz KIMLIĞINI kullanın `"faceId"` .
1. Diğer yüz kimliklerini dizide terim olarak yapıştırın `"faceIds"` .

### <a name="examine-the-results"></a>Sonuçları inceleme

Sorgu yüzlerinizle eşleşen yüzlerin kimliklerini listeleyen bir JSON yanıtı alacaksınız.

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, temel yüz tanıma görevlerini yapmak için yüz REST API nasıl kullanacağınızı öğrendiniz. Daha sonra, kitaplık hakkında daha fazla bilgi edinmek için başvuru belgelerini inceleyin.

> [!div class="nextstepaction"]
> [Yüz Tanıma API'si Başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

* [Yüz Tanıma hizmeti nedir?](../../overview.md)