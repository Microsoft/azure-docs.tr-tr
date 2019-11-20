---
title: 'Hızlı başlangıç: küçük resim oluşturma-REST, Java'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Java ile Görüntü İşleme API'si kullanarak bir görüntüden küçük resim oluşturursunuz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 703453de705ab8a2e1b5b324bdad25ea221cfbfb
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177217"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-java"></a>Hızlı başlangıç: Görüntü İşleme REST API ve Java kullanarak küçük resim oluşturma

Bu hızlı başlangıçta, Görüntü İşleme REST API kullanarak bir görüntüden küçük resim oluşturursunuz. Bir yükseklik ve genişlik belirtirsiniz ve bu, giriş resminin en boy oranından farklı olabilir. Görüntü İşleme, ilgilendiğiniz alanı saptamak ve bu bölgeye göre kırpma koordinatları oluşturmak için akıllı kırpma kullanır.

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
   import java.awt.*;
   import javax.swing.*;
   import java.net.URI;
   import java.io.InputStream;
   import javax.imageio.ImageIO;
   import java.awt.image.BufferedImage;
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
1. İsteğe bağlı olarak, `imageToAnalyze` değerini, küçük resim oluşturmak istediğiniz farklı bir görüntünün URL 'siyle değiştirin.
1. Java projesini kaydedin ve oluşturun.
1. IDE kullanıyorsanız `Main` ' ı çalıştırın. Aksi takdirde, bir komut istemi penceresi açın ve ardından derlenmiş sınıfı çalıştırmak için `java` komutunu kullanın. Örneğin, `java Main`.

```java
// This sample uses the following libraries:
//  - Apache HTTP client (org.apache.httpcomponents:httpclient:4.5.5)
//  - Apache HTTP core (org.apache.httpcomponents:httpccore:4.4.9)
//  - JSON library (org.json:json:20180130).


public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    // After setting, close and then re-open your command shell or project for the changes to take effect.
    String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    String endpoint = ("COMPUTER_VISION_ENDPOINT");

    private static final String uriBase = endpoint + 
            "vision/v2.1/generateThumbnail";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API method.
            URI uri = uriBuilder.build();
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

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200) {
                // Display the thumbnail.
                System.out.println("\nDisplaying thumbnail.\n");
                displayImage(entity.getContent());
            } else {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }

    // Displays the given input stream as an image.
    private static void displayImage(InputStream inputStream) {
        try {
            BufferedImage bufferedImage = ImageIO.read(inputStream);

            ImageIcon imageIcon = new ImageIcon(bufferedImage);

            JLabel jLabel = new JLabel();
            jLabel.setIcon(imageIcon);

            JFrame jFrame = new JFrame();
            jFrame.setLayout(new FlowLayout());
            jFrame.setSize(100, 150);

            jFrame.add(jLabel);
            jFrame.setVisible(true);
            jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>Yanıtı inceleyin

Başarılı bir yanıt, küçük resim için görüntü verilerini temsil eden ikili veriler olarak döndürülür. İstek başarılı olursa, küçük resim yanıttaki ikili verilerden oluşturulur ve örnek uygulama tarafından oluşturulan ayrı bir pencerede görüntülenir. İstek başarısız olursa, yanıt konsol penceresinde görüntülenir. Başarısız istek yanıtı, neyin yanlış olduğunu belirlemenize yardımcı olmak için bir hata kodu ve bir ileti içerir.

## <a name="next-steps"></a>Sonraki adımlar

Optik karakter tanıma (OCR) yapmak için Görüntü İşleme kullanan bir Java esnek uygulaması keşfet; Akıllı kırpılan küçük resimler oluşturun; Ayrıca, bir görüntüdeki yüzler dahil görsel özellikleri algılayın, kategorilere ayırın, etiketleyin ve tanıtın. Görüntü İşleme API'si hızlı bir şekilde denemek için, [Açık API test konsolunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)deneyin.

> [!div class="nextstepaction"]
> [Görüntü İşleme API'si Java öğreticisi](../Tutorials/java-tutorial.md)
