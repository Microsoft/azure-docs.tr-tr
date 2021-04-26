---
title: Bing Resim Arama API'si kullanarak GIF görüntülerini arama
titleSuffix: Azure Cognitive Services
description: Bing Resim Arama API'si, en ilgili. gif görüntülerinin tüm web genelinde arama yapmanızı sağlar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: d1fcac891db240def2e7bbdcb45b45caf7f49a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96341912"
---
# <a name="search-for-gif-images"></a>GIF görüntülerini arama 

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Resim Arama API'si, en ilgili. gif görüntülerinin tüm web genelinde arama yapmanızı sağlar.  Geliştiriciler, çeşitli konuşma senaryolarında etkileyici GIF 'leri tümleştirebilir. 

Aşağıdaki URL, animasyonlu. gif görüntüleri için bir sorgudur.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
[Q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) parametresi arama koşullarını belirtir.  Önceki sorgu, `animatedGif` [ImageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) filtre parametresini kullanmayı da belirtir.

Sonuçların örneklerini görmek için, bing.com aramak için aşağıdaki URL 'YI kullanın.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>Sorgu parametreleri

Sorgu parametreleri ve seçenekleri hakkında daha fazla bilgi için [resım arama API başvurusuna](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters)bakın. Örnek olarak [Java kullanarak animasyonlu GIF araması](#gifExample)yapın.

## <a name="tips-and-suggestions"></a>İpuçları ve öneriler

- [MaxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) ve [minfilesize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) parametrelerini belirtebilirsiniz. Dizinimizde bulunan GIF sayısının çoğunluğunun 2 MB 'ın altında olduğundan, maxFileSize = 2000000 ayarlamayı öneririz.  Bu ayrıca bant genişliği sorun olduğunda (örneğin, mobil hücresel senaryolarda) veri boyutunu denetlemeye yardımcı olur.
- Algılanan performansı artırmaya yardımcı olmak için, kaynak URL yüklemeden önce küçük resmi yükleyin.  
- Henüz bir Kullanıcı sorgunuz olmayan ilk çalıştırma veya giriş sayfası deneyiminde, [popüler RESIMLER API](trending-images.md)'sinden yardım almak için popüler GIF aramalarımızı kullanmayı deneyin.
- [SafeSearch](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) parametresinin üç ayarı vardır.  `strict`Seçeneği yetişkinlere yönelik içeriği engeller.
- Desteklenen dillerin ve konumların tam listesi için bkz. [Mkt](./language-support.md) .
- *Animatedgıhttps* yalnızca bir https adresinden gelen animasyonlu GIF görüntülerini döndürür. Güvenlik için birçok uygulamanın HTTPS üzerinden dış Web bağlantılarına bağlantısı olması gerekir. Örneğin, Apple App Store, iletim sırasında kullanıcı verilerini güvenli şekilde şifreleyen HTTPS üzerinden Web hizmetlerine bağlantı gerektirir.

<a name="gifExample"></a>

## <a name="example-search-for-animated-gif-using-java"></a>Java kullanarak animasyonlu GIF için örnek arama

Aşağıdaki URL, animasyonlu. gif görüntülerini arar: `q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
Aşağıdaki örnekte gösterildiği gibi, URL sorgusu [OCP-apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers) üst bilgisini gerektirir.

Aşağıdaki Java örneği isteği oluşturur ve gönderir.

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
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
        if (subscriptionKey.length() != 32) {
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

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

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>Sonuçlar
Kod, JSON nesneleri olarak aşağıdaki sonuçları alır:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>Sonraki adımlar
- [C# hızlı başlangıcı](quickstarts/csharp.md)
- [Öğretici Resim Arama tek sayfalı uygulama](tutorial-bing-image-search-single-page-app.md)