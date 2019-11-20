---
title: 'Hızlı başlangıç: REST API ve Java ile uzak görüntüyü çözümleme'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Java ile Görüntü İşleme API'si kullanarak bir görüntüyü analiz edersiniz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 3674db68de509c738488b8c3f1d95b7c8c31c306
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176553"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-java"></a>Hızlı başlangıç: Görüntü İşleme REST API ve Java kullanarak uzak görüntüyü çözümleme

Bu hızlı başlangıçta, Java ve Görüntü İşleme REST API kullanarak görsel özellikleri ayıklamak için uzaktan depolanan bir görüntüyü analiz edersiniz. [Görüntüyü çözümle](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) yöntemiyle, görüntü içeriğine göre görsel özellikleri ayıklayabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- [Java @ no__t-1 platformu, Standard Edition geliştirme seti 7 veya 8](https://aka.ms/azure-jdks) (JDK 7 veya 8) yüklü olmalıdır.
- Görüntü İşleme için bir abonelik anahtarınız olması gerekir. Deneme bilişsel [Hizmetler](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)'den ücretsiz bir deneme anahtarı edinebilirsiniz. Ya da Görüntü İşleme abone olmak ve anahtarınızı almak için bilişsel [Hizmetler oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ' daki yönergeleri izleyin. Ardından, sırasıyla `COMPUTER_VISION_SUBSCRIPTION_KEY` ve `COMPUTER_VISION_ENDPOINT` adlı anahtar ve hizmet uç noktası dizesi için [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .

## <a name="create-and-run-the-sample-application"></a>Örnek uygulama oluşturma ve çalıştırma

Örneği oluşturmak ve çalıştırmak için aşağıdaki adımları uygulayın:

1. En sevdiğiniz IDE veya düzenleyicide yeni bir Java projesi oluşturun. Seçenek varsa, bir komut satırı uygulama şablonundan Java projesi oluşturun.
1. Aşağıdaki kitaplıkları Java projenize aktarın. Maven kullanıyorsanız, her kitaplık için Maven koordinatları sağlanır.
   - [Apache HTTP istemcisi](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: HttpClient: 4.5.5)
   - [Apache HTTP Core](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: httpcore: 4.4.9)
   - [JSON kitaplığı](https://github.com/stleary/JSON-java) (org. JSON: JSON: 20180130)
1. Aşağıdaki `import` deyimlerini projeniz için `Main` ortak sınıfını içeren dosyaya ekleyin.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. @No__t-0 ortak sınıfını aşağıdaki kodla değiştirin.
1. İsteğe bağlı olarak, `imageToAnalyze` değerini, çözümlemek istediğiniz farklı bir görüntünün URL 'siyle değiştirin.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    // After setting, close and then re-open your command shell or project for the changes to take effect.
    String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    String endpoint = ("COMPUTER_VISION_ENDPOINT");

    private static final String uriBase = endpoint + 
            "vision/v2.1/analyze";
    private static final String imageToAnalyze =
            "https://upload.wikimedia.org/wikipedia/commons/" +
                    "1/12/Broadway_and_Times_Square_by_night.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="compile-and-run-the-program"></a>Programı derle ve Çalıştır

1. Java projesini kaydedin ve oluşturun.
1. IDE kullanıyorsanız `Main` ' ı çalıştırın.

Alternatif olarak, programı bir komut satırı penceresinden çalıştırıyorsanız, aşağıdaki komutları çalıştırın. Bu komutlar, kitaplıklarınızın `Main.java` ile aynı klasörde olan `libs` adlı bir klasörde olduğunu varsayar; Aksi takdirde, `libs` ' yi kitaplıklarınızın yoluyla değiştirmeniz gerekir.

1. Dosyayı derleyin `Main.java`.

    ```bash
    javac -cp ".;libs/*" Main.java
    ```

1. Programı çalıştırın. Bu işlem, KB 'yi oluşturmak için Soru-Cevap Oluşturma API'si isteği gönderir, ardından sonuçları 30 saniyede bir yoklayacak. Her yanıt komut satırı penceresine yazdırılır.

    ```bash
    java -cp ".;libs/*" Main
    ```

## <a name="examine-the-response"></a>Yanıtı inceleyin

JSON 'da başarılı bir yanıt döndürülür. Örnek uygulama, aşağıdaki örneğe benzer şekilde konsol penceresinde başarılı bir yanıt ayrıştırır ve görüntüler:

```json
REST Response:

{
  "metadata": {
    "width": 1826,
    "format": "Jpeg",
    "height": 2436
  },
  "color": {
    "dominantColorForeground": "Brown",
    "isBWImg": false,
    "accentColor": "B74314",
    "dominantColorBackground": "Brown",
    "dominantColors": ["Brown"]
  },
  "requestId": "bbffe1a1-4fa3-4a6b-a4d5-a4964c58a811",
  "description": {
    "captions": [{
      "confidence": 0.8241405091548035,
      "text": "a group of people on a city street filled with traffic at night"
    }],
    "tags": [
      "outdoor",
      "building",
      "street",
      "city",
      "busy",
      "people",
      "filled",
      "traffic",
      "many",
      "table",
      "car",
      "group",
      "walking",
      "bunch",
      "crowded",
      "large",
      "night",
      "light",
      "standing",
      "man",
      "tall",
      "umbrella",
      "riding",
      "sign",
      "crowd"
    ]
  },
  "categories": [{
    "score": 0.625,
    "name": "outdoor_street"
  }]
}
```

## <a name="next-steps"></a>Sonraki adımlar

Optik karakter tanıma (OCR) yapmak için Görüntü İşleme kullanan bir Java esnek uygulaması keşfet; Akıllı kırpılan küçük resimler oluşturun; Ayrıca, bir görüntüdeki yüzler dahil görsel özellikleri algılayın, kategorilere ayırın, etiketleyin ve tanıtın. Görüntü İşleme API'si hızlı bir şekilde denemek için, [Açık API test konsolunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)deneyin.

> [!div class="nextstepaction"]
> [Görüntü İşleme API'si Java öğreticisi](../Tutorials/java-tutorial.md)
