---
title: 'Hızlı Başlangıç: Görüntü arama-Java için Bing Resim Arama SDK'
titleSuffix: Azure Cognitive Services
description: Bu öğreticiyi API için bir sarmalayıcı olan ve aynı özellikleri içeren Bing Resim Arama SDK'sını kullanarak ilk görüntü aramanızı yapmak için kullanın. Bu basit Java uygulaması bir görüntü arama sorgusu gönderir, JSON yanıtını ayrıştırır ve döndürülen ilk görüntünün URL’sini görüntüler.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 08/26/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 0a9e9a4ee42d36e0ae6da3e92e12ae4144f7b670
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034454"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-java"></a>Hızlı Başlangıç: Java için Bing Resim Arama SDK ile görüntü arama

Bu öğreticiyi API için bir sarmalayıcı olan ve aynı özellikleri içeren Bing Resim Arama SDK'sını kullanarak ilk görüntü aramanızı yapmak için kullanın. Bu basit Java uygulaması bir görüntü arama sorgusu gönderir, JSON yanıtını ayrıştırır ve döndürülen ilk görüntünün URL’sini görüntüler.

Bu örneğin kaynak kodu, ek hata işleme ve açıklama notları ile [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart)’da bulunabilir.

## <a name="prerequisites"></a>Önkoşullar
**Arama** altından bir [Bilişsel Hizmetler erişim anahtarı](https://azure.microsoft.com/try/cognitive-services/) alın.  Ayrıca bkz: [Bilişsel hizmetler fiyatlandırması - Bing arama API'si](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

[Java Geliştirme Seti](https://aka.ms/azure-jdks)’nin (JDK) en son sürümü

Maven, Gradle veya başka bir bağımlılık dosyası yönetim sistemini kullanarak Bing Resim Arama SDK’sı bağımlılık dosyalarını yükleyin. Maven POM dosyası şu bildirimi gerektirir:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Uygulamayı oluşturma ve başlatma

1. En sevdiğiniz IDE veya düzenleyicide yeni bir Java projesi oluşturun ve sınıf uygulamanıza aşağıdaki içeri aktarmaları ekleyin:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. Ana yönteminizde abonelik anahtarınız ve arama teriminiz için değişkenler oluşturun. Ardından Bing Resim Arama istemcisinin örneğini oluşturun.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-api"></a>API 'ye bir arama isteği gönder

1. `bingImages().search()` kullanarak, arama sorgusunu içeren HTTP isteğini gönderin. Yanıtı `ImagesModel` olarak kaydedin.

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Sonucu ayrıştırma ve görüntüleme

Yanıtta döndürülen resim sonuçlarını ayrıştırın.
Yanıt, arama sonuçları içeriyorsa, ilk sonucu depolayın ve küçük resim URL 'SI, özgün URL gibi ayrıntılarını, döndürülen görüntülerin toplam sayısıyla birlikte yazdırın.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
     }

```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bing Resim Arama tek sayfalı uygulama öğreticisi](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Ayrıca bkz.

* [Bing Resim Arama nedir?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Çevrimiçi etkileşimli bir tanıtımı deneyin](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ücretsiz bir Bilişsel Hizmetler erişim anahtarı alın](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Azure Bilişsel Hizmetler SDK’sı için Java örnekleri](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Azure Bilişsel Hizmetler Belgeleri](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Resim Arama API’si başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
