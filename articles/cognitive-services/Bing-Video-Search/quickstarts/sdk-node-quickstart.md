---
title: "Hızlı Başlangıç: Node. js için Bing Video Arama SDK 'sını kullanarak video arayın"
titleSuffix: Azure Cognitive Services
description: Node. js için Bing Video Arama SDK 'sını kullanarak video arama istekleri göndermek için bu hızlı başlangıcı kullanın
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: aahi
ms.openlocfilehash: 12eafca9c673d95813eefcd58d2b3f9ba7b54fd3
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358892"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>Hızlı Başlangıç: Node. js için Bing Video Arama SDK ile video araması gerçekleştirme

Node. js için Bing Video Arama SDK 'Sı ile haberleri aramaya başlamak için bu hızlı başlangıcı kullanın. Bing Video Arama birçok programlama dili ile uyumlu bir REST API sahip olsa da SDK, hizmeti uygulamalarınızla tümleştirmenin kolay bir yolunu sunar. Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js)' da bulunabilir. Daha fazla ek açıklama ve özellik içerir.

## <a name="prerequisites"></a>Önkoşullar

- [Node.js](https://www.nodejs.org/)

Bing Video Arama SDK kullanarak bir konsol uygulaması ayarlamak için:
* Geliştirme `npm install ms-rest-azure` ortamınızda çalıştırın.
* Geliştirme `npm install azure-cognitiveservices-videosearch` ortamınızda çalıştırın.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Uygulamayı oluşturma ve başlatma

1. En sevdiğiniz IDE veya düzenleyicide yeni bir JavaScript dosyası oluşturun ve Bing video arama SDK ve `require()` `CognitiveServicesCredentials` modül için bir ifade ekleyin. Abonelik anahtarınız için bir değişken oluşturun. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Anahtarınızla bir örnek `CognitiveServicesCredentials` oluşturun. Daha sonra video arama istemcisinin bir örneğini oluşturmak için bunu kullanın.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Arama isteğini gönder

1. Bing video arama API'si `client.videosOperations.search()` bir arama isteği göndermek için kullanın. Arama sonuçları döndürüldüğünde, sonucu günlüğe kaydetmek için `.then()` kullanın.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Tek sayfalı Web uygulaması oluşturma](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Ayrıca bkz. 

* [Bing Video Arama API'si nedir?](../overview.md)
* [Bilişsel Hizmetler .NET SDK örnekleri](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)