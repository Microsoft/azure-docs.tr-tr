---
title: 'Hızlı başlangıç: optik karakter tanıma REST API'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta REST API optik karakter tanıma ile çalışmaya başlayın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2f01b1d222470c49505638be64180948b6f7e046
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728267"
---
Yazdırılmış ve el yazısı metin okumak için optik karakter tanıma REST API kullanın.

> [!NOTE]
> Bu hızlı başlangıç, REST API çağırmak için kıvrımlı komutları kullanır. Ayrıca, bir programlama dili kullanarak REST API çağırabilirsiniz. [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)ve [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)örnekleri için GitHub örneklerine bakın.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/) 
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Görüntü İşleme kaynağı oluşturun görüntü işleme bir kaynak oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
  * Uygulamanızı Görüntü İşleme hizmetine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
  * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.
* [kıvrımlı](https://curl.haxx.se/) yüklendi



## <a name="read-printed-and-handwritten-text"></a>Yazdırılmış ve el yazısı metin oku

OCR hizmeti görüntüdeki görünür metni okuyabilir ve bunu bir karakter akışına dönüştürebilir. Metin tanıma hakkında daha fazla bilgi için bkz. [optik karakter tanıma (OCR) genel bakış](../overview-ocr.md).

### <a name="call-the-read-api"></a>Okuma API 'sini çağırma

Örneği oluşturup çalıştırmak için aşağıdaki adımları uygulayın:

1. Aşağıdaki komutu bir metin düzenleyicisine kopyalayın.
1. Gerektiğinde komutta aşağıdaki değişiklikleri yapın:
    1. `<subscriptionKey>` değerini abonelik anahtarınızla değiştirin.
    1. İstek URL 'sinin () ilk kısmını `westcentralus` kendi uç nokta URL 'inizdeki metinle değiştirin.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. İsteğe bağlı olarak, istek gövdesindeki görüntü URL’sini (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`), analiz edilecek farklı bir görüntünün URL’si ile değiştirin.
1. Bir komut istemi penceresi açın.
1. Metin düzenleyicisindeki komutu komut istemi penceresine yapıştırın ve komutu çalıştırın.

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

Yanıt, `Operation-Location` değeri benzersiz BIR URL olan bir üst bilgi içerecektir. Okuma işleminin sonuçlarını sorgulamak için bu URL 'YI kullanırsınız. URL 48 saat içinde sona erecek.

### <a name="get-read-results"></a>Okuma sonuçları al

1. Aşağıdaki komutu metin düzenleyicinize kopyalayın.
1. URL 'YI, `Operation-Location` önceki adımda kopyaladığınız değerle değiştirin.
1. Gerektiğinde komutta aşağıdaki değişiklikleri yapın:
    1. `<subscriptionKey>` değerini abonelik anahtarınızla değiştirin.
1. Bir komut istemi penceresi açın.
1. Metin düzenleyicisindeki komutu komut istemi penceresine yapıştırın ve komutu çalıştırın.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>Yanıtı inceleme

Başarılı bir yanıt JSON biçiminde döndürülür. Örnek uygulama, aşağıdaki örneğe benzer şekilde başarılı bir yanıtı ayrıştırıp komut istemi penceresinde görüntüler:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-04-08T21:56:17.6819115+00:00",
  "lastUpdatedDateTime": "2021-04-08T21:56:18.4161316+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 338,
        "height": 479,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              25,
              14,
              318,
              14,
              318,
              59,
              25,
              59
            ],
            "text": "NOTHING",
            "appearance": {
              "style": {
                "name": "other",
                "confidence": 0.971
              }
            },
            "words": [
              {
                "boundingBox": [
                  27,
                  15,
                  294,
                  15,
                  294,
                  60,
                  27,
                  60
                ],
                "text": "NOTHING",
                "confidence": 0.994
              }
            ]
          }
        ]
      }
    ]
  }
}

```



## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, okuma REST API nasıl çağrılacağını öğrendiniz. Ardından, okuma API 'SI özellikleri hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
>[Okuma API 'sini çağırma](../Vision-API-How-to-Topics/call-read-api.md)

* [OCR genel bakış](../overview-ocr.md)
