---
title: 'Hızlı başlangıç: Bilgi bankası oluşturma - REST, Python - Soru-Cevap Oluşturma'
titleSuffix: Azure Cognitive Services
description: Bu Python REST tabanlı hızlı başlangıçta Bilişsel Hizmetler API hesabınızdaki Azure Panonuzda görünecek olan örnek bir Soru-Cevap Oluşturma bilgi bankasını programlamayla oluşturma adımları gösterilmektedir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 9114d491be1ae11623264c3beaf7c26f1fa143de
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803134"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Hızlı başlangıç: Python kullanarak Soru-Cevap Oluşturma’da bilgi bankası oluşturma

Bu hızlı başlangıçta program aracılığıyla örnek bir Soru-Cevap Oluşturma bilgi bankası (KB) oluşturma ve yayımlama adımları gösterilir. Soru-Cevap Oluşturma, [veri kaynaklarından](../Concepts/data-sources-supported.md) ve SSS gibi yarı yapılandırılmış içerikten soru ve cevapları otomatik olarak ayıklar. JSON ile tanımlanan bilgi bankası modeli API isteğinin gövdesinde gönderilir. 

Bu hızlı başlangıç şu Soru-Cevap Oluşturma API'lerini çağırır:
* [KB Oluşturma](https://go.microsoft.com/fwlink/?linkid=2092179)
* [İşlem Ayrıntılarını Alma](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Python 3.7](https://www.python.org/downloads/)
* [Soru-Cevap Oluşturma hizmetine](../How-To/set-up-qnamaker-service-azure.md) sahip olmanız gerekir. Anahtarınızı ve uç noktanızı (kaynak adını da içerir) almak için Azure portal kaynağınız için **hızlı başlangıç** ' ı seçin. 

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="create-a-knowledge-base-python-file"></a>Bilgi bankası Python dosyası oluşturma

`create-new-knowledge-base-3x.py` adlı bir dosya oluşturun.

## <a name="add-the-required-dependencies"></a>Gerekli bağımlılıkları ekleme

Aşağıdaki satırları `create-new-knowledge-base-3x.py` adlı dosyanın en üstüne ekleyerek projeye gerekli bağımlılıkları dahil edin:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Gerekli sabitleri ekleme
Yukarıdaki gerekli bağımlılıklardan sonra Soru-Cevap Oluşturma hizmetine erişmek için gerekli sabitleri ekleyin. @No__t-0 ve `<your-resource-name>` değerini kendi Soru-Cevap Oluşturma anahtarınızla ve kaynak adınızla değiştirin.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=5-13 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>KB modeli tanımını ekleme

Sabitlerden sonra aşağıdaki KB modeli tanımını ekleyin. Model tanımdan sonra bir dizeye dönüştürülür.

[!code-python[Add the KB model definition](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=15-41 "Add the KB model definition")]

## <a name="add-supporting-function"></a>Destekleyici işlevi ekleme

JSON sonucunu okunabilir biçimde yazdırmak için aşağıdaki işlevi ekleyin:

[!code-python[Add supporting function](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=43-45 "Add supporting function")]

## <a name="add-function-to-create-kb"></a>KB oluşturma işlevini ekleme

Bilgi bankasını oluşturma amacıyla bir HTTP POST isteğinde bulunmak için aşağıdaki işlevi ekleyin. Bu API çağrısı, **Location** üst bilgi alanında işlem kimliğini içeren bir JSON yanıtı döndürür. İşlem kimliğini KB'nin başarıyla oluşturulup oluşturulmadığını belirlemek için kullanın. `Ocp-Apim-Subscription-Key`, Soru-Cevap Oluşturma hizmeti anahtarıdır ve kimlik doğrulaması için kullanılır. 

[!code-python[Add function to create KB](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=48-59 "Add function to create KB")]

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

## <a name="add-function-to-check-creation-status"></a>Oluşturma durumunu denetlemek için işlev ekleme

Aşağıdaki işlev, URL yolunun sonundaki işlem kimliğini göndererek oluşturma durumunu denetler. `check_status` çağrısı, ana _while_ döngüsünün içinde yer alır.

[!code-python[Add function to check creation status](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=61-67 "Add function to check creation status")]

Bu API çağrısı, işlem durumunu içeren bir JSON yanıtı döndürür: 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

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

## <a name="add-main-code-block"></a>Ana kod bloğunu ekleme
Aşağıdaki döngü, oluşturma işlemi tamamlanana kadar düzenli aralıklarla işlemin durumunu yoklar. 

[!code-python[Add main code block](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=70-96 "Add main code block")]

## <a name="build-and-run-the-program"></a>Programı derleme ve çalıştırma

Programı çalıştırmak için aşağıdaki komutu bir komut satırına yazın. Soru-Cevap Oluşturma API'sine KB oluşturma isteği gönderir ve 30 saniyede bir sonucu yoklar. Her yanıt konsol penceresine yazdırılır.

```bash
python create-new-knowledge-base-3x.py
```

Bilgi bankanız oluşturulduktan sonra Soru-Cevap Oluşturma Portalı’nızdaki [Bilgi bankalarım](https://www.qnamaker.ai/Home/MyServices) sayfasından görüntüleyebilirsiniz. Görüntülemek için bilgi bankanızın adını seçin, örneğin Soru-Cevap Oluşturma SSS.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Soru-Cevap Oluşturma (V4) REST API Başvurusu](https://go.microsoft.com/fwlink/?linkid=2092179)
