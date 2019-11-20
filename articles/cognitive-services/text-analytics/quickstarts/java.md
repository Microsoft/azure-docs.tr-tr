---
title: 'Hızlı Başlangıç: Metin Analizi çağırmak için Java kullanın REST API'
titleSuffix: Azure Cognitive Services
description: Azure bilişsel hizmetler 'de Metin Analizi API'si kullanmaya hızlı bir şekilde başlamanıza yardımcı olması için bilgi ve kod örnekleri alın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.custom: seo-java-july2019, seo-java-august2019
ms.openlocfilehash: e875c74884fcea824ac29001aa5bcca9009e3dcb
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142756"
---
# <a name="quickstart-use-java-to-call-the-azure-text-analytics-cognitive-service"></a>Hızlı Başlangıç: Azure Metin Analizi bilişsel hizmetini çağırmak için Java kullanın
<a name="HOLTop"></a>

Bu makalede, Java ile [metin analizi API 'leri](//go.microsoft.com/fwlink/?LinkID=759711) kullanarak [](#SentimentAnalysis) [dilin nasıl algılanacağı](#Detect), yaklaşım analiziyle, [anahtar tümceciklerini ayıklamayla](#KeyPhraseExtraction)ve [bağlantılı varlıkların](#Entities) nasıl kullanılacağı gösterilir.

API'lerle ilgili teknik bilgiler için [API tanımları](//go.microsoft.com/fwlink/?LinkID=759346) sayfasını inceleyin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Ayrıca kayıt sırasında oluşturulan [uç nokta ve erişim anahtarı](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) değerlerine de sahip olmanız gerekir.

<a name="Detect"></a>

## <a name="detect-language"></a>Dili algıla

Dil Algılama API 'SI, [Dili algıla yöntemini](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)kullanarak bir metin belgesinin dilini algılar.

1. Ortam değişkenlerini `TEXT_ANALYTICS_SUBSCRIPTION_KEY` ve `TEXT_ANALYTICS_ENDPOINT` kaynağınızın Azure uç noktası ve abonelik anahtarı için oluşturun. Uygulamayı düzenleme başladıktan sonra bu ortam değişkenlerini oluşturduysanız, ortam değişkenlerine erişmek için kullandığınız düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.
1. En sevdiğiniz IDE (veya masaüstünüzdeki yeni klasör) içinde yeni bir Java projesi oluşturun. Adlı `DetectLanguage.java`bir sınıf oluşturun.
1. Aşağıda belirtilen kodu sınıfınıza ekleyin.
1. [Gson](https://github.com/google/gson) kitaplığının yüklü olduğundan emin olun.
1. Programı IDE 'de çalıştırın veya çalıştırmak için komut satırını kullanın (kod açıklamalarındaki yönergeler).

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (DetectLanguage.java), you can compile and run this program at
 * the command line as follows.
 *
 * Execute the following two commands to build and run (change gson version if needed):
 * javac DetectLanguage.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar DetectLanguage
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

class Document {
    public String id, text;

    public Document(String id, String text){
        this.id = id;
        this.text = text;
    }
}

class Documents {
    public List<Document> documents;

    public Documents() {
        this.documents = new ArrayList<Document>();
    }
    public void add(String id, String text) {
        this.documents.add (new Document (id, text));
    }
}

public class DetectLanguage {
    static String subscription_key_var;
    static String subscription_key;
    static String endpoint_var;
    static String endpoint;

    public static void Initialize () throws Exception {
        subscription_key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
        subscription_key = System.getenv(subscription_key_var);
        if (null == subscription_key) {
            throw new Exception ("Please set/export an environment variable named " + subscription_key_var);
        }

        endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
        endpoint = System.getenv(endpoint_var);
        if (null == endpoint) {
            throw new Exception ("Please set/export an environment variable named " + endpoint_var);
        }
    }

    static String path = "/text/analytics/v2.1/languages";
    
    public static String GetLanguage (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(endpoint+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscription_key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.write(encoded_text, 0, encoded_text.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            Initialize();

            Documents documents = new Documents ();
            documents.add ("1", "This is a document written in English.");
            documents.add ("2", "Este es un document escrito en Español.");
            documents.add ("3", "这是一个用中文写的文件");

            String response = GetLanguage (documents);
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

### <a name="language-detection-response"></a>Dil algılama yanıtı

Başarılı yanıt, aşağıdaki örnekte gösterildiği gibi JSON biçiminde döndürülür: 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}
```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Yaklaşımı analiz etme

Yaklaşım Analizi API'si, [Yaklaşım metodunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) kullanarak bir metin kaydı kümesinin yaklaşımını algılar. Aşağıdaki örnek, biri İngilizce diğeri İspanyolca olan iki belge puanlar.

1. Ortam değişkenlerini `TEXT_ANALYTICS_SUBSCRIPTION_KEY` ve `TEXT_ANALYTICS_ENDPOINT` kaynağınızın Azure uç noktası ve abonelik anahtarı için oluşturun. Uygulamayı düzenleme başladıktan sonra bu ortam değişkenlerini oluşturduysanız, ortam değişkenlerine erişmek için kullandığınız düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.
1. En sevdiğiniz IDE (veya masaüstünüzdeki yeni klasör) içinde yeni bir Java projesi oluşturun. Adında `GetSentiment.java`bir sınıf oluşturun.
1. Aşağıda belirtilen kodu sınıfınıza ekleyin.
1. [Gson](https://github.com/google/gson) kitaplığının yüklü olduğundan emin olun.
1. Programı IDE 'de çalıştırın veya çalıştırmak için komut satırını kullanın (kod açıklamalarındaki yönergeler).

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (GetSentiment.java), you can compile and run this program at
 * the command line as follows.
 *
 * Execute the following two commands to build and run (change gson version if needed):
 * javac GetSentiment.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GetSentiment
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

class Document {
    public String id, language, text;

    public Document(String id, String language, String text){
        this.id = id;
        this.language = language;
        this.text = text;
    }
}

class Documents {
    public List<Document> documents;

    public Documents() {
        this.documents = new ArrayList<Document>();
    }
    public void add(String id, String language, String text) {
        this.documents.add (new Document (id, language, text));
    }
}

public class GetSentiment {
    static String subscription_key_var;
    static String subscription_key;
    static String endpoint_var;
    static String endpoint;

    public static void Initialize () throws Exception {
        subscription_key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
        subscription_key = System.getenv(subscription_key_var);
        if (null == subscription_key) {
            throw new Exception ("Please set/export an environment variable named " + subscription_key_var);
        }

        endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
        endpoint = System.getenv(endpoint_var);
        if (null == endpoint) {
            throw new Exception ("Please set/export an environment variable named " + endpoint_var);
        }
    }

    static String path = "/text/analytics/v2.1/sentiment";
    
    public static String getTheSentiment (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(endpoint+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscription_key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.write(encoded_text, 0, encoded_text.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            Initialize();

            Documents documents = new Documents ();
            documents.add ("1", "en", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.");
            documents.add ("2", "es", "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.");

            String response = getTheSentiment (documents);
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

### <a name="sentiment-analysis-response"></a>Yaklaşım Analizi yanıtı

Sonuç, 0,0 ' e daha yakınsa 1,0 ve negatif bir değer alıyorsa pozitif olarak ölçülür.
Başarılı yanıt, aşağıdaki örnekte gösterildiği gibi JSON biçiminde döndürülür:

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Başlıca sözcük gruplarını ayıkla

Anahtar İfade Ayıklama API'si [Anahtar İfadeler metodunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) kullanarak bir metin belgesindeki anahtar ifadeleri ayıklar. Aşağıdaki örnekte hem İngilizce hem de İspanyolca belgelerin anahtar ifadeleri ayıklanır.

1. Ortam değişkenlerini `TEXT_ANALYTICS_SUBSCRIPTION_KEY` ve `TEXT_ANALYTICS_ENDPOINT` kaynağınızın Azure uç noktası ve abonelik anahtarı için oluşturun. Uygulamayı düzenleme başladıktan sonra bu ortam değişkenlerini oluşturduysanız, ortam değişkenlerine erişmek için kullandığınız düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.
1. En sevdiğiniz IDE (veya masaüstünüzdeki yeni klasör) içinde yeni bir Java projesi oluşturun. Adında `GetKeyPhrases.java`bir sınıf oluşturun.
1. Aşağıda belirtilen kodu sınıfınıza ekleyin.
1. [Gson](https://github.com/google/gson) kitaplığının yüklü olduğundan emin olun.
1. Programı IDE 'de çalıştırın veya çalıştırmak için komut satırını kullanın (kod açıklamalarındaki yönergeler).

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (GetKeyPhrases.java), you can compile and run this program at
 * the command line as follows.
 *
 * Execute the following two commands to build and run (change gson version if needed):
 * javac GetKeyPhrases.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GetKeyPhrases
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

class Document {
    public String id, language, text;

    public Document(String id, String language, String text){
        this.id = id;
        this.language = language;
        this.text = text;
    }
}

class Documents {
    public List<Document> documents;

    public Documents() {
        this.documents = new ArrayList<Document>();
    }
    public void add(String id, String language, String text) {
        this.documents.add (new Document (id, language, text));
    }
}

public class GetKeyPhrases {
    static String subscription_key_var;
    static String subscription_key;
    static String endpoint_var;
    static String endpoint;

    public static void Initialize () throws Exception {
        subscription_key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
        subscription_key = System.getenv(subscription_key_var);
        if (null == subscription_key) {
            throw new Exception ("Please set/export an environment variable named " + subscription_key_var);
        }

        endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
        endpoint = System.getenv(endpoint_var);
        if (null == endpoint) {
            throw new Exception ("Please set/export an environment variable named " + endpoint_var);
        }
    }

    static String path = "/text/analytics/v2.1/keyPhrases";
    
    public static String GetKeyPhrases (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(endpoint+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscription_key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.write(encoded_text, 0, encoded_text.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            Initialize();

            Documents documents = new Documents ();
            documents.add ("1", "en", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.");
            documents.add ("2", "es", "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.");
            documents.add ("3", "en", "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen.");

            String response = GetKeyPhrases (documents);
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

### <a name="key-phrase-extraction-response"></a>Anahtar tümceciği ayıklama yanıtı

Başarılı yanıt, aşağıdaki örnekte gösterildiği gibi JSON biçiminde döndürülür:

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```
<a name="Entities"></a>

## <a name="identify-entities"></a>Varlıkları tanımlama

Varlıklar API'si, [Varlıklar metodunu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634) kullanarak bir metin belgesindeki iyi bilinen varlıkları tanımlar. [Varlıklar](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) , "Birleşik Devletler" gibi metinden sözcükleri ayıklar, ardından bu kelimelerin türünü ve/veya Vimi bağlantısını verir. "Birleşik Devletler" `location`türü, `https://en.wikipedia.org/wiki/United_States`vikipe bağlantısı olduğunda.  Aşağıdaki örnekte İngilizce belgelerin varlıkları tanımlanır.

1. Ortam değişkenlerini `TEXT_ANALYTICS_SUBSCRIPTION_KEY` ve `TEXT_ANALYTICS_ENDPOINT` kaynağınızın Azure uç noktası ve abonelik anahtarı için oluşturun. Uygulamayı düzenleme başladıktan sonra bu ortam değişkenlerini oluşturduysanız, ortam değişkenlerine erişmek için kullandığınız düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.
1. En sevdiğiniz IDE (veya masaüstünüzdeki yeni klasör) içinde yeni bir Java projesi oluşturun. Adında `GetEntities.java`bir sınıf oluşturun.
1. Aşağıda belirtilen kodu sınıfınıza ekleyin.
1. [Gson](https://github.com/google/gson) kitaplığının yüklü olduğundan emin olun.
1. Programı IDE 'de çalıştırın veya çalıştırmak için komut satırını kullanın (kod açıklamalarındaki yönergeler).

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (GetEntities.java), you can compile and run this program at
 * the command line as follows.
 *
 * Execute the following two commands to build and run (change gson version if needed):
 * javac GetEntities.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GetEntities
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

class Document {
    public String id, language, text;

    public Document(String id, String language, String text){
        this.id = id;
        this.language = language;
        this.text = text;
    }
}

class Documents {
    public List<Document> documents;

    public Documents() {
        this.documents = new ArrayList<Document>();
    }
    public void add(String id, String language, String text) {
        this.documents.add (new Document (id, language, text));
    }
}

public class GetEntities {
    static String subscription_key_var;
    static String subscription_key;
    static String endpoint_var;
    static String endpoint;

    public static void Initialize () throws Exception {
        subscription_key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
        subscription_key = System.getenv(subscription_key_var);
        if (null == subscription_key) {
            throw new Exception ("Please set/export an environment variable named " + subscription_key_var);
        }

        endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
        endpoint = System.getenv(endpoint_var);
        if (null == endpoint) {
            throw new Exception ("Please set/export an environment variable named " + endpoint_var);
        }
    }

    static String path = "/text/analytics/v2.1/entities";
    
    public static String GetEntities (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(endpoint+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscription_key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.write(encoded_text, 0, encoded_text.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            Initialize();

            Documents documents = new Documents ();
            documents.add ("1", "en", "Microsoft is an It company.");

            String response = GetEntities (documents);
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

### <a name="entity-extraction-response"></a>Varlık ayıklama yanıtı

Başarılı yanıt, aşağıdaki örnekte gösterildiği gibi JSON biçiminde döndürülür:

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Power BI ile Metin Analizi](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Ayrıca bkz.

 [Metin Analizine genel bakış](../overview.md)  
 [Sık sorulan sorular (SSS)](../text-analytics-resource-faq.md)
