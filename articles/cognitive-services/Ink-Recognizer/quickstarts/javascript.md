---
title: 'Hızlı başlangıç: mürekkep tanıyıcı REST API ve Node. js ile dijital mürekkep tanıma'
titleSuffix: Azure Cognitive Services
description: Dijital mürekkep vuruşlarını tanımayı başlatmak için mürekkep tanıyıcı API 'sini kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 19626bd68ad82108b2ebaa823d196d0f22008e29
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996903"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Hızlı başlangıç: mürekkep tanıyıcı REST API ve JavaScript ile dijital mürekkep tanıma

Mürekkep tanıyıcı API 'sini dijital mürekkep vuruşları üzerinde kullanmaya başlamak için bu hızlı başlangıcı kullanın. Bu JavaScript uygulaması JSON biçimli mürekkep konturu verilerini içeren bir API isteği gönderir ve yanıtı görüntüler.

Bu uygulama JavaScript 'te yazıldığı ve Web tarayıcınızda çalıştırılsa da, API çoğu programlama dili ile uyumlu olan bir yenilenmiş Web hizmetidir.

Genellikle, API 'YI bir dijital mürekkep uygulamasının içinden çağıracağınızdan. Bu hızlı başlangıç, bir JSON dosyasından aşağıdaki el yazısı örneği için mürekkep konturu verileri gönderir.

![El yazısı metin görüntüsü](../media/handwriting-sample.jpg)

Bu hızlı başlangıç için kaynak kodu [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905)' da bulabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Bir Web tarayıcısı
- Bu hızlı başlangıç için örnek mürekkep konturu verileri [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json)'da bulunabilir.

### <a name="create-an-ink-recognizer-resource"></a>Mürekkep tanıyıcı kaynağı oluşturma

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Yeni uygulama oluşturma

1. En sevdiğiniz IDE veya düzenleyicide yeni bir `.html` dosyası oluşturun. Ardından daha sonra ekleyeceğiniz kod için temel HTML 'yi ekleyin.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. @No__t-0 etiketi içinde aşağıdaki HTML 'yi ekleyin:
    1. JSON isteğini ve yanıtını görüntülemek için iki metin alanı.
    2. Daha sonra oluşturulacak `recognizeInk()` işlevini çağırmak için bir düğme.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>Örnek JSON verilerini yükleme

1. @No__t-0 etiketi içinde sampleJson için bir değişken oluşturun. Ardından JSON dosyanızı seçebilmeniz için dosya Gezgini 'ni açan `openFile()` adlı bir JavaScript işlevi oluşturun. @No__t-0 düğmesine tıklandığında, bu işlevi çağırır ve dosyayı okumaya başlar.
2. Dosyayı zaman uyumsuz olarak işlemek için `FileReader` nesnesinin `onload()` işlevini kullanın. 
    1. Dosyadaki `\n` veya `\r` karakterlerini boş bir dize ile değiştirin. 
    2. Metni geçerli JSON 'a dönüştürmek için `JSON.parse()` kullanın
    3. Uygulamadaki `request` metin kutusunu güncelleştirin. JSON dizesini biçimlendirmek için `JSON.stringify()` kullanın. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Mürekkep tanıyıcı API 'sine istek gönderme

1. @No__t-0 etiketi içinde, `recognizeInk()` adlı bir işlev oluşturun. Bu işlev daha sonra API 'YI çağıracak ve sayfayı Yanıtla güncelleştirecek. Bu işlev içindeki aşağıdaki adımlardan kodu ekleyin. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Endpoint URL 'niz, abonelik anahtarınız ve örnek JSON için değişkenler oluşturun. Sonra, API isteğini göndermek için `XMLHttpRequest` nesnesi oluşturun. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. @No__t-0 nesnesi için return işlevini oluşturun. Bu işlev başarılı bir istekten API yanıtını ayrıştırır ve uygulamayı uygulamada görüntüler. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. İstek nesnesi için hata işlevini oluşturun. Bu işlev, hatayı konsola kaydeder. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. İstek nesnesinin `onreadystatechange` özelliği için bir işlev oluşturun. İstek nesnesinin hazırlık durumu değiştiğinde yukarıdaki geri dönüş ve hata işlevleri uygulanır.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. API isteği gönderin. @No__t-0 üstbilgisine abonelik anahtarınızı ekleyin ve `content-type` ' i `application/json` ' e ayarlayın
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Uygulamayı çalıştırma ve yanıtı görüntüleme

Bu uygulama, Web tarayıcınızda çalıştırılabilir. JSON biçiminde başarılı bir yanıt döndürülür. Ayrıca, [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json)'da JSON yanıtını bulabilirsiniz:

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [REST API başvurusu](https://go.microsoft.com/fwlink/?linkid=2089907)

Mürekkep tanıma API 'sinin dijital bir mürekkep uygulamasında nasıl çalıştığını görmek için GitHub 'da aşağıdaki örnek uygulamalara göz atın:
* [C# Evrensel Windows Platformu (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript web tarayıcı uygulaması](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java ve Android mobil uygulaması](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift ve iOS mobil uygulaması](https://go.microsoft.com/fwlink/?linkid=2089805)
