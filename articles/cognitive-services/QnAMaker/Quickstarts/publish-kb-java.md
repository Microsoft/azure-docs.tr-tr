---
title: 'Hızlı başlangıç: yayımlama bilgi tabanı, REST, Java-Soru-Cevap Oluşturma'
titleSuffix: Azure Cognitive Services
description: Bu Java REST tabanlı hızlı başlangıç bilgi tabanınızı yayımlar ve uygulamanızda veya sohbet bot 'ta çağrılabilecek bir uç nokta oluşturur.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: diberry
ms.openlocfilehash: 569846755ea363cd3816a22469cd7f05d8c42f4c
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794072"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Hızlı başlangıç: Java kullanarak Soru-Cevap Oluşturma’da bilgi bankası yayımlama

Bu REST tabanlı hızlı başlangıç, bilgi bankanızı (KB) programlı bir şekilde yayımlama konusunda size yol gösterir. Yayımlama, bilgi tabanının en son sürümünü adanmış bir Azure Bilişsel Arama dizinine gönderir ve uygulamanızda veya sohbet bot 'ta çağrılabilecek bir uç nokta oluşturur.

Bu hızlı başlangıç şu Soru-Cevap Oluşturma API'lerini çağırır:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish): Bu API için istek gövdesinde herhangi bir bilgi iletilmesi gerekmez.

## <a name="prerequisites"></a>Ön koşullar

* [JDK SE](https://aka.ms/azure-jdks)  (Java Geliştirme Seti, Standart Sürüm)
* Bu örnek, HTTP bileşenlerinden Apache [http istemcisini](https://hc.apache.org/httpcomponents-client-ga/) kullanır. Projenize aşağıdaki Apache HTTP istemci kitaplıklarını eklemeniz gerekir: 
    * HttpClient-4.5.3. jar
    * httpcore-4.4.6. jar
    * Commons-logging-1.2. jar
* [Visual Studio Code](https://code.visualstudio.com/)
* [Soru-Cevap Oluşturma hizmetine](../How-To/set-up-qnamaker-service-azure.md) sahip olmanız gerekir. Anahtarınızı ve uç noktanızı (kaynak adını da içerir) almak için Azure portal kaynağınız için **hızlı başlangıç** ' ı seçin.
* Soru-Cevap Oluşturma bilgi bankası (KB) kimliği aşağıda gösterildiği gibi URL'nin kbid sorgu dizesi bölümünde bulunur.

    ![Soru-Cevap Oluşturma bilgi bankası kimliği](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Henüz bir bilgi bankanız yoksa, bu hızlı başlangıçta kullanmak için bir örneğini oluşturabilirsiniz: [Yeni bilgi bankası oluşturma](create-new-kb-csharp.md).

> [!NOTE] 
> Tam çözüm dosyası (ler) [ **Azure-Samples/bilişsel hizmetler-qnamaker-Java** GitHub deposundan](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base)alınabilir.

## <a name="create-a-java-file"></a>Java dosyası oluşturma

VSCode 'u açın ve `PublishKB.java`adlı yeni bir dosya oluşturun.

## <a name="add-the-required-dependencies"></a>Gerekli bağımlılıkları ekleme

`PublishKB.java`en üstünde, sınıfının üzerine gerekli bağımlılıkları eklemek için aşağıdaki satırları ekleyin:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Main yöntemiyle PublishKB sınıfı oluşturma

Bağımlılıklardan sonra aşağıdaki sınıfı ekleyin:

```Go
public class PublishKB {

    public static void main(String[] args) 
    {
    }
}
```

## <a name="add-required-constants"></a>Gerekli sabitleri ekleme

**Main** yönteminde soru-cevap oluşturma erişmek için gereken sabitleri ekleyin. Değerleri kendi değerlerinizle değiştirin.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Bilgi Bankası yayınlamak için POST isteği ekleyin

Gerekli sabitlerden sonra, bir Bilgi Bankası yayımlamak ve yanıtı almak için Soru-Cevap Oluşturma API'si HTTPS isteği yapan aşağıdaki kodu ekleyin:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

Yayımlama başarılı olursa API çağrısı boş yanıt gövdesiyle 204 durumunu döndürür. Kod 204 yanıtları için içerik ekler.

Diğer yanıtlarda döndürülen yanıt değiştirilmez.

## <a name="build-and-run-the-program"></a>Programı derleme ve çalıştırma

Komut satırından programı derleyin ve çalıştırın. İstek otomatik olarak Soru-Cevap Oluşturma API'si gönderilir, ardından konsol penceresine yazdırılır.

1. Dosyayı oluşturun:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Dosyayı çalıştırın:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Sonraki adımlar

Bilgi Bankası yayımlandıktan sonra, [bir yanıt oluşturmak için uç nokta URL 'sine](../Tutorials/create-publish-answer.md#generating-an-answer)ihtiyacınız vardır.  

> [!div class="nextstepaction"]
> [Soru-Cevap Oluşturma (V4) REST API Başvurusu](https://go.microsoft.com/fwlink/?linkid=2092179)
