---
title: 'Hızlı Başlangıç: Bing Yazım Denetimi REST API ve Java ile yazımı denetleme'
titleSuffix: Azure Cognitive Services
description: Yazım ve dilbilgisini denetlemek için Bing Yazım Denetimi REST API kullanmaya başlayın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: 47b47a91b0cd223a17d6779d7f0db7cdb7c88a0b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123290"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Hızlı Başlangıç: Bing Yazım Denetimi REST API ve Java ile yazımı denetleme

Bing Yazım Denetimi REST API ilk çağrlarınızı yapmak için bu hızlı başlangıcı kullanın. Bu basit Java uygulaması, API 'ye bir istek gönderir ve önerilen düzeltmelerin bir listesini döndürür. Bu uygulama Java 'da yazıldığı sırada, API birçok programlama dili ile uyumlu olan bir yeniden sorun Web hizmetidir. Bu uygulamanın kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java)' da kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

* Java Development Kit (JDK) 7 veya üzeri.

* [Gson-2.8.5. jar](https://libraries.io/maven/com.google.code.gson%3Agson) veya en güncel [gson](https://github.com/google/gson) sürümünü içeri aktarın. Komut satırı yürütmesi için, `.jar` ana sınıfla Java klasörünüze ekleyin.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Uygulama oluşturma ve başlatma

1. Seçtiğiniz bir sınıf adı ile en sevdiğiniz IDE veya düzenleyicide yeni bir Java projesi oluşturun ve ardından aşağıdaki paketleri içeri aktarın.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. API uç noktasının ana bilgisayar, yol ve abonelik anahtarınız için değişkenler oluşturun. Daha sonra Pazar için değişkenler, yazım denetimi yapmak istediğiniz metin ve yazım denetimi modu için bir dize oluşturun.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>API isteği oluşturma ve gönderme

1. API isteği oluşturmak ve `check()` göndermek için adlı bir işlev oluşturun. Bunun içinde, aşağıdaki adımları izleyin. İstek parametreleri için bir dize oluşturun. parametreyi Pazar dizeniz `&mode=` ve parametresini yazım denetimi moduna ekleyin. `?mkt=`  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Uç nokta konağını, yolunu ve Parameters dizesini birleştirerek bir URL oluşturun. Yeni `HttpsURLConnection` bir nesne oluşturun.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. URL 'ye bir bağlantı açın. İstek yöntemini olarak `POST`ayarlayın. İstek parametrelerinizi ekleyin. Abonelik anahtarınızı `Ocp-Apim-Subscription-Key` üstbilgiye eklediğinizden emin olun.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Yeni `DataOutputStream` bir nesne oluşturun ve isteği API 'ye gönderin.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>API yanıtını biçimlendirme ve okuma

1. Bu yöntemi sınıfınıza ekleyin. Daha okunabilir bir çıkış için JSON 'ı biçimlendirir.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Bir `BufferedReader` oluşturun ve API 'den yanıtı okuyun. Bunu konsola yazdır.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>API çağırma

Uygulamanızın ana işlevinde, yukarıda oluşturulan Check () yöntemini çağırın.
```java
        public static void main(String[] args) {
            try {
                check();
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Projenizi derleyin ve çalıştırın.

Komut satırını kullanıyorsanız, uygulamayı derlemek ve çalıştırmak için aşağıdaki komutları kullanın.

**Derlemeyi**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Çalışmaz**
```bash
java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
```

## <a name="example-json-response"></a>Örnek JSON yanıtı

Başarılı yanıt, aşağıdaki örnekte gösterildiği gibi JSON biçiminde döndürülür:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Tek sayfalı web uygulaması oluşturma](../tutorials/spellcheck.md)

- [Bing Yazım Denetimi API'si nedir?](../overview.md)
- [Bing Yazım Denetimi API’si v7 Başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
