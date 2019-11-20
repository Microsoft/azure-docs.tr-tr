---
title: "Hızlı Başlangıç: Go kullanarak Metin Analizi API'si çağırın"
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
ms.openlocfilehash: 5c97648bd11a506d3c818584ed7d82d0a12d2e2c
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387507"
---
# <a name="quickstart-using-go-to-call-the-text-analytics-cognitive-service"></a>Hızlı Başlangıç: Metin Analizi bilişsel hizmeti çağırmak için go kullanma 
<a name="HOLTop"></a>

Bu makalede, Go ile [metin analizi API 'leri](//go.microsoft.com/fwlink/?LinkID=759711) kullanarak [dilin nasıl algılanacağı](#Detect), yaklaşım [analiziyle](#SentimentAnalysis), [anahtar tümceciklerini ayıklamada](#KeyPhraseExtraction)ve [bağlantılı varlıkların](#Entities) nasıl kullanılacağı gösterilir.

API'lerle ilgili teknik bilgiler için [API tanımları](//go.microsoft.com/fwlink/?LinkID=759346) sayfasını inceleyin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]


<a name="Detect"></a>

## <a name="detect-language"></a>Dili algılama

Dil Algılama API'si, [Dili Algıla metodunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) kullanarak bir metin belgesinin dilini algılar.

1. Ortam değişkenlerini `TEXT_ANALYTICS_SUBSCRIPTION_KEY` ve `TEXT_ANALYTICS_ENDPOINT` kaynağınızın Azure uç noktası ve abonelik anahtarı için oluşturun. Uygulamayı düzenleme başladıktan sonra bu ortam değişkenlerini oluşturduysanız, ortam değişkenlerine erişmek için kullandığınız düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.
1. Sık kullandığınız kod düzenleyicisinde yeni bir Go projesi oluşturun.
1. Aşağıda sağlanan kodu ekleyin.
1. Dosyayı '.go' uzantısıyla kaydedin.
1. Kök klasörünüzden bir go yüklü olan bilgisayarda bir komut istemi açın.
1. Dosyayı oluşturun, örneğin: `go build detect.go`.
1. Dosyayı çalıştırın, örneğin: `go run detect.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
    if "" == os.Getenv(subscriptionKeyVar) {
        log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
    }
    var subscriptionKey string = os.Getenv(subscriptionKeyVar)
    var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
    if "" == os.Getenv(endpointVar) {
        log.Fatal("Please set/export the environment variable " + endpointVar + ".")
    }
    var endpoint string = os.Getenv(endpointVar)

    const uriPath = "/text/analytics/v2.1/languages"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "text": "This is a document written in English."},
        {"id": "2", "text": "Este es un document escrito en Español."},
        {"id": "3", "text": "这是一个用中文写的文件"},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="detect-language-response"></a>Dili algılama yanıtı

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
1. Sık kullandığınız kod düzenleyicisinde yeni bir Go projesi oluşturun.
1. Aşağıda sağlanan kodu ekleyin.
1. Dosyayı '.go' uzantısıyla kaydedin.
1. Kök klasörünüzden bir go yüklü olan bilgisayarda bir komut istemi açın.
1. Dosyayı oluşturun, örneğin: `go build sentiment.go`.
1. Dosyayı çalıştırın, örneğin: `go run sentiment.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
    if "" == os.Getenv(subscriptionKeyVar) {
        log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
    }
    var subscriptionKey string = os.Getenv(subscriptionKeyVar)
    var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
    if "" == os.Getenv(endpointVar) {
        log.Fatal("Please set/export the environment variable " + endpointVar + ".")
    }
    var endpoint string = os.Getenv(endpointVar)

    const uriPath = "/text/analytics/v2.1/sentiment"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="analyze-sentiment-response"></a>Yaklaşımı analiz etme yanıtı

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

## <a name="extract-key-phrases"></a>Anahtar ifadeleri ayıklama

Anahtar İfade Ayıklama API'si [Anahtar İfadeler metodunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) kullanarak bir metin belgesindeki anahtar ifadeleri ayıklar. Aşağıdaki örnekte hem İngilizce hem de İspanyolca belgelerin anahtarı ifadeleri ayıklanır.

1. Ortam değişkenlerini `TEXT_ANALYTICS_SUBSCRIPTION_KEY` ve `TEXT_ANALYTICS_ENDPOINT` kaynağınızın Azure uç noktası ve abonelik anahtarı için oluşturun. Uygulamayı düzenleme başladıktan sonra bu ortam değişkenlerini oluşturduysanız, ortam değişkenlerine erişmek için kullandığınız düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.
1. Sık kullandığınız kod düzenleyicisinde yeni bir Go projesi oluşturun.
1. Aşağıda sağlanan kodu ekleyin.
1. Dosyayı '.go' uzantısıyla kaydedin.
1. Go yüklü bir bilgisayarda bir komut istemi açın.
1. Dosyayı oluşturun, örneğin: `go build key-phrases.go`.
1. Dosyayı çalıştırın, örneğin: `go run key-phrases.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
    if "" == os.Getenv(subscriptionKeyVar) {
        log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
    }
    var subscriptionKey string = os.Getenv(subscriptionKeyVar)
    var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
    if "" == os.Getenv(endpointVar) {
        log.Fatal("Please set/export the environment variable " + endpointVar + ".")
    }
    var endpoint string = os.Getenv(endpointVar)
    
    const uriPath = "/text/analytics/v2.1/keyPhrases"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
        {"id": "3", "language": "en", "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="extract-key-phrases-response"></a>Anahtar ifadeleri ayıklama yanıtı

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
1. Sık kullandığınız kod düzenleyicisinde yeni bir Go projesi oluşturun.
1. Aşağıda sağlanan kodu ekleyin.
1. Dosyayı '.go' uzantısıyla kaydedin.
1. Go yüklü bir bilgisayarda bir komut istemi açın.
1. Dosyayı oluşturun, örneğin: `go build entities.go`.
1. Dosyayı çalıştırın, örneğin: `go run entities.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
    if "" == os.Getenv(subscriptionKeyVar) {
        log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
    }
    var subscriptionKey string = os.Getenv(subscriptionKeyVar)
    var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
    if "" == os.Getenv(endpointVar) {
        log.Fatal("Please set/export the environment variable " + endpointVar + ".")
    }
    var endpoint string = os.Getenv(endpointVar)
    
    const uriPath = "/text/analytics/v2.1/entities"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "Microsoft is an It company."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="entity-extraction-response"></a>Varlık ayıklama yanıtı

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
