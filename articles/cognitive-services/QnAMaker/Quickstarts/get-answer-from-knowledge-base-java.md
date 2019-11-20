---
title: "Hızlı başlangıç: Bilgi Bankası 'ndan yanıt alın-REST, Java-Soru-Cevap Oluşturma"
titleSuffix: Azure Cognitive Services
description: Bu Java REST tabanlı hızlı başlangıç, Bilgi Bankası 'ndan programlama yoluyla yanıt alma konusunda size kılavuzluk eder.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 813eb624dfab0b88b0e907f86de77ebc0ae35f2c
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802929"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Hızlı başlangıç: Java ile Bilgi Bankası 'ndan bir soruya yanıt alın

Bu hızlı başlangıçta, yayımlanan Soru-Cevap Oluşturma bilgi bankasından programlı bir yanıt alma işlemi adım adım gösterilmektedir. Bilgi Bankası, SSS gibi [veri kaynaklarından](../Concepts/data-sources-supported.md) gelen sorular ve yanıtlar içerir. [Soru](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) soru-cevap oluşturma hizmetine gönderilir. [Yanıt](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) , en fazla tahmini yanıtı içerir. 

## <a name="prerequisites"></a>Önkoşullar

* [JDK SE](https://aka.ms/azure-jdks)  (Java Geliştirme Seti, Standart Sürüm)
* Bu örnek, HTTP bileşenlerinden Apache [http istemcisini](https://hc.apache.org/httpcomponents-client-ga/) kullanır. Projenize aşağıdaki Apache HTTP istemci kitaplıklarını eklemeniz gerekir: 
    * HttpClient-4.5.3. jar
    * httpcore-4.4.6. jar
    * Commons-logging-1.2. jar
* [Visual Studio Code](https://code.visualstudio.com/)
* [Soru-Cevap Oluşturma hizmetine](../How-To/set-up-qnamaker-service-azure.md) sahip olmanız gerekir. Anahtarınızı almak için Azure panonuzda Soru-Cevap Oluşturma kaynağınız için **kaynak yönetimi** altındaki **anahtarlar** ' ı seçin. 
* **Yayımlama** sayfası ayarları. Yayımlanmış bir bilgi tabanınız yoksa, boş bir Bilgi Bankası oluşturun ve ardından **Ayarlar** sayfasına bir Bilgi Bankası alın ve ardından yayımlayın. [Bu temel Bilgi Bankası](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)'nı indirip kullanabilirsiniz. 

    Yayımlama sayfası ayarları, POST yol değerini, ana bilgisayar değerini ve EndpointKey değerini içerir. 

    ![Yayımlama ayarları](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Bu hızlı başlangıç kodu [https://github.com/Azure-Samples/cognitive-services-qnamaker-java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/get-answer) deposudur. 

## <a name="create-a-java-file"></a>Java dosyası oluşturma

VSCode 'u açın ve `GetAnswer.java` adlı yeni bir dosya oluşturun ve aşağıdaki sınıfı ekleyin:

```Java
public class GetAnswer {

    public static void main(String[] args) 
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Gerekli bağımlılıkları ekleme

Bu hızlı başlangıçta HTTP istekleri için Apache sınıfları kullanılmaktadır. GetAnswer sınıfının üzerinde, `GetAnswer.java` dosyasının en üstünde, projeye gerekli bağımlılıkları ekleyin:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Gerekli sabitleri ekleme

@No__t-0 sınıfının en üstünde, Soru-Cevap Oluşturma erişmek için gereken sabitleri ekleyin. Bu değerler, Bilgi Bankası 'nı yayımladıktan sonra **Yayımla** sayfasında bulunur.  

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Soru göndermek için bir POST isteği ekleyin

Aşağıdaki kod, soruyu bilgi tabanına göndermek için Soru-Cevap Oluşturma API'si HTTPS isteği yapar ve yanıtı alır:

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

@No__t-0 üstbilgisinin değeri, `EndpointKey` dizesini içerir. 

[İstek](../how-to/metadata-generateanswer-usage.md#generateanswer-request) ve [Yanıt](../how-to/metadata-generateanswer-usage.md#generateanswer-response)hakkında daha fazla bilgi edinin.

## <a name="build-and-run-the-program"></a>Programı derleme ve çalıştırma

Komut satırından programı derleyin ve çalıştırın. İstek otomatik olarak Soru-Cevap Oluşturma API'si gönderilir, ardından konsol penceresine yazdırılır.

1. Dosyayı oluşturun:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Dosyayı çalıştırın:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Soru-Cevap Oluşturma (V4) REST API Başvurusu](https://go.microsoft.com/fwlink/?linkid=2092179)
