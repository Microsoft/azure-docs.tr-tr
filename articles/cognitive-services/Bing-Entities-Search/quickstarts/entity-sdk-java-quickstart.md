---
title: 'Hızlı Başlangıç: Java için Bing Varlık Arama SDK ile varlıkları arama'
titleSuffix: Azure Cognitive Services
description: Java için Bing Varlık Arama SDK 'Sı ile varlıkları aramak için bu hızlı başlangıcı kullanın
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 63312dcbad515f364bd4b66c5541f1db90af85b6
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479052"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>Hızlı Başlangıç: Java için Bing Varlık Arama SDK ile bir arama isteği gönderme

Java için Bing Varlık Arama SDK ile varlıkları aramaya başlamak için bu hızlı başlangıcı kullanın. Bing Varlık Arama birçok programlama dili ile uyumlu bir REST API sahip olsa da SDK, hizmeti uygulamalarınızla tümleştirmenin kolay bir yolunu sunar. Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch)' da bulunabilir.

## <a name="prerequisites"></a>Önkoşullar

* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* Java için Bing Varlık Arama SDK 'Sı

Bing Varlık Arama SDK'sı bağımlılık dosyalarını Maven, Gradle veya başka bir bağımlılık dosyası yönetim sistemini kullanarak yükleyin. Maven POM dosyası şu bildirimi gerektirir:

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Proje oluşturma ve başlatma

1. Sık kullandığınız IDE ortamında veya düzenleyicide yeni bir Java projesi oluşturun ve aşağıdaki kitaplıkları içeri aktarın.

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. Abonelik anahtarınız için bir değişken oluşturun

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Arama İstemcisi Oluşturma

1. API uç noktanızı ve `ServiceClientCredentials` sınıfının bir örneğini gerektiren istemciyiuygulayın.`dominantEntityLookup`

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Uygulamak `ServiceClientCredentials`için aşağıdaki adımları izleyin:

   1. parametresi olarak bir `OkHttpClient.Builder` nesnesi ile işlevigeçersizkılın.`applyCredentialsFilter()` 
        
       ```java
       //...
       new ServiceClientCredentials() {
               @Override
               public void applyCredentialsFilter(OkHttpClient.Builder builder) {
               //...
               }
       //...
       ```
    
   2. İçinde `applyCredentialsFilter()`, çağırın `builder.addNetworkInterceptor()`. Yeni `Interceptor` bir nesne oluşturun ve `intercept()` yöntemini bir `Chain` dinleyici nesnesi alacak şekilde geçersiz kılın.

       ```java
       //...
       builder.addNetworkInterceptor(
           new Interceptor() {
               @Override
               public Response intercept(Chain chain) throws IOException {
               //...    
               }
           });
       ///...
       ```

   3. `intercept` İşlevi içinde, isteğiniz için değişkenler oluşturun. İsteğinizi `Request.Builder()` derlemek için kullanın. Abonelik anahtarınızı `Ocp-Apim-Subscription-Key` üstbilgiye ekleyin ve istek nesnesine dönün `chain.proceed()` .
            
       ```java
       //...
       public Response intercept(Chain chain) throws IOException {
           Request request = null;
           Request original = chain.request();
           Request.Builder requestBuilder = original.newBuilder()
                   .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
           request = requestBuilder.build();
           return chain.proceed(request);
       }
       //...
       ```
      ## <a name="send-a-request-and-receive-a-response"></a>İstek gönderme ve yanıt alma

1. Abonelik anahtarınızla arama istemcisinin yeni bir örneğini oluşturun. Arama `client.entities().search()` sorgusu`satya nadella`için bir arama isteği göndermek ve bir yanıt almak için kullanın. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. Herhangi bir varlık döndürülürse, bunları bir listeye dönüştürün. Aralarında yineleme yapın ve baskın varlığı yazdırın.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entrys = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entrys) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Tek sayfalı Web uygulaması oluşturma](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Varlık Arama API'si nedir?](../overview.md )
