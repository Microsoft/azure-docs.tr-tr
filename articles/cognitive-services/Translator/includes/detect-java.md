---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 9ce4b25d26c0294cf7618b5851c0956af7687ee7
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837511"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Gradle ile proje başlatma

Bu proje için çalışma dizini oluşturarak başlayalım. Komut satırından (veya terminalden) şu komutu çalıştırın:

```console
mkdir detect-sample
cd detect-sample
```

Sonra, bir Gradle projesi başlatacağız. Bu komut, uygulamanızı oluşturmak ve yapılandırmak için çalışma zamanında kullanılan, en önemlisi, `build.gradle.kts` olan Gradle için temel derleme dosyaları oluşturur. Çalışma dizininizden bu komutu çalıştırın:

```console
gradle init --type basic
```

Bir **DSL**seçmeniz Istendiğinde, **Kotlin**' ı seçin.

## <a name="configure-the-build-file"></a>Yapı dosyasını yapılandırma

@No__t-0 ' yı bulun ve en sevdiğiniz IDE veya metin düzenleyicinizle açın. Sonra bu derleme yapılandırmasında Kopyala:

```
plugins {
    java
    application
}
application {
    mainClassName = "Detect"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Bu örnekte HTTP istekleri için OkHttp üzerinde bağımlılıklar ve JSON 'u işlemek ve ayrıştırmak için Gson 'un bağımlılıkları olduğuna göz atın. Derleme yapılandırmaları hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Yeni Gradle derlemeleri oluşturma](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Java dosyası oluşturma

Örnek uygulamanız için bir klasör oluşturalım. Çalışma dizininizden şunu çalıştırın:

```console
mkdir -p src/main/java
```

Ardından, bu klasörde `Detect.java` adlı bir dosya oluşturun.

## <a name="import-required-libraries"></a>Gerekli kitaplıkları içeri aktar

@No__t-0 ' yı açın ve şu içeri aktarma deyimlerini ekleyin:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Değişkenleri tanımlama

İlk olarak, projeniz için bir ortak sınıf oluşturmanız gerekir:

```java
public class Detect {
  // All project code goes here...
}
```

Bu satırları `Detect` sınıfına ekleyin. Abonelik anahtarı ve uç noktanın ortam değişkenlerinden okunmakta olduğunu fark edeceksiniz:

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/detect?api-version=3.0";
```

Bilişsel hizmetler çoklu hizmet aboneliği kullanıyorsanız, istek parametrelerinize `Ocp-Apim-Subscription-Region` de eklemeniz gerekir. [Multi-Service aboneliğiyle kimlik doğrulama hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>İstemci oluşturma ve istek oluşturma

@No__t-1 ' i oluşturmak için bu satırı `Detect` sınıfına ekleyin:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Sonra POST isteğini oluşturalım. Dil algılama için metni değiştirmeyi ücretsiz olarak hissetmekten çekinmeyin.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Salve mondo!\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Yanıtı ayrıştırmak için bir işlev oluşturma

Bu basit işlev, Translator Metin Çevirisi hizmetinden gelen JSON yanıtını ayrıştırır ve önceden inceler.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Hepsini bir araya getirin

Son adım bir istek yapmak ve yanıt almak için kullanılır. Bu satırları projenize ekleyin:

```java
public static void main(String[] args) {
    try {
        Detect detectRequest = new Detect();
        String response = detectRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Örnek uygulamayı çalıştırma

Bu, örnek uygulamanızı çalıştırmaya hazırsınız. Komut satırından (veya terminal oturumu), çalışma dizininizin köküne gidin ve şunu çalıştırın:

```console
gradle build
```

Oluşturma tamamlandığında, şunu çalıştırın:

```console
gradle run
```

## <a name="sample-response"></a>Örnek yanıt

Örneği çalıştırdıktan sonra, şu şekilde terminalden yazdırılmış olduğunu görmeniz gerekir:

> [!NOTE]
> Bu [diller listesinde](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)ülke/bölge kısaltmasını bulun.

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="next-steps"></a>Sonraki adımlar

Translator Metin Çevirisi API'si ile yapabileceğiniz her şeyi anlamak için API başvurusuna göz atın.

> [!div class="nextstepaction"]
> [API başvurusu](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
