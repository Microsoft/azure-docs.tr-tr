---
title: 'Hızlı başlangıç: Bilgi bankası oluşturma - REST, Java - Soru-Cevap Oluşturma'
titleSuffix: Azure Cognitive Services
description: Bu Java REST tabanlı hızlı başlangıçta Bilişsel Hizmetler API hesabınızdaki Azure Panonuzda görünecek olan örnek bir Soru-Cevap Oluşturma bilgi bankasını programlamayla oluşturma adımları gösterilir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: c5a1af0b26f30cac39a76c4480848fbe1d75477b
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803116"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Hızlı başlangıç: Java kullanarak Soru-Cevap Oluşturma’da bilgi bankası oluşturma

Bu hızlı başlangıçta program aracılığıyla örnek bir Soru-Cevap Oluşturma bilgi bankası (KB) oluşturma adımları gösterilmektedir. Soru-Cevap Oluşturma, [veri kaynaklarından](../Concepts/data-sources-supported.md) ve SSS gibi yarı yapılandırılmış içerikten soru ve cevapları otomatik olarak ayıklar. JSON ile tanımlanan bilgi bankası modeli API isteğinin gövdesinde gönderilir.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Go 1.10.1](https://golang.org/dl/)
* [Soru-Cevap Oluşturma hizmetine](../How-To/set-up-qnamaker-service-azure.md) sahip olmanız gerekir. Anahtarınızı ve uç noktanızı (kaynak adını da içerir) almak için Azure portal kaynağınız için **hızlı başlangıç** ' ı seçin.

[Örnek kod](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) , Java ile soru-cevap oluşturma için GitHub deposunda kullanılabilir.

## <a name="create-a-knowledge-base-file"></a>Bilgi bankası dosyası oluşturma

`CreateKB.java` adlı bir dosya oluşturun

## <a name="add-the-required-dependencies"></a>Gerekli bağımlılıkları ekleme

Aşağıdaki satırları `CreateKB.java` adlı dosyanın en üstüne ekleyerek projeye gerekli bağımlılıkları dahil edin:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Gerekli sabitleri ekleme
Yukarıdaki gerekli bağımlılıklardan sonra Soru-Cevap Oluşturma hizmetine erişmek için `CreateKB` sınıfına gerekli sabitleri ekleyin. 

[Soru-Cevap Oluşturma hizmetine](../How-To/set-up-qnamaker-service-azure.md) sahip olmanız gerekir. Anahtarınızı ve kaynak adınızı almak için Soru-Cevap Oluşturma kaynağınız için Azure portal **hızlı başlangıç** ' ı seçin. 

Sınıfı bitirmek için küme ayracı eklemeniz gerekmez; bu, hızlı başlangıcın sonundaki en son kod parçacığındadır.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>KB modeli tanım sınıfları ekleme
Sabitlerden sonra model tanımı nesnesini JSON’da seri hale getirmek için `CreateKB` sınıfına aşağıdaki sınıf ve işlevleri ekleyin.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Destekleyici işlevleri ekleme

Bir sonraki adımda `CreateKB` sınıfının içine aşağıdaki destekleyici işlevleri ekleyin.

1. JSON sonucunu okunabilir biçimde yazdırmak için aşağıdaki işlevi ekleyin:

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. HTTP yanıtını yönetmek için aşağıdaki sınıfı ekleyin:

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. Soru-Cevap Oluşturma API'lerine POST isteği göndermek için aşağıdaki yöntemi ekleyin. `Ocp-Apim-Subscription-Key`, Soru-Cevap Oluşturma hizmeti anahtarıdır ve kimlik doğrulaması için kullanılır.

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. Soru-Cevap Oluşturma API'lerine GET isteği göndermek için aşağıdaki yöntemi ekleyin.

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>KB oluşturmak için yöntem ekleme
Post yöntemine çağırarak KB’yi oluşturmak için aşağıdaki yöntemi ekleyin.

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

Bu API çağrısı, işlem kimliğini içeren bir JSON yanıtı döndürür. İşlem kimliğini KB'nin başarıyla oluşturulup oluşturulmadığını belirlemek için kullanın.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>Durumu almak için bir yöntem ekleme
Oluşturma durumunu denetlemek için aşağıdaki yöntemi ekleyin.

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

Başarılı veya başarısız bir sonuç alana kadar çağrıyı tekrarlayın:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>Main yöntemi ekleme
Main yöntemi KB'yi oluşturur, sonra da durum için yoklama yapar. İşlem KIMLIĞI, yanıt gönder üst bilgisi alan **konumunda**döndürülür, ardından Get isteğindeki yolun bir parçası olarak kullanılır. @No__t-0 döngüsü tamamlandıysa durumu yeniden dener.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>Programı derleme ve çalıştırma

1. Gson kitaplığının `./libs` dizininde bulunduğundan emin olun. Komut satırında `CreateKB.java` dosyasını derleyin:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Programı çalıştırmak için aşağıdaki komutu bir komut satırına yazın. Soru-Cevap Oluşturma API'sine KB oluşturma isteği gönderir ve 30 saniyede bir sonucu yoklar. Her yanıt konsol penceresine yazdırılır.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

Bilgi bankanız oluşturulduktan sonra Soru-Cevap Oluşturma Portalı’nızdaki [Bilgi bankalarım](https://www.qnamaker.ai/Home/MyServices) sayfasından görüntüleyebilirsiniz.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Soru-Cevap Oluşturma (V4) REST API Başvurusu](https://go.microsoft.com/fwlink/?linkid=2092179)
