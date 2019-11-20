---
title: "Hızlı başlangıç: Bilgi Bankası 'ndan yanıt almak için Postman kullanın-Soru-Cevap Oluşturma"
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Postman kullanarak bilgi tabanınızdan yanıt alma işlemi adım adım gösterilmektedir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 01/01/2019
ms.author: diberry
ms.openlocfilehash: 3df2703ba24091d0d21f06ae55aca389837e93d9
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803043"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Hızlı başlangıç: Postman kullanarak Bilgi Bankası 'ndan bir yanıt alın

Bu Postman tabanlı hızlı başlangıç, bilgi tabanınızdan yanıt alma konusunda size yol gösterir.

## <a name="prerequisites"></a>Önkoşullar

* En son [**Postman**](https://www.getpostman.com/).
* Bir [soru-cevap oluşturma hizmetine](../How-To/set-up-qnamaker-service-azure.md) sahip olmanız ve [sorular ve yanıtlar ile Bilgi Bankası](../Tutorials/create-publish-query-in-portal.md)etmeniz gerekir. 

## <a name="publish-to-get-endpoint"></a>Uç nokta almak için Yayımla

Bilgi tabanınızdan bir soruya yanıt oluşturmaya hazırsanız, bilgi bankaınızı [yayımlayın](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) .

## <a name="use-production-endpoint-with-postman"></a>Üretim uç noktasını Postman ile kullanma

Bilgi Bankası 'niz yayımlandığında **Yayımla** sayfası, yanıt oluşturmak için http isteği ayarlarını görüntüler. Varsayılan Görünüm [Postman](https://www.getpostman.com)'dan bir yanıt oluşturmak için gereken ayarları gösterir.

Aşağıdaki resimdeki sarı sayılar, aşağıdaki adımlarda kullanılacak ad/değer çiftlerini gösterir.

[![Sonuçları Yayımla](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Postman ile yanıt oluşturmak için aşağıdaki adımları izleyin:

1. Postman 'yi açın. Bir yapı taşı seçmeniz istenirse, **temel istek** yapı taşını seçin. **İstek adını** `Generate QnA Maker answer`ve **koleksiyon** olarak `Generate QnA Maker answers` olarak ayarlayın. Bir koleksiyona kaydetmek istemiyorsanız **iptal** düğmesini seçin.
1. Çalışma alanında, **Post**'un http yöntemini seçin.

    [@no__t--Postman 'Da, POST yöntemini ayarla](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. URL için, tüm URL 'YI oluşturmak için ana bilgisayar değerini (görüntüden #2) ve gönderi değerini (görüntüden #1) birleştirin. Tüm örnek URL şöyle görünür: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [@no__t--Postman 'Da, tam URL 'yi ayarlayın](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. URL 'nin altındaki **üstbilgiler** sekmesini seçin ve **Toplu Düzenle**' yi seçin. 

1. Üstbilgileri (#3 ve #4 görüntüden) metin alanına kopyalayın.

    [@no__t--Postman 'Da, üst bilgileri ayarlama](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. **Gövde** sekmesini seçin.
1. **Ham** biçimi seçin ve soruyu temsıl eden JSON (#5 görüntüden) girin.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [@no__t-Postman 'Da, Body JSON değerini ayarlayın](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. **Gönder** düğmesini seçin.
1. Yanıt, istemci uygulaması için önemli olabilecek diğer bilgilerle birlikte yanıtı içerir. 

    [@no__t-Postman 'Da, Body JSON değerini ayarlayın](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Hazırlama uç noktası kullan

Hazırlama uç noktasından bir yanıt almak isterseniz, URL 'YI `isTest` Body özelliğine ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

Yayımla sayfası, kıvrımlı [bir yanıt oluşturmak](get-answer-from-kb-using-curl.md) için bilgi de sağlar. 

> [!div class="nextstepaction"]
> [Yanıt oluştururken meta verileri kullanma](../How-to/metadata-generateanswer-usage.md)
