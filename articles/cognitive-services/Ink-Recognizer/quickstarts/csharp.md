---
title: 'Hızlı başlangıç: mürekkep tanıyıcı REST API ile dijital mürekkebi tanıma veC#'
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
ms.openlocfilehash: 6ef58d1444eb0ec7b2b8891f944b428a613edf3a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515620"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Hızlı başlangıç: mürekkep tanıyıcı REST API ile dijital mürekkebi tanıma veC#

Mürekkep tanıyıcı API 'sine dijital mürekkep vuruşları göndermeye başlamak için bu hızlı başlangıcı kullanın. Bu C# uygulama JSON biçimli mürekkep konturu verilerini IÇEREN bir API isteği gönderir ve yanıtı alır.

Bu uygulama içine C#YAZıLıRKEN, API çoğu programlama dili ile uyumlu olan bir yeniden sorun Web hizmetidir.

Genellikle, API 'YI bir dijital mürekkep uygulamasının içinden çağıracağınızdan. Bu hızlı başlangıç, bir JSON dosyasından aşağıdaki el yazısı örneği için mürekkep konturu verileri gönderir.

![El yazısı metin görüntüsü](../media/handwriting-sample.jpg)

Bu hızlı başlangıç için kaynak kodu [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502)' da bulabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)’nin herhangi bir sürümü.
- [Newtonsoft. JSON](https://www.newtonsoft.com/json)
    - Newtonsoft. json ' i Visual Studio 'da bir NuGet paketi olarak yüklemek için:
        1. **Çözüm Yöneticisi** 'ne sağ tıklayın
        2. **NuGet Paketlerini Yönet...** öğesine tıklayın.
        3. @No__t_0 ara ve paketi yüklensin
- Linux/MacOS kullanıyorsanız, bu uygulama [mono](https://www.mono-project.com/)kullanılarak çalıştırılabilir.

- Bu hızlı başlangıç için örnek mürekkep konturu verileri [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json)'da bulunabilir.

### <a name="create-an-ink-recognizer-resource"></a>Mürekkep tanıyıcı kaynağı oluşturma

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Yeni uygulama oluşturma

1. Visual Studio 'da yeni bir konsol çözümü oluşturun ve aşağıdaki paketleri ekleyin. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Abonelik anahtarınız ve uç noktanız için değişkenler oluşturun ve örnek JSON dosyası. Uç nokta daha sonra, API 'ye erişmek için `inkRecognitionUrl` ile birleştirilir. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>İstek göndermek için bir işlev oluşturma

1. Yukarıda oluşturulan değişkenleri alan `Request` adlı yeni bir zaman uyumsuz işlev oluşturun.

2. İstemcinin güvenlik protokolünü ve üst bilgi bilgilerini bir `HttpClient` nesnesi kullanarak ayarlayın. Abonelik anahtarınızı `Ocp-Apim-Subscription-Key` üstbilgisine eklediğinizden emin olun. Ardından istek için `StringContent` nesnesi oluşturun.
 
3. İsteği `PutAsync()` ile gönderin. İstek başarılı olursa yanıtı döndürün.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Mürekkep tanıma isteği gönder

1. @No__t_0 adlı yeni bir işlev oluşturun. İsteği oluşturun ve uç noktanız, abonelik anahtarınız, API URL 'SI ve dijital mürekkep konturu verileri ile `Request()` işlevini çağırarak gönderin.

2. JSON nesnesinin serisini kaldırma ve konsola yazma. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Dijital mürekkep verilerinizi yükleme

Mürekkep verileri JSON dosyasını yüklemek için `LoadJson()` adlı bir işlev oluşturun. Bir `StreamReader` ve `JsonTextReader` kullanarak `JObject` oluşturun ve döndürün.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>API isteğini gönder

1. Uygulamanızın ana yönteminde, JSON verilerinizi yukarıda oluşturulan işlevle yükleyin. 

2. Yukarıda oluşturulan `recognizeInk()` işlevini çağırın. Uygulamayı çalıştırdıktan sonra konsol penceresini açık tutmak için `System.Console.ReadKey()` kullanın.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Uygulamayı çalıştırma ve yanıtı görüntüleme

Uygulamayı çalıştırın. JSON biçiminde başarılı bir yanıt döndürülür. Ayrıca, [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json)'da JSON yanıtını bulabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [REST API başvurusu](https://go.microsoft.com/fwlink/?linkid=2089907)


Mürekkep tanıma API 'sinin dijital bir mürekkep uygulamasında nasıl çalıştığını görmek için GitHub 'da aşağıdaki örnek uygulamalara göz atın:
* [C# Evrensel Windows Platformu (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript web tarayıcı uygulaması](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java ve Android mobil uygulaması](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift ve iOS mobil uygulaması](https://go.microsoft.com/fwlink/?linkid=2089805)
