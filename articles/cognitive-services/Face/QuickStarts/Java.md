---
title: 'Hızlı Başlangıç: Azure REST API ve Java ile görüntüdeki yüzeyleri algılama'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, bir görüntüdeki yüzeyleri algılamak için Java ile REST API Azure yüz kullanacaksınız.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 09/06/2019
ms.author: pafarley
ms.openlocfilehash: 30e4852668fc12c38cd7d1794c461041acd654db
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859203"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Hızlı Başlangıç: REST API ve Java kullanarak görüntüdeki yüzeyleri algılama

Bu hızlı başlangıçta, bir görüntüdeki insan yüzlerini algılamak için Java ile REST API Azure yüz kullanacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

## <a name="prerequisites"></a>Önkoşullar

- Yüz tanıma API'si abonelik anahtarı. Ücretsiz deneme aboneliği anahtarından alabilirsiniz [Bilişsel Hizmetler'i deneyin](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Veya yönergeleri [Bilişsel Hizmetler hesabı oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) yüz tanıma API'si hizmete abone ve anahtarınızı alın.
- Dilediğiniz Java IDE.

## <a name="create-the-java-project"></a>Java projesi oluşturma

1. IDE 'nize yeni bir komut satırı Java uygulaması oluşturun ve **Main** yöntemine sahip bir **ana** sınıf ekleyin.
1. Aşağıdaki kitaplıkları Java projenize içeri aktarın. Maven kullanıyorsanız, her bir kitaplık için Maven koordinatları sağlanır.
   - [Apache HTTP istemcisi](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: HttpClient: 4.5.6)
   - [Apache HTTP Core](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: httpcore: 4.4.10)
   - [JSON kitaplığı](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Apache Commons günlüğü](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (Commons-Logging: Commons-günlüğe kaydetme: 1.1.2)

## <a name="add-face-detection-code"></a>Yüz algılama kodu ekleme

Projenizin ana sınıfını açın. Burada, görüntüleri yüklemek ve yüzeyleri algılamak için gereken kodu ekleyeceksiniz.

### <a name="import-packages"></a>Paketleri içeri aktarma

Aşağıdaki `import` deyimlerini dosyanın en üstüne ekleyin.

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>Gerekli alanları Ekle

**Ana** sınıfı aşağıdaki kodla değiştirin. Bu veriler, yüz hizmetine bağlanmayı ve giriş verilerinin nereden alınacağını belirtir. `subscriptionKey` Alanı abonelik anahtarınızın değeriyle güncelleştirmeniz ve `uriBase` dizeyi doğru uç nokta dizesini içerecek şekilde değiştirmeniz gerekir. Ayrıca değeri, `imageWithFaces` farklı bir görüntü dosyasına işaret eden bir yola ayarlamak isteyebilirsiniz.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

`faceAttributes` Alanı yalnızca belirli öznitelik türlerinin bir listesidir. Algılanan yüzler hakkında hangi bilgilerin alınacağı belirler.

```Java
public class Main {
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    private static final String uriBase =
        "https://<My Endpoint String>.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>Yüz algılamayı çağırın REST API

**Ana** yöntemi aşağıdaki kodla ekleyin. Uzak görüntüdeki yüz bilgilerini algılamak için yüz tanıma API'si bir rest çağrısı oluşturur ( `faceAttributes` dize hangi yüz özniteliklerini almak üzere belirtir). Ardından, çıkış verilerini bir JSON dizesine yazar.

```Java
    public static void main(String[] args) {
        HttpClient httpclient = HttpClientBuilder.create().build();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();
```

### <a name="parse-the-json-response"></a>JSON yanıtını Ayrıştır

Önceki kodun hemen altına, döndürülen JSON verilerini konsola yazdırmadan önce daha kolay okunabilir bir biçime dönüştüren aşağıdaki bloğu ekleyin. Son olarak, try-catch bloğunu, **Main** yöntemini ve **ana** sınıfı kapatın.

```Java
            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Kodu derleyin ve çalıştırın. Başarılı bir yanıt, yüz verilerini konsol penceresinde kolay okunabilir JSON biçiminde görüntüler. Örneğin:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
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
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir görüntüdeki yüzeyleri algılamak ve özniteliklerini döndürmek için Azure Yüz Tanıma API'si ile REST çağrıları kullanan basit bir Java konsol uygulaması oluşturdunuz. Daha sonra, Android uygulamasında bu işlevle daha fazlasını yapmayı öğrenin.

> [!div class="nextstepaction"]
> [Öğretici: Yüzeyleri algılamak ve çerçeveye eklemek için bir Android uygulaması oluşturma](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
