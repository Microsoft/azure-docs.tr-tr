---
title: 'Hızlı başlangıç: küçük resim oluşturma-REST, git'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Go ile Görüntü İşleme API'si kullanarak görüntüden bir küçük resim oluşturursunuz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ed79686a8c2c649852c4652a3e334b1731491de9
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177243"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-with-go"></a>Hızlı başlangıç: go ile Görüntü İşleme REST API kullanarak küçük resim oluşturma

Bu hızlı başlangıçta, Görüntü İşleme REST API kullanarak görüntüden bir küçük resim oluşturursunuz. Giriş görüntüsünden en boy oranına göre farklılık gösterebilen yükseklik ve genişlik belirtirsiniz. Görüntü İşleme, ilgilendiğiniz alanı saptamak ve bu bölgeye göre kırpma koordinatları oluşturmak için akıllı kırpma kullanır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- [Go](https://golang.org/dl/) yüklü olmalıdır.
- Görüntü İşleme için bir abonelik anahtarınız olması gerekir. Deneme bilişsel [Hizmetler](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)'den ücretsiz bir deneme anahtarı edinebilirsiniz. Ya da Görüntü İşleme abone olmak ve anahtarınızı almak için bilişsel [Hizmetler oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ' daki yönergeleri izleyin. Ardından, sırasıyla `COMPUTER_VISION_SUBSCRIPTION_KEY` ve `COMPUTER_VISION_ENDPOINT` adlı anahtar ve hizmet uç noktası dizesi için [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .

## <a name="create-and-run-the-sample"></a>Örnek oluşturma ve çalıştırma

Örneği oluşturmak ve çalıştırmak için aşağıdaki adımları uygulayın:

1. Aşağıdaki kodu bir metin düzenleyicisine kopyalayın.
1. İsteğe bağlı olarak, `imageUrl` değerini, küçük resim oluşturmak istediğiniz farklı bir görüntünün URL 'siyle değiştirin.
1. Kodu `.go` uzantılı bir dosya olarak kaydedin. Örneğin, `get-thumbnail.go`.
1. Bir komut istemi penceresi açın.
1. İstem sırasında, paketi dosyadan derlemek için `go build` komutunu çalıştırın. Örneğin, `go build get-thumbnail.go`.
1. İstemde, derlenen paketi çalıştırın. Örneğin, `get-thumbnail`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Add your Computer Vision subscription key and endpoint to your environment variables.
    subscriptionKey := os.Getenv("COMPUTER_VISION_SUBSCRIPTION_KEY")
    if (subscriptionKey == "") {
        log.Fatal("\n\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n" +
            "**Restart your shell or IDE for changes to take effect.**\n")

    endpoint := os.Getenv("COMPUTER_VISION_ENDPOINT")
    if ("" == endpoint) {
        log.Fatal("\n\nSet the COMPUTER_VISION_ENDPOINT environment variable.\n" +
            "**Restart your shell or IDE for changes to take effect.**")
    }
    const uriBase = endpoint + "vision/v2.1/generateThumbnail"
    const imageUrl =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the HTTP client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the POST request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the JSON data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the JSON result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="examine-the-response"></a>Yanıtı inceleyin

Başarılı bir yanıt, küçük resim görüntüsü ikili verilerini içerir. İstek başarısız olursa, yanıt neyin yanlış olduğunu belirlemenize yardımcı olmak için bir hata kodu ve bir ileti içerir.

## <a name="next-steps"></a>Sonraki adımlar

Bir görüntüyü çözümlemek, ünlüler ve yer işaretlerini saptamak, küçük resim oluşturmak ve yazdırılmış ve el yazısı metin ayıklamak için Görüntü İşleme API'si bulun. Görüntü İşleme API'si hızlı bir şekilde denemek için, [Açık API test konsolunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)deneyin.

> [!div class="nextstepaction"]
> [Görüntü İşleme API'si keşfet](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
