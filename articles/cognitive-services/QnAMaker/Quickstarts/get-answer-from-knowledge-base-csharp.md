---
title: "Hızlı başlangıç: Bilgi Bankası 'ndan yanıt alın-REST C# ,-soru-cevap oluşturma"
titleSuffix: Azure Cognitive Services
description: Bu C# REST tabanlı hızlı başlangıçta, Bilgi Bankası 'ndan programlama yoluyla yanıt alma işlemi adım adım gösterilmektedir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: e06ded1b684d442c9f6257ebd92b3a3b8fdb87a3
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802999"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Hızlı başlangıç: ile bilgi bankasındaki bir soruya yanıt alınC#

Bu hızlı başlangıçta, yayımlanan Soru-Cevap Oluşturma bilgi bankasından programlı bir yanıt alma işlemi adım adım gösterilmektedir. Bilgi Bankası, SSS gibi [veri kaynaklarından](../Concepts/data-sources-supported.md) gelen sorular ve yanıtlar içerir. [Soru](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) soru-cevap oluşturma hizmetine gönderilir. [Yanıt](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) , en fazla tahmini yanıtı içerir. 


## <a name="prerequisites"></a>Önkoşullar

* En son [**Visual Studio Community sürümü**](https://www.visualstudio.com/downloads/).
* [Soru-Cevap Oluşturma hizmetine](../How-To/set-up-qnamaker-service-azure.md) sahip olmanız gerekir. Anahtarınızı almak için Azure panonuzda Soru-Cevap Oluşturma kaynağınız için **kaynak yönetimi** altındaki **anahtarlar** ' ı seçin. 
* **Yayımlama** sayfası ayarları. Yayımlanmış bir bilgi tabanınız yoksa, boş bir Bilgi Bankası oluşturun ve ardından **Ayarlar** sayfasına bir Bilgi Bankası alın ve ardından yayımlayın. [Bu temel Bilgi Bankası](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)'nı indirip kullanabilirsiniz. 

    Yayımlama sayfası ayarları, POST yol değerini, ana bilgisayar değerini ve EndpointKey değerini içerir. 

    ![Yayımlama ayarları](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Bu hızlı başlangıç kodu [https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/get-answer) deposudur. 

## <a name="create-a-knowledge-base-project"></a>Bilgi bankası projesi oluşturma

1. Visual Studio 2019 Community Edition 'ı açın.
1. Yeni bir konsol uygulaması (.NET Core) projesi oluşturun ve projeyi QnaMakerQuickstart olarak adlandırın. Diğer ayarlar için varsayılan değerleri kabul edin.

## <a name="add-the-required-dependencies"></a>Gerekli bağımlılıkları ekleme

Projeye gerekli bağımlılıkları eklemek için Program.cs dosyasının en üstünde, tek using ifadesini aşağıdaki satırlarla değiştirin:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Gerekli sabitleri ekleme

@No__t-0 sınıfının en üstünde, `Main` ' in içinde, Soru-Cevap Oluşturma erişmek için gereken sabitleri ekleyin. Bu değerler, Bilgi Bankası 'nı yayımladıktan sonra **Yayımla** sayfasında bulunur. 

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=9-41 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Soru göndermek ve yanıt almak için bir POST isteği ekleyin

Aşağıdaki kod, soruyu bilgi tabanına göndermek için Soru-Cevap Oluşturma API'si HTTPS isteği yapar ve yanıtı alır:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=43-76 "Add a POST request to send question to knowledge base")]

@No__t-0 üstbilgisinin değeri, `EndpointKey` dizesini içerir. 

[İstek](../how-to/metadata-generateanswer-usage.md#generateanswer-request) ve [Yanıt](../how-to/metadata-generateanswer-usage.md#generateanswer-response)hakkında daha fazla bilgi edinin. 

## <a name="build-and-run-the-program"></a>Programı derleme ve çalıştırma

Visual Studio 'dan programı derleyin ve çalıştırın. İstek otomatik olarak Soru-Cevap Oluşturma API'si gönderilir, ardından konsol penceresine yazdırılır.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Soru-Cevap Oluşturma (V4) REST API Başvurusu](https://go.microsoft.com/fwlink/?linkid=2092179)
