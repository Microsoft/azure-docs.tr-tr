---
title: Hızlı başlangıç-Java-Bing yerel Iş araması kullanarak API 'ye bir sorgu gönderme
titleSuffix: Azure Cognitive Services
description: Java 'daki istekleri Azure bilişsel hizmeti olan Bing yerel Iş Arama API 'sine göndermeye başlamak için bu hızlı başlangıcı kullanın.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.date: 05/12/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: 001fc80b30eaa736db27ba76384aaf273bdec903
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536656"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>Hızlı başlangıç: Java kullanarak Bing yerel Iş Arama API 'sine bir sorgu gönderme

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Azure bilişsel hizmeti olan Bing yerel Iş Arama API 'sine istek gönderme hakkında bilgi edinmek için bu hızlı başlangıcı kullanın. Bu basit uygulama Java 'da yazılsa da, API, HTTP istekleri yapıp JSON 'u ayrıştırmakla uyumlu olan herhangi bir programlama diliyle uyumlu olan bir yeniden takip eden Web hizmetidir.

Bu örnek uygulama, bir arama sorgusu için API 'den gelen yerel yanıt verilerini alır.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Bing Arama kaynağı oluşturun Bing arama bir kaynak oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.

## <a name="create-the-request"></a>İstek oluştur 

Aşağıdaki kod bir oluşturur `WebRequest` , erişim anahtarı üst bilgisini ayarlar ve *Bellevue içinde otel* için bir sorgu dizesi ekler.  Ardından isteği gönderir ve yanıtı JSON metnini içeren bir dizeye atar.

```java
    // construct URL of search request (endpoint + query string)
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
```

## <a name="run-the-complete-application"></a>Uygulamanın tamamını Çalıştır

Aşağıdaki kod, Bing arama altyapısından arama sonuçları döndürmek için Bing yerel Iş araması API 'sini kullanır. Şu adımları izleyerek bu kodu çalıştırın:
1. gson kitaplığını indirip yükleyin.
2. Tercih ettiğiniz IDE veya düzenleyicide bir Java projesi oluşturun.
3. Aşağıda sağlanan kodu ekleyin.
4. `subscriptionKey` değerini, aboneliğiniz için geçerli olan bir erişim anahtarı ile değiştirin.
5. Programı çalıştırın.

```java
package localSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (localSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac localSearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar localSearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class LocalSearchCls {

    // ***********************************************
    // *** Update or verify the following values. ***
    // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.cognitive.microsoft.com/bing";
        static String path = "/v7.0/localbusinesses/search";

        static String searchTerm = "Hotel in Bellevue";

        public static SearchResults SearchLocal (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&mkt=en-us");
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

            // receive JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();

            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);

            // extract Bing-related HTTP headers
            Map<String, List<String>> headers = connection.getHeaderFields();
            for (String header : headers.keySet()) {
                if (header == null) continue;      // may have null key
                if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                    results.relevantHeaders.put(header, headers.get(header).get(0));
                }
            }

            stream.close();
            return results;
        }

        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }

        public static void main (String[] args) {
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchLocal(searchTerm);

                System.out.println("\nRelevant HTTP Headers:\n");
                for (String header : result.relevantHeaders.keySet())
                    System.out.println(header + ": " + result.relevantHeaders.get(header));

                System.out.println("\nJSON Response:\n");
                System.out.println(prettify(result.jsonResponse));
            }
            catch (Exception e) {
                e.printStackTrace(System.out);
                System.exit(1);
            }
        }
    }

    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }

```

## <a name="next-steps"></a>Sonraki adımlar
- [Yerel Iş arama C# hızlı başlangıç](local-quickstart.md)
- [Yerel Iş arama Node.js hızlı başlangıç](local-search-node-quickstart.md)
- [Yerel Iş arama Python hızlı başlangıç](local-search-python-quickstart.md)
