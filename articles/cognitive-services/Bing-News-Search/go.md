---
title: 'Hızlı başlangıç: Bing Haber Arama REST API ve Go kullanarak haber alın'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta Bing Haber Arama API'si çağırmak için go dili kullanılır. Sonuçlar, sorgu dizesi tarafından tanımlanan haber kaynaklarının adlarını ve URL 'Lerini içerir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 2/21/2019
ms.author: aahi
ms.openlocfilehash: c3d18852086e202d9f818f2cac2c90fa4f464211
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74110810"
---
# <a name="quickstart-get-news-results-using-the-bing-news-search-rest-api-and-go"></a>Hızlı başlangıç: Bing Haber Arama REST API kullanarak haber sonuçları alın ve git

Bu hızlı başlangıçta Bing Haber Arama API'si çağırmak için go dili kullanılır. Sonuçlar, sorgu dizesi tarafından tanımlanan haber kaynaklarının adlarını ve URL 'Lerini içerir.

## <a name="prerequisites"></a>Önkoşullar
* [Go ikililerini](https://golang.org/dl/) yükler
* Sonuçları göstermek için BT 'nin yazıcı için go-Spew kitaplığını yükler
    * Bu Libarary 'ı yüklensin: `$ go get -u https://github.com/davecgh/go-spew`

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-import-libraries"></a>Proje oluşturma ve kitaplıkları içeri aktarma

IDE veya Düzenleyicinizde yeni bir go projesi oluşturun. Ardından istekler için `net/http` içeri aktarın, yanıtı okumak için `ioutil` ve sonuçların JSON metnini işlemek için `encoding/json`. JSON ayrıştırmak için go-Spew kitaplığı gereklidir. 

```
package main

import (
    "fmt"
    "net/http"
    "io/ioutil"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="create-a-struct-to-format-the-news-search-results"></a>Haber arama sonuçlarını biçimlendirmek için bir yapı oluşturma

`NewsAnswer` yapısı, yanıtta sağlanan verileri biçimlendirir. JSON yanıtı çok düzeyli ve oldukça karmaşıktır.  Aşağıdaki uygulama temel bilgileri içerir.

```
// This struct formats the answer provided by the Bing News Search API.
type NewsAnswer struct {
    ReadLink       string `json: "readLink"` 
    QueryContext   struct {
        OriginalQuery   string   `json: "originalQuery"`
        AdultIntent     bool        `json: "adultIntent"`
    } `json: "queryContext"`
    TotalEstimatedMatches   int  `json: totalEstimatedMatches"` 
    Sort  []struct {
        Name    string  `json: "name"`
        ID       string    `json: "id"`
        IsSelected       bool  `json: "isSelected"`
        URL      string   `json: "url"`
    }  `json: "sort"` 
    Value   []struct   {
        Name     string   `json: "name"`
        URL   string    `json: "url"`
        Image   struct   {
            Thumbnail   struct  {
                ContentUrl  string  `json: "thumbnail"`
                Width   int  `json: "width"`
                Height  int   `json: "height"`
            } `json: "thumbnail"` 
            Description  string  `json: "description"`
            Provider  []struct   {
                Type   string    `json: "_type"`
                Name  string     `json: "name"`
            } `json: "provider"` 
            DatePublished   string   `json: "datePublished"`
        } `json: "image"` 
    } `json: "value"` 
}

```

## <a name="declare-the-main-function-and-define-variables"></a>Ana işlevi ve değişkenleri tanımlama  

Aşağıdaki kod Main işlevini bildirir ve gerekli değişkenleri atar. Uç noktasının geçerli olduğunu doğrulayın ve `token` değerini Azure hesabınızdan geçerli bir abonelik anahtarı ile değiştirin.

```
func main() {
    // Verify the endpoint URI and replace the token string with a valid subscription key.  
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

    // Declare a new GET request.
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }

    // The rest of the code in this example goes here in the main function.
}
```

## <a name="query-and-header"></a>Sorgu ve üst bilgi

Sorgu dizesini ve erişim anahtarı üst bilgisini ekleyin

```
// Add the query to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the subscription-key header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

```

## <a name="get-request"></a>İsteği al

İstemcisini oluşturun ve get isteğini gönderin. 

```
// Instantiate a client.  
client := new(http.Client)

// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

```

## <a name="send-the-request"></a>İsteği gönder

`ioutil`kullanarak isteği gönderin ve sonuçları okuyun.

```
resp, err := client.Do(req)
    if err != nil {
        panic(err)
}

// Close the connection.    
defer resp.Body.Close()

// Read the results
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}

```

## <a name="handle-the-response"></a>Yanıtı işleme

`Unmarshall` işlevi, Haber Arama API tarafından döndürülen JSON metnindeki bilgileri ayıklar.  Ardından, `go-spew` oldukça yazıcısını kullanarak sonuçlardan düğümleri görüntüleyebilirsiniz.

```
// Create a new answer object 
ans := new(NewsAnswer)
err = json.Unmarshal(body, &ans)
if err != nil {
    fmt.Println(err)
}
    
fmt.Print("Output of BingAnswer: \r\n\r\n")
    
// Iterate over search results and print the result name and URL.
for _, result := range ans.Value{
spew.Dump(result.Name, result.URL)
}

```

## <a name="results"></a>Sonuçlar

Sonuçlar her sonucun adını ve URL 'sini içerir.

```
(string) (len=91) "Cognitive Services Market: Global Industry Analysis and Opportunity Assessment, 2019 - 2025"
(string) (len=142) "https://www.marketwatch.com/press-release/cognitive-services-market-global-industry-analysis-and-opportunity-assessment-2019---2025-2019-02-21"
(string) (len=104) "Microsoft calls for greater collaboration to harness the power of AI to empower people with disabilities"
(string) (len=115) "https://indiaeducationdiary.in/microsoft-calls-greater-collaboration-harness-power-ai-empower-people-disabilities-2/"
(string) (len=70) "Microsoft 'Intelligent Cloud Hub' to build AI-ready workforce in India"
(string) (len=139) "https://cio.economictimes.indiatimes.com/news/cloud-computing/microsoft-intelligent-cloud-hub-to-build-ai-ready-workforce-in-india/67187807"
(string) (len=81) "Skills shortage is stopping many Asian companies from embracing A.I., study shows"
(string) (len=106) "https://www.cnbc.com/2019/02/20/microsoft-idc-study-skills-shortages-stopping-companies-from-using-ai.html"
(string) (len=143) "Cognitive Computing in Healthcare Market Emerging Top Key Vendors- Apixio, MedWhat, Healthcare X.0, Apple, Google, Microsoft, and IBM 2017-2025"
(string) (len=40) "http://www.digitaljournal.com/pr/4163064"
(string) (len=49) "Microsoft launches AI skills initiative in Brazil"
(string) (len=80) "https://www.zdnet.com/article/microsoft-launches-ai-skills-initiative-in-brazil/"
(string) (len=45) "Kuwait's CITRA and Microsoft host AI OpenHack"
(string) (len=70) "http://www.itp.net/618639-kuwaits-citra-and-microsoft-host-ai-openhack"
(string) (len=51) "CITRA and Microsoft collaborate to host AI workshop"
(string) (len=123) "https://www.zawya.com/mena/en/press-releases/story/CITRA_and_Microsoft_collaborate_to_host_AI_workshop-ZAWYA20190212105751/"

```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bing Haber Arama nedir?](search-the-web.md)
