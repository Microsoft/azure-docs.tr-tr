---
title: "Hızlı başlangıç: Bilgi Bankası 'ndan yanıt almak için kıvrımlı kullanma-Soru-Cevap Oluşturma"
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, bilgi bankasından kıvrımlı aracılığıyla yanıt alma işlemi adım adım gösterilmektedir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: b698b40546ee1655ebbef3980692ede6b51fc7f1
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803019"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Hızlı başlangıç: kıvrımlı kullanarak Bilgi Bankası 'ndan yanıt alın

Bu kıvrımlı tabanlı hızlı başlangıç, bilgi tabanınızdan yanıt alma konusunda size yol gösterir.

## <a name="prerequisites"></a>Önkoşullar

* En son [**kıvır**](https://curl.haxx.se/).
* Bir [soru-cevap oluşturma hizmetine](../How-To/set-up-qnamaker-service-azure.md) sahip olmanız ve [sorular ve yanıtlar ile Bilgi Bankası](../Tutorials/create-publish-query-in-portal.md)etmeniz gerekir.

## <a name="publish-to-get-endpoint"></a>Uç nokta almak için Yayımla

Bilgi tabanınızdan bir soruya yanıt oluşturmaya hazırsanız, bilgi bankaınızı [yayımlayın](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) .

## <a name="use-production-endpoint-with-curl"></a>Kıvrımlı ile üretim uç noktasını kullanma

Bilgi Bankası 'niz yayımlandığında **Yayımla** sayfası, yanıt oluşturmak için http isteği ayarlarını görüntüler. **Kıvrımlı** sekmesinde, komut satırı aracından bir yanıt oluşturmak için gereken ayarlar [gösterilmektedir.](https://www.getpostman.com)

[![Sonuçları Yayımla](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

KıVRıMLı bir yanıt oluşturmak için aşağıdaki adımları izleyin:

1. Metni KıVRıMLı sekmesine kopyalayın. 
1. Bir komut satırı veya Terminal açın ve metni yapıştırın.
1. Bilgi Tabanınızla ilgili olacak soruyu düzenleyin. Soruyu çevreleyen içeren JSON 'ı kaldırmamaya dikkat edin.
1. Komutunu girin. 
1. Yanıt, Yanıtla ilgili bilgileri içerir. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>Kıvrımlı ile hazırlama uç noktası kullanma

Hazırlama uç noktasından bir yanıt almak istiyorsanız `isTest` Body özelliğini kullanın.

```json
isTest:true
```

## <a name="next-steps"></a>Sonraki adımlar

Yayımla sayfası Postman ile [yanıt oluşturmak](get-answer-from-kb-using-postman.md) için bilgi de sağlar. 

> [!div class="nextstepaction"]
> [Yanıt oluştururken meta verileri kullanma](../How-to/metadata-generateanswer-usage.md)
