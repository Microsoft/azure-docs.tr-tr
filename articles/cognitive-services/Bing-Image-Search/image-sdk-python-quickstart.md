---
title: 'Hızlı Başlangıç: Görüntü arama-Python için Bing Resim Arama SDK'
titleSuffix: Azure Cognitive Services
description: Bu öğreticiyi API için bir sarmalayıcı olan ve aynı özellikleri içeren Bing Resim Arama SDK'sını kullanarak ilk görüntü aramanızı yapmak için kullanın. Bu basit Python uygulaması, bir görüntü arama sorgusu gönderir, JSON yanıtını ayrıştırır ve döndürülen ilk görüntünün URL’sini görüntüler.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 08/26/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: b5a4cd36f204dfe708e5acd8818d9925b5accc30
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034706"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-python"></a>Hızlı Başlangıç: Python için Bing Resim Arama SDK ile görüntü arama

Bu öğreticiyi API için bir sarmalayıcı olan ve aynı özellikleri içeren Bing Resim Arama SDK'sını kullanarak ilk görüntü aramanızı yapmak için kullanın. Bu basit Python uygulaması, bir görüntü arama sorgusu gönderir, JSON yanıtını ayrıştırır ve döndürülen ilk görüntünün URL’sini görüntüler.

Bu örneğin kaynak kodu, ek hata işleme ve açıklama notları ile [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py)’da bulunabilir.

## <a name="prerequisites"></a>Önkoşullar
**Arama** altından bir [Bilişsel Hizmetler erişim anahtarı](https://azure.microsoft.com/try/cognitive-services/) alın.  Ayrıca bkz: [Bilişsel hizmetler fiyatlandırması - Bing arama API'si](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

* [Python 2.7 veya 3.4](https://www.python.org/) ve üzeri.

* Python için [Azure Görüntü Arama SDK’sı](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/)
    * `pip install azure-cognitiveservices-search-imagesearch` kullanarak yükleme

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Uygulamayı oluşturma ve başlatma

1. Sık kullandığınız IDE ortamında veya düzenleyicide yeni bir Python betiği ve aşağıdaki içeri aktarımları oluşturun:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Abonelik anahtarınız ve arama teriminiz için değişkenler oluşturun.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Görüntü arama istemcisi oluşturma

1. `CognitiveServicesCredentials` örneği oluşturun ve istemcinin bir örneğini başlatmak için bunu kullanın:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
1. Bing Resim Arama API’sine arama sorgusu gönderme:
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>Sonuçları işleme ve görüntüleme

Yanıtta döndürülen resim sonuçlarını ayrıştırın.


Yanıt arama sonuçları içeriyorsa, ilk sonucu depolayın ve döndürülen toplam görüntü sayısının yanı sıra bu ilk sonucun küçük resim URL'si, asıl URL gibi ayrıntılarını yazdırın.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bing Resim Arama tek sayfalı uygulama öğreticisi](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Ayrıca bkz.

* [Bing Resim Arama nedir?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Çevrimiçi etkileşimli bir tanıtımı deneyin](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ücretsiz bir Bilişsel Hizmetler erişim anahtarı alın](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Azure Bilişsel Hizmetler SDK’sı için Python örnekleri](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Azure Bilişsel Hizmetler Belgeleri](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Resim Arama API’si başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
